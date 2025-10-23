# 🧠 3강: Text Generation 2 — Parameter Efficient Tuning (PEFT)
> 강필성 교수 (서울대학교 산업공학과)  
> 출처: NAVER Connect Foundation 강의 교재  
> 참고 논문: Lialin et al. (2023), Hu et al. (2021), Houlsby et al. (2019)

---

## **1. LLM 발전과 PEFT 방법론의 중요성**

### 🔹 1.1 LLM의 발전 과정

- **Transformer 기반 언어모델**은 2017년 이후 NLP 전반에서 획기적 성능 향상을 가져옴  
- **2020년 이후:** “General-purpose LLM”의 등장  
  - 다양한 태스크를 단일 모델로 수행 가능  
  - 학습 데이터 및 모델 파라미터 크기가 증가할수록 성능 향상

📈 *참고 논문:*  
- *A Survey of Large Language Models* (Zhao et al., 2023)  
- *Predictability and Surprise in Large Generative Models* (Ganguli et al., 2022)

---

### ⚙ 언어모델 활용의 전통적 절차

1. **Pre-training**: 대규모 범용 웹 데이터 기반으로 사전학습  
2. **Fine-tuning**: Downstream task(요약, 분류, QA 등)에 맞게 파인튜닝  

➡️ 특정 태스크 성능은 높지만, 자원 효율성과 지속 학습에는 한계 존재

---

## **2. 기존 학습 방법론 (Conventional Approaches)**

### 🔸 Fine-Tuning (전통적 방법)
| 접근 방식 | 설명 | 성능 | 효율성 |
|------------|------|------|--------|
| **Full fine-tuning** | 모든 파라미터 업데이트 | 최고 | 낮음 |
| **Partial fine-tuning** | 출력 레이어만 업데이트 | 중간 | 중간 |
| **Feature extraction** | 임베딩만 추출 후 분류기 학습 | 낮음 | 높음 |

> 전체 파라미터 업데이트 시 성능은 높지만, 학습·저장·배포 비용이 매우 큼:contentReference[oaicite:0]{index=0}.

---

### 🔸 In-Context Learning (ICL)
- GPT-3 이후 제안된 접근법  
- 모델 파라미터 수정 없이 **few-shot prompting**으로 태스크 수행  
- Prompt 내에 *Demonstration + Test Input*을 함께 입력

📘 *참고:* *Language Models are Few-Shot Learners* (Brown et al., 2020)

---

## **3. 기존 방법론의 한계**

### ⚠ 성능적 한계
- **Catastrophic Forgetting:**  
  새로운 태스크 학습 시 기존 지식을 잃는 현상  
  (*Chen et al., 2020 – Recall and Learn*)  
- 모델을 지속적으로 fine-tuning하면 이전 데이터 기억력 저하

### ⚠ 자원적 한계
- 모델 크기가 수십억 파라미터로 커지면서 전체 학습이 불가능  
- Downstream task별 독립 모델 저장·배포에 막대한 자원 소모  
  (*Lifearchitect.ai 통계 참고*)

### ⚠ 신뢰성 한계
- ICL은 prompt 구성에 따라 성능이 들쑥날쑥  
- **랜덤 레이블**을 넣어도 일정 성능을 보이는 현상 존재  
  (*Min et al., 2022 – Rethinking the Role of Demonstrations*)

---

## **4. Parameter-Efficient Fine-Tuning (PEFT) 개요**

### 🧩 정의
> 모델의 **일부 파라미터만 학습**하여 효율적으로 성능을 향상시키는 방법론

- 기존 모델의 가중치는 **동결(freeze)**  
- 적은 수의 **추가 학습 파라미터**만 업데이트  
- Fine-tuning 시 메모리·시간 절감

📗 *참고:* *Scaling Down to Scale Up: A Guide to PEFT* (Lialin et al., 2023)

---

### 🔸 PEFT 주요 목표

| 목표 | 설명 |
|------|------|
| **Parameter Efficiency** | 적은 학습 파라미터로 동일 성능 달성 |
| **Computation Efficiency** | 메모리 및 GPU 자원 절감 |
| **Task Adaptability** | 다양한 downstream task에 손쉽게 적용 |
| **Model Integrity** | 원 모델 구조 유지, 안정성 보장 |

---

## **5. PEFT 주요 방법론**

### ⚙️ 1. Adapter (Houlsby et al., 2019)

- **구조:**  
  각 Transformer 레이어에 **Feed-Forward Network (FFN)**을 추가  
  → 입력을 낮은 차원으로 압축 → 비선형 변환 → 원래 차원 복원  
- **핵심:** 병목(bottleneck) 구조  
- **Fine-tuning:** Adapter 모듈만 학습, 나머지는 고정  

📊 *성과:*  
GLUE benchmark 9개 task에서 **Full Fine-tuning에 근접한 성능**,  
하지만 학습 파라미터는 수십 배 감소.

---

### ⚙️ 2. Prefix Tuning (Li et al., 2021)

- Transformer 각 레이어에 **trainable prefix vector** 추가  
- Prefix는 “가상의 임베딩”으로 간주되어 기존 모델에 병합 가능  
- 각 태스크별 최적 prefix를 학습하여 빠른 적응 가능  

> 🔹 모델의 attention layer 앞단에 prefix vector를 주입하는 방식

---

### ⚙️ 3. Prompt Tuning (Lester et al., 2021)

- 입력 레이어에 **trainable prompt embedding** 추가  
- “자연어 prompt”가 아닌 **임베딩 공간상의 가상 벡터**  
- 임베딩 레이어 일부만 학습  
- 소형 모델에 특히 효과적

📘 *차이점:*  
Prompt Tuning은 입력층만 수정,  
Prefix Tuning은 모든 Transformer 레이어에 prefix를 추가함.

---

### ⚙️ 4. Low-Rank Adaptation (LoRA) — Hu et al., 2021

- 사전학습된 모델 파라미터는 **고정(freeze)**  
- 각 weight matrix \( W \)에 대해,  
  **저랭크 행렬 분해 (Low-Rank Decomposition)**을 추가:
  \[
  W' = W + BA
  \]
  (A: down-projection, B: up-projection, rank ≪ dimension)

- **핵심 아이디어:**  
  파라미터를 효율적으로 조정하면서 기존 모델 성능 유지  
- **특징:**  
  - Inference latency 증가 없음  
  - 모델 구조 변경 불필요  
  - Encoder/Decoder 기반 모델 모두 적용 가능 (RoBERTa, GPT 등)  
  - GPU 메모리 사용량 대폭 절감  

📊 *성능:*  
- GPT-3 (175B)에서도 효과 입증  
- 기존 Fine-tuning 대비 **95% 이상의 파라미터 절감**  
- **PEFT 중 가장 널리 사용됨**

---

## 🧠 6. PEFT 실제 적용

- **Hugging Face `peft` 라이브러리**를 통해 간편하게 구현 가능  
  ```python
  from peft import LoraConfig, get_peft_model
  model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b")
  peft_config = LoraConfig(r=8, lora_alpha=16, lora_dropout=0.05)
  model = get_peft_model(model, peft_config)


## ✅ 결론

  | 요약 항목      | 내용                                          |
| ---------- | ------------------------------------------- |
| **기존 문제점** | 전체 파라미터 학습의 비효율성, 자원 낭비, 신뢰성 문제             |
| **해결 방향**  | 일부 파라미터만 학습하는 PEFT 방식 도입                    |
| **핵심 방법론** | Adapter, Prefix Tuning, Prompt Tuning, LoRA |
| **대표 기법**  | LoRA — 효율성과 성능의 균형점                         |
| **효과**     | 동일 성능을 훨씬 적은 자원으로 구현, LLM 활용도 극대화           |


📚 참고 문헌

Zhao et al., A Survey of Large Language Models, 2023
Ganguli et al., Predictability and Surprise in Large Generative Models, 2022
Chen et al., Recall and Learn: Fine-tuning Deep Pretrained Language Models with Less Forgetting, 2020
Min et al., Rethinking the Role of Demonstrations in In-Context Learning, 2022
Houlsby et al., Parameter-Efficient Transfer Learning for NLP, 2019
Li et al., Prefix-Tuning: Optimizing Continuous Prompts for Generation, 2021
Lester et al., The Power of Scale for Parameter-Efficient Prompt Tuning, 2021
Hu et al., LoRA: Low-Rank Adaptation of Large Language Models, 2021
Lialin et al., Scaling Down to Scale Up: A Guide to Parameter-Efficient Fine-Tuning, 2023



