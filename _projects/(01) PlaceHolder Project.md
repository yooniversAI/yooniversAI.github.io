---
name: "[ 1 ] 글로컬 AI 조교 구축 프로젝트"
tools: [NLP, LLM, 2024.07-]
image: https://github.com/user-attachments/assets/bbf5b4de-8f58-4bc2-82e6-f3295a7d8e73
description: ▶️ Langchain과 HyperclovaX를 활용한 RAG 서버 구축 프로젝트(PoC)
---
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script> 

# RAG-파이프라인 구축
- [1. Preview](#preview)
- [2. Main Role](#main-role)
- [3. Challenges](#challenges)
- [4. 회고](#회고)


### Preview
---
글로컬 30 AI 조교 구축 사업
- **프로젝트 개요 :** Langchain과 HyperclovaX를 활용한 RAG 서버 구축 PoC
- **기간 :** 2024.07 ~ 2024.08
- **역할:** Langchain과 상용 LLM을 활용한 RAG 서버 구축 
- **Skills :** Python, Poetry, HuggingFace, Langchain, Langserve, FastAPI, Milvus, Redis, Docker
- **성과 :** 약 1달여 기간동안 빠른 속도로 1차 PoC 구현 및 QA 통과

<br>

### Main Role
---
- Langchain 위에서의 HyperclovaX 적용을 위한 커스텀 모델 구현 및 채팅 API 구현
- 다양한 format을 지원하도록 loader 통합 구현
- Multi-turn 대화를 위한 유저, 세션별 히스토리 기록 저장 파이프라인 구현
- User의 공통 공간 파일 업로드와 Session별 파일 업로드를 구분한 vectorstore 파이프라인 구현

<br>

### Challenges
---
**_1. HyperclovaX의 token limitation_**
  - **이슈 :** 
    - HyperclovaX의 token length가 약 4096으로 제한되어 있어, context, chat_history, response를 모두 포함하기엔 부족한 이슈  
      > Input을 줄이면 환각이 발생하고, Output을 줄이면 답변이 부족해지는 trade off 발생  
      > 답변이 너무 길어도 다음 질문의 history로 사용되어 적절한 입/출력 토큰 비율 조정이 필요한 상황
  - **해결 방법 :** 
    - Context, History, Response, User Query에 대해 사용 토큰을 적절하게 제한
    - User Query에 대해 검색되는 chunk를 다시한번 re-ranking 진행하여 불필요하게 사용되는 context 토큰 절약
      - Re-ranking 진행시 느려지는 레이턴시 문제를 해결하기 위해 streaming 방식으로 변경
    - Multi-turn 대화가 길어질 경우 trimming을 통해 사용되는 토큰을 최대한 줄이도록 설계
    - User Query의 의도를 선제적으로 분류하여, 분류에 맞는 토큰만 소모되도록 유도
      - 검색이나 요약을 요청하는 경우에는 history에 토큰 사용 안하는 등의 방식으로 토큰 절약

<br>
**_2. Langchain의 불안정성_**
  - **이슈 :**
    - LCEL 문법을 지향하는 Langchain의 레거시 문제로 인해 커스텀이 어려운 부분이나 버전 이슈가 자주 발생
      > ex) 메모리 절약을 위해 이전 대화 내용을 요약하면서 누적시키는 `ConversationSummaryBufferMemory`와 히스토리 내용을 Redis에 저장하게 도와주는 `RunnableWithMessageHistory` 동시 사용 불가능
  - **해결 방법 :**
    - Poetry를 사용하여 최대한 라이브러리들의 버전관리에 주의하며 개발 진행
    - PoC 단계에서는 시간을 지체하기 힘들어 trimming 기능을 사용함으로써 우회
      > 향후 업데이트를 통해 해결한다는 이야기가 있어 지켜볼 예정

<br>
**_3. 파일 사전 업로드 vs 세션 내 파일 업로드_**
  - **이슈 :**
    - 기획상 파일을 사전에 업로드하는 경우와 세션 내에서 파일을 업로드 하는 경우가 분리되어 있음.
    - 이때, 새 대화 세션을 열자마자 아무런 파일을 업로드하지 않은채 질문 했을시 사전 업로드 된 파일을 토대로 답변하는 할루시네이션 발생
  - **해결 방법 :**
    - Retrieve 진행 시에 expression 쿼리를 통한 partition이 잘 나뉘도록 Milvus collection의 필드 재정의
    - 사용자 쿼리 의도를 분류하는 chain을 통해 요청의 대상이 되는 파일을 언급했는지 분류 후 각 의도에 맞는 chain 매핑 

### 회고
---

| Keep                                                         | Problem                                                      | Try  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---- |
| - Langchain이라는 SDK를 조금이나마 deepdive한 경험이 매우 만족스러웠음<br />- DS와는 조금 거리가 있는 프로젝트였던 만큼 남는 시간을 개발 자체에 몰입하고자 했던 경험이 좋았음 | - 몇몇 부분에서 문제를 근원적으로 해결하는 것이 아니라 짧은 마감기한을 핑계로 우회 개발함<br />- 확장성을 고려하지 못함<br /> |- 일정관리에 신경쓰자. 운이 좋아서 잘 맞췄지만, 일정 예측이 힘든 만큼 더 시간 투자가 필요해보인다.<br />- Conventional Chain 말고 미리미리 확장성이 좋은 agent 방식으로 조금씩 바꾸기       |

