# 🧠 5강: Image Generation 1 — Image Generation Models Overview
> 강필성 교수 (서울대학교 산업공학과)  
> NAVER Connect Foundation  
> 주제: 이미지 생성 모델의 발전 — GAN → AE → Diffusion  

---

## **1️⃣ Generative Adversarial Networks (GANs)**

### **1.1 GANs 개요**
- **구조:** Generator(생성자)와 Discriminator(판별자)가 경쟁적으로 학습하는 구조
- **목표:** Generator는 실제 데이터와 구분 불가능한 이미지를 생성,  
  Discriminator는 실제/가짜 이미지를 판별하도록 학습

**Loss Function**
\[
L_{GAN} = \min_G \max_D V(D, G) =
E_{x∼p_{data}(x)}[log D(x)] + E_{z∼p_z(z)}[log(1−D(G(z)))]
\]

> 출처: *Goodfellow et al., NeurIPS 2014*

#### ⚠️ 한계점
- 학습 불안정 (Mode collapse, Gradient vanishing)
- Generator/Discriminator 간 학습 불균형
- 고해상도 이미지 생성의 어려움

---

## **2️⃣ Conditional GANs (cGAN, Pix2Pix)**

### **2.1 Conditional GAN**
- 입력에 **조건(condition)** 을 추가하여 특정 속성의 이미지를 생성
- 예: “개” → “개 얼굴 생성”  
  “밤 사진” → “낮으로 변환”

**Loss Function**
\[
L_{cGAN} = E_{x∼p_{data}}[log D(x|y)] + E_{z∼p_z}[log(1−D(G(z|y)))]
\]

> *Mirza & Osindero, 2014*

---

### **2.2 Pix2Pix (Image-to-Image Translation)**
- 입력 이미지를 조건으로 사용하여 다른 이미지를 생성하는 구조  
- **Paired Data 필요**
  - 예: 흑백 ↔ 컬러 이미지 변환
- Generator는 U-Net 구조, Discriminator는 PatchGAN 활용

> *Isola et al., CVPR 2017*

---

## **3️⃣ CycleGAN & StarGAN**

### **3.1 CycleGAN**
- **Unpaired Data** 로도 이미지 변환 가능
- 핵심: **Cycle Consistency Loss**

**Loss Function**
\[
L(G,F,D_X,D_Y) = L_{GAN}(G,D_Y,X,Y) + L_{GAN}(F,D_X,Y,X) + λL_{cyc}(G,F)
\]
\[
L_{cyc}(G,F) = E_{x∼p(x)}[||F(G(x))−x||_1] + E_{y∼p(y)}[||G(F(y))−y||_1]
\]

> *Zhu et al., ICCV 2017*

📌 **특징**
- Label 없는 이미지 변환 가능 (예: 여름 ↔ 겨울, 말 ↔ 얼룩말)
- Cycle Loss로 원본 복원 보장

---

### **3.2 StarGAN**
- 여러 도메인 변환을 **하나의 모델**로 수행 가능
- CycleGAN의 확장판  
- 각 도메인에 대한 **도메인 레이블(c)** 을 추가하여 다중 변환 수행

**Loss Function**
\[
L_G = L_{GAN} + λ_{cls}L_{cls}^f + λ_{rec}L_{rec}
\]
- \( L_{GAN} \): Adversarial Loss  
- \( L_{cls} \): 도메인 분류 Loss  
- \( L_{rec} \): Cycle Consistency Loss

> *Choi et al., CVPR 2018*

---

## **4️⃣ ProgressiveGAN & StyleGAN**

### **4.1 ProgressiveGAN**
- 고해상도 이미지를 생성하기 위해  
  저해상도 → 점진적(Progressive)으로 해상도를 높이는 구조
- 각 단계마다 기존 이미지를 **weighted sum**으로 결합하여 안정적으로 학습

> *Karras et al., ICLR 2018*

---

### **4.2 StyleGAN**
- ProgressiveGAN에 **Style Injection** 개념 추가  
- 잠재 공간 \(Z\) → Mapping Network \(f\) → Style 공간 \(W\) 변환

**핵심 구조**
- Adaptive Instance Normalization (AdaIN):
  \[
  AdaIN(x_i, y) = y_{s,i} \frac{x_i - μ(x_i)}{σ(x_i)} + y_{b,i}
  \]

- Style별로 coarse~fine 수준의 시각적 요소 제어 가능  
- 실제 인물 수준의 고품질 이미지 생성

> *Karras et al., CVPR 2019*

---

## **5️⃣ Autoencoders (AEs)**

### **5.1 기본 Autoencoder**
- Encoder(입력 압축) + Decoder(복원) 구조
- Reconstruction Loss (MSE or MAE) 사용

\[
L_{AE}(θ, φ) = \frac{1}{n} Σ ||x_i - f_θ(g_φ(x_i))||^2
\]

> *Rumelhart et al., 1986*

---

### **5.2 Variational Autoencoder (VAE)**
- 잠재 공간을 **확률 분포**로 가정
- KL Divergence 추가하여 정규 분포에 맞게 제약

\[
L_{VAE}(θ, φ) = E_{z∼q_φ(z|x)}[log p_θ(x|z)] - D_{KL}(q_φ(z|x) || p(z))
\]

> *Kingma & Welling, ICLR 2014*

📌 **장점**
- 잠재 공간의 구조적 의미 학습  
- 다양한 샘플 생성 가능 (연속성 보장)

---

### **5.3 Vector Quantized VAE (VQ-VAE)**
- **이산적 잠재 공간(discrete latent space)** 사용  
- Codebook(embedding dictionary) 기반으로 양자화 수행

\[
L_{VQ-VAE} = log p(x|z_q) + ||sg[z_e] - e||^2 + β||z_e - sg[e]||^2
\]

> *Van Den Oord & Vinyals, NeurIPS 2017*

📌 **활용**
- 텍스트, 음성, 이미지 모두 적용 가능  
- Diffusion 모델의 기본 구조로 사용됨

---

## **6️⃣ Diffusion Models**

> 2020년 이후 이미지 생성 분야의 **패러다임 전환**

### **6.1 DDPM (Denoising Diffusion Probabilistic Models)**
- 이미지에 점진적으로 **가우시안 노이즈 추가 (Forward Process)**  
- Reverse Process에서 노이즈를 제거하며 이미지 복원  
- 확률적 모델링 기반, 매우 높은 퀄리티의 이미지 생성 가능

> *Ho et al., NeurIPS 2020*

---

### **6.2 DDIM (Denoising Diffusion Implicit Models)**
- DDPM의 **deterministic sampling** 버전  
- 전체 step을 거치지 않아도 고품질 생성 가능  
- **Non-Markovian Diffusion** 사용

> *Song et al., ICLR 2021*

---

### **6.3 CFG (Classifier & Classifier-Free Guidance)**

#### (1) Classifier Guidance
- Diffusion 과정 중, 특정 class로 가이드를 주기 위해  
  **학습된 classifier의 gradient**를 활용
\[
∇_x log p(x_t|y) = ∇_x log p_θ(x_t) + γ∇_x log p_φ(y|x_t)
\]
> *Dhariwal & Nichol, NeurIPS 2021*

#### (2) Classifier-Free Guidance
- 별도 classifier 없이,  
  conditional / unconditional score를 혼합하여 제어
\[
∇_x log p(x_t|y) = γ∇_x log p_θ(x_t|y) + (1−γ)∇_x log p_θ(x_t)
\]
> *Ho & Salimans, arXiv 2022*

📌 **장점**
- 추가 모델 없이 condition 반영 가능  
- Guidance scale 조절로 생성 제어 용이

---

### **6.4 LDM (Latent Diffusion Models)**
- 이미지 대신 **저차원 잠재 공간(latent space)** 에서 Diffusion 수행  
- 고해상도 이미지 학습 비용 절감  
- Cross-Attention으로 Text, Layout 등 condition 반영

> *Rombach et al., CVPR 2022*

📌 **응용 분야**
- Text-to-Image (Stable Diffusion)  
- Inpainting (결손 영역 복원)  
- Super-Resolution (저해상도 → 고해상도)

---

## ✅ **요약 정리**

| 구분 | 주요 모델 | 핵심 아이디어 | 대표 논문 |
|------|-------------|----------------|-------------|
| **GANs** | GAN, cGAN, CycleGAN, StarGAN | Adversarial Learning | Goodfellow et al. (2014) |
| **Autoencoder 계열** | AE, VAE, VQ-VAE | Encoding & Reconstruction | Kingma & Welling (2014) |
| **Diffusion 계열** | DDPM, DDIM, CFG, LDM | 확률적 노이즈 제거 기반 생성 | Ho et al. (2020), Rombach et al. (2022) |

---

📚 **참고 문헌**
- Goodfellow et al., *Generative Adversarial Nets*, NeurIPS 2014  
- Isola et al., *Pix2Pix*, CVPR 2017  
- Zhu et al., *CycleGAN*, ICCV 2017  
- Choi et al., *StarGAN*, CVPR 2018  
- Karras et al., *ProgressiveGAN / StyleGAN*, ICLR·CVPR 2018–2019  
- Kingma & Welling, *VAE*, ICLR 2014  
- Van Den Oord et al., *VQ-VAE*, NeurIPS 2017  
- Ho et al., *DDPM*, NeurIPS 2020  
- Song et al., *DDIM*, ICLR 2021  
- Rombach et al., *LDM (Stable Diffusion)*, CVPR 2022  

---
