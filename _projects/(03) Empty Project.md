---
name: "[ 3 ] 사내 지식거래 플랫폼 문서 유사도 모델링"
tools: [NLP, 2023.01-2023.04, 문서 유사도]
image: https://github.com/user-attachments/assets/00c30155-d7ba-4c6f-99f1-c81714aa90f9
description: ▶️ 사내 서비스 내 문서간 유사도를 계산해 사용자의 게시글과 유사한 문서끼리 묶어주는 기능 개발

[//]: # (external_url: https://github.com/YoussefRaafatNasry)
---

# 사내 지식거래 플랫폼 문서 유사도 모델링

- [1. 프로젝트 개요](#1-프로젝트 개요)
- [2. Method](#2-method)
  - [2.1 특이사항](#21-특이사항)
  - [2.2 기능 구현](#22-기능-구현)

## 1. 프로젝트 개요
- **기간** : 2023.01-2023.04
- **목표** : 사내 서비스 내 문서간 유사도를 계산해 노출(추천) 시켜주는 ML 파이프라인 구현
- **데이터** : KorSTS, KorNLI
- **기술 스택** : Docker, MongoDB, Pytorch, FastAPI, HuggingFace

<br>


## 2. Method

![image](https://github.com/user-attachments/assets/5a708ab8-7154-46da-b2e3-27a627391ae3)

> 1. 문서 적재
> 2. 적재된 문서에 대해 전처리, Bert 임베딩, SimCSE 임베딩을 순차적으로 진행
> 3. Bert 임베딩을 기준으로 Candidate 문서를 선정, SimCSE 임베딩을 기준으로 candidate 문서 후보들 중 가장 유사한 문서를 리랭킹

<br>

### 2.1 특이사항
   - `지식에 대한 권리를 사고 판다`는 서비스의 기획상 먼저 작성된 글에 대한 권리를 더 인정해주는 방향으로 개발을 진행
   - 임의의 두 게시글의 유사성이 높을 때, 나중에 작성된 글은 먼저 작성된 글 아래에 "유사 문서"의 형태로 노출
   - 따라서 문서가 실시간으로 입력될 때마다 전체를 대상으로 유사도를 계산하는 것이 아닌, 새로운 문서가 기존 문서와 얼마나 유사한지만 계산하면 됨

### 2.2 기능 구현 
   - Candidate retrieval : Bert 임베딩을 기준으로 대충 유사해 보이는 문서 후보를 선정
   - Re-ranking : SimCSE 임베딩을 기준으로 후보들 중 가장 유사한 문서를 리랭킹 진행
   - 리랭킹은 배치 단위로 진행
   - 리랭킹 결과는 MongoDB에 저장, 문서 id request시 유사 문서의 인덱스와 스코어를 response 하는 api 구현
