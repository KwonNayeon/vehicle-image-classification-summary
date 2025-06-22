# EfficientNet-B0 기반 중고차 분류 모델

## 📋 EfficientNet-B0 슈도코드

### 1. **Setup**
   * torch, torchvision 등 PyTorch 관련 모듈
   * 이미지 처리용 라이브러리
   * efficientnet_pytorch에서 EfficientNet 불러오기

### 2. **디바이스 설정**
   * GPU 사용 가능하면 GPU로 설정 (torch.device 사용)

### 3. **하이퍼파라미터 설정**
   * 이미지 크기: 224 x 224
   * 배치 크기, 학습률, 에폭 수 등

### 4. **데이터셋 로드**
   * 학습 데이터 폴더 구성: ImageFolder 사용
   * 훈련/검증 데이터로 나누기 (train : val)
   * 전처리 설정: 리사이즈, 텐서 변환, 정규화 등
   * DataLoader 사용해 배치 단위로 불러오기

### 5. **모델 불러오기**
   * EfficientNet-B0 사전학습 모델 로드
   * 마지막 출력층을 차종 클래스 수(예: 396개)로 수정

### 6. **손실 함수 및 옵티마이저 설정**
   * 손실 함수: CrossEntropyLoss
   * 옵티마이저: Adam 등

### 7. **모델 학습 루프**
   * 각 에폭(epoch)마다 반복:
      * 모델을 학습 모드로 설정
      * 훈련 데이터로:
         * 순전파 (forward)
         * 손실 계산
         * 역전파 (backward)
         * 파라미터 업데이트
      * 모델을 평가 모드로 전환
      * 검증 데이터로 성능 평가 (정확도, 검증 손실 등)
      * 검증 정확도 향상 시 모델 저장

### 8. **테스트 데이터 예측**
   * 테스트 이미지 불러오기 및 동일한 전처리 적용
   * 학습된 모델로 추론 수행
   * 소프트맥스 확률값 추출

### 9. **제출 파일 생성**
   * 각 테스트 이미지에 대해 클래스별 확률값 정리
   * sample_submission.csv 형식에 맞게 저장

## 📌 구조 요약

| 단계 | 내용 |
|------|------|
| **데이터 준비** | `ImageFolder`, `transforms.Resize(224x224)` |
| **모델** | `EfficientNet-B0` + `num_classes=396` |
| **학습** | `CrossEntropyLoss`, `Adam`, `Epoch loop` |
| **검증** | `Validation Accuracy/Loss 계산`, `Best model 저장` |
| **예측** | `Test set 추론`, `Softmax 결과`, `submission.csv 저장` |

## 주요 특징

- **효율성**: EfficientNet의 compound scaling으로 성능 대비 효율적인 모델
- **전이학습**: ImageNet 사전학습 가중치 활용
- **데이터 증강**: 회전, 플립, 색상 조정 등을 통한 일반화 성능 향상
- **평가 지표**: Log Loss 최적화를 통한 확률 예측 정확도 개선