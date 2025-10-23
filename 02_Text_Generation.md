# 🧠 2강: Text Generation 1 — LLM Pretrained Models  
> 강필성 교수 (서울대학교 산업공학과)  
> 출처: NAVER Connect Foundation 강의 교재

---

## **1. Large Language Model 기본**

### 🔹 1.1 LLM 개념

**Large Language Model (LLM)**  
→ 대규모 텍스트 데이터로 사전학습되어, **범용적인 언어 태스크 수행이 가능한 모델**

**주요 특징**
- **범용 태스크 수행 가능:** 번역, 요약, QA 등 다양한 작업 수행  
- **사전학습 데이터 규모:** 온라인에서 수집 가능한 최대 텍스트 (예: LLaMA 학습 데이터 4TB)  
- **모델 파라미터 수:** 7B~65B 이상, 하드웨어 한계까지 확장  
- **하나의 모델로 다양한 태스크 수행 가능**
- **Zero-shot / Few-shot Learning** 가능
- **Prompt 기반 태스크 제어**  

📘 *예시 모델:* GPT-3.5 / GPT-4 / ChatGPT / LLaMA / Mistral / Solar 등  

---

### 🧩 Pretrained LM vs LLM

| 구분 | Pretrained LM | LLM |
|------|----------------|------|
| **예시 모델** | GPT-2, BERT, T5 | GPT-3, GPT-4, LLaMA, Mistral |
| **학습 방식** | 태스크별 Finetune 필요 | 사전학습 후 Prompt로 다양한 태스크 수행 |
| **목표** | 특정 목적의 모델 구축 | 범용 목적 모델 구축 |
| **태스크 수** | 1 모델 1 태스크 | 1 모델 다수 태스크 수행 가능 |

---

### ⚙ Zero-shot / Few-shot Learning

- **Zero-shot**: 예시 없이 Prompt만으로 태스크 수행  
- **Few-shot**: 입력 + 예시(입력/출력 쌍, Demonstration)를 통해 태스크 수행  
- **핵심 아이디어:** 모델은 Prompt에 포함된 맥락으로 새로운 태스크를 “이해하고 수행”  

📘 *참고 논문:* *Language Models are Few-Shot Learners (Brown et al., 2020)*

---

### 🧱 Prompt 구조

| 구성요소 | 설명 |
|-----------|-------|
| **Task Description** | 수행할 태스크에 대한 지시문 |
| **Demonstration** | 입력-출력 예시 (Few-shot용) |
| **Input** | 실제 모델이 수행할 입력 데이터 |

> 🗣️ 예시  
> ```
> Task: 아래 영화 리뷰의 감성을 분석해줘.  
> Demo: "지루하지 않고 흥미로웠다." → 긍정  
> Input: "스토리가 별로였다."  
> Output: 부정
> ```

---

## **1.2 Model Architecture**

LLM은 **Transformer** 기반 구조를 사용하며, 크게 두 가지 유형으로 나뉩니다.

| 구조 | 설명 | 대표 모델 |
|------|------|------------|
| **Encoder–Decoder** | 입력 이해(Encoder) + 문장 생성(Decoder) | T5, BART |
| **Decoder-Only** | 단일 모델로 이해 및 생성 수행 | GPT 계열, LLaMA, Mistral |

- **Encoder–Decoder 구조**: 입력과 출력을 분리하여 처리 (ex. 번역 모델)
- **Decoder-Only 구조**: Self-Attention과 Masked Attention으로 순차적 생성
- **최근 LLM들은 대부분 Decoder-Only 구조 사용**

---

## **1.3 Pretrain Task**

모델 구조에 따라 사전학습 방법이 다릅니다.

| 구조 | 주요 학습 태스크 | 설명 |
|------|----------------|------|
| **Encoder–Decoder** | **Span Corruption** | 입력 문장의 일부를 마스킹하고 복원 (T5에서 제안) |
| **Decoder-Only** | **Language Modeling** | 이전 단어를 기반으로 다음 단어 예측 (GPT-1에서 제안) |

> 대부분의 최신 LLM은 **Causal Decoder 구조 + Next Token Prediction**을 사용함  
> ⇒ 연산 효율성과 확장성 측면에서 유리

---

## **1.4 Pretrain Corpus**

**코퍼스(Corpus)** = LLM 학습용 대규모 텍스트 데이터 집합  
(뉴스, 블로그, 서적, 위키, 커뮤니티, 댓글 등)

- 데이터 크기: **약 5TB 이상**
- **정제 과정 필수**
  - 욕설/혐오 표현 제거
  - 중복 데이터 제거
  - 개인정보(이메일, 전화번호, 주소 등) 필터링

**⚠ 주의사항**
- **Memorization**: LLM이 훈련 데이터의 특정 문장을 암기하는 현상  
  → 중복된 데이터가 많을수록 암기율 증가  
- **개인정보 노출 위험**: 학습 데이터에 포함 시, 모델이 그대로 출력할 수 있음

> 📘 *참고 연구:*  
> - *Quantifying Memorization Across Neural Language Models* (Carlini et al., 2023)  
> - *What’s in My Big Data?* (Elazar et al., 2023)

---

# 🧭 2. Instruction Tuning

### 🎯 목적

LLM이 “단순 텍스트 예측”을 넘어, **사람의 지시를 따르는 능력(Instruction Following)** 을 강화하기 위함.

| 목표 | 설명 |
|------|------|
| **Safety** | 사회적 편향, 혐오, 위험 발언 방지 |
| **Helpfulness** | 사용자의 다양한 요청에 유용한 답변 제공 |

---

### ⚙ Instruction Tuning 과정 (RLHF 구조)

1. **Supervised Fine-Tuning (SFT)**  
   - 다양한 사용자 요청(Prompt)에 대해 “적절한 답변(Demonstration)”을 학습  
   - 지도학습 기반, 안전하고 유용한 답변 생성  

2. **Reward Modeling**  
   - LLM 생성문에 대해 인간의 선호도를 점수화  
   - **Helpfulness/Safety** 기준으로 랭킹 학습  

3. **Reinforcement Learning from Human Feedback (RLHF)**  
   - Reward Model의 높은 점수를 받는 방향으로 학습  
   - **PPO (Proximal Policy Optimization)** 알고리즘 사용  

---

### 🧠 예시 흐름

| 단계 | 입력 | 출력 |
|------|------|------|
| **SFT** | “달 착륙 과정을 6살에게 설명해줘” | “사람들이 로켓을 타고 달에 가는 이야기야.” |
| **Reward Modeling** | 답변 후보 여러 개 생성 → 인간 평가로 점수 매김 |
| **RLHF** | 높은 점수 답변 방향으로 모델 업데이트 |

---

### 📈 효과

- **Instruction Following 능력 향상**  
- **거짓 정보(Hallucination)** 생성 빈도 감소  
- 작은 모델(1.3B)도 대형 모델(175B)보다 높은 성능 가능  
- 다양한 벤치마크에서 성능 향상 확인

> “모델 크기보다 **Instruction Tuning의 품질**이 더 중요하다.”  
> — *Ouyang et al., 2023 (OpenAI RLHF 논문)*

---

## ✅ 결론 요약

| 항목 | 내용 |
|------|------|
| **LLM 정의** | 대규모 데이터와 파라미터로 학습된 범용 언어모델 |
| **학습 특징** | Zero/Few-shot Learning 가능 |
| **Pretraining** | 대규모 Corpus로 사전학습 (Language Modeling 기반) |
| **Instruction Tuning** | 사람의 지시를 잘 따르도록 미세 조정 (SFT + RLHF) |
| **핵심 가치** | Safety + Helpfulness 중심의 인간 친화적 언어모델 |

---

📚 **참고 문헌**
- Brown et al., *Language Models are Few-Shot Learners*, NeurIPS 2020  
- Raffel et al., *Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer*, 2020  
- Ouyang et al., *Training Language Models to Follow Instructions with Human Feedback*, 2023  
- Zhao et al., *A Survey of Large Language Models*, 2023  
- Solaiman & Dennison, *Process for Adapting LMs to Society (PALMS)*, NeurIPS 2021  

---
