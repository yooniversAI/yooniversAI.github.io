---
name: "[ 5 ] (SI) 와인나라 추천 솔루션"
tools: [2021.11 ~ 2022.03, RecSys]
image: https://images.unsplash.com/photo-1535869462434-f92cc30bf40c?ixlib=rb-1.2.1&q=85&fm=jpg&crop=entropy&cs=srgb&w=3600
description: ▶️ Implicit feedback을 활용한 개인화 와인 추천 모델 개발
---

# 와인나라 추천 솔루션 
1. [프로젝트 개요](#1-프로젝트 개요)
2. [유사한 와인 추천 모델](#2-유사한-와인-추천-모델)
3. [유저 기반 와인 추천 모델](#3-유저-기반-와인-추천-모델)
4. [프로젝트 한계점](#4-프로젝트-한계점)

## 1. 프로젝트 개요
- **기간** : 2021.11 ~ 2022.03(유지보수 : ~ 2022.05)
- **목표** : `Implicit feedback`을 활용한 2가지 와인 추천 모델 개발
  - 유사한 와인 추천 모델 (Item-based)
  - User Interaction 기반 와인 추천 모델 (User Interaction based)
- **데이터** : 와인나라 사용자의 구매 이력 데이터
  - 와인 1465개의 메타데이터 (와인명, 가격, 국가, 종류, 평점, info 등)
  - User interaction 데이터 65만건 (User, Item, Count)
- **평가** : AUROC
- **기술 스택** : FastAPI, Python, MongoDB, Pytorch


## 2. 유사한 와인 추천 모델
- **목표** :
  - 장바구니 및 와인 상세페이지 하단에서 해당 와인과 유사한 와인을 추천해주기 위한 모델

- **사용 데이터** :
  - **메타 정보** : 
    - 상품코드, 상품명, 종류, 국가, 당도, 산도, 바디감, 탄닌, 빈티지, 소지역, 품종, 알코올도수, 테이스팅 노트  
    ![image](https://github.com/user-attachments/assets/045aec6b-d9fc-4dc5-be6c-ee38ac2cbda7)
- **방법론** : 
  - 와인의 메타 정보, 설명(info) 정보를 각각 embedding 하고 concatenate 후 와인 간 유사도 계산(코사인 유사도)
  - 테이스팅 노트는 Huggingface의 pre-trained bert 모델을 이용하여 768차원으로 embedding 진행
  - 메타 정보는 총 `12개`의 feature지만, 그 중 `포도품종`은 총 86개가 존재함. 와인 1개에는 여러개의 포도품종이 존재할 수 있으므로 이를 고려하여 embedding 진행시 `Multi-hot encoding`을 통해 처리
  - 메타데이터 피쳐 98개 98차원 임베딩 + 테이스팅 노트 768차원 임베딩 = 866차원
    - 테이스팅 노트의 가중치 1/98 수준으로 조정 후 적용
  
## 3. 유저 기반 와인 추천 모델
- **목표** : 
  - 사용자의 구매 이력을 바탕으로 해당 사용자에게 맞춤형 와인을 추천해주기 위한 모델
- **사용 데이터** :
  - **User Interaction 데이터** : 
  ![image](https://github.com/user-attachments/assets/61b3bc8e-fd03-492b-9c05-f99d2c298bf4)
- **문제 인식** : 
  - 사용자의 구매 이력 데이터는 sparse하며, 사용자가 구매한 와인의 종류 수가 적음
  - 사용자가 구매한 와인의 종류가 적기 때문에, 사용자의 취향을 정확히 파악하기 어려움 - Cold Start 문제
- **방법론** : 
  - ALS(Alternating Least Squares)를 이용한 Matrix Factorization을 통해 사용자와 아이템의 잠재요인을 학습
  - 모델 선정 이유 : 
    - als는 sparse한 implicit feedback 데이터에 대해 효과적이며, 사용자와 아이템 간의 상호작용을 통해 잠재요인을 학습하기 때문에 콜드 스타트 문제에 강건함

## 4. 프로젝트 한계점
- **데이터 부족** : 
  - 주류의 특성상 배달이 안되는 한계로 인해 오프라인 매장에서 판매해야 함. 구매 이력은 있는데 유저가 특정되지 않아 User-Item 매핑 데이터가 부족함
  - 이로 인해 구매 이력 데이터가 Sparse하며 Cold Start 문제가 발생함
- **개인적 소회** : 
  - 입사 이전에 RFP가 결정된 상태였어서 히스토리 없이 투입되다 보니 부족한 부분이 많았다. 