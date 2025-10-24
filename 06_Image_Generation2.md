# 🧠 Generative AI 6강 — Image Generation 2: Stable Diffusion & Evaluation

**강사:** 강필성 교수  
**출처:** NAVER Connect Foundation  

---

## **1. Stable Diffusion**

### **1.1 What is Stable Diffusion?**
- **개요**
  - 2022년 8월 Stability AI에서 발표한 오픈소스 Text-to-Image 모델.
  - 논문 *“High-Resolution Image Synthesis with Latent Diffusion Models” (Rombach et al., CVPR 2022)* 기반으로 개발됨.
  - 대규모 이미지-텍스트 데이터셋 **LAION-5B**로 학습.
  - **Latent Diffusion Model (LDM)** 구조를 개선하여, 효율적인 고해상도 이미지 생성을 구현.

- **핵심 개념**
  - 직접적인 이미지 픽셀 학습이 아닌 **잠재 공간(latent space)**에서 연산 수행.
  - 학습 및 추론 효율성 향상, 연산 비용 감소, 빠른 생성 속도 확보.

---

### **1.2 Stable Diffusion Architecture**
Stable Diffusion은 3가지 주요 컴포넌트로 구성됩니다.

#### **(1) Autoencoder**
- 원본 이미지를 압축해 저차원 잠재 벡터로 변환.
- **Encoder:** 이미지 → Latent  
- **Decoder:** Latent → 이미지 복원
- 잠재 공간의 크기: `(H/f × W/f × 4)`  
- 학습 비용을 절감하고 빠른 학습 가능.

#### **(2) Image Information Creator (U-Net + Noise Scheduler)**
- **U-Net:** 노이즈를 예측하는 핵심 네트워크.
- **Noise Scheduler:** 시점별로 노이즈 양을 제어.
- 입력: `latent + noise + timestep`
- 출력: 노이즈 예측값 → 실제 노이즈와의 MSE 손실로 학습.

#### **(3) Text Encoder**
- 텍스트를 토큰 단위 임베딩으로 변환.
- **CLIP Text Encoder** 사용 (BERT 대비 더 나은 품질).
- 입력 문장은 77개 토큰으로 맞춰 padding 후 `[B, 77, 768]` 형태로 임베딩.
- **Cross-Attention**을 통해 텍스트 정보(Key, Value)와 노이즈 잠재값(Query)을 결합.

> ✅ Stable Diffusion 2에서는 더 대형 모델 **OpenCLIP**을 채택하여 생성 품질 향상.

---

## **1.3 Training Stable Diffusion**

### **학습 환경**
- **Dataset:** LAION-2B (256×256 pretrain), LAION-5B (512×512 fine-tune)
- **Text Encoder:** CLIP ViT-L/14 (63M)
- **U-Net:** 860M parameters
- **Hardware:** 32×8×A100 GPU
- **Batch Size:** 2048

### **학습 과정**
1. 이미지와 텍스트를 각각 Encoder로 변환 → latent & token embedding 생성  
2. latent에 무작위 timestep에 따라 노이즈 추가 (Noise Scheduler)
3. U-Net에 `noisy latent + time embedding + token embedding` 입력
4. 예측된 노이즈와 실제 노이즈의 MSE를 계산하여 역전파(backprop)
5. 노이즈 제거 능력을 점진적으로 학습

---

## **1.4 Inference (추론)**

### **Text-to-Image**
- 가우시안 노이즈에서 시작.
- 입력 텍스트 임베딩과 함께 U-Net을 통해 반복적으로 노이즈 제거.
- 최종 latent를 Decoder로 복원 → 고해상도 이미지 생성.

### **Inpainting**
- 기존 이미지에서 latent를 추출 후 일부 영역에만 노이즈를 추가.
- 작은 timestep → 원본 이미지 유지, 큰 timestep → 새롭게 변형.
- 이후 Text-to-Image와 동일한 방식으로 예측 및 복원.

---

## **2. After Stable Diffusion**

### **2.1 Stable Diffusion 2**
- **발표:** 2022년 11월  
- **개선점:**
  - 해상도: `512×512 → 768×768`
  - Text Encoder: CLIP → **OpenCLIP**
  - **v-prediction 방식** 도입으로 학습 안정성 향상 및 성능 개선.
  - **Super-Resolution Upscaler Diffusion Model** 추가 → 최대 2048×2048 고해상도 이미지 생성 가능.
  - **Depth2Img:** 깊이 맵(Depth map) 기반 이미지 생성 기능 추가.

---

### **2.2 Stable Diffusion XL (SDXL)**
- **발표:** 2023년 7월  
- **특징:**
  - Two-Stage 구조 (Base + Refiner)
  - 해상도 1024×1024 이미지 생성 가능
  - Text Encoder 2개 사용 → 더 정교한 프롬프트 반영
  - 파라미터 수 증가 (865M → 2.6B)
  - 다양한 비율(Multi-Aspect) 이미지 생성 가능
  - Autoencoder 개선 → 고주파(high-frequency) 디테일 보존

> 논문: *SDXL: Improving Latent Diffusion Models for High-Resolution Image Synthesis (Dustin Podell et al., 2023)*

---

### **2.3 SDXL Turbo**
- **발표:** 2023년 11월  
- **핵심 기술:** *Adversarial Diffusion Distillation (ADD)*  
- **특징:**
  - 기존 SDXL보다 훨씬 빠른 **One-Step Generation**
  - 단 1-step에서도 높은 품질의 결과 생성
  - 4-step generation에서는 다단계 모델보다 높은 성능 기록
  - 빠른 생성 속도 + 높은 정합도 유지

> 논문: *Adversarial Diffusion Distillation (Axel Sauer et al., 2023)*

---

## **3. Evaluation Metrics**

### **3.1 Inception Score (높을수록 좋음)**
- 생성된 이미지의 **품질(Fidelity)**과 **다양성(Diversity)**을 동시에 평가.
- **Inception v3** 모델로 이미지를 분류하여 클래스 확률 분포 계산.
- 두 분포의 **KL Divergence**로 점수를 계산:
  - \( IS = \exp(\mathbb{E}_x [ KL(p(y|x) || p(y)) ]) \)
- Class가 명확하고, 다양한 class가 존재할수록 점수가 높음.

---

### **3.2 FID (Frechet Inception Distance) Score (낮을수록 좋음)**
- 실제 이미지와 생성 이미지의 임베딩 분포 차이를 측정.
- **Inception Network**로 얻은 feature embedding 사용.
- 두 분포의 **프레쳇 거리(Frechet distance)**를 계산:
  - \( FID = || \mu_r - \mu_g ||^2 + Tr(\Sigma_r + \Sigma_g - 2(\Sigma_r \Sigma_g)^{1/2}) \)
- 낮을수록 실제 이미지와 유사함.

---

### **3.3 CLIP Score (높을수록 좋음)**
- 이미지와 캡션(텍스트) 간의 의미적 일치도를 평가.
- **CLIP 모델**을 이용해 이미지와 텍스트 각각을 임베딩하고,
  두 벡터의 **Cosine Similarity**로 점수를 계산.
- Reference-free metric으로, Caption 없이도 의미적 품질을 평가 가능.

---

## **📊 요약**

| 모델 | 발표 시기 | 주요 특징 |
|------|------------|------------|
| Stable Diffusion | 2022.08 | Latent Diffusion 기반 Text-to-Image 모델 |
| SD 2.0 | 2022.11 | OpenCLIP, v-prediction, 고해상도 업스케일 |
| SDXL | 2023.07 | 2-Stage 구조, 정밀한 프롬프트 해석, 1024x1024 |
| SDXL Turbo | 2023.11 | ADD 기반 One-Step 초고속 생성 |

---

## **📘 참고 논문**
- *High-Resolution Image Synthesis with Latent Diffusion Models* — Rombach et al., CVPR 2022  
- *Photorealistic Text-to-Image Diffusion Models with Deep Language Understanding* — Saharia et al., NeurIPS 2022  
- *SDXL: Improving Latent Diffusion Models for High-Resolution Image Synthesis* — Podell et al., arXiv 2023  
- *Adversarial Diffusion Distillation* — Sauer et al., arXiv 2023  
- *CLIPScore: A Reference-free Evaluation Metric for Image Captioning* — Hessel et al., arXiv 2021

---
