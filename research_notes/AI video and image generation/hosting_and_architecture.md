# Inference Hosting Providers & Product Architecture for an AI Image + Video Generation App (as of Oct 5, 2026)

> Method note: Direct fetches of provider pricing pages (fal.ai, modal.com, runpod.io, replicate.com, runware.ai) and several aggregator blogs were blocked by the research environment's egress proxy. Most figures below come from web-search snippets of provider docs/pages and of third-party pricing trackers (dated mid-to-late 2026). Treat every price as "verify on the provider page before committing"; items flagged **[UNCERTAIN]** have a single secondary source, look inconsistent, or may be stale.

## Q1. Hosted APIs / aggregators: models, per-image & per-second-video pricing, latency, SDKs

### Takeaway
For a new product, a hosted media API (fal.ai, Runware, Replicate, WaveSpeed; with Vertex/Bedrock/Azure as the enterprise options) is the cheapest and fastest route to production. FLUX-class images run from ~$0.0005 to $0.04 each, and video runs from ~$0.03 to $0.60 per output second depending on model, resolution and audio. LLM-first inference clouds (Fireworks, partly Together) are weak on video. Gateways (Vercel AI Gateway, OpenRouter, HF Inference Providers, Cloudflare AI Gateway) mostly pass provider prices through and are useful for routing and fallback. Important: OpenAI's Sora 2 API was switched off on Sept 24, 2026, so don't plan around Sora.

### Cited Findings

**fal.ai (media-specialist API + serverless GPU)**
- Video per-second prices on fal: Veo 3.1 from $0.10/s (Fast) and $0.20/s (Standard), $0.40/s at 4K without audio and $0.60/s with audio; Kling 3.0 Pro $0.112/s without audio and $0.168/s with audio; Kling 2.5 Turbo Pro $0.07/s; Wan 2.5 $0.05/s; Sora 2 Pro $0.50/s — [fal learn page](https://fal.ai/learn/tools/ai-image-to-video-generators), [teamday.ai Sep 2026 comparison](https://www.teamday.ai/blog/ai-api-pricing-comparison-2026), [devtk.ai](https://devtk.ai/en/blog/ai-video-generation-pricing-2026/) (snippet-level; Sora 2 Pro listing likely stale, see Sora shutdown below)
- FLUX.2-dev is about $0.012/image on fal — [Spheron fal alternatives (search snippet)](https://www.spheron.network/blog/fal-ai-alternatives/)
- Queue API: you submit to a persistent queue, then poll or get the result by **webhook**. Status can be streamed (`streamStatus()` over SSE in JS, `iter_events()` in Python) with runner logs (`logs: true` / `?logs=1`). Retries are automatic — [fal queue docs](https://fal.ai/docs/documentation/model-apis/inference/queue)
- fal serverless (custom models) scales from zero, with multi-layer caching to cut cold starts. Knobs are `min_concurrency` (keep warm), `max_concurrency` (cap spend) and `concurrency_buffer` (pre-warm) — [fal docs](https://docs.fal.ai/documentation)
- fal dedicated compute: H100 80GB "from $1.89/hr", H200 from $2.10/hr, A100 40GB from $0.99/hr — [fal compute pricing docs (search snippet)](https://fal.ai/docs/documentation/compute/pricing) **[UNCERTAIN: well below market and likely a committed/dedicated-instance floor; verify]**
- Vercel Marketplace has a native fal integration, plus `@ai-sdk/fal` for `generateImage` — [Vercel fal docs](https://vercel.com/docs/ai/fal)

**Replicate**
- FLUX Schnell costs $3.00 per 1,000 images (~$0.003/image). Veo 3 with audio costs $0.40 per output second, so a 9 s clip is $3.60 — [flexprice Replicate index](https://flexprice.io/pricing-index/replicate), [pricepertoken](https://pricepertoken.com/image)
- Some third parties claim direct APIs are "10–17x cheaper" than Replicate for some models — [TokenMix blog](https://tokenmix.ai/blog/replicate-alternative-cheaper) (vendor-biased claim)

**Runware (low-cost media API)**
- FLUX Schnell from $0.0006/image (optimized resolutions), FLUX Dev $0.0038, SDXL $0.0026 — [Puter FLUX pricing Sep 2026](https://developer.puter.com/tutorials/flux-api-pricing/), [Runware pricing](https://runware.ai/pricing)
- BFL **FLUX 3 Video** on Runware: T2V/I2V $0.17/s at 720p and $0.29/s at 1080p; video continuation $0.43/s (720p) and $0.54/s (1080p); "Draft" mode $0.04/s at 720p — [Runware FLUX 3 Video](https://runware.ai/flux-3-video)

**Together AI**
- FLUX.1 Krea [dev] costs $0.025/MP (~$0.026 per 1024² image). Veo 3.0 + audio costs $3.20 per 8 s video (= $0.40/s) — [digitalapplied 12-provider comparison](https://www.digitalapplied.com/blog/ai-image-generation-api-pricing-comparison-2026)

**Fireworks AI**
- FLUX.1 schnell $0.0014/image, FLUX.1 dev $0.014/image, FLUX.1 Kontext Pro $0.04/image. Fireworks has no video generation — [digitalapplied](https://www.digitalapplied.com/blog/ai-image-generation-api-pricing-comparison-2026), [WaveSpeed review of Fireworks](https://wavespeed.ai/blog/posts/fireworks-ai-review-2026/) (competitor source)

**DeepInfra**
- FLUX.1 Schnell $0.0005 per 1024² image (cheapest found), FLUX-2 Klein 9B $0.015/image, FLUX-2 Max $0.07/image — [DeepInfra FLUX](https://deepinfra.com/flux), [pricepertoken FLUX](https://pricepertoken.com/flux-pricing)

**WaveSpeed AI**
- A media acceleration platform with one API covering 600–700+ image/video/audio models. Third-party listings show Veo 3.1 at $0.40/s and Sora 2 at $0.10/s — [ai-cmo review](https://ai-cmo.net/tools/wavespeed-ai), [costbench WaveSpeed](https://costbench.com/software/ai-media-apis/wavespeed/) **[UNCERTAIN: the Sora 2 listing is likely stale after the API shutdown]**

**Hugging Face Inference Providers**
- Routes image and video generation through fal, Replicate, WaveSpeed, HF Inference, Nscale and Together on a unified API, with automatic or explicit provider selection. "No extra markup on provider rates", plus a free tier and PRO credits — [HF Inference Providers docs](https://huggingface.co/docs/inference-providers/index), [HF blog](https://huggingface.co/blog/inference-providers)

**Cloudflare Workers AI**
- Priced in Neurons at $0.011 per 1,000 Neurons, with 10,000 Neurons/day free — [Vercel gateway comparison](https://vercel.com/i/vercel-ai-gateway-vs-cloudflare-ai-gateway)
- FLUX-1-schnell: $0.0000528 per 512² tile plus $0.0001056 per step. FLUX-2-Dev: $0.00021 per input tile per step and $0.00041 per output tile per step. FLUX-2-Klein-4B: $0.000287 per output tile. FLUX-2-Klein-9B: $0.015 for the first MP, $0.002 per extra MP. Leonardo Lucid-Origin: $0.006996 per tile. Leonardo Phoenix 1.0: $0.00583 per tile. Workers AI has no video models listed — [Cloudflare Workers AI pricing](https://developers.cloudflare.com/workers-ai/platform/pricing/), [FLUX-2 Klein changelog Jan 2026](https://developers.cloudflare.com/changelog/post/2026-01-15-flux-2-klein-4b-workers-ai/index.md)

**Vercel AI Gateway / AI SDK**
- The AI Gateway passes provider pricing through at cost with zero markup, supports BYOK at no fee, and has a $5/month free credit — [Vercel: AI Gateway vs OpenRouter](https://vercel.com/i/vercel-ai-gateway-vs-openrouter)
- The AI SDK has unified `generateImage` and video-generation APIs. The image templates default to Replicate, Vertex, OpenAI and Fireworks, and `@ai-sdk/fal` is available — [Vercel AI SDK image generator template](https://vercel.com/templates/next.js/ai-sdk-image-generator), [Vercel fal docs](https://vercel.com/docs/ai/fal)

**OpenRouter**
- 400+ models across 70+ providers, with documentation for image and video generation. It charges provider list prices plus a 5.5% fee on credit purchases ($0.80 minimum). It raised a $113M Series B in May 2026. It lists Veo 3.1 — [Vercel best AI gateways](https://vercel.com/i/best-ai-gateways), [OpenRouter Veo 3.1](https://openrouter.ai/google/veo-3.1)

**Google Vertex AI (now partly branded "Gemini Enterprise Agent Platform")**
- Veo 3.1: video+audio $0.40/s at 720p/1080p and $0.60/s at 4K; video-only $0.20/s (720p/1080p) and $0.40/s (4K); Veo 3.1 Lite (no audio) from $0.03/s — [Google Cloud pricing](https://cloud.google.com/gemini-enterprise-agent-platform/generative-ai/pricing), [costgoat Veo Oct 2026](https://costgoat.com/pricing/google-veo)
- Imagen 4 Fast $0.02/image, Imagen 4 $0.04, Imagen 4 Ultra $0.06 — same sources

**AWS Bedrock**
- Nova Reel costs about $0.08/s for 6 s 720p video (other sources say $0.05–0.10/s) — [Spheron Bedrock 2026](https://www.spheron.network/blog/aws-bedrock-pricing-2026-managed-api-cost/), [CloudRoute Nova pricing](https://www.cloudroutehq.com/aws-ai/amazon-nova-pricing) **[UNCERTAIN: exact current rate]**

**Azure AI Foundry**
- Sora 2 launched on Foundry (public preview) at $0.10 per second of video (Standard Global) — [Azure blog](https://azure.microsoft.com/en-us/blog/sora-2-now-available-in-azure-ai-foundry/), [aibase](https://www.aibase.com/news/22055). A Microsoft Q&A thread reports Sora 2 missing from the Foundry model list — [MS Q&A](https://learn.microsoft.com/en-us/answers/questions/5774829/sora-2-not-available-in-azure-ai-foundry-model-list) **[UNCERTAIN: Azure availability after OpenAI's shutdown]**

**Sora 2 shutdown (critical)**
- OpenAI announced Sora's discontinuation on Mar 24, 2026. The consumer app closed Apr 26, 2026, and the Sora 2 API (sora-2, sora-2-pro and their dated aliases) was switched off on **Sept 24, 2026** with no replacement model — [OpenAI deprecations](https://developers.openai.com/docs/deprecations), [Zilliz timeline](https://zilliz.com/ai-faq/what-is-the-sora-shutdown-timeline), [invideo Aug 2026](https://invideo.io/blog/sora-ai-video-generator/)

**First-party video pricing for reference**
- Seedance 2.5 at 720p costs about $0.23/s (~$1.15 per 5 s clip). ByteDance Volcano Engine examples: ¥3.36 per 5 s 480p video and ¥7.56 per 5 s 720p video — [MindStudio Seedance 2.5](https://www.mindstudio.ai/blog/seedance-2-5-review-pricing/)
- Kling consumer plans cost $0–$180/month, with Kling 3.0 billed at 6–12 credits per second (30 for 4K). Kling raised prices in Apr 2026 — [costbench Kling change](https://costbench.com/changelog/kling-price-increase-2026-04/), [eesel Kling](https://eesel.ai/blog/kling-ai-pricing)

### Inferences
- Rough price tiers for video per output second (720p–1080p): budget/open models (Wan 2.5, Veo 3.1 Lite, FLUX 3 Video Draft) cost $0.03–0.05/s; mid tier (Kling 2.5 Turbo, Nova Reel, Veo Fast, Kling 3.0 Pro) costs $0.07–0.17/s; premium with audio (Veo 3.1, Seedance 2.5, FLUX 3 Video 1080p) costs $0.20–0.40/s; 4K plus audio reaches up to $0.60/s. A typical 5 s clip therefore costs **$0.15 to $3.00**. Credit pricing must be per-model and per-second.
- Images: commodity FLUX schnell/Klein costs $0.0005–0.003, FLUX dev class $0.004–0.026, and premium models (Imagen Ultra, Kontext Pro, FLUX-2 Max) cost $0.04–0.07. The same model can differ by 5–10x between providers, which is a strong argument for a routing layer.
- The same Veo 3/3.1 price ($0.40/s with audio) appears at Google, Replicate, Together and WaveSpeed. Resellers seem to pass first-party closed-model prices through, so aggregators compete on open models and developer experience rather than on Veo/Kling prices.
- Fireworks and Cloudflare Workers AI are image-only. Use them for cheap image paths, not for video.

### Gaps
- No measured p50/p95 latency numbers per provider were found. Latency claims are vendor marketing. Plan an in-house benchmark.
- Exact current per-image prices on fal for FLUX.1 schnell/dev, and WaveSpeed's own per-model rate card, could not be fetched.
- I could not confirm whether Azure still serves Sora 2 after Sept 24, 2026.
- Bedrock's current image models (Nova Canvas, Stability) and their per-image prices were not verified.

## Q2. Self-hosting GPU clouds: hourly prices, cold starts, cost per image and per video-second

### Takeaway
Serverless H100 costs about $3.35–4.80/hr (Modal $3.95, RunPod Flex $4.18–4.79). Dedicated or marketplace H100 costs $1.49–3.99/hr (Vast, RunPod pods, Lambda), and hyperscaler-grade H100 costs $4.25–6.50/hr (CoreWeave, Baseten). At these rates, self-hosting unoptimized Wan 2.2 14B (~11 min per 5 s 720p clip on H100) costs about $0.07–0.15 per video-second. That is more than fal's $0.05/s for Wan 2.5. Self-hosting only wins with high utilization, step-distillation or quantization, or custom models/LoRAs that APIs don't serve.

### Cited Findings
- **Modal** (per second, June 2026): B200 $0.001736/s (~$6.25/hr), H200 $0.001261/s (~$4.54/hr), H100 $0.001097/s (~$3.95/hr), L40S $0.000542/s (~$1.95/hr) — [Spheron Modal pricing](https://www.spheron.network/blog/modal-gpu-pricing-2026-per-second-billing/), [morphllm Baseten vs Modal](https://www.morphllm.com/comparisons/baseten-vs-modal)
- **RunPod Serverless**: H100 Flex quoted at $4.79/hr (other sources say ~$4.55/hr). H100 PRO Flex is $0.00116/s ($4.18/hr) and Active is $0.00093/s ($3.35/hr), about 20% cheaper. Active workers pay off above ~25% monthly utilization (~180 h). RTX 4090 serverless costs ~$1.10/hr. RTX 5090 costs $1.58/hr serverless and $0.99/hr as a pod. H100 pods start from $2.89/hr (Oct 2026) — [RunPod H100 guide Oct 2026](https://www.runpod.io/articles/guides/nvidia-h100), [Aliteq serverless pricing](https://aliteq.com/runpod-serverless-pricing-explained-2026), [Spheron RunPod](https://www.spheron.network/blog/runpod-h100-pricing-2026/) **[UNCERTAIN: sources disagree on H100 Flex]**
- **Baseten** (dedicated, billed per minute): H100 $0.10833/min (~$6.50/hr), B200 $0.16633/min (~$9.98/hr). No L40S. The rate includes autoscaling and serving tooling — [morphllm Baseten pricing](https://www.morphllm.com/baseten-pricing)
- **Lambda**: H100 $3.29–3.99/GPU-hr; B200 $6.69 (8x) or $6.99 (1x) — [CloudZero H100 2026](https://www.cloudzero.com/blog/h100-gpu-cost/), [Spheron GPU comparison](https://www.spheron.network/blog/gpu-cloud-pricing-comparison-2026/)
- **CoreWeave**: H100 $4.25 (PCIe) to $6.16/GPU-hr (8x HGX), H200 $6.31/GPU-hr, B200 $8.60/GPU-hr ($68.80/hr per 8-GPU node) — [Spheron CoreWeave](https://www.spheron.network/blog/coreweave-h100-h200-pricing-2026/), [gmicloud H200](https://www.gmicloud.ai/en/blog/h200-gpu-provider-pricing)
- **Vast.ai** (marketplace): H100 $1.49–2.27/GPU-hr (verified hosts), H200 ~$4.32 (range $3.59–5.09) — [Spheron Vast](https://www.spheron.network/blog/vastai-pricing-2026/), [Thunder Compute H200 Oct 2026](https://www.thundercompute.com/blog/nvidia-h200-pricing)
- B200 market range is $3.99–5.50/hr on dedicated clouds. B200 delivers about 2.5x H100 training performance, so it is often cheaper per result — [Spheron B200](https://www.spheron.network/blog/nvidia-b200-cloud-pricing-2026/)
- **Wan 2.2 14B**: about 10–12 min per 5 s 720p clip on an H100 PCIe (FP8), or about $0.34–0.40 per clip at marketplace rates — [Spheron Wan GPU guide](https://www.spheron.network/blog/ai-video-generation-gpu-guide/), [Spheron Wan 2.1/2.2 deploy](https://www.spheron.network/blog/deploy-wan-2-1-ai-video-generation-gpu-setup/). Wan 2.2 can do 720p on a single 24 GB GPU — [LocalAIMaster](https://localaimaster.com/blog/wan-video-generation-guide)
- **FLUX.1 dev**: 1024² in 10–20 s on H100 (secondary snippet) — [Spheron fal alternatives](https://www.spheron.network/blog/fal-ai-alternatives/) **[UNCERTAIN: depends heavily on step count and on compile/FP8 optimizations]**

### Cost math (from the cited rates; assumes 100% GPU utilization, no cold-start overhead)
- **FLUX dev on Modal H100 ($3.95/hr = $0.0011/s)**: 10–20 s per image → **$0.011–0.022/image**. On a Vast H100 at ~$2/hr → $0.0056–0.011. API comparison: Runware FLUX dev $0.0038, Fireworks $0.014, fal FLUX.2-dev ~$0.012. Unoptimized self-hosting does not beat cheap APIs. To win you need 2–4x speedups (torch.compile/TensorRT, FP8, fewer steps) and high utilization.
- **FLUX schnell (4 steps)**: about 1/7 of dev's time, roughly **$0.002–0.003/image** on Modal H100, versus $0.0005–0.0014 on DeepInfra/Runware/Fireworks APIs.
- **Wan 2.2 14B on H100, 11 min per 5 s 720p clip**: Modal ($3.95/hr) ≈ $0.72 per clip = **$0.145/video-s**. RunPod Active ($3.35/hr) ≈ $0.61 = $0.12/s. Vast (~$2/hr) ≈ $0.37 = $0.073/s. Compare fal Wan 2.5 at $0.05/s ($0.25 per 5 s clip) and Veo 3.1 Lite at $0.03/s.
- **Break-even intuition**: serverless per-second billing means idle time costs nothing, but you pay for every cold start. Dedicated GPUs (Lambda, Vast, RunPod pods) only beat serverless above roughly 25–40% sustained utilization (consistent with RunPod's ~25% Active-worker breakpoint).

### Inferences
- Self-hosting makes sense for: (a) fine-tuned/LoRA models or custom ComfyUI pipelines no API serves; (b) steady high volume on one open model where you can run distilled/quantized variants (e.g., few-step Wan LoRAs, FP8) on B200/H100 at high utilization; (c) data residency.
- Start with serverless (Modal for Python-native developer experience, RunPod for the cheapest serverless and consumer GPUs, fal serverless if already on fal). Move steady baseline load to reserved H100/B200 later.
- L40S (~$1.95/hr on Modal) and RTX 4090/5090 (~$1.10–1.58/hr serverless) are cost-effective for FLUX-class images and Wan 5B/LTX. Reserve H100/H200/B200 for 14B+ video models.

### Gaps
- No primary benchmarks of cold-start times were found (Modal, RunPod FlashBoot, Baseten, fal). Vendor claims only. Cold starts for 14B video models (30–60 GB weights) are likely tens of seconds to minutes, but no citable figure was found.
- Lambda/Vast prices for L40S and A100 in Oct 2026, and Modal A100/L4/T4 per-second rates, were not retrieved.
- B200 per-clip throughput for Wan-class models was not found.

## Q3. Serving frameworks: ComfyUI-as-API, diffusers + FastAPI, Triton/TensorRT, BentoML, Cog

### Takeaway
ComfyUI is excellent for prototyping complex graphs (LoRA stacks, ControlNet, upscalers) and can be wrapped as an API (comfy-pack/BentoML, Baseten, Beam, RunPod workers). For high-volume single-model paths, diffusers with torch.compile, served behind a queue (FastAPI or BentoML), is reported to give 2–3x more throughput. Cog is Replicate's packaging format and Truss is Baseten's.

### Cited Findings
- ComfyUI ships a full HTTP and WebSocket server that can serve as an image/video backend. BentoML's **comfy-pack** turns workflows into APIs with typed schemas, HTTP endpoints and BentoCloud autoscaling — [BentoML comfy-pack blog](https://www.bentoml.com/blog/comfy-pack-serving-comfyui-workflows-as-apis), [BentoML docs](https://docs.bentoml.com/en/latest/examples/comfyui.html)
- Baseten documents deploying custom ComfyUI workflows as APIs (Truss) — [Baseten blog](https://baseten.co/blog/deploying-custom-comfyui-workflows-as-apis). Beam and Cerebrium offer similar recipes — [Beam ComfyUI](https://www.beam.cloud/use-cases/comfyui), [Cerebrium](https://docs.cerebrium.ai/v4/examples/comfyUI)
- ComfyUI limits at scale (10k+ images/day): no native batching, unpredictable memory, a single-process bottleneck and a WebSocket API that is slow under concurrency. Diffusers with torch.compile is reported at 2–3x the throughput, and BentoML is suggested for headless REST with built-in queueing — [markaicode ComfyUI alternatives](https://markaicode.com/alternatives/comfyui-alternatives/) (secondary blog)
- fal's queue and SSE status/log streaming apply to custom fal apps too — [fal docs](https://fal.ai/docs/documentation/deployment/requests)

### Inferences
- Recommended path: prototype in ComfyUI → export the winning graph as an API (comfy-pack/RunPod worker) for niche features → port hot paths to diffusers (torch.compile, FP8, optionally TensorRT) in a FastAPI/BentoML/Modal worker. Use Cog only if you will publish on Replicate.
- Triton/TensorRT mostly pays off for very high-volume fixed-resolution image models. For video diffusion, the compile effort is high and the models change fast.

### Gaps
- No sourced Triton/TensorRT benchmarks for FLUX or Wan in 2026 were retrieved, and no independent comparison of Cog and BentoML was found.

## Q4. Reference architecture: async jobs, progress, storage/CDN, transcoding, moderation, credits, routing, prompt enhancement, timelines

### Takeaway
Treat every generation as an async job. The API writes a job row and reserves credits, then submits to the provider queue with a webhook URL. Progress is pushed to the client via SSE or WebSocket. On completion, outputs are copied to your own R2/S3 and served via CDN (video via Cloudflare Stream or Mux for HLS), moderation runs pre and post, and credits are captured (or refunded on failure). A provider router with per-model fallbacks absorbs outages and price changes, as the Sora shutdown showed.

### Cited Findings
- fal's queue offers submit → poll or webhook, SSE status streaming with queue position and logs, and automatic retries. This is a ready-made async primitive — [fal queue docs](https://fal.ai/docs/documentation/model-apis/inference/queue)
- **Cloudflare R2**: $0.015/GB-month, Class A writes $4.50/M, Class B reads $0.36/M, **zero egress**, and a free tier of 10 GB plus 1M/10M operations — [apiscout Mux vs Stream](https://apiscout.dev/guides/mux-vs-cloudflare-stream-api-2026), [leanopstech media storage 2026](https://leanopstech.com/blog/media-storage-serverless-cost-comparison-2026/)
- **Cloudflare Stream**: $5 per 1,000 minutes stored and $1 per 1,000 minutes delivered. Encoding is included and there is no separate egress — [Cloudflare Stream pricing](https://f75b423c.previews.developers.cloudflare.com/stream/pricing/index.md)
- **Mux**: just-in-time encoding (free, including 4K). Storage is about $0.003/min/month and delivery about $0.001/min (both vary by resolution tier) — [Mux blog](https://mux.com/blog/mux-is-cheaper-than-s3), [budgetforge Mux 2026](https://www.budgetforge.dev/tools/mux-pricing-2026)
- **Gateways for routing/fallback**: Vercel AI Gateway (zero markup, BYOK), OpenRouter (5.5% credit fee, image+video docs), Cloudflare AI Gateway (free analytics, caching and rate limiting; universal OpenAI/Anthropic-format endpoint added May 2026), and HF Inference Providers (auto provider selection across fal, Replicate, WaveSpeed and Together) — [Vercel best gateways](https://vercel.com/i/best-ai-gateways), [HF docs](https://huggingface.co/docs/inference-providers/index)
- **Provider risk**: the Sora 2 API was removed Sept 24, 2026 with no replacement — [OpenAI deprecations](https://developers.openai.com/docs/deprecations). Kling raised prices in Apr 2026 — [costbench](https://costbench.com/changelog/kling-price-increase-2026-04/)

### Recommended reference architecture (synthesized; components cited above)
1. **Frontend** (Next.js plus Vercel AI SDK, or any SPA). Creates jobs and subscribes to progress via SSE/WebSocket (Supabase Realtime, Pusher/Ably, or a Cloudflare Durable Object).
2. **API layer**: validates input, then runs **prompt moderation**. Optionally runs **LLM prompt enhancement** (a cheap LLM rewrites the prompt into a model-specific format such as camera/motion cues for video). Estimates cost from (model, resolution, seconds, audio) and **reserves credits** atomically in Postgres.
3. **Job table plus router**: a `jobs` row (status queued/running/succeeded/failed, provider, provider_request_id, cost_estimate, progress). The router picks a provider from a per-model priority list (e.g., Wan → fal, then WaveSpeed, then self-hosted RunPod; Veo → Vertex, then fal or Replicate) using health, price and latency. On a 5xx or timeout it falls back. Every request uses idempotency keys.
4. **Execution**: provider queue APIs (fal/Replicate/Runware/Vertex long-running operations) with **webhooks** to `/webhooks/{provider}` (verify signatures). Self-hosted models run on Modal/RunPod/Baseten serverless workers pulling from your own queue (SQS, Cloudflare Queues, Redis/BullMQ, or Inngest/Trigger.dev/Temporal for durable long video workflows). A poller backstops any missed webhook.
5. **Progress**: forward the provider's queue position, logs and step % to the client. For multi-shot videos, report per-shot progress.
6. **Post-processing**: download outputs immediately (provider URLs expire), run **output moderation** (image/video frame sampling with NSFW/CSAM hash checks), add watermark/C2PA metadata, and store originals in **R2** (zero egress). Video goes to **Cloudflare Stream or Mux** for HLS playback and thumbnails. Images are served from R2 behind a CDN with an image resizing service.
7. **Billing**: capture the reserved credits on success and refund on failure. Use Stripe for subscriptions and credit packs. Keep a ledger table (append-only) and track real provider cost per job for margin monitoring.
8. **Editing/timeline**: store projects as a timeline JSON of clips, each referencing generated assets, with trims, transitions and audio. Use I2V/last-frame chaining or the provider's "video continuation" endpoints (e.g., FLUX 3 Video continuation at $0.43–0.54/s on Runware) to extend shots. Render final cuts server-side with FFmpeg (or Remotion) on CPU workers, then upload to Stream/Mux.
9. **Observability**: per-provider success rate, p50/p95 latency, and cost per job. Alert on fallback rate.

### Inferences
- Webhooks plus a persistent job table are mandatory for video (jobs take minutes). Synchronous HTTP should only be used for fast image models (FLUX schnell/Klein).
- R2 zero egress matters a lot for a media app. Serving a 10 MB clip 1M times from S3 would incur large egress fees, while R2 incurs none.
- Hosting video on Stream or Mux is cheap next to generation cost. A 5 s clip is $0.15–3.00 to generate but a fraction of a cent to store and deliver.

### Gaps
- No primary-source guidance on specific moderation APIs (e.g., Hive, AWS Rekognition, OpenAI moderation for images) or their 2026 prices was gathered.
- No sourced data on C2PA/watermark requirements for 2026 (e.g., EU AI Act transparency obligations) is included here.

## Q5. Open-source reference apps / starter kits

### Takeaway
Most open-source starters are Next.js + Stripe + Replicate (or fal) image apps with credit systems. Few cover video properly, so the async/webhook/video parts will be custom. Vercel's official AI SDK image-generator and fal image-generator templates are the cleanest multi-provider starting points.

### Cited Findings
- **Vercel AI SDK Image Generator** template: multi-provider (Replicate, Vertex, OpenAI, Fireworks) comparison UI using `generateImage` — [Vercel template](https://vercel.com/templates/next.js/ai-sdk-image-generator). There is also a **fal image generator** template — [Vercel fal template](https://vercel.com/templates/other/fal-image-generator)
- **HeadShots.fun**: an open-source Next.js SaaS using Replicate and Stripe, built on next-saas-stripe-starter — [GitHub](https://github.com/ullrai/HeadShots.fun)
- **Pictoria AI**: Next.js + Supabase (auth/DB/storage) + Replicate (training and inference) + Stripe — [starterindex](https://starterindex.com/boilerplate/pictoria-ai-starter-code)
- **Visionary AI** and **lushnis/ai-saas**: Next.js + Replicate + Stripe with image, video and music generation and free-tier limits — [Visionary AI](https://github.com/mj-gowda/Visionary-AI), [ai-saas](https://github.com/lushnis/ai-saas)
- **PlutoSaaS**: Next.js 15 + Supabase + Stripe + Replicate (Flux, SDXL) — [PlutoSaaS](https://lacy-yoke-439.notion.site/PlutoSaaS-Build-Your-AI-Image-SaaS-in-Minutes-eeae4c7f9f1c42e590aee330dd070198)
- More repos are listed under GitHub topic `replicate-api` — [GitHub topic](https://github.com/topics/replicate-api?o=desc&s=updated)

### Inferences
- Combine Vercel's AI SDK template (provider abstraction) with a Supabase/Stripe credit starter (Pictoria-style). Then add the job/webhook/R2/Stream pieces from Q4.

### Gaps
- Star counts, maintenance status and licenses of these repos were not verified. Several (Visionary AI, ai-saas) look like older tutorial-grade projects.
- No mature open-source *video-first* SaaS starter (with timeline editing) was found.
