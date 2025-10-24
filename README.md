# 🧠 Generative AI Lecture Summary (1~6강)
> NAVER Connect Foundation — 강필성 교수  
> 정리: 이정인  
> 내용: Generative AI의 핵심 이론, 모델 구조, 학습 및 평가 개요  

---

## 📚 **Lecture Overview**

| 강의 | 주제 | 핵심 키워드 |
|------|------|--------------|
| 1강 | Generative AI Introduction | LLM, NLP, Image Generation 개요 |
| 2강 | Text Generation 1: LLM Pretrained Models | LLM 구조, Pretraining, Instruction Tuning |
| 3강 | Text Generation 2: Parameter Efficient Tuning | PEFT, LoRA, Prefix Tuning |
| 4강 | Text Generation 3: Small & Open LLMs | Alpaca, Self-Instruct, LLM 평가 |
| 5강 | Image Generation 1: Overview | GAN, AE, Diffusion 모델 |
| 6강 | Image Generation 2: Stable Diffusion | LDM, SDXL, Evaluation Metrics |

---

## 🧩 **1강 — Generative AI Introduction**

- **주요 내용**
  - LLM의 기본 구조와 발전 과정
  - Statistical → Neural → Pretrained → LLM으로의 진화
  - Generative Image Models의 개념 및 분류 (GAN, VAE, Diffusion)
- **핵심 포인트**
  - LLM은 Finetune 없이 다양한 태스크 수행 가능
  - Instruction Tuning, Prompting의 중요성
  - Multi-modal 모델 (Text-to-Image, Text-to-Video 등)의 확장
- **활용 사례**
  - ChatGPT, HyperCLOVA, DALL·E, Stable Diffusion

---

## 🧠 **2강 — Text Generation 1: LLM Pretrained Models**

- **LLM 구조**
  - Encoder–Decoder (T5) / Decoder-only (GPT) 구조
  - 대규모 파라미터(7B~65B) 및 4TB 이상 학습 데이터
- **Pretraining Task**
  - Encoder–Decoder: Span Corruption
  - Decoder-only: Next Token Prediction
- **Instruction Tuning**
  - SFT → Reward Modeling → RLHF 3단계 학습
  - 목표: Helpfulness + Safety 확보
- **핵심 개념**
  - Zero-shot / Few-shot Learning
  - RLHF로 Human Alignment 향상

---

## ⚙️ **3강 — Text Generation 2: Parameter Efficient Fine-Tuning (PEFT)**

- **기존 Fine-tuning의 한계**
  - 자원 소모 ↑ / Catastrophic Forgetting / 신뢰성 문제
- **PEFT 핵심 방법론**
  | 방법 | 설명 | 특징 |
  |------|------|------|
  | Adapter | 각 레이어에 FFN 추가 | 파라미터 효율적 |
  | Prefix Tuning | Transformer에 Prefix 벡터 추가 | 태스크 확장성 높음 |
  | Prompt Tuning | 임베딩에 학습 가능한 프롬프트 추가 | 입력 중심 |
  | **LoRA** | Weight를 Low-rank 분해 | 성능·효율 모두 우수 |
- **결론**
  - PEFT = “부분 파라미터만 학습”
  - LoRA는 GPT·BERT 등 모든 LLM에서 가장 널리 활용됨
  - Hugging Face PEFT 라이브러리로 구현 용이

---

## 🌍 **4강 — Text Generation 3: sLLM & Self-Instruct**

- **Open-source LLM**
  - LLaMA, Falcon, Mistral 등 상업적 활용 가능한 모델 등장
  - License 차이에 따른 사용 범위 주의 (MIT vs CC-BY-SA vs NC)
- **Self-Instruct**
  - GPT가 자체적으로 Instruction 데이터 생성
  - 175개 seed → 52,000개 데이터 자동 확장
  - 단계: Prompt Pool → Task Classification → Instance Generation → Filtering → SFT
- **LLM 평가**
  - MMLU, HellaSwag, HumanEval, GSM8K, G-Eval 등
  - LLM의 범용적 지능과 안전성 평가
- **미래 방향**
  - Chain-of-Thought, Self-Diagnosis, Ethics 강화

---

## 🎨 **5강 — Image Generation 1: Image Generation Models Overview**

- **Generative Model 패러다임**
  - **GANs:** Generator–Discriminator 적대적 학습 (Goodfellow et al., 2014)
  - **cGAN, Pix2Pix:** 조건 기반 이미지 생성
  - **CycleGAN, StarGAN:** Unpaired / Multi-domain 변환
  - **StyleGAN:** Progressive 학습 + Style Injection (Karras et al., 2019)
- **Autoencoder 계열**
  - AE, VAE, VQ-VAE (잠재공간 기반 복원)
- **Diffusion 계열**
  - DDPM → DDIM → CFG → LDM 발전
  - Stable Diffusion은 LDM의 대표 응용
- **핵심**
  - GAN → VAE → Diffusion으로 진화하며 현실감과 안정성 향상

---

## 🖼️ **6강 — Image Generation 2: Stable Diffusion & Evaluation**

- **Stable Diffusion (2022, Stability AI)**
  - Latent Diffusion 기반, 오픈소스 Text-to-Image
  - 구성: Autoencoder + U-Net + CLIP Text Encoder
  - 학습 데이터: LAION-5B
- **발전 버전**
  | 버전 | 발표 | 주요 특징 |
  |-------|--------|------------|
  | SD v1 | 2022.8 | 512×512 LDM 기반 |
  | SD v2 | 2022.11 | 768×768, OpenCLIP, Super-res model |
  | SDXL | 2023.7 | Two-stage (Base+Refiner), Multi-aspect |
  | SDXL Turbo | 2023.11 | Adversarial Diffusion Distillation, 1-step generation |
- **평가 지표**
  - Inception Score (IS): 품질 + 다양성
  - FID: 실제 vs 생성 분포 거리
  - CLIP Score: 이미지–텍스트 의미 유사도
- **의의**
  - LDM을 통해 고해상도 이미지를 효율적으로 생성
  - 생성 속도 향상 및 상업적 활용 확산

---

## 🧾 **Reference Papers**

| 주제 | 논문 | 학회/년도 |
|------|------|------------|
| GAN | *Generative Adversarial Nets* – Goodfellow et al. | NeurIPS 2014 |
| LLM | *Attention is All You Need* – Vaswani et al. | NeurIPS 2017 |
| LDM | *High-Resolution Image Synthesis with Latent Diffusion Models* – Rombach et al. | CVPR 2022 |
| PEFT | *LoRA: Low-Rank Adaptation of LLMs* – Hu et al. | ICLR 2022 |
| Self-Instruct | *Aligning LLMs with Self-Generated Instructions* – Wang et al. | arXiv 2023 |
| SDXL | *Improving LDMs for High-Resolution Image Synthesis* – Podell et al. | arXiv 2023 |
| ADD | *Adversarial Diffusion Distillation* – Sauer et al. | arXiv 2023 |

---

## 🧠 **정리 포인트**
> - 1~3강: Text 생성의 진화 (LLM 구조 → 효율적 튜닝 → Self-Instruct)
> - 4~6강: 이미지 생성의 발전 (GAN → Diffusion → Stable Diffusion)
> - 모든 발전의 중심에는 **효율성, 품질, 안전성**이 있음.

---

📌 **추천 학습 흐름**
1️⃣ LLM 구조 이해  
2️⃣ Fine-tuning / PEFT 기법 익히기  
3️⃣ Self-Instruct 데이터 구축 실습  
4️⃣ GAN → Diffusion 모델 비교  
5️⃣ Stable Diffusion 실행 및 평가 지표 실습  

---