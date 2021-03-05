# Anomaly detection

Anomaly detection(이상치 탐지)란 예상된 동작에 맞지 않은 비정상적인 패턴을 식별하는 기술입니다.

Anomaly detection는 Novelty detection , Outlier detection라고 부르기도 합니다. 

## Anomaly detection 예시

* 금융 분야의 Fraud Detection - 구매 장소, 구매 금액 등 평소 고객의 소비 패턴과 다른 패턴을 탐지하여 신용카드 복제 및 도난 의심 , 금융 관련 데이터에서 불법 행위 검출
* Medical Anomaly Detection - 의료 영상, 뇌파 기록 등 의학 데이터에 대해서 이상치 탐색
* Fault detection - 제조업에서 불량 관리
* 등 ....

## Supervised learning

학습 데이터셋에 정상/비정상 라벨이 있는 경우

Anomaly detection에서는 정상 데이터가 비정상 데이터 보다 많고, 비정상적인 데이터의 수가 매우 적다는 특징 때문에 데이터 확보에 많은 시간과 비용이 들게 됩니다.

## Semi-supervised 

라벨이 없는 데이터가 다량 있고, 소량의 라벨링된 데이터를 사용하는 경우 

* Deep SAD(Deep Semi-supervised Anomaly Detection)

## Unsupervised learning

대부분 데이터가 normal이라고 가정하여 Label이 존재하지 않은 데이터를 사용하는 경우

* Autoencoder
  * 정상 데이터의 중요한 특징들만 압축적으로 배울 수 있습니다.
  * 학습된 Autoencoder에 정상 데이터를 넣으면 잘 복원하지만 비정상 데이터를 넣으면 잘 복원하지 못합니다.
  * ![autoencoder 이미지 검색결과](https://miro.medium.com/max/3110/0*uq2_ZipB9TqI9G_k)
* AnoGAN
* SVDD
* Deep SVDD

## SVDD(Support Vector Data Description)

SVDD는 input space에 있는 데이터들을 특정 kernel function에 넣어 feature space에 mapping하고,  mapping된 정상 데이터를 둘러싸는 가장 작은 구를 찾는 것입니다. 이 구를 기반으로 구 안에 있는 데이터는 정상 데이터, 경계 밖에 있는 데이터는 비정상 데이터로 구분합니다.

* Parameters
  * R: 구의 반지름, c: 구의 중심, penalties for soft margin
* Hyperparameters
  * kernel function

![image-20210216210638900](C:\Users\sunpy\AppData\Roaming\Typora\typora-user-images\image-20210216210638900.png)



## Deep SVDD (Unsupervised)
[Code - Github](https://github.com/lukasruff/Deep-SVDD-PyTorch)

Deep SVDD는기존 SVDD에서 input space에서 feature space로 매칭하는 방법이 kernel function에서 neural network로 바꾼 것입니다.

정상 데이터는 구 안으로, 비정상 데이터는 구 밖으로 매칭하도록 Neural network를 학습합니다.

또한 신경망의 가중치 W는 정상 데이터의 공통적인 요소를 추출하여 데이터를 구의 중심 c에 가깝게 하도록 합니다.

Deep SVDD는 구 외부에 있는 데이터에 패널티를 부여하는 Soft-boundary Deep SVDD와 단순화한 One-Class Deep SVDD가 있습니다.

![image-20210215204258351](C:\Users\sunpy\AppData\Roaming\Typora\typora-user-images\image-20210215204258351.png)

![deep SVDD 이미지 검색결과](https://postfiles.pstatic.net/MjAyMDExMTJfMTUx/MDAxNjA1MTgxNjAyNzkw.YBBiQr2ZcpinwUqwyvjTOMTzV2oMsd-qXPF1KtbU3Lwg.knZdrABB-zvKaCXpVaz7dfag71FlmUsNXXmQ_zjf25Qg.PNG.winddori2002/%EC%BA%A1%EC%B2%98.PNG?type=w773)

#### Soft-boundary Deep SVDD

* Parameters
  * W : 가중치, R : 반지름
* Hyperparameters
  * c : center
* Objective Function

![image-20210216212134287](C:\Users\sunpy\AppData\Roaming\Typora\typora-user-images\image-20210216212134287.png)

W와 R을 동시에 최적화하기 위해 W와 R을 교대로 최적화합니다.

w=0, R=0,  c=0 --> 모든 값이 무조건 0 -> 이를 방지하기 위해 c를 

Deep SVDD는 CNN 구조로 동일한 구조의 encoder를 가진 pre-trained Convolutional AutoEncoder의 encoder parameters를 가중치 초기값으로 사용 -> 초기 값으로 설정한 CNN에 모든 train data를 넣어 feature를 추출하고, 그 feature의 평균을 초기 c로 설정함

모든 output이 c와 같아지는 것을 방지하기 위해 bias term을 사용하지 않음

#### One-Class Deep SVDD
[One-Class Code - Github](https://github.com/lukasruff/Deep-SVDD)
Objective 최적화를 통해 W는 feature space로 매핑되는 데이터를 구의 중심 c와 가깝게 하도록 학습합니다.

* Objective Function

![image-20210216215549860](C:\Users\sunpy\AppData\Roaming\Typora\typora-user-images\image-20210216215549860.png)

* anomaly score
  * W* = 학습된 가중치
  * 정상 데이터

![image-20210216220722270](C:\Users\sunpy\AppData\Roaming\Typora\typora-user-images\image-20210216220722270.png)