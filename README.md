# LG_AIMERS Hachathon 3기

## 참고
해당 repository에는 Informer 모델 코드만 올려두었음  


**[수정한 파일]**
+ **models/embeded.py** : 'season'과 'numweek'를 추가하여 `TimeFeatureEmbedding` 수정, 제품의 대•중•소•브랜드 코드 임베딩을 위해서 `CategoryEmbedding` 추가 구현. 이를 반영해서 `DataEmbedding`의 순전파 수정
+ **utils/timefeatures.py** : 시간 특성을 잘 반영하기 위해 'season'과 'numweek'를 추가하여 수정

## 대회 개요
+ **주최/주관** | LG AI Research
+ **운영** | 데이콘
+ **기간** | 2023.08.01 ~ 2023.08.29(온라인), 2023.09.16 ~ 2023.09.17(오프라인)
+ **주제** | 온라인 채널 제품 판매량 예측
+ **링크** | [온라인](https://dacon.io/competitions/official/236129/overview/description), [오프라인](https://dacon.io/competitions/official/236156/overview/description)
  
## 목표
특정 온라인 쇼핑몰의 일별 제품별 판매 데이터를 바탕으로 향후 약 3주간의 제품별 판매량을 예측하는 AI 모델을 개발

## 결과
- **최종 20위 / 747팀 (상위 3%)**

## DataSet

**INPUT - [대회 제공 내부 데이터](https://dacon.io/competitions/official/236129/data)**
+ **train** : 실제 판매되고 있는 고유 ID, 제품코드, 제품의 대•중•소•브랜드 코드, 실제 일별 판매량 
+ **sales** : 실제 판매되고 있는 고유 ID, 제품코드, 제품의 대•중•소•브랜드 코드, 실제 일별 총 판매금액
+ **brand_keyword_cnt** : 브랜드 코드, 브랜드의 연관키워드 언급량을 정규화한 일별 데이터
+ **product_info** : 제품 코드, 제품 특성 데이터
+ **데이터 기간** : 2022-01-01 ~ 2023-04-04

**TARGET**
+ **제품별 판매량** : 2023-04-04 이후 3주간의 판매량

## 시도한 Model
- `INFORMER`, `LSTM`

## 모델 선정 과정
1️⃣ 데이터의 장기 의존성을 나타내기 위해서 Transformer 기반의 `Informer`가 유용할 것이라 판단  
2️⃣ 시간 특성을 잘 반영하기 위해, 계절과 주차라는 시간 변수를 해당 모델의 임베딩 코드에 추가 구현  
3️⃣ Label Encoding으로 인한 카테고리 변수의 잘못된 모델 반영을 방지하기 위해, 4개의 특정 변수(대•중•소•브랜드 코드)들에 대한 임베딩 코드를 새로 모델에 구현   
4️⃣ 모델 성능 평가 중 다음과 같은 아쉬운점 발생(모델의 학습속도 저하, 주어진 변수들에 비해 Informer의 복잡도가 큼)   
5️⃣ 따라서 오프라인 해커톤에서는 학습속도의 유리함을 위해 최종 모델을 LSTM으로 변경해서 진행  

## Feature Engineering
1️⃣ 판매량 데이터가 0인 이상치 대치(0값 부분 전후 7일의 평균값으로)  
2️⃣ 판매량 데이터 Min-Max scaling을 통해서 스케일 통일  
3️⃣ 변주형 변수 Label Encoding  
4️⃣ 브랜드 0값 대치(일자별 평균 브랜드 언급량으로)  
5️⃣ 파생 변수 생성(시간 관련 변수 common, 차분 변수 diff_data)  
