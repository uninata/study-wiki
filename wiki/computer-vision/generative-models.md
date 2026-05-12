# Generative Models

**Summary**: Deep generative models for image synthesis. Covers Generative Adversarial Networks (GANs) and Variational Autoencoders (VAEs), including the adversarial training framework, the reparameterization trick, and the ELBO objective.

**Course**: computer-vision

**Sources**: deep_learning_2_mpv_2025.pdf

**Last updated**: 2026-05-11

---

## Generative Adversarial Networks (GANs)

Two networks trained in a minimax game (source: computer-vision/deep_learning_2_mpv_2025.pdf):

### Architecture
- **Generator G**: maps random noise $\mathbf{z} \sim p(\mathbf{z})$ to a synthetic image $G(\mathbf{z})$
- **Discriminator D**: classifies images as real or fake — outputs $D(\mathbf{x}) \in [0, 1]$

### Training Objective

$$\min_G \max_D \left[ \mathbb{E}_{\mathbf{x} \sim p_{\text{data}}} \log D(\mathbf{x}) + \mathbb{E}_{\mathbf{z} \sim p_{\mathbf{z}}} \log(1 - D(G(\mathbf{z}))) \right]$$

- D tries to maximize: correctly classify real as real, fake as fake
- G tries to minimize: fool D into classifying fake as real
- At convergence (theoretically): G generates images from the true data distribution, D outputs 0.5 everywhere

### Training Challenges
- **Mode collapse**: G produces limited variety, ignoring parts of the data distribution
- **Training instability**: D and G must stay balanced — if D is too strong, G gradients vanish
- **Non-convergence**: minimax optimization may oscillate rather than converge

### GAN Variants
- **DCGAN**: use convolutions/transposed convolutions (no FC layers), batch norm, specific architecture guidelines for stability
- **Conditional GAN**: condition both G and D on additional information (class label, text)
- **Progressive GAN**: grow both networks gradually from low to high resolution
- **StyleGAN**: style-based generator with adaptive instance normalization, disentangled latent space

## Variational Autoencoders (VAEs)

Probabilistic generative model with an encoder-decoder architecture (source: computer-vision/deep_learning_2_mpv_2025.pdf):

### Architecture
- **Encoder** $q_\phi(\mathbf{z}|\mathbf{x})$: maps input $\mathbf{x}$ to a distribution over latent variables $\mathbf{z}$ (outputs mean $\boldsymbol{\mu}$ and variance $\boldsymbol{\sigma}^2$)
- **Decoder** $p_\theta(\mathbf{x}|\mathbf{z})$: maps latent $\mathbf{z}$ to a distribution over outputs (reconstructed image)

### ELBO (Evidence Lower Bound)

Maximize the variational lower bound on $\log p(\mathbf{x})$ (source: computer-vision/deep_learning_2_mpv_2025.pdf):

$$\text{ELBO} = \mathbb{E}_{\mathbf{z} \sim q}\left[\log p_\theta(\mathbf{x}|\mathbf{z})\right] - D_{\text{KL}}\left(q_\phi(\mathbf{z}|\mathbf{x}) \| p(\mathbf{z})\right)$$

- **Reconstruction term**: decoder should reconstruct $\mathbf{x}$ well from $\mathbf{z}$
- **KL divergence term**: posterior $q_\phi(\mathbf{z}|\mathbf{x})$ should stay close to the prior $p(\mathbf{z}) = \mathcal{N}(\mathbf{0}, \mathbf{I})$

### Reparameterization Trick

To backpropagate through the stochastic sampling $\mathbf{z} \sim q_\phi(\mathbf{z}|\mathbf{x}) = \mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\sigma}^2)$ (source: computer-vision/deep_learning_2_mpv_2025.pdf):

$$\mathbf{z} = \boldsymbol{\mu} + \boldsymbol{\sigma} \odot \boldsymbol{\epsilon}, \quad \text{where } \boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$$

This moves the stochasticity to $\boldsymbol{\epsilon}$ (which doesn't depend on parameters), making the gradient well-defined with respect to $\boldsymbol{\mu}$ and $\boldsymbol{\sigma}$.

### Properties
- **Smooth latent space**: interpolating in $\mathbf{z}$-space produces smooth transitions between images
- **Posterior collapse**: KL term can dominate, causing the encoder to ignore the input ($\mathbf{z} \approx$ prior) — mitigated by KL annealing
- Compared to GANs: more stable training, explicit density model, but typically blurrier outputs

## GANs vs VAEs

| Aspect | GANs | VAEs |
|--------|------|------|
| Training | Adversarial (unstable) | Variational (stable) |
| Output quality | Sharp, realistic | Often blurry |
| Density estimation | Implicit | Explicit (ELBO) |
| Latent space | May be entangled | Structured, smooth |
| Mode coverage | Prone to mode collapse | Better coverage |

## Related pages

- [[computer-vision/cnn-architectures]]
- [[computer-vision/object-detection-segmentation]]
- [[shared/cnn-fundamentals]]
- [[computer-vision/self-supervised-learning]]
