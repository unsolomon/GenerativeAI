# 🧠 11주차 강의 정리: NAVER Generative AI — 다중 모드 데이터를 사용한 산업 데이터 분석
> 강필성 교수 (서울대학교 산업공학과)  
> 연구실: [http://dsba.snu.ac.kr](http://dsba.snu.ac.kr)  
> YouTube: [@dsba2979](https://www.youtube.com/@dsba2979)

---

## 🧩 1. Generative AI 개요 및 흐름

### 🔹 1.1 Generative AI: NLP (언어 생성형 AI)

#### 📘 Large Language Model (LLM)이란?
- 텍스트 입력을 받아 적절한 출력을 생성하는 **언어모델 (Language Model)**
- **대량의 텍스트 데이터**로 사전학습(pre-training)
- **수십억~수천억 개의 파라미터(Billion-scale parameters)**를 보유

#### 🔹 LLM 발전 동향
- **InstructGPT / ChatGPT** 이후 폭발적인 발전  
  - Corpus 정제, Instruction Tuning 등 학습 방법론 연구
  - 의료, 법률 등 **도메인별 활용 연구**
  - 추론 속도, 메모리 최적화, 입력 길이 확장 등 **효율화 연구**

---

### ⚙ NLP 모델 발전 단계

| 구분 | 주요 모델/기술 | 특징 |
|------|----------------|------|
| **Statistical LM** | TF-IDF, BM25 등 | 단어 빈도 기반, 맥락 반영 불가, 시스템 간 전이 어려움 |
| **Neural LM** | Word2Vec 등 | 벡터 표현 사용, 의미 반영 가능하지만 문맥 반영은 제한 |
| **Pretrained LM** | BERT, GPT-1/2, T5 등 | 대규모 코퍼스로 사전학습, 태스크별 Finetuning 필요 |
| **LLM (Large LM)** | GPT-3, GPT-4 등 | Finetune 없이 다양한 태스크 수행 가능, Prompting 기반 제어 |

---

### 🧠 LLM의 특징과 장점
- **Finetuning 없이 다양한 태스크 수행 가능**
- **Prompt 기반 제어 가능** — 입력 문장만으로도 태스크 수행
- **다양한 도메인 적용 가능 (법률, 의료, 서비스 등)**  
- **단일 모델로 멀티태스크 수행**

> ⚡ LLM 사용이 특히 유용한 경우  
> - 인간 행동 모사(Chatbot 등)  
> - 데이터 부족 상황  
> - 고난이도 추론 태스크  
> - 사실 기반 생성(Factual generation)

---

## 🎨 1.2 Generative AI: Vision (시각 생성형 AI)

### 📷 생성형 이미지 모델 개념
- 데이터 분포 \(P_{data}\)를 기반으로 새로운 이미지를 생성하는 확률모델 \(P_{model}\)
- 학습 목표: **데이터 생성 확률(likelihood) 최대화**

---

### 🧩 대표적 생성형 모델 유형

| 모델 유형 | 핵심 아이디어 | 대표 연구 |
|------------|----------------|-------------|
| **GANs (Generative Adversarial Networks)** | 생성자(G)와 판별자(D)가 경쟁하며 학습 | *Goodfellow et al., 2014* |
| **VAE (Variational Autoencoder)** | 잠재 변수 분포를 정의해 확률적 생성 | *Kingma & Welling, 2014* |
| **Flow-based Models** | 가역 함수 기반의 확률밀도 변환 | *Real NVP, 2017* |
| **Diffusion Models** | 점진적 노이즈 추가·제거 과정으로 생성 | *Ho et al., 2020 / Lilian Weng, 2021* |

---

### 🧱 주요 구조 비교

| 모델 | 구조 | 특징 |
|------|------|------|
| **GAN** | Generator + Discriminator | 적대적 학습, 사실감 높은 이미지 |
| **VAE / VQ-VAE** | Encoder + Decoder | 잠재 변수 분포 정의 (연속형 / 이산형) |
| **Flow-based** | 가역 함수 기반 | 확률 해석 명확, 계산 복잡 |
| **Diffusion** | Forward + Reverse Process | 노이즈를 통해 점진적 복원 |

---

### 💡 Diffusion 모델 연구 방향
- **Algorithm 개발** (Diffusion 알고리즘 개선)
- **타 생성 모델과 결합** (Hybrid 구조)
- **Application 확장** (이미지·영상·음악 생성)

---

### 🧰 생성형 이미지 모델의 주요 활용 분야
| 분야 | 설명 |
|------|------|
| **Style Transfer** | 특정 스타일을 다른 이미지에 적용 |
| **Inpainting** | 손상된 이미지 영역 복원 |
| **Image Editing** | 이미지 변경·개선 |
| **Super-resolution** | 저해상도 이미지를 고해상도로 변환 |

---

### 🔀 멀티모달 생성형 모델
| 유형 | 설명 |
|------|------|
| **Text-to-Image** | 텍스트를 기반으로 이미지 생성 *(e.g. DALL-E)* |
| **Text-to-Video** | 텍스트를 기반으로 영상 생성 *(e.g. Lumiere)* |
| **Image-to-Video** | 이미지와 프롬프트를 결합해 영상 생성 |

---

## 💬 2. 생성 모델 활용 사례

### 🧩 ChatGPT and GPT API
- GPT-3.5 / GPT-4 기반 챗봇형 서비스 (2022년 11월 공개)
- 주요 기능: **대화, 문서 요약, 첨삭, 분석**
- **Prompt 구성 요소**
  - **Instruction:** 수행할 작업 지시문  
  - **Demonstration:** 입력-출력 예시  
  - **Input:** 실제 수행 데이터

> ChatGPT = 웹 인터페이스 기반  
> GPT API = 외부 시스템에 LLM 기능을 통합 가능

---

### ⚙ GPT-4 주요 버전
| 버전 | 특징 |
|-------|------|
| **GPT-4** | 기본형 API |
| **GPT-4-32k** | 긴 문서(32k tokens) 지원 |
| **GPT-4-Vision** | 이미지 입력 + 텍스트 처리 가능 |

---

### 📱 GPT 활용 사례
| 서비스 | 활용 내용 |
|---------|------------|
| **스픽(Speak)** | 영어 회화 학습, 실시간 응답 생성 |
| **토스(Toss)** | 경제 뉴스 요약 및 주가 영향 분석 |
| **배달의민족** | 상황 기반 메뉴 추천 |
| **네이버 CLOVA X** | 블로그 글 초안, 목차, 문체 보정 등 자동화 |

---

## 🖼️ 2.2 DALL·E 3 & Stable Diffusion

### 🎨 DALL·E 3
- **Text-to-Image** 생성 모델 (OpenAI)
- 더 나은 captioning 구조로 이미지 품질 개선  
- **ChatGPT Pro** 구독자에게 통합 제공  
- API를 통해 별도 서비스에서도 사용 가능

### 🌈 Stable Diffusion
- **Latent Diffusion Models** 기반 (Stability AI & Runway)
- Conditioning Mechanism으로 이미지뿐 아니라 다양한 조건 반영
- 오픈소스 공개: [HuggingFace](https://huggingface.co/stabilityai)
- **WebUI (AUTOMATIC1111)**로 개인 서버 구성 가능  
  - 파라미터 조절  
  - 모델 교체 / 추가 학습  
  - 확장 플러그인 사용 가능  

---

## 📚 참고 자료
- Zhao et al., *A Survey of Large Language Models*, 2023  
- Yang et al., *Harnessing the Power of LLMs in Practice*, 2023  
- Goodfellow, *Generative Adversarial Networks*, NIPS 2016  
- Kingma & Welling, *Auto-Encoding Variational Bayes*, ICLR 2014  
- Rombach et al., *Latent Diffusion Models*, CVPR 2022  
- Betker et al., *Improving Image Generation with Better Captions*, OpenAI 2023  
- Bar-Tal et al., *Lumiere: Space-Time Diffusion Model for Video Generation*, 2024  

---

# ✅ 요약
- **Generative AI**는 텍스트·이미지 등 다중 모드 데이터를 생성·변환하는 AI 기술.
- **LLM (GPT 계열)**과 **Diffusion 모델 (Stable Diffusion 등)**이 대표적.
- **Prompt Engineering**을 통해 하나의 모델로 다양한 태스크 수행 가능.
- 산업 현장에서는 **언어 + 시각 생성형 모델의 융합(Multimodal)**이 핵심 트렌드.

---
