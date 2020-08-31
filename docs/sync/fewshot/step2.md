
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