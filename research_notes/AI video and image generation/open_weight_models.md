# Open-Weight (Self-Hostable) AI Image and Video Generation Models — State as of October 2026

Research date: 2026-10-05. Method note: WebFetch was blocked by the network egress proxy for most domains (invideo.io, artificialanalysis.ai, wan27.org, magica.com), so findings below come from search-result snippets of the cited pages, not full-page reads. Treat numbers as "as reported by the cited page" and verify against primary model cards before publishing. Items I could not source this session are in the Gaps sections, clearly labelled as unverified background knowledge.

## 1. Open-weight IMAGE models: inventory (architecture, size, dates, hardware, variants)

### Takeaway
By late 2026 the open-weight image field is led by large 2025–2026 releases: NVIDIA Cosmos3-Super-Text2Image (65B, May 2026), HiDream-O1-Image (8B, pixel-native, no VAE, May 2026), Baidu ERNIE-Image (8B), Qwen-Image-2512 (20.4B), and FLUX.2 [dev] (32B). Small, fast, permissively licensed models have also become a category of their own: Z-Image-Turbo (6B, Apache 2.0) and FLUX.2 [klein] 4B (Apache 2.0). FLUX.1-era models and SDXL/SD3.5 are now legacy, although their fine-tune ecosystems are still widely used.

### Cited Findings

**Black Forest Labs: FLUX.2 family (released November 2025)**
- FLUX.2 [dev] is a 32B-parameter rectified-flow transformer paired with a Mistral Small 3.1/3.2 24B vision-language text encoder. The FLUX.2 family was released in November 2025. — [Spheron deploy guide](https://www.spheron.network/blog/deploy-flux2-gpu-cloud-production-guide/); [RunPod guide](https://www.runpod.io/articles/guides/deploying-flux-2)
- FLUX.2 [dev] VRAM: the FP8 checkpoint takes about 32 GB VRAM and runs best on a single H100/A100 80GB. An FP8 mixed-precision file of about 35.5 GB is reported to run in under 20 GB VRAM with offloading. city96 publishes GGUF quants from Q2 to Q8 for low-VRAM users. — [Spheron](https://www.spheron.network/blog/deploy-flux2-gpu-cloud-production-guide/); [stablediffusiontutorials (Nov 2025)](https://www.stablediffusiontutorials.com/2025/11/flux-2.html)
- In ComfyUI, FLUX.2 [dev] loads as a standard diffusion model plus a single text encoder plus a VAE, and accepts optional multi-reference images. — [stablediffusiontutorials](https://www.stablediffusiontutorials.com/2025/11/flux-2.html)
- FLUX.2 [klein] ships in 4B and 9B open-weight variants. BFL markets it as generating images in under a second. — [BFL docs](https://docs.bfl.ai/flux_2); [Vercel AI gateway listing](https://vercel.com/ai-gateway/models/flux-2-klein-4b/about)
- A third-party page refers to "56B FLUX.2" in comparison, which likely means the 32B DiT plus the 24B encoder. — [WaveSpeed blog](https://wavespeed.ai/blog/posts/hidream-o1-image-dev-pixel-unified-transformer/)

**Alibaba Tongyi-MAI: Z-Image (released 26 November 2025)**
- 6B-parameter S3-DiT (single-stream DiT). The distilled Z-Image-Turbo variant uses Decoupled-DMD plus DMDR (DMD + RL distillation) and needs 8 NFEs. It produces images in under a second on datacenter GPUs and runs on 16 GB consumer cards. It has strong bilingual English–Chinese text rendering. — [RunDiffusion](https://www.rundiffusion.com/z-image); [NYU Shanghai RITS](https://rits.shanghai.nyu.edu/ai/z-image-alibabas-efficient-6b-open-source-image-generation-model/); [getimg.ai review](https://getimg.ai/blog/z-image-turbo-review-how-this-small-ai-model-delivers-big-results)

**Alibaba Qwen: Qwen-Image line**
- Qwen-Image-Edit-2511 weights were released on 23 December 2025 and Qwen-Image-2512 on 31 December 2025. Both have 20.4B parameters. The base Qwen-Image is known for complex text rendering and precise editing. — [QwenLM/Qwen-Image GitHub](https://github.com/QwenLM/Qwen-Image); [Nunchaku-Qwen-Image-2512 HF](https://huggingface.co/QuantFunc/Nunchaku-Qwen-Image-2512) (Nunchaku 4-bit SVDQuant build exists)
- **Qwen-Image-2.1** (open-weighted 20 September 2026) is a 7B DiT that handles both T2I and editing. It supports native 2048×2048, native RGBA/transparent PNG output, and up to ten reference images. The local download is about 33 GB. — [AlternativeTo news, Sep 2026](https://alternativeto.net/news/2026/9/alibaba-launches-qwen-image-2-1-a-7b-ai-model-with-native-transparency-and-a-license-change/); [aiweekly](https://aiweekly.co/alerts/alibaba-ships-qwen-image-21-7b-dit-with-native-2048x2048-and-rgba-drops-apache); [aireiter review](https://aireiter.com/zh/blog/qwen-image-2-1-review-tested-local-vram-license); [locallyuncensored](https://locallyuncensored.com/blog/qwen-image-2-1-explained.html)
- **Qwen-Image-3.0 has no open weights** (checked 13 August 2026). Qwen Image Max 2512 also appears on leaderboards as a hosted model. — [freeimggen](https://freeimggen.com/blog/qwen-image-3-has-no-open-weights/); [orcarouter](https://www.orcarouter.ai/blog/qwen-image-2-1-vs-qwen-image-3-0)

**HiDream-ai: HiDream-O1-Image (open-sourced 8 May 2026)**
- 8B parameters, built on a "Pixel-level Unified Transformer (UiT)" with no external VAE and no separate text encoders. It does T2I, instruction editing, subject-driven personalization and storyboards at up to 2048×2048. — [HF model card](https://huggingface.co/HiDream-ai/HiDream-O1-Image); [aifilms](https://studio.aifilms.ai/blog/hidream-o1-image-open-source-pixel-native)
- Variants: the full HiDream-O1-Image (50 steps, CFG 5.0); the distilled HiDream-O1-Image-Dev (28 steps, CFG 0); and HiDream-O1-Image-Dev-2604 (14 May 2026), which adds a reasoning prompt agent that rewrites prompts. A community FP8 build exists. ComfyUI has a native workflow. — [aifilms](https://studio.aifilms.ai/blog/hidream-o1-image-open-source-pixel-native); [drbaph FP8 HF](https://huggingface.co/drbaph/HiDream-O1-Image-Dev-FP8); [ComfyUI docs](https://docs.comfy.org/tutorials/image/hidream/hidream-o1)
- Supersedes the earlier HiDream-I1 (17B, 2025) as HiDream's flagship (inference from the release sequence).

**Baidu: ERNIE-Image** — an 8B text-to-image family with standard and Turbo checkpoints. — [magichour roundup](https://magichour.ai/blog/open-source-image-generation-models)

**NVIDIA: Cosmos3-Super-Text2Image (Hugging Face, 31 May 2026)**
- 65B parameters, part of the Cosmos 3 omnimodal "world model" family. It uses a Mixture-of-Transformers design: an autoregressive tower for discrete tokens plus a diffusion transformer tower for continuous generation. It is described as "agentic" on the AA arena. — [creativeainews](https://www.creativeainews.com/articles/nvidia-cosmos-3-open-source-t2i-image-video-2026/); [HF COSMOS3-NANO card](https://huggingface.co/NVIDIA/COSMOS3-NANO); [Gigazine 2 Jun 2026](https://gigazine.net/gsc_news/en/20260602-nvidia-cosmos-3)

**Tencent: HunyuanImage 3.0** — an 80B-parameter Mixture-of-Experts model, open-sourced. — [layer3labs comparison](https://www.layer3labs.io/comparisons/best-chinese-ai-models-for-image-generation)

### Inferences
- Hardware tiers: (a) runs on 8–16 GB consumer GPUs: Z-Image-Turbo, FLUX.2 klein 4B, HiDream-O1 8B (FP8), ERNIE-Image 8B, Qwen-Image-2.1 7B (quantized). (b) Needs 24–32 GB or heavy quantization: Qwen-Image-2512 (20.4B) and FLUX.2 dev (32B + 24B encoder). (c) Datacenter class: HunyuanImage 3.0 (80B MoE) and Cosmos3-Super (65B).
- Architectures are moving from latent DiT + VAE + separate encoders toward unified or multimodal transformers (HiDream-O1 pixel-native UiT, Cosmos3 MoT, HunyuanImage 3.0 AR-MoE, FLUX.2 with a VLM encoder). Generation and editing are increasingly combined in one checkpoint.
- Superseded or legacy as of October 2026: FLUX.1 [dev]/[schnell]/Kontext [dev] (replaced by FLUX.2), the original Qwen-Image (Aug 2025, replaced by 2512 and 2.1), HiDream-I1 (replaced by O1), and SD3.5/SDXL as base models.

### Gaps
- Not verified this session; the following is background from training knowledge (to mid-2026) and needs checking against primary sources:
  - FLUX.1 [dev]: 12B, Aug 2024, FLUX.1 Non-Commercial License.
  - FLUX.1 [schnell]: 12B, Apache 2.0, 1–4 steps.
  - FLUX.1 Kontext [dev]: 12B editing model, Jun 2025, non-commercial.
  - SD 3.5 Large / Large Turbo / Medium: 8.1B, 8.1B, 2.5B (Oct 2024), MMDiT, Stability AI Community License.
  - SDXL: 3.5B base, Jul 2023. Pony Diffusion V6 XL and Illustrious XL are SDXL fine-tunes popular for anime and stylized work.
  - Chroma: about 8.9B, derived from FLUX.1 schnell, Apache 2.0.
  - Sana: NVIDIA, 0.6B/1.6B, linear DiT, 4K capable.
  - Lumina-Image 2.0: about 2.6B, Apache 2.0.
  - OmniGen / OmniGen2: VectorSpace Lab, unified generation and editing.
  - BAGEL: ByteDance, 7B active / 14B total MoT, Apache 2.0.
  - HunyuanImage 2.1: 17B, 2K output.
  - HiDream-I1: 17B, MIT.
- No primary detail was found for ERNIE-Image's release date or architecture, for Cosmos3-Super's VRAM, or for HunyuanImage 3.0's release date and VRAM. HunyuanImage 3.0 was reportedly released around September 2025 and needs multiple 80 GB GPUs; this is unverified.
- Exact max resolution for FLUX.2 dev and klein was not confirmed this session.

## 2. Open-weight VIDEO models (including lip-sync/avatar): inventory

### Takeaway
The biggest 2026 shift is that **Alibaba stopped open-sourcing Wan after 2.2**: Wan 2.5, 2.6, 2.7 and 3.0 are API-only. Leadership in open video passed to Lightricks' **LTX-2 / LTX-2.5** (22B, joint audio+video, native 4K), Tencent's **HunyuanVideo 1.5** (8.3B, 14 GB VRAM), and MiniMax's **H3 / Hailuo 3.0** (open weights from 31 July 2026, but geo-restricted). Wan 2.1/2.2 (Apache 2.0) remain the backbone of the lip-sync ecosystem (MultiTalk, InfiniteTalk).

### Cited Findings

**Alibaba Wan**
- Wan 2.2 is the newest Wan version with downloadable weights. Wan 2.1 and 2.2 are Apache 2.0. Wan 2.5 (Sep 2025) and Wan 2.6 (Dec 2025) were API-only, and their weights were never published despite earlier promises. — [aiwiki Wan 2.5](https://www.aiwiki.ai/wiki/wan_2_5); [Spheron Wan 2.5](https://www.spheron.network/blog/deploy-wan-2-5-gpu-cloud/); [localaimaster](https://localaimaster.com/blog/wan-2-7-open-source)
- Wan 2.7 shipped API-only in April 2026. As of September 2026 the official Wan-AI Hugging Face and Wan-Video GitHub organizations list no Wan 2.7 weights. Some SEO sites claim an Apache 2.0 release, but authoritative checks contradict them. — [localaimaster](https://localaimaster.com/blog/wan-2-7-open-source); [queststudio](https://queststudio.io/blog/wan-2-7-open-source); contradicted by [creativeaishow](https://creativeaishow.com/wan-2-7-open-source-free-ai-video/) (claims open; treat as unreliable)
- Wan 3.0: public beta on 6 August 2026, official launch on 24 August 2026. It generates up to 30 s at up to 1080p in one pass. **No published weights.** — [orcarouter](https://www.orcarouter.ai/blog/wan-3-0-vs-minimax-h3); [AtlasCloud](https://www.atlascloud.ai/blog/tips/is-wan-3.0-open-source); [kingy.ai](https://kingy.ai/blog/wan-3-0-analysis/)

**Lightricks LTX**
- LTX-2 was first released in November 2025, with weights open-sourced later as a base 19B model plus a distilled model. In January 2026 Artificial Analysis reported LTX-2 as the new leading open-weights video model, surpassing Wan 2.2 A14B in both T2V and I2V. — [Artificial Analysis on X](https://x.com/ArtificialAnlys/status/2012256702788153604); [introl blog](https://introl.com/blog/ltx-2-audiovisual-diffusion-synchronized-video-audio-2026)
- LTX-2.5 (open weights, about 11 August 2026) is a 22B asymmetric dual-stream DiT with separate video and audio streams joined by bidirectional cross-attention. It uses a 12B Gemma 4 text encoder and prompt enhancer. It supports T2V, I2V and V2V with synchronized audio, 720p to native 4K HDR, 6–20 s clips, and up to 50 fps. Distilled and trainable checkpoints (both 22B) are on Hugging Face. It is available in ComfyUI. — [OpenSourceForU, Aug 2026](https://www.opensourceforu.com/2026/08/ltx-2-5-brings-open-weight-video-generation/); [comfyui-wiki, 11 Aug 2026](https://comfyui-wiki.com/en/news/2026-08-11-ltx-2-5-open-weights-release); [comfy.org](https://comfy.org/ltx-2.5/)
- An intermediate LTX-2.3 ("Pro") also appears on the AA board. — [AA T2V board](https://artificialanalysis.ai/video/leaderboard/text-to-video)

**Tencent HunyuanVideo**
- HunyuanVideo-1.5 is a lightweight 8.3B model released on 21 November 2025. It runs on consumer GPUs with 14 GB VRAM and generates 480p/720p natively with super-resolution to 1080p. — [promptus](https://www.promptus.ai/blog/hunyuan-video-1-5); [toolnavs](https://toolnavs.com/en/article/803-hunyuanvideo-15-480p720p%EC%97%90%EC%84%9C-1080p%EB%A1%9C-hd-%EB%B9%84%EB%94%94%EC%98%A4-%EC%83%9D%EC%84%B1); [ComfyUI docs](https://docs.comfy.org/ja/tutorials/video/hunyuan/hunyuan-video-1-5)
- One source calls HunyuanVideo 1.5 "the best open option for natural motion and physics, with the most cinematic default look." This is an opinion. — [techsy via search](https://techsy.io/en/blog/best-ai-video-models)
- HunyuanVideo-Avatar turns a photo plus audio into a talking or singing digital human at 720p. It needs at least 24 GB VRAM, with 96 GB recommended. — [promptus avatar](https://www.promptus.ai/blog/hunyuan-ai-avatar-free-open-source-tool); [hunyuanvideo-avatar.com](https://hunyuanvideo-avatar.com/)

**MiniMax H3 (Hailuo 3.0), launched 31 July 2026**
- An omni-modal transformer that understands text, image, video and audio. It outputs video with native stereo sound at up to 2K and 15 s. It shipped with downloadable weights and has ComfyUI support. — [HF blog](https://huggingface.co/blog/ResterChed/minimax-h3-hailuo-3-0); [comfy.org](https://comfy.org/minimax-h3/); [fal](https://fal.ai/minimax-h3); [orcarouter](https://www.orcarouter.ai/blog/wan-3-0-vs-minimax-h3)

**NVIDIA Cosmos 3** — an omnimodal world-model family (31 May 2026) with five open model types, including video-capable checkpoints. — [Gigazine](https://gigazine.net/gsc_news/en/20260602-nvidia-cosmos-3); [nvidia llm-info](https://www.nvidia.com/en-us/ai/cosmos/llm-info/)

**Lip-sync and avatar models**
- InfiniteTalk (MeiGen-AI) does unlimited-length audio-driven talking video, supporting both I2V and V2V dubbing, and drives lips, head, body and expression. It is Apache 2.0, recommends 24 GB+ VRAM, and has a low-VRAM mode (`--num_persistent_param_in_dit 0`). It builds on MeiGen's MultiTalk, which does multi-person, phoneme-aware audio-driven video. ComfyUI workflows exist. — [InfiniteTalk GitHub](https://github.com/MeiGen-AI/InfiniteTalk); [RunComfy workflow](https://www.runcomfy.com/comfyui-workflows/comfyui-infinitetalk-workflow-audio-portrait-to-lip-synced-video)
- SoulX-FlashTalk (arXiv, Dec 2025) does real-time infinite streaming of audio-driven avatars through self-correcting bidirectional distillation. — [arXiv 2512.23379](https://arxiv.org/pdf/2512.23379)

### Inferences
- Practical recommendations for October 2026:
  - Best quality with commercial use under $10M revenue: LTX-2.5.
  - Best for consumer GPUs: HunyuanVideo 1.5 (14 GB), but not usable in the EU, UK or South Korea.
  - Most permissive license with a mature LoRA/lip-sync ecosystem: Wan 2.2 (Apache 2.0), though its quality is now behind.
  - Highest-quality open weights: MiniMax H3, but its license blocks the US, UK, EU and South Korea, which makes it nearly unusable for Western businesses.
- Native audio generation (LTX-2/2.5, MiniMax H3) became expected in open video during 2026.

### Gaps
- Not verified this session; background from training knowledge that needs checking:
  - Wan 2.1: 1.3B and 14B, Feb 2025, Apache 2.0. The 1.3B runs in about 8 GB.
  - Wan 2.2: A14B MoE (high-noise and low-noise experts) plus a TI2V-5B, Jul 2025, 720p at 24 fps, about 5 s.
  - HunyuanVideo (original): 13B, Dec 2024.
  - Mochi 1: Genmo, 10B AsymmDiT, Apache 2.0, Oct 2024.
  - CogVideoX: 2B/5B, Zhipu, 2024; the 5B is under the CogVideoX License.
  - SkyReels V1/V2: Skywork, HunyuanVideo/Wan-based, infinite-length "diffusion forcing".
  - Open-Sora 2.0: 11B, Mar 2025, Apache 2.0.
  - MAGI-1: Sand AI, 24B autoregressive chunked video, Apache 2.0, Apr 2025.
  - Step-Video-T2V: StepFun, 30B, Feb 2025, MIT.
  - Hallo / Hallo2 / Hallo3: Fudan, portrait animation.
- No VRAM figures were found for LTX-2.5 or MiniMax H3, and no parameter count was found for MiniMax H3.
- The exact LTX-2 open-weights date (reported only as "recently" in January 2026) was not confirmed. LTX-2 is cited as 19B and LTX-2.5 as 22B.

## 3. Licenses and commercial implications

### Takeaway
Fully commercial open licenses (Apache 2.0 or MIT) cover Z-Image, FLUX.2 klein-4B, Qwen-Image/2512/Edit-2511, HiDream-O1, ERNIE-Image, Wan 2.1/2.2 and InfiniteTalk. Revenue-gated licenses cover LTX-2.x (free below $10M annual revenue) and Stability (Community License). Non-commercial or research-only licenses cover FLUX.1/FLUX.2 dev, klein-9B and, as of September 2026, **Qwen-Image-2.1**, a notable regression from Apache. Territory-restricted licenses cover Tencent Hunyuan (excludes the EU, UK and South Korea) and MiniMax H3 (excludes the US, UK, EU and South Korea).

### Cited Findings
- **FLUX.2 [klein] 4B: Apache 2.0**, unrestricted commercial use. **klein 9B and FLUX.2 [dev] 32B: FLUX Non-Commercial License.** Outputs may be used commercially, but deploying or serving the model in a commercial product requires a paid BFL license. — [invideo license matrix (Aug 2026)](https://invideo.io/blog/open-source-image-models-licenses/); [invideo Flux 2 explainer](https://invideo.io/blog/flux-ai-image-generator/); [canirun.ai FLUX NC](https://canirun.ai/license/flux-non-commercial/)
- **Z-Image / Z-Image-Turbo: Apache 2.0**, fully commercial. — [RunDiffusion](https://www.rundiffusion.com/z-image); [NYU RITS](https://rits.shanghai.nyu.edu/ai/z-image-alibabas-efficient-6b-open-source-image-generation-model/)
- **Qwen-Image, Qwen-Image-2512, Qwen-Image-Edit-2511: Apache 2.0.** — [QwenLM GitHub](https://github.com/QwenLM/Qwen-Image)
- **Qwen-Image-2.1 (Sep 2026): Qwen Research License Agreement.** It allows non-commercial research and evaluation only; commercial use needs a separate license from Qwen. Alibaba "drops Apache" with this release. — [aiweekly](https://aiweekly.co/alerts/alibaba-ships-qwen-image-21-7b-dit-with-native-2048x2048-and-rgba-drops-apache); [aicybr](https://aicybr.com/blog/qwen-image-2-1-local-image-generation-editing-guide); [eesel](https://www.eesel.ai/blog/qwen-image-2-1); [cellcog: "7B Open Weights You Cannot Ship"](https://cellcog.ai/blog/qwen-image-2-1/)
- **HiDream-O1-Image: MIT** (code and models). — [HF model card](https://huggingface.co/HiDream-ai/HiDream-O1-Image); [aifilms](https://studio.aifilms.ai/blog/hidream-o1-image-open-source-pixel-native)
- **ERNIE-Image: Apache 2.0** (official code and weights). — [magichour](https://magichour.ai/blog/open-source-image-generation-models)
- **Cosmos3-Super-Text2Image: OpenMDW 1.1**, which allows commercial use of outputs with attribution. — [creativeainews](https://www.creativeainews.com/articles/nvidia-cosmos-3-open-source-t2i-image-video-2026/)
- **HunyuanImage 3.0: Tencent Community License.** It is free unless the user exceeds 100M monthly active users. — [layer3labs](https://www.layer3labs.io/comparisons/best-chinese-ai-models-for-image-generation). The territory clause is presumed to apply as for other Hunyuan models; this is inferred, not confirmed for 3.0 specifically.
- **HunyuanVideo 1.5: Tencent Hunyuan Community License.** Commercial use is allowed in principle, but the license excludes the **EU, UK and South Korea**. Users there may not use, reproduce, modify, distribute or display the model or its outputs. — [promptus](https://www.promptus.ai/blog/hunyuan-video-1-5); [chatforest review](https://chatforest.com/reviews/hunyuanvideo-tencent-open-source-video-generation/)
- **LTX-2 / LTX-2.x Community License:** free commercial and production use for entities with **under $10M annual revenue**. At $10M or above a paid commercial license is required. Revenue is counted across affiliates and subsidiaries under common control, and the threshold also covers derivatives. — [Lightricks LTX-2 license PDF](https://static.lightricks.com/legal/ltx-2-open-weights-license-0.X.pdf); [ScanCode LicenseDB ltx-2-cla-2026](https://scancode-licensedb.aboutcode.org/ltx-2-cla-2026.html); [ltx.io license](https://ltx.io/model/license); [canirun.ai](https://canirun.ai/license/ltx-2-community/)
- **LTX-2.5** is under the same LTX-2.x Community License with the revenue threshold. — [OpenSourceForU](https://www.opensourceforu.com/2026/08/ltx-2-5-brings-open-weight-video-generation/)
- **Wan 2.1 / 2.2: Apache 2.0.** — [aiwiki](https://www.aiwiki.ai/wiki/wan_2_5); [localaimaster](https://localaimaster.com/blog/wan-2-7-open-source)
- **MiniMax H3:** weights are downloadable, but the license **excludes the US, UK, EU and South Korea**. — [orcarouter](https://www.orcarouter.ai/blog/wan-3-0-vs-minimax-h3)
- **InfiniteTalk: Apache 2.0.** — [GitHub](https://github.com/MeiGen-AI/InfiniteTalk)

### Inferences
- For a US or EU commercial product with no licensing negotiation, the safe open set as of October 2026 is:
  - Image: Z-Image(-Turbo), FLUX.2 klein-4B, Qwen-Image-2512 / Edit-2511, HiDream-O1, ERNIE-Image, and Cosmos3 (check the OpenMDW attribution terms).
  - Video: Wan 2.1/2.2 (plus InfiniteTalk/MultiTalk) and LTX-2.x if revenue is under $10M.
- The Chinese labs that defined open weights in 2025 are moving toward closed or restricted releases in 2026: Wan 2.5–3.0 are API-only, Qwen-Image-2.1 is research-only, and Qwen-Image-3.0 is closed. MiniMax H3 is open but geo-fenced. Western and other labs (NVIDIA, Lightricks, HiDream, Baidu ERNIE) are partly filling the gap.

### Gaps
- The Stability AI Community License (SD3.5) terms were not re-verified this session. Background knowledge says it is free commercial use below $1M annual revenue, with an enterprise license above that, and that SDXL uses the CreativeML Open RAIL++-M license. Verify both.
- The FLUX.1 [dev] Non-Commercial License and BFL's paid licensing pricing were not re-checked.
- The exact license text for HunyuanImage 3.0's territory clause and the full MiniMax H3 license were not fetched.
- The Pony V6 and Illustrious license specifics (Fair AI Public License and other derivative terms) were not verified.

## 4. Ranking vs closed models (Artificial Analysis / LMArena)

### Takeaway
On Artificial Analysis's image arena in mid-2026, the best open-weights model (Cosmos3-Super-Text2Image, Elo about 1219–1230) sits close to the top closed models. Open models fill several of the top slots (HiDream-O1-Dev-2604, ERNIE-Image, FLUX.2 dev). In video, the gap is wider: the open leader MiniMax H3 (Elo 1138) trails Wan 3.0 (closed, 1157) only slightly, but the next open models (LTX-2.5, about 945) are about 200 Elo behind.

### Cited Findings
- AA Text-to-Image Arena, open-weights leaders: Cosmos3-Super-Text2Image (agentic) at Elo 1230, HiDream-O1-Image-Dev-2604 at 1187, and ERNIE Image at 1167. — [AA leaderboard via search](https://artificialanalysis.ai/text-to-image/arena/leaderboard-text); [pinggy](https://pinggy.io/blog/best_free_open_source_ai_image_generators_to_self_host/)
- A July 2026 snapshot (mixed open and closed) listed: Cosmos3-Super 1219, HiDream-O1-Image-Dev-2604 1183, Ideogram 4.0 Quality (closed) 1164, ERNIE Image 1163, Qwen Image Max 2512 1154, FLUX.2 [dev] 1152. — [techsy best AI image models](https://techsy.io/en/blog/best-ai-image-models)
  - Caveat: this is an aggregator. It is unclear whether this list covers open models only plus some closed ones, or the full board. The top closed models (GPT-Image, Gemini/Nano Banana, Seedream) are not shown, and "Qwen Image Max" is believed to be hosted-only.
- When it launched (May 2026), AA said HiDream-O1-Image-Dev-2604 "debuts as the leading open weights Text to Image model." — [AA on X](https://x.com/ArtificialAnlys/status/2061189088204755291)
- A secondary blog claimed HiDream-O1-Image-Dev (8B) "beat 56B FLUX.2." — [WaveSpeed](https://wavespeed.ai/blog/posts/hidream-o1-image-dev-pixel-unified-transformer/)
- AA Video T2V v2.0: Wan 3.0 (closed or API-only) leads at Elo 1157. The open-weights leader is MiniMax H3 (768p) at 1138, then LTX-2.5 Fast at 948 and LTX-2.5 Pro at 944. Wan2.7-260612 (closed) is at 1030 and LTX-2.3 Pro at 894. — [AA T2V v2.0](https://artificialanalysis.ai/video/leaderboard/text-to-video); [AA open-weights T2V board](https://artificialanalysis.ai/video/leaderboard/text-to-video/open-weights)
- In January 2026 AA said LTX-2 surpassed Wan 2.2 A14B in both T2V and I2V to become the open-weights video leader. — [AA on X](https://x.com/ArtificialAnlys/status/2012256702788153604)

### Inferences
- Image: open-weight models are now within about 0–70 Elo of the hosted frontier on AA (inferred; the absolute #1 closed Elo was not captured).
- Video: apart from MiniMax H3 (which is geo-restricted), the usable open models (LTX-2.5, HunyuanVideo 1.5, Wan 2.2) trail the closed frontier by about 200 Elo.

### Gaps
- The full AA leaderboards could not be fetched (egress blocked), so the top closed image models' Elo and the dates of the Elo snapshots are unconfirmed.
- No LMArena (text-to-image or video arena) open-weights rankings were retrieved.
- No AA Elo was found for HunyuanVideo 1.5, Z-Image, Qwen-Image-2512 or FLUX.2 klein.

## 5. Ecosystem support (diffusers, ComfyUI, quantization)

### Takeaway
ComfyUI is the de facto day-0 runtime. FLUX.2, HiDream-O1, HunyuanVideo 1.5, LTX-2.5 and MiniMax H3 all have native or official Comfy workflows. Quantized community builds are standard: GGUF (city96), FP8 and Nunchaku/SVDQuant 4-bit.

### Cited Findings
- FLUX.2 [dev] works in ComfyUI in GGUF (Q2–Q8, city96), FP8 and BF16. — [stablediffusiontutorials](https://www.stablediffusiontutorials.com/2025/11/flux-2.html)
- HiDream-O1-Image has a native ComfyUI workflow in the official docs. — [ComfyUI docs](https://docs.comfy.org/tutorials/image/hidream/hidream-o1)
- HunyuanVideo 1.5 has an official ComfyUI tutorial. — [ComfyUI docs](https://docs.comfy.org/ja/tutorials/video/hunyuan/hunyuan-video-1-5)
- LTX-2.5 and MiniMax H3 have pages on comfy.org. — [comfy.org LTX-2.5](https://comfy.org/ltx-2.5/); [comfy.org MiniMax H3](https://comfy.org/minimax-h3/)
- A Nunchaku 4-bit build of Qwen-Image-2512 exists on Hugging Face. — [QuantFunc HF](https://huggingface.co/QuantFunc/Nunchaku-Qwen-Image-2512)
- InfiniteTalk has community ComfyUI workflows (e.g. RunComfy). — [RunComfy](https://www.runcomfy.com/comfyui-workflows/comfyui-infinitetalk-workflow-audio-portrait-to-lip-synced-video)
- A Docker Hub image exists for Cosmos3-Super (ai/cosmos3-super). — [Docker Hub](https://hub.docker.com/r/ai/cosmos3-super)

### Inferences
- Models whose architecture breaks from VAE + DiT (HiDream-O1 pixel-native, Cosmos3 MoT, HunyuanImage 3.0 AR-MoE) tend to get ComfyUI support first and diffusers support later, or rely on the vendor's own inference code (inferred from the pattern; not verified per model).

### Gaps
- Diffusers pipeline support was not verified per model this session. Background: diffusers has pipelines for FLUX.1/FLUX.2, SD3.5, SDXL, Qwen-Image, HiDream-I1, Sana, Lumina2, Chroma, Wan 2.1/2.2, HunyuanVideo, LTX-Video, Mochi and CogVideoX. Status for HiDream-O1, Z-Image, LTX-2.5, MiniMax H3 and Cosmos3 is unknown.
- The LoRA training ecosystem (ai-toolkit, musubi-tuner, kohya) was covered only in passing: [RunComfy FLUX.2 LoRA trainer](https://www.runcomfy.com/trainer/ai-toolkit/flux-2-dev-lora-training).
