# AI Image & Video Generation: Core Techniques, Open-Source Tooling, and Evaluation (as of October 5, 2026)

Notes on sourcing: Live web research was done on 2026-10-05. Foundational technique claims cite the original arXiv papers (canonical references). Claims about 2026 releases come from GitHub repos, release notes and press, and are dated. Several 2026 model names (e.g., Ideogram 4, Krea 2, MiniMax-H3, Gemini Omni Flash, LTX-2.5) came up in only one or two sources during this pass and were not checked further. Treat them as "reported."

## 1. Core techniques (latent diffusion, flow matching, DiT/MMDiT, video VAEs, AR/unified models, distillation, CFG, quality vs. speed)

### Takeaway
The default 2026 recipe is a **flow-matching (rectified-flow) transformer (DiT/MMDiT or single-stream DiT) trained in the latent space of a VAE** (a spatiotemporal 3D VAE for video), with an LLM or VLM as the text encoder. Few-step distillation (DMD-style or consistency-style, usually shipped as "turbo/lightning/distilled" checkpoints or LoRAs) is how teams get production speed. Video is moving toward **causal or autoregressive, few-step "forcing" students** for real-time and streaming use, and toward **joint audio-video generation**.

### Cited Findings
**Latent diffusion & flow matching**
- Latent diffusion runs the diffusion process in the compressed latent space of a pretrained autoencoder, which sharply cuts compute compared with pixel-space diffusion. This is the basis of the Stable Diffusion lineage. — [Rombach et al., LDM](https://arxiv.org/abs/2112.10752)
- Flow matching / rectified flow trains a velocity field along (near-)straight paths from noise to data. That gives simpler training and fewer sampling steps than DDPM-style noise schedules. — [Lipman et al., Flow Matching](https://arxiv.org/abs/2210.02747); [Liu et al., Rectified Flow](https://arxiv.org/abs/2209.03003)
- SD3 brought rectified-flow training together with the **MMDiT** architecture: separate weights for text and image token streams, joined by attention. That design was carried into FLUX and later models. — [Esser et al., SD3](https://arxiv.org/abs/2403.03206)
- DiT replaced the U-Net with a transformer over latent patches and showed quality scaling with compute. — [Peebles & Xie, DiT](https://arxiv.org/abs/2212.09748)
- 2026 pipelines added to diffusers show the architecture convergence:
  - "Ideogram 4 (flow-matching text-to-image)", "Krea 2 (single-stream MMDiT with Qwen3-VL encoding)" and "PRX Pixel (pixel-space generation)" were added in diffusers v0.39. — [diffusers releases](https://github.com/huggingface/diffusers/releases); [releasebot summary](https://releasebot.io/updates/huggingface/diffusers)
  - Earlier 2025–26 additions included FLUX.2, FLUX.2 Klein, Z-Image, Qwen-Image Edit Plus/Layered, Bria FIBO, and video models LTX-2, Sana-Video, Kandinsky 5, HunyuanVideo 1.5 and Wan Animate. — [diffusers releases](https://github.com/huggingface/diffusers/releases)

**Video VAEs & spatiotemporal attention**
- Modern open video models (Wan, HunyuanVideo) use a causal 3D VAE that compresses both space and time, plus a DiT running full 3D (spatiotemporal) attention over video tokens. — [Wan tech report](https://arxiv.org/abs/2503.20314); [HunyuanVideo](https://arxiv.org/abs/2412.03603)
- LTX-2 (open-sourced 2026-01-06) is a joint audio+video model: 14B video plus 5B audio parameters, synchronized sound, up to 20 s, "native 4K" at 50 fps. It ships with a distilled variant and NVFP8 quantization (about 30% smaller, up to 2x faster). License: free for academic use and for companies under $10M ARR. — [LTX blog](https://ltx.io/blog/ltx-2-is-now-open-source); [OpenSourceForU](https://www.opensourceforu.com/2026/01/ltx-2-from-lightricks-delivers-native-4k-audio-video-with-fully-open-weights/)
- diffusers v0.40 reportedly adds LTX-2.5, MiniMax-H3 and Wan-Animate-2 pipelines, plus tensor parallelism. — [diffusers releases](https://github.com/huggingface/diffusers/releases)

**Autoregressive / unified multimodal generation**
- BAGEL is an open unified understanding+generation model built as a Mixture-of-Transformers (~14B total, 7B active). Text is generated autoregressively and images in parallel (diffusion/flow). It was pretrained on interleaved text-image-video-web data. — [BAGEL paper](https://arxiv.org/abs/2505.14683)
- Emu3.5 uses pure next-token prediction over more than 10T interleaved vision-language tokens. Its "Discrete Diffusion Adaptation (DiDA)" turns token-by-token decoding into bidirectional parallel prediction, about 20x faster per image. — [Emu3.5](https://arxiv.org/abs/2510.26583)
- diffusers v0.39 added "JoyAI-Image-Edit (unified multimodal editing)" and Cosmos 3, a "unified world foundation model". — [releasebot](https://releasebot.io/updates/huggingface/diffusers)

**Step distillation**
- LCM (Latent Consistency Models) distills to roughly 1–4 steps. — [LCM](https://arxiv.org/abs/2310.04378)
- Adversarial Diffusion Distillation powers SDXL-Turbo (1–4 steps). — [ADD](https://arxiv.org/abs/2311.17042)
- DMD/DMD2 (Distribution Matching Distillation) match the student's output distribution to the teacher's using score functions. — [DMD](https://arxiv.org/abs/2311.18828); [DMD2](https://arxiv.org/abs/2405.14867)
- Self Forcing does on-policy distillation with self-generated rollouts under the DMD principle. It distills a bidirectional teacher into a block-by-block causal student, which reduces exposure bias and enables real-time generation. — [Causal Forcing paper, background section](https://arxiv.org/html/2602.02214v2); [Self Forcing](https://arxiv.org/abs/2506.08009); [CausVid](https://arxiv.org/abs/2412.07772)
- Causal Forcing (ICML 2026) uses an autoregressive teacher for ODE initialization to close the gap between bidirectional and causal attention. It reports +19.3% Dynamic Degree, +8.7% VisionReward and +16.7% Instruction Following over Self Forcing. — [ICML 2026 poster](https://icml.cc/virtual/2026/poster/65646); [arXiv 2602.02214](https://arxiv.org/html/2602.02214v2)
- LiveTalk applies improved on-policy distillation to real-time multimodal interactive video. — [LiveTalk](https://arxiv.org/pdf/2512.23576)
- LightX2V step distillation cuts Wan-class video models from 40–50 steps to 4. — [LightX2V docs](https://lightx2v-en.readthedocs.io/en/latest/_sources/method_tutorials/step_distill.md)
- diffusers v0.39 added "AnyFlow (any-step video diffusion)". — [releasebot](https://releasebot.io/updates/huggingface/diffusers)

**CFG**
- Classifier-free guidance mixes conditional and unconditional predictions. That doubles the compute per step, and high scales oversaturate images. — [Ho & Salimans, CFG](https://arxiv.org/abs/2207.12598)
- A 2026 paper argues that T2I benchmark comparisons are skewed by guidance-scale choices ("Guidance Matters"). — [arXiv 2602.22570](https://arxiv.org/pdf/2602.22570)

### Inferences
- **Quality vs. speed trade-offs:**
  - Full models at 20–50 steps with CFG give the best quality and diversity.
  - Distilled 4–8-step models are 5–10x+ faster but lose some diversity, sometimes prompt adherence, and usually negative-prompt/CFG control, because distilled models typically run at CFG = 1.
  - Caching (Section 3) gives about 2x more with small quality loss.
  - Teams should prototype on full models, then ship distilled models plus caching plus quantization.
- Being able to train the text encoder or VLM is now a major quality lever, since newer models use Qwen-VL-class encoders instead of CLIP/T5.

### Gaps
- No authoritative 2026 survey covering all of these architectures was found. Specs of reported 2026 closed models (Ideogram 4, MiniMax-H3, Gemini Omni Flash) were not verified from primary sources.

## 2. Control & customization (LoRA, IP-Adapter, ControlNet, editing, in/outpainting, upscaling, interpolation, background removal, identity, motion, first/last frame, extension, lip-sync, audio)

### Takeaway
LoRA remains the universal customization unit for both image and video. In 2026, instruction or reference-based editing models (the FLUX Kontext / Qwen-Image-Edit / FLUX.2 class) have largely replaced the IP-Adapter + ControlNet stacks used for many tasks. For video post-production, the open stack is:
- SeedVR2 for upscaling (an open alternative to Topaz)
- RIFE/FILM for interpolation
- InfiniteTalk/LatentSync for lip-sync
- HunyuanVideo-Foley/MMAudio for audio, or a joint audio-video model like LTX-2

### Cited Findings
**Adapters and editing**
- LoRA trains low-rank update matrices and keeps the base weights frozen. — [LoRA](https://arxiv.org/abs/2106.09685)
- IP-Adapter is a decoupled cross-attention image-prompt adapter, about 22M parameters, used for style or subject reference. — [IP-Adapter](https://arxiv.org/abs/2308.06721)
- ControlNet adds spatial conditioning (depth, pose, canny, etc.) through a trainable copy of the encoder joined by zero-initialized convolutions. — [ControlNet](https://arxiv.org/abs/2302.05543)
- FLUX.1 Kontext does in-context image editing and generation with text plus reference images in one flow model. — [Kontext](https://arxiv.org/abs/2506.15742)
- Nunchaku ships 4-bit Qwen-Image-Edit, Qwen-Image-Edit-2509 and "lightning" (distilled) variants, which shows that edit models are now mainstream workloads. — [Nunchaku GitHub](https://github.com/nunchaku-ai/nunchaku)
- diffusers added FLUX.2 inpaint support in v0.38, plus Qwen-Image Layered and Z-Image Omni. — [diffusers releases](https://github.com/huggingface/diffusers/releases)

**Video LoRA**
- musubi-tuner trains LoRAs for HunyuanVideo/1.5, Wan 2.1/2.2, FramePack, FLUX.1 Kontext, FLUX.2, Qwen-Image, Z-Image, HiDream-O1, Kandinsky 5, MiniMax-H3, Ideogram 4 and Krea 2.
  - Features: fp8 weights, block swap, multi-GPU.
  - Last updates: 2026-09-27 (dependency bump, PyTorch 2.6+) and MiniMax-H3 support on 2026-09-16.
  — [musubi-tuner](https://github.com/kohya-ss/musubi-tuner)
- AI Toolkit added Wan 2.2 I2V LoRA training to its UI. Diffusion-pipe's Wan 2.2 support was described as "in flux", with "rough edges". — [HF forum notes](https://huggingface.co/datasets/John6666/forum1/blob/e2772d17729a88bbc2d7379c6220c46b85ef5a31/wan22_lora_training.md)

**Upscaling**
- SeedVR2 (ByteDance Seed) is a one-step diffusion video/image restoration model:
  - 3B and 7B sizes, Apache-2.0, plus "Sharp" 7B variants.
  - Native ComfyUI support.
  - Positioned as a free Topaz alternative that adds detail without "reinventing the subject the way SUPIR does".
  — [ComfyUI docs](https://docs.comfy.org/tutorials/utility/seedvr2); [InstaSD](https://www.instasd.com/post/seedvr2-video-upscale-comfyui-topaz-alternative); [SeedVR2 paper](https://arxiv.org/abs/2506.05301)
- SUPIR is an SDXL-based generative image restoration and upscaling model. — [SUPIR](https://arxiv.org/abs/2401.13627)

**Frame interpolation**
- RIFE (real-time intermediate flow estimation) and FILM (large-motion interpolation) are the standard open interpolators. — [RIFE](https://arxiv.org/abs/2011.06294); [FILM](https://arxiv.org/abs/2202.04901)

**Lip-sync**
- LatentSync (ByteDance, open-sourced Dec 2024) uses audio-conditioned latent diffusion to regenerate the mouth region. It has about 5.9k stars, and one 2026 comparison calls LatentSync 1.6 "still the local model to beat" for dubbing short videos. — [sync.so](https://sync.so/blog/what-is-latentsync); [instavar comparison](https://instavar.com/research/ai-video/open-source-lip-sync-models)
- InfiniteTalk does audio-driven video-to-video lip-sync and talking video, typically run in ComfyUI. — [Floyo workflow](https://www.floyo.ai/workflows/infinitetalk-lip-sync-any-video-to-a-vd67vzp503k1)

**Audio for video**
- HunyuanVideo-Foley (open-sourced 2025-08-28) does text+video-to-audio:
  - multimodal DiT with Synchformer-based sync
  - 48 kHz output
  - 100k-hour curated dataset
  — [GitHub](https://github.com/Tencent-Hunyuan/HunyuanVideo-Foley)
- MMAudio is an earlier widely used video-to-audio model. — [MMAudio](https://arxiv.org/abs/2412.15322)
- LTX-2 and MiniMax-H3 generate audio jointly with video. — [LTX blog](https://ltx.io/blog/ltx-2-is-now-open-source); [musubi-tuner](https://github.com/kohya-ss/musubi-tuner)

### Inferences
- **Identity preservation:** typical practice is a subject LoRA, or reference-image editing models, plus first-frame I2V conditioning for video.
- **Camera and motion control:** handled through model-native I2V, first/last-frame conditioning (Wan-family workflows), motion/animate models (Wan Animate / Animate-2) or control LoRAs.
- **Background removal:** usually a segmentation model (e.g., BiRefNet/RMBG-class) run as a ComfyUI node.

### Gaps
- No primary sources were fetched in this pass for:
  - background-removal models (BiRefNet/RMBG-2.0 versions and licenses)
  - camera-control methods (e.g., ReCamMaster, Uni3C)
  - Wan 2.2 first/last-frame (FLF2V) and video-extension specifics
  - Topaz's current product lineup
- How Wan 2.5/2.6 compares with Wan 2.2 on open-weight status was not confirmed.

## 3. Tooling (inference frontends, training tools, optimization) and maintenance status

### Takeaway
**ComfyUI is the de facto open workflow engine**, and Comfy Cloud is now out of beta with an API. diffusers is the library baseline, with "Modular Diffusers" now stable. Forge's original repo is dormant; use Forge Neo/Classic. musubi-tuner and ai-toolkit are the most active video/image LoRA trainers. For speed: Nunchaku (4-bit), SageAttention, MagCache/TeaCache and distilled LoRAs.

### Cited Findings
**Frontends and libraries**
- **Comfy Cloud** has graduated from beta.
  - About 90% of local custom nodes work without setup.
  - Billing covers active execution only.
  - The Cloud API submits workflows on Comfy-managed GPUs and needs a subscription plus Comfy Credits.
  - ComfyUI v0.26.0 was released in 2026.
  — [Comfy Cloud announcement](https://comfyui.org/zh/comfy-cloud-is-out-of-beta-and-its); [Comfy APIs overview](https://support.comfy.org/articles/1674221690-comfy-apis-an-overview); [docs.comfy.org](https://docs.comfy.org/development/overview)
- **diffusers:**
  - Modular Diffusers launched in v0.37 (March 5, 2026). It composes pipelines from reusable blocks and was promoted out of experimental in v0.40.
  - v0.37 added MagCache and TaylorSeer caching.
  - v0.36 added kernels-based attention backends (FlashAttention 2/3, SAGE).
  - v0.38 (May 1, 2026) added a FlashAttention 4 backend.
  - v0.40 added SDNQ and "Nunchaku Lite" quantization backends and tensor parallelism.
  - Date caveat: the GitHub page summary returned 2024 dates, but other trackers date v0.38 to 2026-05-01. The 2026 dates are correct.
  — [diffusers releases](https://github.com/huggingface/diffusers/releases); [releasebot](https://releasebot.io/updates/huggingface/diffusers)
- **Forge:** the original lllyasviel/stable-diffusion-webui-forge has had only minor changes since late 2024. **Forge Neo** (Haoming02, v2.28.1) is the maintained continuation, and Forge Classic is also active. — [Forge discussion #2780](https://github.com/lllyasviel/stable-diffusion-webui-forge/discussions/2780); [Grokipedia (secondary)](https://grokipedia.com/page/Stable_Diffusion_WebUI_Forge_Neo)
- **SwarmUI:** independent under mcmonkeyprojects since Stability AI stopped maintaining it in June 2024. v0.9.8-Beta in Feb 2026, with a ComfyUI backend. — [StableSwarmUI repo](https://github.com/Stability-AI/StableSwarmUI); [offlinecreator](https://offlinecreator.com/compare/swarmui-vs-forge-2026)

**Training tools**
- **musubi-tuner (kohya-ss)** is very active as of Sept 2026, with the broadest video and new-image architecture coverage. — [musubi-tuner](https://github.com/kohya-ss/musubi-tuner)
- A third-party 1-click GUI wraps musubi for Krea 2, Ideogram 4, FLUX.2/Klein, Z-Image, LTX 2.3 and MiniMax-H3. — [SECourses](https://github.com/FurkanGozukara/SECourses_Musubi_Trainer)
- **AI Toolkit (ostris):** CLI plus UI with Wan I2V training; hosted trainers (e.g., fal Wan 2.2 trainer) also exist. — [HF forum notes](https://huggingface.co/datasets/John6666/forum1/blob/e2772d17729a88bbc2d7379c6220c46b85ef5a31/wan22_lora_training.md); [fal](https://fal.ai/models/fal-ai/wan-22-trainer/t2v-a14b/playground)

**Optimization**
- **Nunchaku / SVDQuant** is a 4-bit W4A4 engine. Low-rank branches absorb outliers.
  - Claims: 3.6x memory reduction vs. BF16 on FLUX.1-dev; 3x faster than an NF4 W4A16 baseline; 3.1x faster than BF16 on an RTX 5090 with NVFP4.
  - v1.2.0 (Jan 12, 2026) added native LoRA support and INT4 on 20-series GPUs.
  - Supported models: FLUX.1 family, Qwen-Image/Edit, Z-Image-Turbo, SANA, PixArt-Σ. No Wan video support was listed.
  - Integrated into diffusers and SGLang.
  — [Nunchaku GitHub](https://github.com/nunchaku-ai/nunchaku); [HF blog](https://huggingface.co/blog/nunchaku-diffusers); [SGLang docs](https://docs.sglang.io/diffusion/quantization.html); [SVDQuant paper](https://arxiv.org/abs/2411.05007)
- **MagCache:** 2.10–2.68x speedups on Open-Sora, CogVideoX, Wan 2.1 and HunyuanVideo, with one-sample calibration. Reported to beat TeaCache on both speed and quality. — [MagCache](https://arxiv.org/abs/2506.09045)
- **TeaCache:** timestep-embedding-aware caching. — [TeaCache](https://arxiv.org/abs/2411.19108)
- **FIS-DiT (2026):** training-free sparsity that targets the few-step regime, where caching gains shrink. — [FIS-DiT](https://arxiv.org/pdf/2605.11869)
- **SageAttention:** quantized (INT8/FP8) attention kernel and drop-in replacement. — [SageAttention](https://arxiv.org/abs/2410.02367)
- **xDiT:** parallel DiT inference (PipeFusion, sequence parallelism) across GPUs. — [xDiT repo](https://github.com/xdit-project/xDiT); [PipeFusion](https://arxiv.org/abs/2405.14430)
- **LightX2V:** 4-step distillation for video models. — [LightX2V](https://lightx2v-en.readthedocs.io/en/latest/_sources/method_tutorials/step_distill.md)
- A SeedVR2 "TensorRT Studio" exists for ComfyUI. — [InstaSD](https://www.instasd.com/post/seedvr2-video-upscale-comfyui-topaz-alternative)

### Inferences
- **Recommended starting stack:**
  - ComfyUI for exploration
  - diffusers (Modular) or ComfyUI-as-API / Comfy Cloud API for production
  - musubi-tuner or ai-toolkit for LoRAs
  - Nunchaku for image models it supports
  - SageAttention + MagCache + distilled LoRAs for video
- A1111 and original Forge should be treated as legacy.

### Gaps
- No 2026 status was checked for InvokeAI, kohya_ss GUI/sd-scripts, SimpleTuner, OneTrainer, diffusion-pipe, or TensorRT / torch.compile specifics. These were all active in 2025 per prior knowledge but are unverified here.

## 4. Evaluation (FID/FVD, CLIP score, GenEval, T2I-CompBench, DPG-Bench, HPS, ImageReward, VQAScore, VBench/VBench-2, arenas)

### Takeaway
Distribution metrics (FID/FVD) and CLIPScore are now mostly legacy or sanity checks. GenEval is saturated. In practice teams use:
- VLM-based alignment metrics (VQAScore, Soft-TIFA/GenEval 2, OneIG-Bench)
- learned preference models (HPSv3, ImageReward, VisionReward)
- VBench/VBench-2.0 for video
- blind pairwise human preference (Elo arenas such as Artificial Analysis) as the final arbiter

### Cited Findings
**Classic metrics and compositional benchmarks**
- FID compares Inception feature statistics. FVD is the video analogue using I3D features. — [FID](https://arxiv.org/abs/1706.08500); [FVD](https://arxiv.org/abs/1812.01717)
- CLIPScore is reference-free image-text similarity. — [CLIPScore](https://arxiv.org/abs/2104.08718)
- GenEval (object-focused compositional checks via detection) and T2I-CompBench (attribute binding, spatial, etc.) are standard compositional benchmarks. DPG-Bench tests dense, long prompts. — [GenEval](https://arxiv.org/abs/2310.11513); [T2I-CompBench](https://arxiv.org/abs/2307.06350); [ELLA/DPG-Bench](https://arxiv.org/abs/2403.05135)
- **GenEval is saturated:** a human study found Gemini 2.5 Flash Image scoring 96.7%. GenEval 2 plus the Soft-TIFA method were proposed to address "benchmark drift". — [GenEval 2](https://www.researchgate.net/publication/398851070_GenEval_2_Addressing_Benchmark_Drift_in_Text-to-Image_Evaluation)

**VLM-based and learned-preference metrics**
- VQAScore measures how likely a VQA model is to judge that the image depicts the prompt. It "significantly outperforms" CLIPScore. — [VQAScore](https://arxiv.org/abs/2404.01291)
- HPSv3 is built on Qwen2-VL, trained on HPDv3, and improves on HPSv2. ImageReward is an earlier learned preference model. — [search summary of HPSv3](https://huggingface.co/papers?q=GenAI-Bench); [HPSv2](https://arxiv.org/abs/2306.09341); [ImageReward](https://arxiv.org/abs/2304.05977)
- Newer 2026 T2I benchmarks include OneIG-Bench, Qwen-Image-Bench ("from generation to creation") and WeGenBench. Using MLLMs as evaluators is an active research line. — [Qwen-Image-Bench](https://arxiv.org/pdf/2605.28091); [WeGenBench](https://arxiv.org/pdf/2606.20100); [MLLMs as T2I evaluators](https://arxiv.org/pdf/2505.00759)

**Video benchmarks and arenas**
- **VBench** breaks video quality into 16 dimensions. **VBench-2.0** targets "intrinsic faithfulness" across Human Fidelity, Controllability, Creativity, Physics and Commonsense, using VLM/LLM generalists plus specialist anomaly detectors, validated against human annotations. — [VBench](https://arxiv.org/abs/2311.17982); [VBench-2.0](https://arxiv.org/html/2503.21755v2)
- **Artificial Analysis arena** (blind-vote Elo):
  - April 2026 T2V: Seedance 2.0 720p at 1274, then SkyReels V4 (1245), Kling 3.0 1080p Pro (1241) and PixVerse V6 (1240).
  - August 2026 (T2V with audio): "Gemini Omni Flash" leads at 1245, three points over MiniMax-H3, with Seedance 2.0 third.
  - Standings move weekly.
  — [magichour](https://magichour.ai/model-leaderboard/text-to-video); [invideo / techsy summaries](https://techsy.io/en/blog/best-ai-video-models). These are secondary aggregators and were not verified against artificialanalysis.ai directly.

### Inferences
- **Practical eval loop for a new project:**
  1. A fixed internal prompt set that covers the product's use cases.
  2. Automatic VLM-judge or VQAScore alignment scoring plus a preference model (HPSv3/ImageReward) for regression tracking.
  3. VBench(-2) dimensions for video.
  4. Periodic blind pairwise human A/B tests.
- FID/FVD matter mainly for model-training research, not for product decisions.
- Hold CFG and steps fixed across compared models (per "Guidance Matters").

### Gaps
- No primary HPSv3 paper URL was fetched. The VBench leaderboard and the official LMArena / Artificial Analysis image-arena pages were not fetched directly.

## 5. Prompting practices & LLM prompt enhancement

### Takeaway
Because modern models use LLM/VLM text encoders and are trained on long, dense captions, the best results come from detailed natural-language prompts. Most first-party pipelines now run an **LLM prompt rewriter/extender** by default, and teams should do the same, keeping the original intent and logging the rewritten prompt.

### Cited Findings
- Wan ships a `prompt_extend.py` utility that expands user prompts with an LLM before generation. — [Wan prompt_extend.py](https://huggingface.co/spaces/dangthr/Wan-2.2-5B/raw/main/wan/utils/prompt_extend.py)
- Qwen Image 3.0 runs prompts through an LLM rewrite by default, unless it is disabled. — [Runware docs](https://runware.ai/docs/models/alibaba-qwen-image-3-0/guides/prompt-extension)
- ComfyUI-QwenPromptRewriter brings Qwen-Image's online rewriting into ComfyUI. — [RunComfy](https://www.runcomfy.com/comfyui-nodes/ComfyUI-QwenPromptRewriter)
- PromptEnhancer-32B (fine-tuned from Qwen2.5-VL-32B) rewrites T2I prompts with chain-of-thought (global → details → summary) while preserving intent. — [Featherless model card](https://featherless.ai/models/PromptEnhancer/PromptEnhancer-32B); [jimmysong.io](https://jimmysong.io/en/ai/promptenhancer)
- Hosted services offer small prompt enhancers (e.g., Llama 3.1 8B) for T2I/T2V. — [Runware](https://runware.ai/models/llama-3-1-8b-prompt-enhancer)

### Inferences
- **Prompt structure:** subject → action → setting → composition/camera → lighting/style. For video, add camera movement and temporal beats. Put any text to render in quotes. Negative prompts matter little for distilled, CFG = 1 models.
- Evaluate with both the raw and the enhanced prompt, because rewriters can inflate benchmark scores.

### Gaps
- No authoritative cross-model prompting guide for 2026 was found. Model-specific official prompt guides (e.g., FLUX.2, Wan, LTX-2) were not fetched.
