# RNN


- Recuurent Neural Network는 hidden layer에서 나온 결과 값이 다시 hidden layer로 들어가 새로운 input 값과 연산을 수행하는 순환구조이다.
  FC layer, CNN은 hidden layer에서 나온 결과 값이 output layer 방향으로 이동하지만, RNN은 출력층 방향으로 보내면서도 hidden layer node로 다시 들어가 순환한다는 점에서 차이가 있다.

  <img width="692" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/2dc710d2-610b-4419-be09-a2cf7870caa7">

  다음은 RNN의 순환구조인데, 오른쪽의 구조는 왼쪽의 재귀적 구조를 펼친 형태이다. input으로 [x_0,x_1, ... , x_t]를 순차적으로 넣었을때 RNN cell에선 각각에 대응하여 [h_0,h_1, ... , h_t]을 출력한다.
  이때 RNN layer는 모두 같은 층이며 [h_0,h_1, ... , h_t]를 각각 k 시점에서의 hidden state(RNN 셀이 출력층 방향 또는 다음 시점인 t+1의 자신에게 보내는 값)라고 한다.

  이러한 구조 덕분에 RNN은 시계열 데이터와 텍스트 데이터에 적합하다.

  - 또한 RNN은 입력과 출력의 길이를 다르게 설계하는 것이 가능하므로 다양한 용도로 사용할 수 있다.

    <img width="434" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/ecea8b27-88e6-405a-bc3d-1e40c275355d">

    - one-to-many : 하나의 이미지 입력에 대해서 제목이나 상황을 출력하는 image captioning에 활용할 수 있다.
    - many-to-one : word sequence에 대해서 하나의 출력을 하므로 sentiment calssification, spam detection 등의 task에 활용할 수 있다.
    - many-to-many : 문장을 입력하면 문장이 나오는 번역이나 챗봇등에 활용할 수 있다.

- RNN의 수식
  <img width="327" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/3e58b3de-dbb5-4280-81be-8697a34f3dba">

  현재 시점 t에서의 hidden state값을 $h_t$라고 정의하면 hidden layer의 RNN cell은 $h_t$을 계산하기 위해서 총 두개의 weight를 사용한다. 하나는 input layer를 위한 weight $W_x$이고,
  하나는 이전 시점 t-1의 hidden state값인 h_t-1을 위한 weight $W_h$이다.

  이를 식으로 표현하면
  - hidden layer : $h_t$ = $tanh$($W_x$ $x_t$ + $W_t$ h_t-1 + b)
  - output layer : $y_t$ = $f$($W_t$ $h_t$ + b) 단, $f$는 non-linear activation function 중 하나.

  RNN의 hidden layer 연산을 vector와 행렬 연산으로 표현하면 다음과 같다.

  ($x_t$ = word vector, $d$ = dimension(word vector), $D_h$ = hidden state의 크기)

  - $x_t$ = (dx1)
  - $W_x$ = ($D_h$ x d)
  - $W_h$ = ($D_h$ x $D_h$)
  - $h$ _t-1 = ($D_h$ x 1)
  - $b$ = ($D_h$ x 1)

  <img width="640" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/fa76e7a4-f4cc-4d67-a9d2-f4a6e31bd38f">

  (Batch size : 1, d, $D_h$ = 4)

- 예를 들어 'You say goodbye and I say hello'에서 각 단어들의 다음 단어를 예측하는 문제를 푼다고 할때, 문장에서 embedding된 단어를 순차적으로 RNN에 입력한다.
  이후 각각에 입력이 RNN cell을 통과하여 출력된 hidden state값(해당 시점까지의 단어 정보를 나타냄)으로 다음에 나올 단어를 예측한다.

  이처럼 feed forward와 back propagation을 반복하며 model을 학습시켜서 최종적으로 각 단어 다음에 나올 단어를 정확도가 높게 예측하는 hidden state를 출력하는 것이 주요 목적 중 하나이다.

  <img width="732" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/d79e00f8-f845-4558-b649-5bb9c1a1148e">

  이 그림은 'You say goodbye and I say hello'에서 각 단어 다음에 나올 단어를 예측하는 RNN구조를 시각화한것이다.

  You가 embedding을 거친 후 RNN에 입력되면, you의 hidden state인 $h_you$가 출력되고 다음에 올 단어인 say를 에측한다.
  그 후 hidden state $h_you$은 다시 다음 RNN cell로 들어가 embedding된 say와 연산을 진행한다. 이것이 반복.

  - feed forward와 back propagation의 구조

    - feed forward
      
      <img width="696" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/eb78ff69-04e3-47b6-8d7b-3d0e8f163be4">

      <img width="676" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/74355856-ba3e-4fc2-a507-9e9606f04529">

    - backpropagation

      <img width="704" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/8e250597-956b-4b03-84f3-2b1c6275722d">

      <img width="666" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/de5163bb-4186-480b-ae2a-36e39bf50582">

      - Gradient vanishing
        - RNN에서 backpropagation이 진행되면서 기울기가 소실되는 문제가 발생함.

           <img width="684" alt="image" src="https://github.com/mySongminkyu/RNN/assets/132251519/3704395d-9d32-43ed-a6a5-8896ef920ddc">

          이는 역전파를 통해 기울기/미분값을 구하는 과정인데 $h_row$ 를 $w_h$로 미분하는 것이다.
          RNN의 특성상 전의 hidden state를 계속 반영하며 연산이 진행되는데 이처럼 미분이 계속 진행되다 보면 출력층에서 멀어질수록 기울기가 감소하는 문제가 생긴다.

          이를 gradient vanishing이라 하며, 추후에 LSTM이 이를 보완했다.




























*참조 : https://www.yes24.com/Product/Goods/72173703*, *https://wikidocs.net/22886*
