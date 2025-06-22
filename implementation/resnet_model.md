# ResNet50 기반 중고차 분류 모델

## 📋 ResNet50 슈도코드

### 1. **라이브러리 임포트 및 초기 설정**
  * torch, torchvision, PIL, sklearn 등 필요 모듈
  * 구글 드라이브 마운트 및 opencv 설치
  * 시드 고정으로 재현성 확보

### 2. **디바이스 및 하이퍼파라미터 설정**
  * GPU 사용 가능하면 GPU로 설정
  * 이미지 크기: 224 x 224 (이미 전처리됨)
  * 배치 크기: 16, 에폭: 5, 학습률: 1e-3

### 3. **커스텀 데이터셋 클래스 정의**
  * 훈련용: 클래스별 폴더 구조 (`class_name/image.jpg`)
  * 테스트용: 평평한 구조 (`image.jpg`)
  * 파일 존재 여부 검증 포함

### 4. **데이터 전처리 및 증강**
  * 훈련용: RandomHorizontalFlip, RandomRotation, ColorJitter, RandomAffine
  * 검증용: 정규화만 적용
  * ImageNet 평균/표준편차로 정규화

### 5. **데이터셋 로드 및 분할**
  * 전체 데이터를 8:2 비율로 훈련/검증 분할 (stratify 적용)
  * DataLoader로 배치 단위 로딩

### 6. **개선된 모델 아키텍처**
  * ResNet50 백본 + 사전학습 가중치
  * 커스텀 분류 헤드: 2048 → 1024 → 512 → num_classes
  * 각 레이어 사이에 Dropout, BatchNorm, ReLU 적용

### 7. **학습 설정**
  * 손실함수: CrossEntropyLoss
  * 옵티마이저: AdamW (weight_decay=1e-4)
  * 스케줄러: ReduceLROnPlateau (LogLoss 기준)

### 8. **학습 루프 (5 에폭)**
  * 각 에폭마다:
    * 훈련 모드: 순전파 → 손실계산 → 역전파 → 파라미터 업데이트
    * 검증 모드: 정확도, 손실, LogLoss 계산
    * 베스트 LogLoss 기준으로 모델 저장
    * 학습률 자동 조정

### 9. **테스트 데이터 추론**
  * 베스트 모델 로드
  * 테스트 이미지들에 대해 소프트맥스 확률 계산
  * 클래스별 확률값 추출

### 10. **제출 파일 생성**
  * 테스트 파일명을 ID로 사용 (확장자 제거)
  * 각 차종 클래스별 예측 확률을 컬럼으로 구성
  * `improved_submission.csv` 형식으로 저장

## 📌 구조 요약

| 단계 | 내용 |
|------|------|
| **데이터 준비** | `CustomImageDataset`, `train_test_split(0.2)`, `transforms` |
| **모델** | `ResNet50` + `개선된 분류 헤드` + `num_classes=자동감지` |
| **학습** | `CrossEntropyLoss`, `AdamW`, `ReduceLROnPlateau` |
| **검증** | `LogLoss 최적화`, `Stratified split`, `Best model 저장` |
| **예측** | `Test set 추론`, `Softmax 결과`, `improved_submission.csv 저장` |

## 주요 특징

- **백본 네트워크**: ResNet50의 강력한 특징 추출 능력 활용
- **개선된 분류 헤드**: 다층 퍼셉트론으로 성능 향상
- **정규화 기법**: Dropout과 BatchNorm으로 과적합 방지
- **학습률 스케줄링**: ReduceLROnPlateau로 최적화 안정성 확보
- **데이터 증강**: 다양한 변환으로 모델 일반화 성능 개선

## 주요 개선사항

- **강화된 데이터 증강**: RandomAffine, ColorJitter 등 추가
- **개선된 분류 헤드**: 2단계 → 3단계 분류기로 확장
- **학습률 스케줄러**: 동적 학습률 조정으로 수렴성 개선