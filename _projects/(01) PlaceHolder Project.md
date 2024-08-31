---
name: "[ 1 ] 글로컬 대학 AI 조교 구축 프로젝트"
tools: [NLP, LLM, 2024.07-]
image: https://github.com/user-attachments/assets/6f679575-35ac-4c56-bb0b-205f21dec080
description: ▶️ 각 대학에서 요구하는 AI 조교를 구축하는 프로젝트 입니다. 
---

# 글로컬 대학 AI 조교 구축 프로젝트
- [1. 프로젝트 개요](#1-프로젝트-개요)
- [2. 역할](#2-역할)
- [3. 기술 스택](#3-기술-스택)
- [4. Challenges](#4-challenges)

## 1. 프로젝트 개요
---
- **기간** : 2024.07-(진행중)
- **목표** : 각 대학에서 요구하는 AI 조교(LLM) 구축, 특징은 각 대학이 요구하는 다양한 양식대로 답변하도록 템플릿 작성에 힘써야함
- **모델** : HyperclovaX, ChatGPT
- **기술 스택** : HuggingFace, Langchain, Langserve, FastAPI, Milvus, Redis
- **역할** : RAG 서버(Langchain 서버) 구축 담당

## 2. 역할
---
- 역할 : RAG 서버(Langchain 서버) 구축 담당
  - Langchain을 활용한 채팅 Chain 설계
  - 다양한 format을 지원하도록 loader 통합 구현
  - multi-turn 대화를 위한 유저, 세션별 히스토리 기록 Redis로 저장
  - Milvus vectorstore 파이프라인 구현
  - HyperclovaX의 Langchain 동작을 위한 커스텀
  - Langserve를 활용한 채팅 API 서빙

## 3. 기술 스택
---
- 기술스택 : Python, Poetry, HuggingFace, Langchain, Langserve, FastAPI, Milvus, Redis, Docker, NCP
- 도구 : Pycharm, RedisInsight, Attu 
- 협업 : Gitlab, Height, Notion

## 4. Challenges
---
- HyperclovaX의 token length 문제
  - HyperclovaX의 token length가 약 4096으로 제한되어 있어, context, chat_history, response를 포함하기엔 부족함
  - 이를 해결하기 위해, RunnableBranch로 나누어 유저 쿼리가 소모하는 토큰의 양을 최대한 절약하도록 파이프라인 설계
  - 토큰이 넉넉한 ChatGPT로의 유저 선택이 가능하도록 설계
- Langchain의 불안정성
  - LCEL 문법을 지향하는 Langchain의 레거시 문제로 인해 커스텀이 어려운 부분이나 버전 이슈가 자주 발생
  - 최대한 커스텀을 진행하면서 문제 상황을 해결하는 중
    - ex) ConversationSummaryBufferMemory + RunnableWithMessageHistory 
