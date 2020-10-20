
!!! info

    이곳은 **fewshot-learning** 을 구현하기 위한 [**WICWIU**](https://github.com/WICWIU/WICWIU) 개발팀의 [**WICWIU**](https://github.com/WICWIU/WICWIU) 분석/코딩 과정을 기록해두는 곳입니다.

[**WICWIU** 의 학습 과정은](../../dev/wicwiu/learn.md) 다음과 같이 이루어졌다는 것을 이미 알아보았습니다.

- [Step 1. 학습 데이터 &rarr; `Tensor`](step1-1.md)

- Step 2. 순전파

- [Step 3. 역전파](step3.md)

- [Step 4. Weight 파라미터 갱신](step4.md)

여기는 fewshot 팀이 "**Step 2. 순전파**" 을 구현하기 위한 과정을 기록해두는 곳입니다.

---

순전파를 구현하기 위해서는 **FaceNet** 에 대한 이해가 필요하므로 **FaceNet** 논문을 읽어야 한다. 하지만 실제 논문을 읽기 전에 얼굴 인식에 대한 배경지식이 부족하여 [**Keras** 로 **FaceNet** 을 구현하여 얼굴 인식 시스템을 개발하는 블로그 글](https://machinelearningmastery.com/how-to-develop-a-face-recognition-system-using-facenet-in-keras-and-an-svm-classifier/) 을 참고하면서 **FaceNet** 을 이해하기 위한 사전작업을 해보았다.

!!! info

    **FaceNet** 사전지식과 논문 정리는 [이곳](../../paper/facenet.md)으로 이동되었습니다.

이렇게 **FaceNet** 을 이해해보았다. 어때요 참 쉽죠?

---

# Analysis: Forward Propagation

!!! info "새로운 분석 방향"

    MNIST 를 CNN 으로 학습하는 것을 어떻게 구현했는지 분석

!!! tldr "분석 지도"

    - [ ] `MNIST/main.cpp`: `main` 함수

## `MNIST/main.cpp`: `main` 함수

!!! help "분석 목표"

    MNIST 데이터셋 CNN 학습 이해하기

```c++ linenums="1"
int main(int argc, char const *argv[]) {
    ...
    // create input, label data placeholder -> Tensorholder
    Tensorholder<float> *x     = new Tensorholder<float>(1, BATCH, 1, 1, 784, "x");
    Tensorholder<float> *label = new Tensorholder<float>(1, BATCH, 1, 1, 10, "label");
    // ======================= Select net ===================
    NeuralNetwork<float> *net = new my_CNN(x, label);
    // ======================= Prepare Data ===================
    MNISTDataSet<float> *train_dataset = new MNISTDataSet<float>("data/train-images-idx3-ubyte", "data/train-labels-idx1-ubyte", TRAINING);
    DataLoader<float> * train_dataloader = new DataLoader<float>(train_dataset, BATCH, TRUE, 20, FALSE);
    MNISTDataSet<float> *test_dataset = new MNISTDataSet<float>("data/t10k-images-idx3-ubyte", "data/t10k-labels-idx1-ubyte", TESTING);
    DataLoader<float> * test_dataloader = new DataLoader<float>(test_dataset, BATCH, FALSE, 20, FALSE);

    net->SetDeviceGPU(GPUID);
    net->PrintGraphInformation();
    ...
    for (int i = epoch + 1; i < EPOCH; i++) {
        if ((i + 1) % 50 == 0) {
            float lr = net->GetOptimizer()->GetLearningRate();
            net->GetOptimizer()->SetLearningRate(lr * 0.1);
        }

        // ======================= Train =======================
        float train_accuracy = 0.f;
        float train_avg_loss = 0.f;
        net->SetModeTrain();

        for (int j = 0; j < LOOP_FOR_TRAIN; j++) {
            std::vector<Tensor<float> *> * temp =  train_dataloader->GetDataFromGlobalBuffer();
            Tensor<float> *x_t = (*temp)[0];
            Tensor<float> *l_t = (*temp)[1];
            delete temp;

            x_t->SetDeviceGPU(GPUID);  
            l_t->SetDeviceGPU(GPUID);
            net->FeedInputTensor(2, x_t, l_t);
            net->ResetParameterGradient();
            net->Train();
            train_accuracy += net->GetAccuracy();
            train_avg_loss += net->GetLoss();
            ...
        }

        // ======================= Test ======================
        float test_accuracy = 0.f;
        float test_avg_loss = 0.f;

        net->SetModeInference();

        for (int j = 0; j < (int)LOOP_FOR_TEST; j++) {
            std::vector<Tensor<float> *> * temp =  test_dataloader->GetDataFromGlobalBuffer();
            Tensor<float> *x_t = (*temp)[0];
            Tensor<float> *l_t = (*temp)[1];
            delete temp;
            x_t->SetDeviceGPU(GPUID);
            l_t->SetDeviceGPU(GPUID);

            net->FeedInputTensor(2, x_t, l_t);
            net->Test();

            test_accuracy += net->GetAccuracy();
            test_avg_loss += net->GetLoss();
            ...
        }
    }
    ...
    return 0;
}
```

!!! done "분석 결론"

    `main` 함수에서는 `Tensorholder` 를 생성하여 `my_CNN` 에 전달하여 모델을 만든다. 그리고 `MNISTDataSet` 으로 데이터를 가져오고 `DataLoader` 에 입력하여 데이터 로더를 만든다. 이후 `DataLoader::GetDataFromGlobalBuffer` 로 데이터를 가져와서 `my_CNN::FeedInputTensor` 로 모델에 입력하고, `my_CNN::Train` 으로 학습을 한다.

!!! info "새로운 분석 방향"
    
    `Tensorholder`, `my_CNN` 이 어떤 클래스인지 분석, `MNISTDataSet` 과 `DataLoader` 로 어떻게 MNIST 데이터셋을 가져와서 `my_CNN::FeedInputTensor` 로 입력하는지 분석, `my_CNN::Train` 으로 어떻게 학습이 진행되는지 분석.

    이때 `Tensorholder` 는 `Operator` 를 상속하므로 이것부터 분석.

!!! tldr "분석 지도"

    - [x] `MNIST/main.cpp`: `main` 함수

    - [ ] `src/Operator.hpp`: `Operator` 생성자

    - [ ] `src/Tensorholder.cpp`: `Tensorholder` 생성자

    - [ ] `MNIST/MNIST.hpp`: `MNISTDataSet` 생성자

    - [ ] `src/DataLoader.hpp`: `DataLoader` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN::Train` 메소드

## `src/Operator.cpp`: `Operator` 생성자

!!! help "분석 목표"

    Operator 생성자 이해하기

`main` 함수의 `Tensorholder` 는 다음과 같이 생성된다.

```c++ 
Tensorholder<float> *x     = new Tensorholder<float>(1, BATCH, 1, 1, 784, "x");
Tensorholder<float> *label = new Tensorholder<float>(1, BATCH, 1, 1, 10, "label");
```

그러므로 다음의 생성자가 호출된다.

```c++ 
Tensorholder(int pTimeSize, int pBatchSize, int pChannelSize, int pRowSize, int pColSize, std::string pName, int pTrainable = TRUE, int pLoadflag = TRUE) : Operator<DTYPE>(pName, pLoadflag) {
    this->Alloc(pTimeSize, pBatchSize, pChannelSize, pRowSize, pColSize, pTrainable);
}
```

그러므로 `Operator` 에서 다음의 생성자가 호출된다.

```c++ 
template<typename DTYPE> Operator<DTYPE>::Operator(std::string pName, int pLoadflag) {
    m_apOutput    = NULL;
    m_apInput     = NULL;
    m_aaResult    = NULL;
    m_aaGradient  = NULL;
    m_name        = pName;
    m_Device      = CPU;
    m_Mode        = TRAIN;
    m_isParameter = FALSE;
    m_isTrainable = FALSE;
    m_idOfDevice  = -1;
    m_Loadflag    = TRUE;
    Alloc();
}
```

`Alloc` 메소드는 다음과 같이 정의되어 있다.

```c++ 
template<typename DTYPE> int Operator<DTYPE>::Alloc() {
    m_apOutput   = new Container<Operator<DTYPE> *>();
    m_apInput    = new Container<Operator<DTYPE> *>();
    m_aaResult   = new Container<Tensor<DTYPE> *>();
    m_aaGradient = new Container<Tensor<DTYPE> *>();

    return TRUE;
}
```

!!! done "분석 결론"

    `Operator` 는 몇가지 변수를 초기화하고 `Container` 를 생성하여 멤버변수로 저장한다.

!!! info "새로운 분석 방향"
    
    `Container` 를 먼저 분석

!!! tldr "분석 지도"

    - [x] `MNIST/main.cpp`: `main` 함수

    - [x] `src/Operator.hpp`: `Operator` 생성자

    - [ ] `src/Container.hpp`: `Container` 생성자

    - [ ] `src/Tensorholder.cpp`: `Tensorholder` 생성자

    - [ ] `MNIST/MNIST.hpp`: `MNISTDataSet` 생성자

    - [ ] `src/DataLoader.hpp`: `DataLoader` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN::Train` 메소드

## `src/Container.hpp`: `Container` 생성자

!!! help "분석 목표"

    Container 생성자 이해하기

```c++ 
Container() {
    m_aElement = NULL;
    m_size     = 0;
}
```

필요한 변수를 초기화하고 끝낸다. `Container` 는 API 주석이 잘 달려있는데 단지 인덱스 접근과 역순 접근이 가능한 `queue` 라고 한다. 

!!! note

    그러면 `std::deque` 를 쓰면 안될까.

!!! tldr "분석 지도"

    - [x] `MNIST/main.cpp`: `main` 함수

    - [x] `src/Operator.hpp`: `Operator` 생성자

    - [x] `src/Container.hpp`: `Container` 생성자

    - [ ] `src/Tensorholder.cpp`: `Tensorholder` 생성자

    - [ ] `MNIST/MNIST.hpp`: `MNISTDataSet` 생성자

    - [ ] `src/DataLoader.hpp`: `DataLoader` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN::Train` 메소드

## `src/Tensorholder.cpp`: `Tensorholder` 생성자

!!! help "분석 목표"

    ```c++ 
    Tensorholder<float> *x     = new Tensorholder<float>(1, BATCH, 1, 1, 784, "x");
    Tensorholder<float> *label = new Tensorholder<float>(1, BATCH, 1, 1, 10, "label");
    ```

    위 코드 에서의 Tensorholder 생성자 이해하기

```c++ 
Tensorholder(int pTimeSize, int pBatchSize, int pChannelSize, int pRowSize, int pColSize, std::string pName, int pTrainable = TRUE, int pLoadflag = TRUE) : Operator<DTYPE>(pName, pLoadflag) {
    this->Alloc(pTimeSize, pBatchSize, pChannelSize, pRowSize, pColSize, pTrainable);
}
```

`Alloc` 메소드는 다음과 같다.

```c++  linenums="1"
template<typename DTYPE> class Tensorholder : public Operator<DTYPE>{
...
    int Alloc(int pTimeSize, int pBatchSize, int pChannelSize, int pRowSize, int pColSize, int pTrainable) {
        Tensor<DTYPE> *pTensor = Tensor<float>::Zeros(pTimeSize, pBatchSize, pChannelSize, pRowSize, pColSize);
        this->SetResult(pTensor);
        this->SetIsTensorholder(TRUE);
        this->SetIsTrainable(pTrainable);
        Shape *shapeOfDelta = new Shape(pTensor->GetShape());
        this->AddGradient(new Tensor<DTYPE>(shapeOfDelta));
        return TRUE;
    }
...
}
```

- 5:

    `SetResult` 는 `Operator` 에 정의되어 있는데, 다음과 같다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::SetResult(Tensor<DTYPE> *pTensor) {
        if (m_aaResult->GetSize()) {
            Tensor<DTYPE> *temp = m_aaResult->Pop();
            delete temp;
            temp = NULL;
        }
        m_aaResult->Push(pTensor);
        return TRUE;
    }
    ```

    `Container<Tensor<DTYPE> *> *m_aaResult;` 의 `Container::GetSize` 메소드는 다음과 같다.

    ```c++ 
    template<typename DTYPE> class Container {
    ...
        int GetSize() {
            return m_size;
        }
    ...
    }
    ```

    `m_size` 는 `Container` 생성자에서 `0` 으로 초기화된 것을 이미 확인했다.

    ```c++ 
    template<typename DTYPE> class Container {
        ...
        int Push(DTYPE pElement) {
            DTYPE *temp = new DTYPE[m_size + 1];
            for (int i = 0; i < m_size; i++) 
                temp[i] = m_aElement[i];
            temp[m_size] = pElement;

            if (m_aElement) {
                delete[] m_aElement;
                m_aElement = NULL;
            }

            m_aElement = temp;
            return TRUE;
        }
        ...
    }
    ```

    `Push` 는 단지 `m_size + 1` 동적 배열을 할당하고 기존의 배열을 복사한 후 마지막 인덱스에 파라미터를 저장한다.

    결국 `this->SetResult(pTensor);` 란 `m_aaResult` 라는 `queue` 에 입력 데이터의 형상으로 생성된 `Tensor` 객체를 `Push` 한다.

- 6:

    `Operator` 의 `SetIsTensorholder` 는 다음과 같다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::SetIsTensorholder(int pIsParameter) {
        m_isParameter = pIsParameter;
        return TRUE;
    }
    ```

    `m_isParameter` 는 생성시 `FALSE` 로 초기화되는데 이 코드로 `TRUE` 로 바뀐다.

- 7:

    `Operator` 의 `SetIsTrainable` 는 다음과 같다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::SetIsTrainable(int pIsTrainable) {
        m_isTrainable = pIsTrainable;
        return TRUE;
    }
    ```

    `m_isTrainable` 는 생성시 `FALSE` 로 초기화되는데 이 코드로 `TRUE` 로 바뀐다.

- 9:

    `Operator` 의 `AddGradient` 는 다음과 같다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::AddGradient(Tensor<DTYPE> *pTensor) {
        m_aaGradient->Push(pTensor);
        return TRUE;
    }
    ```

    결국 `Operator` 멤버변수인 `Container<Tensor<DTYPE> *> *m_aaGradient;` 에 `pTensor` 형상의 새로운 `Tensor` 객체를 `Push` 한다.

!!! done "분석 결론"

    `Tensorholder` 는 입력 데이터 형상으로 `pTensor` 를 만들어서 `m_aaResult` 큐에 푸쉬하고, 동일한 형상의 `Tensor` 를 만들어서 `m_aaGradient` 큐에 `Push` 한다.

    그리고 `m_isParameter` 와 `m_isTrainable` 을 `TRUE` 로 만든다.

!!! tldr "분석 지도"

    - [x] `MNIST/main.cpp`: `main` 함수

    - [x] `src/Operator.hpp`: `Operator` 생성자

    - [x] `src/Container.hpp`: `Container` 생성자

    - [x] `src/Tensorholder.cpp`: `Tensorholder` 생성자

    - [ ] `MNIST/MNIST.hpp`: `MNISTDataSet` 생성자

    - [ ] `src/DataLoader.hpp`: `DataLoader` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN` 생성자

    - [ ] `MNIST/net/my_CNN.hpp`: `my_CNN::Train` 메소드

## `src/Module.hpp`: `Module` 생성자

!!! help "분석 목표"

    `Module` 생성자 이해하기

```c++  linenums="1"
template<typename DTYPE> Module<DTYPE>::Module(std::string pName) : Operator<DTYPE>(pName) {
    m_aaExcutableOperator    = NULL;
    m_numOfExcutableOperator = 0;
    m_pLastOperator          = NULL;
    m_idOfDevice             = -1;
    Alloc();
}
```

이 생성자가 정의되어 있는데 `Module()` 생성자가 호출되므로 디폴트 생성자가 호출된다. 근데 디폴트 생성자가 안보이네. 그런데 선언부에 `Module(std::string pName = "No Name");` 이렇게 선언되어 있더라. 그래서 이 생성자가 잘 호출된다.

- 6:

    ```c++ 
    template<typename DTYPE> int Module<DTYPE>::Alloc() {
        m_apInput             = new Container<Operator<DTYPE> *>();
        m_apParameter         = new Container<Operator<DTYPE> *>();
        m_aaExcutableOperator = new Container<Operator<DTYPE> *>();
        return TRUE;
    }
    ```

## `src/NeuralNetwork.hpp`: `NeuralNetwork` 생성자

!!! help "분석 목표"

    이해하기

```c++ linenums="1"
template<typename DTYPE> NeuralNetwork<DTYPE>::NeuralNetwork() : Module<DTYPE>() {
    m_aLossFunction = NULL;
    m_aOptimizer    = NULL;
    m_Device     = CPU;
    m_idOfDevice = -1;
#ifdef __CUDNN__
    m_cudnnHandle = NULL;
#endif  // if __CUDNN__
    Alloc();
}
```

- 1:

    `Module()` 의 디폴트 생성자가 호출된다.

- 2: 

    멤버 변수를 초기화한다.

    ```c++ 
    LossFunction<DTYPE> *m_aLossFunction; ///< 신경망의 손실함수
    Optimizer<DTYPE> *m_aOptimizer; ///< 신경망의 Optimizer
    Device m_Device; ///< 장치 사용 구분자, CPU 또는 GPU, Device 참고
    int m_idOfDevice; ///< GPU 사용 시, 사용하려는 GPU의 번호. CPU의 경우 -1
    ```

- 9:

    ```c++ 
    template<typename DTYPE> int NeuralNetwork<DTYPE>::Alloc() { return TRUE; }
    ```

## `MNIST/my_CNN.hpp`: `my_CNN` 생성자

!!! help "분석 목표"

    ```c++ 
    NeuralNetwork<float> *net = new my_CNN(x, label);
    ```

    이해하기

```c++  linenums="1"
class my_CNN : public NeuralNetwork<float>{
    ...
public:
    my_CNN(Tensorholder<float> *x, Tensorholder<float> *label) {
        SetInput(2, x, label);
        Operator<float> *out = NULL;
        out = new ReShape<float>(x, 28, 28, "Flat2Image");
        // ======================= layer 1=======================
        out = new ConvolutionLayer2D<float>(out, 1, 10, 3, 3, 1, 1, 0, FALSE, "Conv_1");
        out = new Relu<float>(out, "Relu_1");
        out = new Maxpooling2D<float>(out, 2, 2, 2, 2, "MaxPool_1");
        // ======================= layer 2=======================
        out = new ConvolutionLayer2D<float>(out, 10, 20, 3, 3, 1, 1, 0, FALSE, "Conv_2");
        out = new Relu<float>(out, "Relu_2");
        out = new Maxpooling2D<float>(out, 2, 2, 2, 2, "MaxPool_2");
        // ======================= layer 3=======================
        out = new ReShape<float>(out, 1, 1, 5 * 5 * 20, "Image2Flat");
        // ======================= layer 3=======================
        out = new Linear<float>(out, 5 * 5 * 20, 1024, TRUE, "Fully-Connected_1");
        out = new Relu<float>(out, "Relu_3");
        //// ======================= layer 4=======================
        out = new Linear<float>(out, 1024, 10, TRUE, "Fully-connected_2");
        AnalyzeGraph(out);
        // ======================= Select LossFunction Function ===================
        SetLossFunction(new SoftmaxCrossEntropy<float>(out, label, "SCE"));
        // ======================= Select Optimizer ===================
        SetOptimizer(new GradientDescentOptimizer<float>(GetParameter(), 0.001, 0.9, MINIMIZE));
    }
    ...
};
```

- 5:

    ```c++ 
    template<typename DTYPE> int Module<DTYPE>::SetInput(int pNumOfInput, ...) {
        Operator<DTYPE> *temp = NULL;
        va_list ap;
        va_start(ap, pNumOfInput);
        for (int i = 0; i < pNumOfInput; i++) {
            temp = va_arg(ap, Operator<DTYPE> *);
            this->SetInput(temp);
        }
        va_end(ap);
        return TRUE;
    }
    ```

    `Tensorholder` 를 가변인자로 전달하는데 `Operator` 의 자식 클래스이므로 `va_arg` 에서 저렇게 타입을 전달하는 것이 가능하다.

    이 메소드를 통해 다음의 메소드가 호출된다.

    ```c++ 
    template<typename DTYPE> Operator<DTYPE> *Module<DTYPE>::SetInput(Operator<DTYPE> *pInput) {
        m_apInput->Push(pInput);
        m_InputDegree++;
        this->AddEdgebetweenOperators(pInput);
        return pInput;
    }
    ```

    `m_apInput` 으로 `Tensorholder` 를 `Push` 한다. 즉 `x` 와 `label` 이 푸쉬된다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::AddEdgebetweenOperators(Operator<DTYPE> *pInput) {
        this->AddInputEdge(pInput);
        pInput->AddOutputEdge(this);
        return TRUE;
    }
    ```

    이 메소드로 들어와서 엣지를 서로 연결해준다.

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::AddInputEdge(Operator<DTYPE> *pInput) {
        m_apInput->Push(pInput);
        ...
    }
    ```

    ```c++ 
    template<typename DTYPE> int Operator<DTYPE>::AddOutputEdge(Operator<DTYPE> *pOutput) {
        m_apOutput->Push(pOutput);
        ...
    }
    ```

    근데 `SetInput` 메소드에서 `m_apInput->Push(pInput);` 을 해줬는데 왜 `AddInputEdge` 에서 또 해주는 거지?

    어쨌든 `Tensorholder` 의 아웃풋이 이 클래스 즉, `my_CNN` 이 되고, `my_CNN` 의 인풋이 텐서홀더가 된다.

- 7:

    ```cpp 
    template<typename DTYPE>
    class ReShape : public Operator<DTYPE>{
        ...
        ReShape(Operator<DTYPE> *pInput, int pRowSize, int pColSize, std::string pName, int pLoadflag = TRUE) : Operator<DTYPE>(pInput, pName, pLoadflag) {
            this->Alloc(pInput, 0, 0, 0, pRowSize, pColSize);
        }
        ...
    }
    ```

    위 생성자가 호출된다.

    ```cpp 
    template<typename DTYPE>
    class ReShape : public Operator<DTYPE>{
        ...
        int Alloc(Operator<DTYPE> *pInput, int pTimeSize, int pBatchSize, int pChannelSize, int pRowSize, int pColSize) {
            Shape *pInputShape = pInput->GetResult()->GetShape();
            if (pTimeSize == 0) pTimeSize = (*pInputShape)[0];
            if (pBatchSize == 0) pBatchSize = (*pInputShape)[1];
            if (pChannelSize == 0) pChannelSize = (*pInputShape)[2];
            Tensor<DTYPE> *result = new Tensor<DTYPE>(pInput->GetResult());
            result->ReShape(pTimeSize, pBatchSize, pChannelSize, pRowSize, pColSize);
            this->SetResult(result);  // copy data
            this->SetDelta(new Tensor<DTYPE>(pTimeSize, pBatchSize, pChannelSize, pRowSize, pColSize));
            return TRUE;
        }
        ...
    }
    ```

    이 코드는 단지 `1, BATCH, 1, 1, 784` 형상으로 생성된 텐서홀더를 `..., 28, 28` 형상으로 바꾸어주는 것 같다. 첨부터 `28, 28` 로 했으면 안바꿔도 되지 않나? 그리고 이렇게 `int` 를 받을 필요없이 가변 형상을 받을 수 있어야 할텐데.

    `SetResult` 는 `m_aaResult` 를 초기화하고 `SetDelta` 는 `m_aaGradient` 를 초기화한다.

- 9:

    ```cpp 
    template<typename DTYPE> class ConvolutionLayer2D : public Module<DTYPE>{
    private:
    public:
        ConvolutionLayer2D(Operator<DTYPE> *pInput, int pNumInputChannel, int pNumOutputChannel, int pNumKernelRow, int pNumKernelCol, int pStrideRow, int pStrideCol, int pPadding, int use_bias = FALSE, std::string pName = "NO NAME") : Module<DTYPE>(pName){
            Alloc(pInput, pNumInputChannel, pNumOutputChannel, pNumKernelRow, pNumKernelCol, pStrideRow, pStrideCol, pPadding, pPadding, use_bias, pName);
        }
    ```

    좀 전에 생성한 `28, 28` 형상 데이터와 입력 채널 `1`, 출력 채널 `10`, $3 \times 3$ 커널, 스트라이드 $s = 1 \times 1$, 패딩 $p = 0$ 으로 합성곱 계층을 만든다.

    ```cpp linenums="1"
    template<typename DTYPE> class ConvolutionLayer2D : public Module<DTYPE>{
        int Alloc(Operator<DTYPE> *pInput, int pNumInputChannel, int pNumOutputChannel, int pNumKernelRow, int pNumKernelCol, int pStrideRow, int pStrideCol, int pPaddingRow, int pPaddingCol, int use_bias, std::string pName) {
            this->SetInput(pInput);
            Operator<DTYPE> *out = pInput;
            Tensorholder<DTYPE> *pWeight = new Tensorholder<DTYPE>(Tensor<DTYPE>::Random_normal(1, pNumOutputChannel, pNumInputChannel, pNumKernelRow, pNumKernelCol, 0.0, 0.02), "Convolution2D_Weight_" + pName);
            out = new Convolution2D<DTYPE>(out, pWeight, pStrideRow, pStrideCol, pPaddingRow, pPaddingCol, "Convolution2D_Convolution2D_" + pName);

            if(use_bias){
                Tensorholder<DTYPE> *pBias = new Tensorholder<DTYPE>(Tensor<DTYPE>::Constants(1, 1, pNumOutputChannel, 1, 1, 0), "Convolution2D_Bias_" + pName);
                out = new AddChannelWise<DTYPE>(out, pBias, "Convolution2D_Add_" + pName);
            }

            this->AnalyzeGraph(out);

            return TRUE;
        }
    };
    ```

    - 3:

        이전에 생성된 텐서홀더를 `pInput` 으로 전달받아 `SetInput` 에 전달한다. 이 메소드는 이미 살펴봤듯이 `m_apInput` 에 `pInput` 을 `Push` 하고 `AddEdgebetweenOperators` 로 두 엣지를 서로 연결한다.
    
    - 5:

        $\mu = 0.0, \sigma = 0.02$ 로 생성된 랜덤값으로 가중치 커널을 생성한다.
    
    - 6:

        생성된 가중치 필터와 스트라이드, 패딩 정보를 `Convolution2D` 객체에 전달한다.

        ```cpp 
        template<typename DTYPE> class Convolution2D : public Operator<DTYPE>{
        private:
            int m_stride[2]; // [0]은 row, [1]은 colunm을 각각 의미한다.
            int m_padding[2]; // [0]은 height, [1]은 width를 각각 의미한다.
            ...
            Convolution2D(Operator<DTYPE> *pInput, Operator<DTYPE> *pWeight, int stride1, int stride2, std::string pName = "NO NAME", int pLoadflag = TRUE) : Operator<DTYPE>(pInput, pWeight, pName, pLoadflag) {
                Alloc(pInput, pWeight, stride1, stride2, 0, 0);
            }
            ...
        ```

        생성자는 단지 `Alloc` 메소드에 파라미터를 포워딩해준다. 그리고 `Operator` 상위 클래스에 `pInput`, 즉 기존에 `ConvolutionLayer2D` 의 `out` 으로 전달된 객체를 전달한다. 랜덤값으로 생성된 가중치도 `Operator` 에 함께 전달해준다. 이 경우 다음의 생성자가 호출된다.

        ```cpp 
        template<typename DTYPE> Operator<DTYPE>::Operator(Operator<DTYPE> *pInput0, Operator<DTYPE> *pInput1, std::string pName, int pLoadflag) {
            ...
            Alloc();
            AddEdgebetweenOperators(2, pInput0, pInput1, pLoadflag);
        }
        ```

        즉, 필요한 초기화를 한 후 전달된 두 `Operator` 를 서로 연결해주고 있다. 이후 `Alloc` 메소드로 입력 `Operator`, 가중치 `Operator`, 스트라이드, 패딩을 전달해준다. 

        !!! note
        
            패딩은 `0` 으로 하드코딩되어있네. 왜그럴까?

        ```cpp 
        template<typename DTYPE> class Convolution2D : public Operator<DTYPE>{
            ...
            int Alloc(Operator<DTYPE> *pInput, Operator<DTYPE> *pWeight, int stride1, int stride2, int padding1, int padding2) {
                int outputWidth  = 0;
                int outputHeight = 0;
                Shape *shapeOfInput  = pInput->GetResult()->GetShape();
                Shape *shapeOfWeight = pWeight->GetResult()->GetShape();
                m_stride[0] = stride1;
                m_stride[1] = stride2;
                m_padding[0] = padding1;
                m_padding[1] = padding2;

                outputHeight = ((*shapeOfInput)[3] - (*shapeOfWeight)[3] + (2 * m_padding[0])) / m_stride[0] + 1;
                outputWidth  = ((*shapeOfInput)[4] - (*shapeOfWeight)[4] + (2 * m_padding[1])) / m_stride[1] + 1;

                this->SetResult(new Tensor<DTYPE>((*shapeOfInput)[0], (*shapeOfInput)[1], (*shapeOfWeight)[1], outputHeight, outputWidth));
                this->SetDelta(new Tensor<DTYPE>((*shapeOfInput)[0], (*shapeOfInput)[1], (*shapeOfWeight)[1], outputHeight, outputWidth));

                return TRUE;
            }
        ```

        합성곱 계층에서 스트라이드와 패딩에 따른 출력 데이터 형상을 구하는 공식에 따라서 `outputHeight, outputWidth` 를 초기화한다.

        그러고 나서 적절한 형상으로 `Tensor` 객체를 만들고 `m_aaResult` 와 `m_aaGradient` 를 생성해주고 끝낸다.
    
    - 12:

        마지막으로 `AnalyzeGraph` 를 호출하고 끝낸다.

        ```cpp  linenums="1"
        template<typename DTYPE> Operator<DTYPE> *Module<DTYPE>::AnalyzeGraph(Operator<DTYPE> *pResultOperator) {
            Container<Operator<DTYPE> *> queue;
            queue.Push(pResultOperator);

            m_pLastOperator = pResultOperator;
            Container<Operator<DTYPE> *> *nextOp = NULL;
            Container<Operator<DTYPE> *> *prevOp = NULL;

            int numOfInputEdge                   = 0;
            Operator<DTYPE> *out = NULL;
            while (queue.GetSize() > 0) {
                out = queue.Pop();

                if (!(this->IsInput(out))) {
                    if (this->IsValid(out)) {
                        if (out->GetIsTensorholder()) {
                            this->SetParameter(out);
                        } else {
                            this->SetExecutableOperater(out);
                        }

                        nextOp         = out->GetInputContainer();
                        numOfInputEdge = nextOp->GetSize();

                        for (int i = 0; i < numOfInputEdge; i++) {
                            prevOp = (*nextOp)[i]->GetOutputContainer();
                            prevOp->Pop(out);
                            queue.Push((*nextOp)[i]);
                        }
                    } else continue;
                } else continue;
            }
            m_aaExcutableOperator->Reverse();
            return pResultOperator;
        }
        ```

        `AnalyzeGraph` 는 주석에 따르면 다음과 같은 기능을 한다.

        !!! cite "comment"
        
            학습 가능한 형태로 모듈 그래프를 구성해주는 메소드

            모듈의 Output에 해당하는 Operator를 매개변수로 받아 너비 우선 탐색으로 모듈 그래프를 구성한다.

            매개변수로 받은 모듈의 Output에 해당하는 Operator를 시작으로 모듈의 Input에 해당하는 Operator까지 역순으로, Operator가 Input Tensor 및 학습 파라미터인 경우 Module 클래스의 Input Container 멤버 변수에 추가하고 나머지 경우에는 Module 클래스의 Excutable Operator Container 멤버 변수에 추가한다.

            NeuralNetwork 클래스의 Excutable Operator Container 멤버 변수에 Operator들이 모두 추가되면 Container를 역순으로 변경한다.

            Operator 탐색 순서는 너비 우선 탐색을 따르며, 매개변수로 받은 Output Operator부터 해당 Operator의 Input Operator 리스트를 너비 우선 탐색 방식을 이용해 순서대로 진행한다.

            각 Operator들은 Module<DTYPE>::IsValid(Operator<DTYPE> *pOperator) 메소드를 이용하여 모듈 그래프 안에서의 중복 여부를 확인하며 중복되는 경우 그래프에 추가하지 않는다.

        메소드 이름과 다르게 `this->SetExecutableOperater(out);` 는 `Set` 기능을 하는 것이 아니라 `Add` 기능, 즉 `Push` 기능을 한다. 그러므로 결국 컨테이너 `m_aaExcutableOperator` 에 실행가능한 `Operator` 를 쭉 넣어주게 된다.


        `this->SetParameter(out);` 도 마찬가지로 파라미터인 `Operator` 를 `m_apParameter` 에 `Add` 해준다.

        22:

            `nextOp         = out->GetInputContainer();` 로 `out` 의 인풋 컨테이너에 존재하는 `Operator` 들을 `nextOp` 에 저장하고 다시 큐에 저장한다. 

            ```cpp 
            template<typename DTYPE> class ConvolutionLayer2D : public Module<DTYPE>{
            int Alloc(Operator<DTYPE> *pInput, int pNumInputChannel, int pNumOutputChannel, int pNumKernelRow, int pNumKernelCol, int pStrideRow, int pStrideCol, int pPaddingRow, int pPaddingCol, int use_bias, std::string pName) {
                this->SetInput(pInput);
                ...
            ```

            위 코드의 `SetInput` 에서 `pInput` 이 저장된 것이 나오게 된다. `pInput` 은 `out = new ReShape<float>(x, 28, 28, "Flat2Image");` 에서 온 것이었다. 이 `pInput` 이 다시 

            ```cpp 
            out = new Convolution2D<DTYPE>(out, pWeight, pStrideRow, pStrideCol, pPaddingRow, pPaddingCol, "Convolution2D_Convolution2D_" + pName);
            ```

            로 들어갔다가 `AnalyzeGraph` 로 들어간 것이었다. 

            다시.. `Convolution2D` 에서는 

            ```cpp 
            Convolution2D(Operator<DTYPE> *pInput, Operator<DTYPE> *pWeight, int stride1, int stride2, std::string pName = "NO NAME", int pLoadflag = TRUE) : Operator<DTYPE>(pInput, pWeight, pName, pLoadflag) {
                Alloc(pInput, pWeight, stride1, stride2, 0, 0);
            }
            ```

            이게 호출 되고, 이에 따라 `Operator` 에서 

            ```cpp 
            template<typename DTYPE> Operator<DTYPE>::Operator(Operator<DTYPE> *pInput0, Operator<DTYPE> *pInput1, std::string pName, int pLoadflag) {
                ...
                Alloc();
                AddEdgebetweenOperators(2, pInput0, pInput1, pLoadflag);
            }
            ```

            이게 호출 되고, 

            ```cpp 
            template<typename DTYPE> int Operator<DTYPE>::AddEdgebetweenOperators(Operator<DTYPE> *pInput) {
                this->AddInputEdge(pInput);
                pInput->AddOutputEdge(this);
                return TRUE;
            }
            ```

            이게 호출되고

            ```cpp 
            template<typename DTYPE> int Operator<DTYPE>::AddInputEdge(Operator<DTYPE> *pInput) {
                ...
                    m_apInput->Push(pInput);
                ...
            }
            ```

            이게 호출된다. 
            
            일단 `SetInput` 도 마찬가지로 `Set` 기능이 아니라 `Add` 기능을 하는 메소드였었다.

            그러니까 정리하자면 맨 처음에 `ConvolutionLayer2D` 의 `SetInput` 을 통해 `ConvolutionLayer2D` 객체의 `m_apInput` 에 전달된 `ReShape` 객체인 `pInput` 이 `Push` 되었고, `Convolution2D` 의 상위클래스 `Operator` 의 생성자 속의 `AddEdgebetweenOperators` 속의 `AddInputEdge` 에 `pInput` 에 저장되어 있던 `ReShape` 객체인 `out` 전달되는데, 이것이 `Convolution2D` 객체의 `m_apInput` 에 `Push` 되고, `ReShape` 객체의 `m_apOutput` 에 `Convolution2D` 가 `Push` 된다.

            !!! danger
            
                데이터 구조를 좀 심플하게 만들 필요가 있어보이는데, 중복으로 `Push` 하는 부분도 많아 보이고, 그래서 `isValid` 를 호출해야만 하는 것 같고,

                상속 구조도 코드 분석을 난해하게 만드는 불필요한 상속관계가 많은 것 같다.k

                전체적인 구조가 단순할 수 있는데, 불필요하게 너무 복잡해진 것 같고, 중복 로직이 너무 많고, 불필요한 로직도 너무 많아 보인다. 이러면 코드 관리가 계속 계속 힘들어지게 된다. 

            어쨌든 결국 `Convolution2D` 의 `GetInputContainer` 를 통해 `ReShape` 객체가 나오게 된다. 
            