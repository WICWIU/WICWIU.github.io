
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

이 생성자가 정의되어 있는데 `Module()` 생성자가 호출되므로 디폴트 생성자가 호출된다. 근데 디폴트 생성자가 안보이네.

- 6:

    ```c++ 
    template<typename DTYPE> int Module<DTYPE>::Alloc() {
        m_apInput             = new Container<Operator<DTYPE> *>();
        m_apParameter         = new Container<Operator<DTYPE> *>();
        m_aaExcutableOperator = new Container<Operator<DTYPE> *>();
        return TRUE;
    }
    ```

이러면 아마도 위에서 초기화한 멤버변수들에 쓰레기 값이 들어갈 것 같다.

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

    `Module()` 의 디폴트 생성자를 호출하는데 `Module` 클래스에서 정의되어 있지 않아서 멤버변수가 쓰레기값으로 초기화되지 않나?

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
    template<typename DTYPE> int NeuralNetwork<DTYPE>::Alloc() {
        return TRUE;
    }
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

    이 메소드를 통해 다음의 메소드가 호출된다.

    ```c++ 
    template<typename DTYPE> Operator<DTYPE> *Module<DTYPE>::SetInput(Operator<DTYPE> *pInput) {
        m_apInput->Push(pInput);
        m_InputDegree++;
        this->AddEdgebetweenOperators(pInput);

        return pInput;
    }
    ```

    `m_apInput` 으로 `Push` 해서 잘 사용하고 있는 것 보니까 객체가 잘 생성됐다는 건데 디폴트 생성자를 호출했는데 어떻게 객체가 잘 생성됐나?

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

    컨테이너 클래스의 `Push` 는 다음과 같이 새로운 배열을 생성하여 `m_aElement` 에 저장한다.

    ```c++ 
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
        m_size++;
        return TRUE;
    }
    ```
