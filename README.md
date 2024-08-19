# Home_Credit-_Credit_Risk_Model_Stability
[머신러닝 / 분류] 고객 대출 Default 가능성을 예측하는 Binary Classfication 문제 | Home Credit Kaggle

참조: [Kaggle 대회](https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/overview)

## Summary

### 🛠️ 문제 정의

- **목표**: 신용 기록이 부족한 사람들(사회초년생, 현금 사용 선호, 경제 활동 부족 등)을 포함한 최대한 넓은 범위의 고객에게 대출 서비스 기회를 제공하기 위해 시간이 지나도 안정적인 고객 대출 연체(default)를 예측하는 모델을 만드는 것
- 평가 기준: 고객들의 대출 default 가능성을 예측하는 binary classification 문제로, GINI + Stability metric을 사용

### ⚙️ 수행 역할
- Polars 및 Memory Reduction 기법을 활용하여 대용량 데이터 효율적으로 전처리
- Class Imbalance 문제를 Down-sampling 기법 사용하여 데이터 분할 진행
- High Cardinality변수를 처리하기 위해 LightGBM의 자체 옵션 사용


### 📈 결과 및 직무에 적용할 점
1. 	Polars 라이브러리 활용한 대용량 데이터 처리 인사이트 확보
2. 모델링 과정에서 Feature importance 메트릭 활용한 중요 변수 선정


<br><br>


$${\color{gray}(※ 아래는 프로젝트의 상세 내용 입니다.)}$$
## 상세

### 📝 데이터 수집
* **데이터셋 설명**
  * 주어진 데이터셋은 클라이언트 정보와 그들의 채무 불이행 예측 정보를 포함하고 있음
  * 이 데이터셋은 다양한 수준의 데이터 집계와 데이터 소스로 구성된 여러 테이블로 구성([자세한 설명](https://github.com/hyeonnjii/Home_Credit-_Credit_Risk_Model_Stability/blob/main/Data%20Description))
  * Sample Size:
    * Total: 1,526,659
    * Target 0: 1,478,665
    * Target 1(Is_Default) : 47,994
  
  
  * **데이터 구조 형식**
    ```
    ├── internal # 내부 데이터 소스
    │   ├── static_0            # 정적 데이터 소스 0
    │   ├── applprev_1          # 이전 신청 데이터 소스 1
    │   ├── applprev_2          # 이전 신청 데이터 소스 2
    │   ├── person_1            # 개인 데이터 소스 1
    │   ├── person_2            # 개인 데이터 소스 2
    │   ├── deposit_1       # 예금 데이터 소스 1
    │   ├── debitcard_1 # 직불카드 데이터 소스 1
    │   └── other_1             # 기타 데이터 소스 1
    │  
    └── external # 외부 데이터 소스
        ├── static_cb_0         # 외부 정적 데이터 소스
        ├── tax_registry_a_1    # 세금 등록부 데이터 소스 a 1
        ├── tax_registry_b_1    # 세금 등록부 데이터 소스 b 1
        ├── tax_registry_c_1    # 세금 등록부 데이터 소스 c 1
        ├── credit_bureau_a_1   # 신용 조회기관 데이터 소스 a 1
        ├── credit_bureau_b_1   # 신용 조회기관 데이터 소스 b 1
        ├── credit_bureau_a_2   # 신용 조회기관 데이터 소스 a 2
        └── credit_bureau_b_2   # 신용 조회기관 데이터 소스 b 2

    ```
  * **데이터 Depth에 따른 구조 형식**
<div align="center">

  | Data Source | Depth=0 | Depth=1 | Depth=2 |
| --- | --- | --- | --- |
| internal | static_0 | applrev_1 | applrev_2 |
|  |  | person_1 | person_2 |
|  |  | deposit_1 | - |
|  |  | debitcard_1 | - |
|  |  | other_1 | - |
| external | static_cb_0 | credit_bureau_a_1 |credit_bureau_a_2 |
|  |  | credit_bureau_b_1 | credit_bureau_b_2 |
|  |  | tax_registry_a_1 | - |
|  |  | tax_registry_b_1 | - |
|  |  | tax_registry_c_1 | - |
  
</div> 

---
### 🔍 📊 데이터 전처리 & EDA
#### 대용량 데이터 처리
- **Polars 사용**:
  - Polars 라이브러리를 사용하여 대용량 데이터를 효율적으로 처리
  - Polars는 Pandas보다 빠른 성능과 메모리 사용량 최소화로 대용량 데이터 처리에 적합

- **메모리 사용량 최적화**:
  - `reduce_mem_usage` 함수를 통해 데이터 타입을 최적화하여 메모리 사용량 절감
  - 정수형은 int8, 실수형은 float16으로 변환

- **결측치 및 고유값 많은 컬럼 제거**:
  - 결측치 비율 95% 이상인 컬럼 제거
  - 범주 값이 1개(너무 적음) 또는 200개 이상(너무 많음)인 컬럼 제거

#### Aggregation
- 각 파일들에 대하여 case_id로 집계하는 로직이 필요
- **일반 집계 함수**
  - 각 컬럼마다 알파벳이 붙어있는데, 해당 알파벳의 성질에 따라 아래 로직 함수 정의
      * P: DPD (Days past due) 컬럼을 수치형 데이터로 간주, num_aggregators에 정의된 집계 함수 적용
      * M: 문자형 데이터로 간주, str_aggregators에 정의된 집계 함수 적용, str_mode가 True일 경우 최빈값 추가 계산
      * A: 수치형 데이터로 간주, num_aggregators에 정의된 집계 함수 적용
      * D: 수치형 데이터로 간주, num_aggregators에 정의된 집계 함수 적용, 날짜 데이터를 수치적으로 다룰 수 있도록 변환
      * T, L: 문자형 데이터로 간주, str_aggregators에 정의된 집계 함수 적용
      * 각 그룹마다 max, min, first, last, mean(수치형 데이터의 경우만, mean 포함) 집계 함수를 적용
  
- **depth 2까지 존재하는 컬럼 집계 함수**
  - depth1, depth 2의 파일들에서는 `num_group1`, `num_group2` column이 등장
  - num_group1, num_group2 컬럼의 정의가 데이터셋마다 다르다고 판단
    * `credit_bureau`
      - `num_group1`은 contract, `num_group2`는 해당 contract에 대한 payment
      - `num_group1`은 `case_id`에 대해서 **historical order**를 보여주는 컬럼
      - `num_group2` 역시 depth=2 테이블의 `case_id`에 대한 historical order를 보여주는 컬럼
  
      ```
      이를 활용하여 case_id 별로 최근 데이터만 추출(num_group1 or num_group2 == 0인 경우)하는 aggregation 수행
      ```
    * `person`
      - `num_group1`은 person, `num_group2`는 employment를 의미
  
      ```
      해당 컬럼에서는 중요하지 않은 컬럼이라 판단, 이후 1차적인 피처 엔지니어링에서 제거하여 aggregation 미필요
      ``` 
#### 범주형 변수 인코딩
- 한 컬럼의 category수가 200, 300개 넘어가는 경우 즉, high cardinality한 컬럼이 많음
- one-hot encoding을 하기에는 오히려 성능이 저하될 수 있다 판단
-  1차 로직(범주 값이 1개(너무 적음) 또는 200개 이상(너무 많음)인 컬럼 제거) 후에도 여전히 변수형 컬럼 다수 존재
```
  Categorical value에 대한 처리 → 모델링에서 LightGBM 자체의 옵션을 사용(object → category)
``` 

<br><br>

※ 각 데이터 파일에 대한 EDA 및 코드는 해당 [링크](https://github.com/hyeonnjii/Home_Credit-_Credit_Risk_Model_Stability/tree/main/EDA)에서 보실 수 있습니다 :)
      
---
### 🧠 모델링
#### 평가 지표
  * Stability Metric(자체 평가 지표)
      * GINI 점수를 주간 단위로 계산하여 평균을 내는 방식으로 분류기(classifier)의 점수를 매긴 후, 이렇게 얻은 주간 GINI 점수의 안정성(gini stability)을 회귀 모델을 사용하여 평가
      * 안정성 지표는 시간이 지남에 따라 예측 능력이 저하되는 모델에 패널티를 부여
        * 선형 회귀 모델의 기울기 a가 감소하는 모델에 패널티를 부여
        * 선형 회귀 모델의 잔차(residuals)가 클수록 안정성이 떨어진다고 판단
        ```
        stability metric = mean(gini) + 88.0 · min(0, a) - 1/2 std(residual)
                       2AUC - 1                  ax + b
        ```
        
#### 모델 구성 및 학습
1. **문제점** 
  - 🚩**Class Imbalance**
    * Target 0:1 = 97:3
    * Default한 고객(=target 1)이 전체 약 3%로 **Imbalance Target Ratio**가 존재

2. **문제 해결 방안**
   * `Downsampling`
     - 베이스모델로 LGBM 선택
     - Downsampling 비율 테스트
       - No sampling / 10:1 / 5:1 
         
   * `Base Table에 데이터를 하나씩 추가하여 Feature importance 시각화에서 기존 depth_0의 컬럼보다 상위에 나타나는 컬럼 확인 후, 해당 컬럼 선택`
     - Home Credit이 추구하는 사업 가치를 고려하면, 크레딧 기록이 없는 사람들에 대한 판단 기준이 추가적으로 필요
     - depth0의 `static0`, `static_cb_0` 데이터는 기존의 home credit에서 자체적으로 가공한 자료로 판단됨
     - 따라서 `credit burear(신용기관)`, `tax_registry`, `person` 등의 depth_1, depth_2 데이터에서 중요한 컬럼을 찾아야 한다고 판단함


3. **모델 별 성능 평가**
   - 해당 성능 평가는 credit_bureau_a_2를 추가한 결과입니다.
   - 이 후 결과는 대회 종료로 인해 추가적으로 실험 불가
<div align="center">


| Model | Sampling Ratio | Train AUC | Valid AUC | Test AUC |
| --- | --- | --- | --- | --- |
| Base model | No sampling | 0.8464 | 0.8296 | 0.8290 |
|  | 10 : 1 | 0.8454 | 0.8326 | 0.8268 |
|  | 5 : 1 | 0.8463 | 0.8248 | 0.8313 |
| (+) credit bureau_a_2 | No sampling | 0.8597 | 0.8448 | 0.8442 |
|  | 10 : 1 | 0.8564 | 0.8471 | 0.8416 |
| | 5 : 1 | 0.8602 | 0.8417 | 0.8467 |



</div>

  - Feature Importance Plots
<div align=center>
  
  Base model
  
</div>

![image](https://github.com/user-attachments/assets/78b44feb-2d4b-4ba1-a985-4459fac39e21)

<br><br>

<div align=center>
  
  (+) credit bureau_a_2 
  
</div>

![image](https://github.com/user-attachments/assets/70b886e3-62c0-4220-b018-e4b9a7235348)


#### 결론

```

1. Downsampling의 관점에서:
   - LightGBM은 downsampling에 비교적 강건한 모델 중 하나
   - Downsampling을 진행해도 엄청난 큰 성능 차이는 보이지 않았음

2. 데이터 추가 후:
   - Depth 0의 데이터를 추가했을 때 전반적인 성능 향상 확인
   - 이러한 성능을 올리는 피처를 찾아 해당 컬럼만 가지고 전체적으로 성능과 안정성이 높은 모델을 만드는 것이 목표

```

- Metric을 이용한 Hacking으로 인해 대회가 중단되고 다시 대회가 시작하기 전에 팀플이 끝난 점으로 인하여 추후 실험이 중단

---
### 🎯 결과 및 기대효과
- class imbalance 문제를 downsampling으로 진행하였을 때 성능이 좋은 것을 확인
- Feature Importance 메트릭을 통해 중요한 변수를 식별하는 로직으로 성능 개선 확인

<br>

- **대용량 데이터 처리**: Polars와 메모리 최적화 기법을 통해 대용량 데이터를 효율적으로 전처리함으로써 데이터 처리 속도와 효율성을 높일 수 있었음.

- **Class Imbalance 문제 해결**: 이 후 같은 문제가 발생할 경우에도 같은 전략을 사용하여 성능을 올릴 수 있을 것.

- **실제 비즈니스에 적용**: 본 대회를 통해 얻은 인사이트와 모델을 실제 비즈니스에 적용하여 신용 평가의 신뢰성을 높일 수 있음. 대출 연체를 예측하는 안정적인 모델을 통해 금융 리스크를 줄이고, 보다 많은 고객에게 공정한 대출 기회를 제공할 수 있음.








