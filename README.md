# Multimodal Introduction：多模态大模型原理系列课

讲「多模态大模型是怎么工作的」的系列课，每个系列拿一个真实开源模型从头走到尾，数字按它的技术报告、源码和 config 核对。
视频是竖屏 1080×1920、每集 3～5 分钟，适合手机看；部分系列配 16:9 讲义，比视频讲得更细。

| 系列 | 讲什么 | 示例模型 | 进度 |
| --- | --- | --- | --- |
| [理解篇](#理解篇看图的大模型怎么工作) | 看图的大模型怎么把图读进去、怎么训出来 | Qwen3-VL-8B | 4 集完结，含讲义 |
| [生成篇](#生成篇生图模型怎么工作) | 生图模型怎么从噪声画出图、怎么听懂文字、怎么训出来 | Qwen-Image | 连载中（已出 4 集），讲义待补 |

后续系列也会放在本仓库，各占一个子目录。

## 理解篇：看图的大模型怎么工作

拿 **Qwen3-VL-8B** 走一遍：图怎么切块、怎么编码、怎么交给语言模型、整个模型怎么训出来。

| 集 | 主题 | 视频 | 讲义（在线翻页） | 口播稿 |
| --- | --- | --- | --- | --- |
| 01 | AI 怎么看懂图片和视频（总览：切块 → 投影 → 编码 → 对齐 → 拼接） | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/v1/vlm-introduction-ep01.mp4)（3:24） | [ep01](https://managerzhang10.github.io/multimodal-introduction/vlm-introduction/lecture/ep01/) | [ep01.md](vlm-introduction/transcripts/ep01.md) |
| 02 | 视觉编码：缩放、切块、位置编码、ViT、合并器、视频 | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/v1/vlm-introduction-ep02.mp4)（4:18） | [ep02](https://managerzhang10.github.io/multimodal-introduction/vlm-introduction/lecture/ep02/) | [ep02.md](vlm-introduction/transcripts/ep02.md) |
| 03 | 图怎么交给大模型：占位符替换、因果注意力、MRoPE 三个坐标、视频时间戳、KV cache | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/v1/vlm-introduction-ep03.mp4)（4:11） | [ep03](https://managerzhang10.github.io/multimodal-introduction/vlm-introduction/lecture/ep03/) | [ep03.md](vlm-introduction/transcripts/ep03.md) |
| 04 | 训练：87 亿个参数从哪来（训练目标、四段预训练、SFT / 蒸馏 / 强化学习） | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/v1/vlm-introduction-ep04.mp4)（4:15） | [ep04](https://managerzhang10.github.io/multimodal-introduction/vlm-introduction/lecture/ep04/) | [ep04.md](vlm-introduction/transcripts/ep04.md) |

- 视频在 [Release v1](https://github.com/ManagerZhang10/multimodal-introduction/releases/tag/v1)。
- 事实来源：Qwen3-VL 技术报告（[arXiv 2511.21631](https://arxiv.org/abs/2511.21631)）、transformers 的 `qwen3_vl` 源码、HF 上 `Qwen/Qwen3-VL-8B-Instruct` 的 config；EP04 另引 SAPO、DeepSeekMath、Qwen3、LLaVA、GKD、MiniLLM 等论文。出处和页码见 [`vlm-introduction/docs/sources/`](vlm-introduction/docs/sources/)。
- 数字以 8B 为准；`docs/sources/` 里有一部分 4B 实测记录，已在文中注明。[`topic-position-encoding.md`](vlm-introduction/docs/topic-position-encoding.md) 是讲 EP02 时追问出来的位置编码拓展问题，标「待核」的条目尚未查证。

## 生成篇：生图模型怎么工作

拿开源的 **Qwen-Image** 走一遍：从噪声一步步画出图、模型由哪两个部件组成、文字怎么控制画面、模型怎么训出来。片中的二维小实验都是真实计算，画面左上角标「真实实验」。

| 集 | 主题 | 视频 | 口播稿 |
| --- | --- | --- | --- |
| 01 | 从噪声画出图：为什么要从噪声开始、改上 50 遍（flow matching、速度、采样步数和时间表） | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/gen-v1/gen-introduction-ep01.mp4)（4:12） | [ep01.md](gen-introduction/transcripts/ep01.md) |
| 02 | 模型长什么样：压缩器 VAE + 去噪网络 MMDiT | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/gen-v1/gen-introduction-ep02.mp4)（4:50） | [ep02.md](gen-introduction/transcripts/ep02.md) |
| 03 | 文本条件怎么输入模型：文本编码、和图拼接、MSRoPE 坐标、CFG | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/gen-v1/gen-introduction-ep03.mp4)（4:38） | [ep03.md](gen-introduction/transcripts/ep03.md) |
| 04 | 生图模型是怎么训出来的：预训练、SFT、强化学习 | [下载 / 播放](https://github.com/ManagerZhang10/multimodal-introduction/releases/download/gen-v1/gen-introduction-ep04.mp4)（3:41） | [ep04.md](gen-introduction/transcripts/ep04.md) |

- 视频在 [Release gen-v1](https://github.com/ManagerZhang10/multimodal-introduction/releases/tag/gen-v1)。
- 事实来源：Qwen-Image 技术报告（[arXiv 2508.02324](https://arxiv.org/abs/2508.02324)）、diffusers 的 Qwen-Image pipeline 与 transformer 源码，以及 DDPM、DDIM、Flow Matching、Rectified Flow、SD3、LDM、DiT、Flow-GRPO 等论文。逐项出处见 [`gen-introduction/docs/sources/`](gen-introduction/docs/sources/)。

## 怎么看讲义

- **在线看（推荐）**：点上面表格里的讲义链接，先看到全部页的总览，点任意一页或右下角「开始演示」进入翻页（← / → 键，ESC 回总览）。
- **下载到本地**：点仓库页面绿色的 Code → Download ZIP，解压后双击 `vlm-introduction/lecture/ep01/index.html`（其他集同理）。每页是 `slides/` 里一个单独的 HTML，由 `index.html` 拼成一套讲义，所以要下载整个文件夹。也可以在仓库根目录跑 `python3 -m http.server`，再打开 `http://localhost:8000/vlm-introduction/lecture/ep01/`。讲义字体从 Google Fonts 加载，离线时会退回系统字体。

## 阅读提示

- 标了「**推断**」「**报告未写**」的地方，是报告和源码都没有直接给出的内容，请按标注理解，不要当成官方结论。
- 口播稿另有只含字幕原文的 JSON 版（`transcripts/ep0N.json`）。

## 制作说明

- 口播用的是作者本人声音的克隆；出镜画面是本人录像加对口型。
- 背景音乐由 ElevenLabs Music 生成。
- 示意图里的折耳猫照片在各系列反复使用，随本仓库一起以 CC BY 4.0 发布。生成篇 EP01 的博美照片来自 Pexels（[Pet foto](https://www.pexels.com/photo/white-volpino-dog-17880516/)，Pexels License）。
- 制作流水线（配音、对口型、渲染脚本）不在本仓库。

## 目录

```text
multimodal-introduction/
├── vlm-introduction/          ← 理解篇
│   ├── lecture/ep01…ep04/     ← 讲义：index.html 翻页外壳 + slides/ thumbs/ assets/
│   ├── transcripts/           ← 口播稿（Markdown + 仅字幕原文的 JSON）
│   └── docs/                  ← 事实核对笔记、拓展专题
├── gen-introduction/          ← 生成篇
│   ├── transcripts/
│   └── docs/sources/
└── 以后的系列/
```

## 许可

内容（讲义、口播稿、笔记、视频）按 [CC BY 4.0](LICENSE) 发布，转载请注明出处。
讲义中引用的论文和源码的版权归原作者所有。
