# EP02 论文事实表：模型长什么样（VAE + DiT）

> 文中 `_work/`、`toy/`、`engine/` 等路径指视频制作工程目录，未随本仓库公开；论文原文和抽字文本也不在本仓库。

- 核对日期：2026-10-11
- arXiv 元数据：API `export.arxiv.org/api/query?id_list=2112.10752,2212.09748,2501.01423,2410.06940,2510.11690,2508.02324` 一次查 6 篇。
- 论文原文：PDF 在 `_work/pdf/<arXiv id>.pdf`，用 pypdf 抽成文字存在 `_work/txt_<arXiv id>.txt`（均为本地文件，不随本仓库公开）。页码是 PDF 的页序号；LDM、DiT、REPA、RAE 页脚印的页码和它一致，VA-VAE 页脚没有印页码。
- 论文原图：清单在 `_work/figs/ep02/README.md`。
- 状态：**已核** = 原文逐字对上；**推断** = 按原文数字算出来的，或者是合理推断；**未找到** = 查过，原文没写。
- 本文件只放论文事实，脚本写在同目录的 `ep02.md`（另一个 agent 维护）。

---

## 1. arXiv 核对（API 一次查 6 篇）

| 论文 | arXiv ID / 标题 | 首发日期（最新版本） | 状态 |
| --- | --- | --- | --- |
| LDM / Stable Diffusion | 2112.10752，High-Resolution Image Synthesis with Latent Diffusion Models（Rombach、Blattmann、Lorenz、Esser、Ommer，CVPR 2022） | 2021-12-20（v2 2022-04-13） | 已核 |
| DiT | 2212.09748，Scalable Diffusion Models with Transformers（Peebles、Xie） | 2022-12-19（v2 2023-03-02） | 已核 |
| VA-VAE / LightningDiT | 2501.01423，Reconstruction vs. Generation: Taming Optimization Dilemma in Latent Diffusion Models（Yao、Yang、Wang，华中科技大学） | 2025-01-02（v3 2025-03-10） | 已核 |
| REPA | 2410.06940，Representation Alignment for Generation: Training Diffusion Transformers Is Easier Than You Think（Yu 等 7 人，ICLR 2025 Oral） | 2024-10-09（v4 2025-06-18） | 已核 |
| RAE | 2510.11690，Diffusion Transformers with Representation Autoencoders（Zheng、Ma、Tong、Xie，NYU） | 2025-10-13（只有 v1） | 已核 |
| Qwen-Image（只取图） | 2508.02324，Qwen-Image Technical Report | 2025-08-04 | 已核 |

任务里给的 5 个 ID 全部和标题对得上，没有错号。

---

## 2. LDM（2112.10752）

| 内容 | 数值或原文 | 来源 | 状态 |
| --- | --- | --- | --- |
| 两段压缩的说法 | 似然模型的学习大致分两段：先是 "a perceptual compression stage which removes high-frequency details but still learns little semantic variation"，再是 "the actual generative model learns the semantic and conceptual composition of the data (semantic compression)" | §1 "Departure to Latent Space"，p2 | 已核 |
| 大部分比特人眼看不出 | Figure 2 图注："Most bits of a digital image correspond to imperceptible details." | Figure 2，p2 | 已核 |
| 为什么像素空间浪费 | 同一图注：扩散模型可以压低这些无意义信息对应的损失项，但梯度和网络 "still need to be evaluated on all pixels, leading to superfluous computations" | Figure 2，p2 | 已核 |
| LDM 的做法 | 先训一个自编码器，给出 "perceptually equivalent to the data space" 的低维空间；自编码器只训一次，可以给多个扩散模型复用 | §1，p2 | 已核 |
| 下采样因子对比实验 | f ∈ {1, 2, 4, 8, 16, 32}，记作 LDM-f，LDM-1 就是像素空间扩散；单张 A100、相同步数、相同参数量 | §4.1，p5 | 已核 |
| 对比结论（原句） | "small downsampling factors for LDM-{1,2} result in slow training progress, whereas ii) overly large values of f cause stagnating fidelity after comparably few training steps" | §4.1，p5 | 已核 |
| 中间档最好（原句） | "LDM-{4-16} strike a good balance between efficiency and perceptually faithful results, which manifests in a significant FID gap of 38 between pixel-based diffusion (LDM-1) and LDM-8 after 2M training steps." | §4.1，p5 | 已核 |
| 总结句 | "In summary, LDM-4 and -8 offer the best conditions for achieving high-quality synthesis results." | §4.1，p5 | 已核 |
| 压缩过头 | Figure 6 图注："Too much perceptual compression as in LDM-32 limits the overall sample quality." | Figure 6，p6 | 已核 |
| 各档 latent 形状 | LDM-1 256×256×3；LDM-2 128×128×2；LDM-4 64×64×3；**LDM-8 32×32×4**；LDM-16 16×16×8；LDM-32 8×8×32（ImageNet 类别条件、2M 步） | Table 13，p24 | 已核 |
| f=8、4 通道是哪个模型 | 本文里有三处：① 上面的 LDM-8（VQ 正则，码本 16384）；② 文生图 LAION 模型：f=8、z-shape 32×32×4、1.45B 参数，Figure 5 写明是 "LDM-8 (KL)"；③ 自编码器表里的 KL f=8 c=4：rFID 0.90、PSNR 24.19 | Table 13，p24；Table 15，p25；Figure 5，p6；Table 8，p21 | 已核 |
| Stable Diffusion 的 VAE = f8、4 通道 | LDM 论文本身没提 "Stable Diffusion" 这个名字。DiT 原文："off-the-shelf pre-trained VAE ... from Stable Diffusion ... downsample factor of 8 ... z = E(x) has shape 32×32×4"；RAE 原文："SD-VAE maps 256² images to 32²×4 latents" | DiT §4，p6；RAE §3，p3 | 已核（SD v1 用的就是 LDM 的 KL-f8 自编码器，这一点属推断） |
| 文生图效果 | MS-COCO 256²：LDM-KL-8 FID 23.31；加 CFG（s=1.5）的 LDM-KL-8-G FID 12.63，1.45B 参数 | Table 2，p6 | 已核 |

## 3. DiT（2212.09748）

| 内容 | 数值或原文 | 来源 | 状态 |
| --- | --- | --- | --- |
| 一句话 | "replacing the commonly-used U-Net backbone with a transformer that operates on latent patches" | 摘要，p1 | 已核 |
| 输入 | SD 的现成 VAE，下采样 8 倍：256×256×3 → 32×32×4 | §3.2 Patchify，p4；§4，p6 | 已核 |
| patchify 和 token 数 | 每个 p×p 小块线性嵌入成一个 token；`T = (I/p)²`；"halving p will quadruple T, and thus at least quadruple total transformer Gflops"；参数量基本不变；设计空间 p = 2, 4, 8 | §3.2，p4；Figure 4 图注，p4 | 已核 |
| 256² 图的 token 数 | I = 32：p=2 → 256 个；p=4 → 64 个；p=8 → 16 个 | 按 `T = (I/p)²` 代入 | 推断 |
| 512² 图的 token 数 | "this XL/2 model processes a total of 1024 tokens after patchifying the 64×64×4 input latent (524.6 Gflops)" | §5.1，p9 | 已核 |
| 同一模型换 patch 的 Gflops | DiT-XL/8 7.39；XL/4 29.05；XL/2 118.64（每降一档约 4 倍） | Table 4，p13 | 已核 |
| 四种模型规模 | S：12 层、d=384、6 头；B：12、768、12；L：24、1024、16；XL：28、1152、16 | Table 1，p5 | 已核 |
| 四种条件注入 block | in-context（t、c 当两个额外 token）、cross-attention（t、c 拼成长度 2 的序列，多加一层交叉注意力，约 15% Gflops 开销）、adaLN、adaLN-Zero | §3.2，p4–5；Figure 3，p3 | 已核 |
| adaLN 定义（原文） | "Rather than directly learn dimension-wise scale and shift parameters γ and β, we regress them from the sum of the embedding vectors of t and c." | §3.2，p5 | 已核 |
| adaLN-Zero 定义（原文） | "In addition to regressing γ and β, we also regress dimension-wise scaling parameters α that are applied immediately prior to any residual connections within the DiT block. We initialize the MLP to output the zero-vector for all α; this initializes the full DiT block as the identity function." | §3.2，p5 | 已核 |
| adaLN-Zero 回归几组参数 | 调制 MLP 输出维度 = 6 × 隐藏维（adaLN 是 4×）；即注意力、MLP 两个子层各一组 γ、β、α | 附录 A，p12；Figure 3 里的 γ1、β1、α1、γ2、β2、α2 | 已核 |
| 为什么初始化成恒等 | 借鉴 ResNet 把残差块初始化成恒等函数的经验（Goyal 等：最后一个 BN 的 γ 置零）；扩散 U-Net 也把每块最后一层卷积置零 | §3.2，p5 | 已核 |
| 四种 block 的 Gflops | in-context 119.4；cross-attention 137.6；adaLN 118.6；adaLN-Zero 118.6（都是 XL/2） | §5，p6 | 已核 |
| Figure 5 的 400K 步 FID | in-context **35.24**；cross-attention **26.14**；adaLN **25.21**；adaLN-Zero **19.47**（FID-50K，不加 CFG）。Figure 5 曲线上没标数字，数字取自附录 Table 4 的同一批模型 | Table 4，p13 | 已核 |
| Figure 5 的结论 | "At 400K training iterations, the FID achieved with the adaLN-Zero model is nearly half that of the in-context model"；"adaLN-Zero ... significantly outperforms vanilla adaLN" | §5，p6 | 已核 |
| Gflops 与 FID 强相关 | Figure 8：400K 步时 Gflops 和 FID 的相关系数 **−0.93**；"We find a strong negative correlation between model Gflops and FID-50K" | Figure 8，p8；§5，p8 | 已核 |
| 参数量不决定效果 | 同模型降 patch，参数基本不变、只有 Gflops 涨，FID 明显变好；Gflops 接近的不同配置 FID 也接近，例如 S/2 与 B/4 | §5，p8 | 已核 |
| 上面那个例子的数字 | DiT-S/2：6.06 Gflops、FID 68.40；DiT-B/4：5.56 Gflops、FID 68.38 | Table 4，p13 | 已核 |
| 最终效果 | DiT-XL/2（7M 步）ImageNet 256²：不加 CFG FID 9.62；CFG=1.50 时 **FID 2.27**（之前最好是 LDM 的 3.60）；512²：3.04 | Table 2、Table 3，p9；摘要，p1 | 已核 |
| 算力对比 | DiT-XL/2 118.6 Gflops；LDM-4 103.6；ADM 1120；ADM-U 742 | §5.1，p8 | 已核 |

## 4. VA-VAE / LightningDiT（2501.01423）

| 内容 | 数值或原文 | 来源 | 状态 |
| --- | --- | --- | --- |
| 两难的说法 | "while increasing the per-token feature dimension in visual tokenizers improves reconstruction quality, it requires substantially larger diffusion models and more training iterations to achieve comparable generation performance" | 摘要，p1 | 已核 |
| Figure 1 的数字（固定算力训扩散模型） | f16d16：rFID 0.49、gFID 20.3；f16d32：rFID 0.29、gFID 28.7；f16d64：rFID 0.18、gFID 45.8。图上标 "better reconstruction / worse generation" | Figure 1，p1 | 已核 |
| Table 2 的数字（同一现象，另一组设置） | LDM 基线 VAE：f16d16 rFID 0.49、gFID 16.24；f16d32 0.26、22.62；f16d64 0.17、36.83（gFID 是 LightningDiT-B、160 epoch、FID-10K） | Table 2，p5 | 已核 |
| 加 VF loss（DINOv2）后 | f16d32：rFID 0.28、gFID 15.82（−6.80）；f16d64：rFID 0.14、gFID 24.00（−12.83）；f16d16 加了反而略差（15.79/10.02/8.71 对 16.24/9.49/8.28），原文说低维不需要 VF loss | Table 2，p5；§5.2，p6 | 已核 |
| Figure 2 的三个点 | VAE f16d16：rFID 0.43、gFID 21.20；VAE f16d32：rFID 0.24、gFID 30.90；VA-VAE f16d32：rFID 0.27、gFID 16.53 | Figure 2，p2（看图确认对应关系） | 已核 |
| 原因的说法 | 高维 tokenizer 学到的 latent "less spread-out"，"the optimization dilemma stems from the inherent difficulty of learning unconstrained high-dimensional latent spaces from scratch" | §1，p2 | 已核 |
| VF loss 对齐谁 | 冻结的视觉基础模型；实验比较了 **MAE 和 DINOv2** 两种，"VF loss (DINOv2) brings the most significant improvement"，最终的 VA-VAE 用 DINOv2 | Figure 3，p3；§5.1，p5；§5.4，p6–7 | 已核 |
| VF loss 组成 | 先线性投影 `Z' = W·Z` 到基础模型的维度；逐位置余弦相似度损失（margin m1）+ 两两距离矩阵相似度损失（margin m2）；再用自适应权重和重建损失配平 | §3.1–3.3，p4（Eq.1–5） | 已核 |
| 超参 | m1 = 0.5，m2 = 0.25，w_hyper = 0.1 | §5.1，p5 | 已核 |
| 收敛加速（VF loss 本身） | f16d32、f16d64 上分别加速 **2.54×、2.76×**；贡献列表写 "over 2.5× faster DiT training" | §5.2，p6；Figure 4，p6；§1，p2 | 已核 |
| LightningDiT 是什么 | 不改 DiT 主体，叠训练和结构技巧：Rectified Flow、大 batch、AdamW β2=0.95、logit-normal 采样、速度方向损失、SwiGLU、RMSNorm、RoPE；用 SD-VAE 时 80 epoch FID 7.13，再换成 patch=1 + VA-VAE 到 4.29 | Table 1，p4；§4，p5 | 已核 |
| patch=1 | "We set the patch size of DiT to 1, ensuring that the downsampling rate of the entire system is 16" | §5.1，p6 | 已核 |
| 最终 FID | 800 epoch：加 CFG **1.35**、不加 2.17；64 epoch：加 CFG **2.11**、不加 5.14；VA-VAE rFID 0.28 | Table 3，p7；摘要，p1 | 已核 |
| 收敛加速倍数（原句） | 摘要："reaching an FID score of 2.11 in just 64 epochs – representing an over 21× convergence speedup compared to the original DiT"；Table 3 图注："demonstrating a 21.8× faster convergence"；§5.4 标题 "Convergence 21.8× Faster than DiT" | 摘要，p1；Table 3，p7；§5.4，p6 | 已核 |
| 21.8× 怎么来的 | DiT 1400 epoch 到 2.27，LightningDiT 64 epoch 到 2.11：1400/64 ≈ 21.9 | 按 Table 3 算 | 推断 |

## 5. REPA（2410.06940）

| 内容 | 数值或原文 | 来源 | 状态 |
| --- | --- | --- | --- |
| 一句话（原文） | "aligns the projections of noisy input hidden states in denoising networks with clean image representations obtained from external, pretrained visual encoders" | 摘要，p1 | 已核 |
| 动机 | 预训练好的 SiT 内部表征 linear probing 远不如 DINOv2，且和 DINOv2 的对齐（CKNNA）很弱，更大模型、更长训练只能慢慢改善 | §3.2，p5；Figure 2，p2 | 已核 |
| 损失 | `L_REPA = −E[ (1/N)·Σ_n sim(y*[n], h_φ(h_t[n])) ]`，y* = 编码器对**干净图**的 patch 特征，h_t = DiT 对**加噪输入**的隐状态，h_φ 是可训练的 MLP 投影头；总损失 `L = L_velocity + λ·L_REPA` | §3.3 Eq.(8)(9)，p6 | 已核 |
| 对齐哪一层 | XL、L 模型对齐**第 8 个 block 的输出**，B 模型对齐第 4 个："We use the 4th layer hidden states for the base model and use the 8th layer hidden states for the large and xlarge model." 正文："we apply REPA to the first 8 layers" | 附录 G，p25；Table 7，p23；§4.2，p8 | 已核 |
| 为什么只对齐前几层 | 只约束前几层生成反而更好（第 6、8 层最好），"this enables the remaining layers to concentrate on capturing high-frequency details" | §4.2，p8；Table 2，p7 | 已核 |
| 对齐到哪个编码器 | 主结果用 **DINOv2-B**，λ = 0.5，负余弦相似度；消融里比了 MAE、DINO、MoCov3、I-JEPA、CLIP、SigLIP、DINOv2-B/L/g | Table 7，p23；附录 G，p25；Table 2，p7 | 已核 |
| 编码器越强越好 | SiT-L/2 400K 步：不加 18.8；MAE-L 12.5；CLIP-L 11.0；DINOv2-B **9.7** | Table 2，p7 | 已核 |
| 主干是谁 | 主要实验在 **SiT**（DiT 同结构、换成 flow 目标）上做，也做了 DiT；输入同样是 SD-VAE 的 32×32×4 | §4.1，p6；Table 3，p9 | 已核 |
| 17.5×（原句） | "our method can speed up SiT training by over 17.5×, matching the performance (without classifier-free guidance) of a SiT-XL model trained for 7M steps in less than 400K steps" | 摘要，p1 | 已核 |
| 17.5× 的数字 | SiT-XL/2 7M 步 FID 8.3；加 REPA 400K 步 FID 7.9（7M / 400K = 17.5） | Table 3，p9 | 已核 + 推断（除法） |
| DiT 上的效果 | DiT-XL/2 400K 步：19.5 → 12.3；DiT-XL/2 训 7M 步的 FID 9.6，加 REPA 850K 步就到 9.6 | Table 3，p9 | 已核 |
| DiT 上折算倍数 | 7M / 850K ≈ 8.2×（论文没写这个倍数） | 按 Table 3 算 | 推断 |
| 最终 FID | SiT-XL/2 + REPA 800 epoch：CFG w=1.35 时 **1.80**；再加 guidance interval 时 **1.42**（原 SiT-XL/2 1400 epoch 是 2.06） | Table 4，p9；§4.3，p9 | 已核 |

## 6. RAE（2510.11690）

| 内容 | 数值或原文 | 来源 | 状态 |
| --- | --- | --- | --- |
| 一句话（原文） | "we explore replacing the VAE with pretrained representation encoders (e.g., DINO, SigLIP, MAE) paired with trained decoders, forming what we term Representation Autoencoders (RAEs)" | 摘要，p1 | 已核 |
| 编码器冻结 | Figure 1 图注："RAE uses frozen pretrained representations as the encoder with a lightweight decoder to reconstruct input images without compression." | Figure 1，p1；§3，p3 | 已核 |
| 默认配置 | 编码器 DINOv2-B（带 registers，p_e=14，d=768）+ ViT-XL 解码器；丢掉 CLS/REG token，每个 token 做一次不带仿射的 LayerNorm | Table 1 图注，p4；附录 C.1，p22 | 已核 |
| 256² 图几个 token | 256 个 token，和 SD-VAE + patch 2 的 DiT 一样多；DINOv2 只有 p=14，所以输入插值到 224×224、解码器 p_d=16 还原成 256×256 | §3，p3；附录 C.1，p22 | 已核 |
| 每个 token 的维度 | RAE（DINOv2-B）768 维；SD-VAE 每个位置 4 维 | Figure 2，p3 | 已核 |
| 解码器怎么训 | L1 + LPIPS + 对抗损失；判别器用冻结的 DINO-S/8 | §3，p4；附录 C.2，p22 | 已核 |
| 重建（rFID，ImageNet） | DINOv2-B 0.49；SigLIP2-B 0.53；MAE-B 0.16；SD-VAE 0.62 | Table 1a，p4 | 已核 |
| 表征（linear probing top-1） | DINOv2-B 84.5；SigLIP2-B 79.1；MAE-B 68.0；SD-VAE 8.0 | Table 1d，p4 | 已核 |
| 算力 | SD-VAE 编码器 135 GFLOPs、解码器 310；RAE 编码器 22、解码器 106（256² 一张图），"about 6× and 3× more GFLOPs" | Figure 2，p3 | 已核 |
| 直接套标准 DiT 不行 | gFID：DiT-S 在 RAE 上 215.76（SD-VAE 上 51.74）；DiT-XL 23.08（SD-VAE 上 7.13） | Table 2，p5 | 已核 |
| 对 DiT 的改动 1：宽度 | "the diffusion model's width must match or exceed the RAE's token dimension"（Theorem 1：宽度 d < 维度 n 时损失有下界） | §4.1，p6 | 已核 |
| 对 DiT 的改动 2：按维度 shift | 沿用 SD3 的 shift 公式，但把 "分辨率" 换成有效数据维度（token 数 × 每个 token 的维度），基准 n = 4096；gFID 23.08 → 4.81 | §4.2，p6–7；Table 4，p7 | 已核 |
| 对 DiT 的改动 3：加噪训练解码器 | 训练解码器时给 latent 加噪声，σ ~ \|N(0, τ²)\|；gFID 4.81 → 4.28，rFID 0.49 → 0.57 | §4.3，p7；Table 5，p7 | 已核 |
| 对 DiT 的改动 4：宽头 DiT^DH（一句话） | 在标准 DiT 后面接一个 "shallow yet wide" 的 DDT head（2 层、2048 维），用它满足宽度要求而不让整个主干变宽：`z_t = M(x_t ∣ t, y)`，`v_t = H(x_t ∣ z_t, t)` | §5，p8 | 已核 |
| 不加宽头时 | DiT-XL on RAE：80 epoch gFID 4.28，720 epoch 2.39；相对 SiT-XL 训练加速 47×，相对 REPA-XL 16× | §4.3 末段，p7 | 已核 |
| 加宽头后 | DiT^DH-XL（839M）80 epoch gFID 2.16 | §5，p8；Table 8，p9 | 已核 |
| ImageNet 256² 最终 FID | DiT^DH-XL（DINOv2-B）800 epoch：不加引导 **1.51**；加引导 **1.13** | Table 8，p9；摘要，p1 | 已核 |
| ImageNet 512² 最终 FID | 加引导 **1.13**（400 epoch，之前最好是 EDM2 的 1.25） | Table 7，p9；§5.1，p10 | 已核 |
| 引导方式 | 1.13 用的是 **AutoGuidance**（拿 14 epoch 的 DiT^DH-S 当弱模型、scale 1.42），不是普通 CFG；原文说 CFG 不加 interval 反而让 FID 变差 | 附录 I，p27 | 已核 |
| 是否需要对齐损失 | "Our approach achieves faster convergence without auxiliary representation alignment losses" | 摘要，p1 | 已核 |

---

## 7. 和任务给定信息不一致或需要注意的地方

1. **ID 全部正确**，标题、作者都对得上。RAE 首发是 2025-10-13。
2. **DiT Figure 5 的曲线上没有标数字。** 400K 步的 FID 35.24 / 26.14 / 25.21 / 19.47 取自附录 Table 4（p13），是同一批模型。
3. **VA-VAE 的倍数有两种写法**：摘要写 "over 21×"，Table 3 图注和 §5.4 标题写 "21.8×"。VF loss 本身的加速是 2.54× / 2.76×，别和 21.8× 混在一起。
4. **VA-VAE 的 rFID/gFID 有三套数**（Figure 1、Figure 2、Table 2，设置各不相同）。视频里用哪张图就配哪套数，不要混着用。例如 f16d32 的 rFID 在 Figure 1 是 0.29，在 Table 2 是 0.26。
5. **VA-VAE 对齐的不只是 DINOv2**：论文同时试了 MAE 和 DINOv2，最终版用 DINOv2。
6. **REPA 的 17.5× 是 SiT-XL/2 的数**，不是 DiT。DiT-XL/2 上论文只给了 "850K 步追平 7M 步的 9.6"，折算约 8.2×（自己算的）。
7. **REPA 对齐的层**：XL/L 模型是第 8 个 block 的输出，B 模型是第 4 个；编码器是 DINOv2-B。
8. **RAE 的 1.13 用的是 AutoGuidance**，不是 CFG；256² 和 512² 恰好都是 1.13。
9. **LDM 论文里没有 "Stable Diffusion" 这个名字。** "SD-VAE 是 f8、4 通道" 这句话在 DiT（p6）和 RAE（p3）的原文里有；LDM 原文里 f=8、32×32×4 的是 LDM-8 和 LAION 上 1.45B 的文生图模型。
