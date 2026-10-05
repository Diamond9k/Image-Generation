# Proprietary (Closed) AI Image Generation & Editing Models and APIs — State as of October 2026

Research date: 2026-10-05. **Methodology caveat:** direct fetches of primary pages (artificialanalysis.ai, lmarena.ai/arena.ai, ai.google.dev, docs.x.ai, openai.com, wikipedia) were blocked by the network egress proxy in this environment, so most facts below come from web-search result snippets of vendor pages, vendor X posts, reputable press, and aggregator/reseller blogs. Aggregator-sourced figures are flagged. Leaderboard numbers change weekly; treat all Elo values as point-in-time snapshots (late Sept 2026). Video models are out of scope.

## 1. Which closed image models lead today, and what is each one's spec sheet (release, strengths, API, price, limits, policy, watermarking)?

### Takeaway
As of early Oct 2026, OpenAI leads clearly: GPT Image 2.5 (Sunburst = top quality, Flare = faster), released 2026-09-08, holds #1/#2 on both Artificial Analysis (AA) and LMArena text-to-image. Behind it, the chase pack is xAI Grok Imagine Image 2.0 (Aug 2026), Microsoft MAI-Image-2.6 (Sep 2026), Google's Nano Banana 2 / Nano Banana Pro, ByteDance Seedream 5.0 Pro (Jul 2026), Reve 2.1 (Jul 2026), and BFL FLUX.2 [max]. Editing is more contested: Microsoft MAI-Image and Reve 2.1 rank at or above GPT Image 2 in AA's editing arena. Google retired the standalone Imagen 4 API on 2026-08-17 and moved everyone to Gemini-native "Nano Banana" image models.

### Cited Findings

**OpenAI: GPT Image line (gpt-image-1 → 1.5 → 2 → 2.5)**
- ChatGPT Images 2.0 / API `gpt-image-2` launched 2026-04-21. It is OpenAI's first image model with native "thinking" (Instant and Thinking modes), has 2K resolution and multi-image consistency, and can generate up to 8 coherent images from one prompt. Thinking features in ChatGPT are limited to Plus, Pro and Business. — [The New Stack](https://thenewstack.io/chatgpt-images-20-openai/); [BuildFastWithAI](https://www.buildfastwithai.com/blogs/chatgpt-images-2-0-gpt-image-2-2026)
- At launch, GPT Image 2 (high) debuted at #1 in AA Text-to-Image, ahead of Nano Banana 2, FLUX.2 [max] and Seedream 4.0. AA praised its prompt adherence, photorealism and text rendering. — [Artificial Analysis on X](https://x.com/ArtificialAnlys/status/2047184062012706980?lang=en)
- gpt-image-2 token pricing (via aggregators): $8/M image-input tokens, $5/M text-input tokens, $30/M image-output tokens, $2/M cached reads. — [OpenRouter](https://openrouter.ai/openai/gpt-image-2); [search summary of Bifrost/cloudprice](https://cloudprice.net/models/openai-gpt-image-2). *(Not verified against openai.com/pricing because the fetch was blocked.)*
- ChatGPT Images 2.5 launched 2026-09-08 for all ChatGPT, ChatGPT Work and Codex tiers. OpenAI claims "sharper details, faster generation, more precise editing" and up to 50% lower latency than Images 2.0. — [9to5Mac, 2026-09-08](https://9to5mac.com/2026/09/08/openai-releases-chatgpt-images-2-5-with-sharper-details-and-more-precise-editing/)
- Two API models shipped with it: **GPT-Image-2.5 Flare**, the speed-optimised default with 2–4x the throughput of GPT Image 2 and claimed comparable or better quality, and **GPT-Image-2.5 Sunburst**, the quality-max model. Third-party coverage also mentions a sketch tool, multi-turn editing and 4K output. — [DataNorth via search](https://datanorth.ai/news/openai-launches-chatgpt-images-2-5); [OpenPR](https://www.openpr.com/news/4629771/gpt-image-2-5-launches-openai-introduces-flare-and-sunburst) *(press-release site; lower reliability)*
- GPT Image 2.5 pricing is token-based and the same for Flare and Sunburst: $5/M text input, $8/M image input, $30/M image output. Estimated output cost at 1024×1024 by quality tier: Low ≈$0.006 (196 tokens), Medium ≈$0.013, High ≈$0.053, XHigh ≈$0.094, Max ≈$0.211. At 4K (3840×2160), Medium ≈$0.026 and Max ≈$0.40. — [Atlas Cloud blog](https://www.atlascloud.ai/blog/tips/gpt-image-2.5-api-cost); [eesel](https://www.eesel.ai/blog/chatgpt-images-2-5-pricing) *(aggregators; quality-tier names "XHigh/Max" are as reported, not seen on the OpenAI page)*
- Rate limits for GPT Image 2.x, reported by tier: Tier 1 is 100k TPM / 5 images per minute (IPM); Tier 2 is 250k / 20; Tier 3 is 800k / 50; Tier 4 is 3M / 150; Tier 5 is 8M / 250. There is no free API tier, and limits apply per organisation (all projects share them). — [Atlas Cloud](https://www.atlascloud.ai/blog/tips/gpt-image-2.5-rate-limits); [WaveSpeed](https://wavespeed.ai/blog/posts/gpt-image-2-rate-limits-2026/) *(secondary)*
- Provenance: on 2026-05-19 OpenAI said it is becoming C2PA-conformant and adding Google's **SynthID** invisible watermark to images from ChatGPT, Codex and the API. It also released "Verify", a public research-preview tool that checks images for C2PA and SynthID. — [TechCrunch, 2026-05-19](https://techcrunch.com/2026/05/19/openai-is-making-it-easier-to-check-if-an-image-was-made-by-their-models/); [PetaPixel, 2026-05-20](https://petapixel.com/2026/05/20/openai-gets-serious-about-detecting-fake-images/); [OpenAI content provenance docs](https://developers.openai.com/api/docs/guides/content-provenance)
- Also available through Azure OpenAI / Microsoft Foundry, with separate quotas. — [Microsoft Learn](https://learn.microsoft.com/en-us/azure/foundry/openai/quotas-limits)

**Google: Gemini-native "Nano Banana" line (Imagen retired)**
- Nano Banana Pro (Gemini 3 Pro Image Preview) was released 2025-11-20. It outputs 1K/2K/4K, fuses up to 14 reference images, keeps characters consistent, renders multilingual text, and can ground generation in Google Search. All outputs carry SynthID and support C2PA metadata. — [Google blog](https://blog.google/innovation-and-ai/products/nano-banana-pro/); [OpenRouter](https://openrouter.ai/google/gemini-3-pro-image-preview); [DataCamp](https://www.datacamp.com/tutorial/nano-banana-pro)
- Nano Banana Pro official API price: $0.134 per 1K/2K image and $0.24 per 4K image on the standard lane; $0.067 and $0.12 on the Batch/Flex lanes. Image input costs about $0.0011 per image. — [aifreeapi summary](https://www.aifreeapi.com/en/posts/nano-banana-pro-price); [benchlm](https://benchlm.ai/media-pricing/nano-banana) *(secondary; ai.google.dev fetch blocked)*
- Nano Banana 2 (Gemini 3.1 Flash Image): AA's X post says it took #1 in Text-to-Image "at half the price of Nano Banana Pro". The post ID dates to roughly late Feb 2026, when the model was in preview. — [Artificial Analysis on X](https://x.com/ArtificialAnlys/status/2027052241019175148). One aggregator gives a release date of 2026-06-18, which probably means GA, not preview. **Dates conflict, so flag this.** — [OpenRouter](https://openrouter.ai/google/gemini-3.1-flash-image)
- Nano Banana 2 price per image: $0.045 at 0.5K, $0.067 at 1K, $0.101 at 2K, $0.151 at 4K. Token rates: $0.50/M input, $60/M image-output tokens; Search grounding costs $14 per 1K calls. — [benchlm](https://benchlm.ai/media-pricing/nano-banana); [OpenRouter](https://openrouter.ai/google/gemini-3.1-flash-image)
- Nano Banana 2 Lite launched about 2026-06-30 / 07-01. It makes a 1K image in about 4 seconds for $0.034 per image ($0.0168 in batch). — [ChatMaxima](https://chatmaxima.com/blog/nano-banana-2-lite-cheap-fast-image-generation-business/); [BuildFastWithAI](https://www.buildfastwithai.com/blogs/nano-banana-2-lite-review-fastest-ai-image-generator-2026)
- Imagen 4 (generate / fast / ultra) was deprecated on 2026-06-15 and shut down in the Gemini API on 2026-08-17. Google's deprecation table names `gemini-3.1-flash-image` as the successor. One snippet says "Gemini 3 Image models" were also deprecated, so the Nano Banana Pro *preview* endpoint may have been retired or renamed; this is unverified. — [byteiota](https://byteiota.com/imagen-4-shutdown-august-17-migrate-to-gemini-image-api-now/); [Google dev forum](https://discuss.google.dev/t/imagen-4-0-deprecation-and-canada-hosted-alternatives/342923); [Gemini deprecations page](https://ai.google.dev/gemini-api/docs/deprecations)
- On LMArena (Sept 24, 2026 update), Google's best entry is Nano Banana 2 in web-search mode at #9 (1261). — [flami.pro LMArena summary](https://flami.pro/en/blog/lmarena-text-to-image-leaderboard)

**xAI: Grok Imagine Image 2.0**
- GA on 2026-08-07 as the "Quality Mode" on grok.com/imagine and in the iOS and Android apps. It adds region-level editing and multi-image reference inputs. — [Unite.AI](https://www.unite.ai/xai-ships-grok-imagine-image-2-0-with-precise-editing-and-a-top-arena-ranking/); [DataNorth](https://datanorth.ai/news/spacexai-releases-grok-imagine-image-2-0)
- API (docs.x.ai): $0.04 per image at 1K low, $0.06 at 2K low or 1K medium, $0.08 at 2K medium, plus $0.01 per input image for edits. It has only low and medium quality modes. — [xAI docs page (via search)](https://docs.x.ai/developers/models/grok-imagine-image-2.0); [ofox](https://ofox.ai/blog/grok-imagine-image-2-0-api-pricing-by-quality/)
- It is #4 on AA Text-to-Image (Elo 1157). — [AA leaderboard snippet](https://artificialanalysis.ai/image/leaderboard/text-to-image)

**Microsoft: MAI-Image line**
- MAI-Image-2.6 and MAI-Image-2.6-Flash were released 2026-09-04 in public preview on Microsoft Foundry. — [Microsoft Tech Community](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/mai-image-2-6-and-mai-image-2-6-flash-quality-and-speed-at-production-scale/4550970)
- Pricing: MAI-Image-2.6 costs $5/M text input, $8/M image input and $38/M image output, or about $0.039 per image (Arena estimate $38.90 per 1,000). Flash costs $1.75/M, $2.50/M and $19/M. — [runtimewire](https://runtimewire.com/article/microsoft-mai-image-2-6-foundry-four-cents-image); [kie.ai](https://kie.ai/blog/what-is-mai-image-2-6)
- It is #5 on AA T2I (1148) and #4 on LMArena T2I. MAI-Image-2.6-Preview leads AA Image Editing (Elo 1286), and MAI-Image-2.5-Pro previously led at 1272. — [AA editing snippet](https://artificialanalysis.ai/image/leaderboard/editing); [flami.pro](https://flami.pro/en/blog/lmarena-text-to-image-leaderboard)

**Black Forest Labs: FLUX.2 [pro]/[max]/[flex] (closed API tiers; [klein]/[dev] are open weights)**
- BFL prices by megapixel. As of 2026-07-31, text-to-image starts at $0.014 (Klein 4B), $0.015 (Klein 9B), $0.03 (Pro), $0.05 (Flex) and $0.07 (Max). For Max, the first output MP costs $0.07, each further MP $0.03, and reference/input images $0.03/MP. Pro editing starts at $0.045. — [BFL docs pricing](https://docs.bfl.ml/quick_start/pricing); [OpenRouter FLUX.2 Max](https://openrouter.ai/black-forest-labs/flux.2-max)
- BFL describes FLUX.2 [max] as its top tier for quality, prompt understanding and editing consistency. — [OpenRouter](https://openrouter.ai/black-forest-labs/flux.2-max)
- The API is also served by Together, DeepInfra, fal and others. — [Together](https://www.together.ai/models/flux-2-pro); [DeepInfra](https://deepinfra.com/flux)

**ByteDance: Seedream 5.0 Pro**
- Announced 2026-07-08 on BytePlus and Dreamina; the API is "Dola Seedream 5.0 Pro" on BytePlus. — [X trending](https://x.com/i/trending/2074857001994145804); [The Rundown](https://www.therundown.ai/tools/seedream-5-0-pro)
- Strengths: precision editing, infographics, realistic portraits, native text in 14 languages, brush and layer-separation tools, and fusion of up to 10 reference images. — [The Rundown](https://www.therundown.ai/tools/seedream-5-0-pro)
- BytePlus price: $0.045 per successful output up to about 2.36 MP and $0.09 above that. The first reference image is free and each extra one costs $0.003. — [search summary of BytePlus listing](https://openrouter.ai/bytedance-seed/seedream-5-0-pro)
- Tied 10th/11th on LMArena T2I at 1256. — [flami.pro](https://flami.pro/en/blog/lmarena-text-to-image-leaderboard)

**Reve: Reve 2.1**
- Launched 2026-07-09 with a focus on layout, structure and spatial reasoning. — [eesel](https://www.eesel.ai/blog/reve-2-1)
- API uses credits: the minimum purchase is $10 for 7,500 credits. A v2 Create or Edit costs 150 credits (about $0.20); legacy Fast costs 5 credits (about $0.007). Resellers charge about $0.24–0.25 per image. — [eesel pricing](https://www.eesel.ai/blog/reve-2-1-pricing); [Atlas Cloud](https://www.atlascloud.ai/models/reve-ai/reve-2.1/edit)
- #2 on AA Image Editing (Elo 1263) in an earlier snapshot. — [AA editing snippet](https://artificialanalysis.ai/image/leaderboard/editing)

**Midjourney: V8 / V8.1 / V8.2**
- V8 Alpha shipped 2026-03-17 and V8.1 in mid-April 2026. V8.2 has been the default since 2026-07-24. V8 rewrote the stack (GPU/PyTorch), made generation about 5x faster and added native 2K ("HD"). — [WaveSpeed](https://wavespeed.ai/blog/posts/what-is-midjourney-v8-features-pricing-how-to-use-2026/); [tech-insider](https://tech-insider.org/how-to-use-midjourney-v8-1-2026/)
- API: historically there was no public API. Reports in May 2026 describe an official API in "limited rollout" running V8.2. Sources disagree, so treat GA availability as **unconfirmed**. — [search summary of apiframe/WaveSpeed](https://apiframe.ai/models/midjourney)

**Ideogram: 4.0 (open weights plus API) and 4.5**
- Ideogram 4.0 was released 2026-06-03 as a 9.3B-parameter open-weights model. The weights are free for evaluation and non-commercial use; commercial self-hosting needs a licence. The hosted API charges $0.03 (Turbo), $0.06 (Default) and $0.10 (Quality) per image. — [AA on X](https://x.com/ArtificialAnlys/status/2065135515171709056); [fal](https://fal.ai/ideogram-4)
- Ideogram 4.5 was released 2026-09-30 and focuses on precise multi-turn editing with less drift. It is available on the Ideogram API, MCP integration and apps. — [OrcaRouter](https://www.orcarouter.ai/blog/ideogram-4-5-launch-precise-edit-model)
- "P-Image-Ideogram (Very High)" was added to the AA leaderboard in about Sept 2026. — [AA snippet](https://artificialanalysis.ai/image/leaderboard/text-to-image)

**Recraft: V4 / V4.1**
- V4 was released Feb 2026, V4 Vector on 2026-05-13, and V4.1 later. Its distinguishing feature is native SVG output with structured, editable layers. Prices: V4 Vector $0.08, V4 Pro Vector $0.30, V4 Styles Vector from $0.05, V4 Styles Pro Vector from $0.12 per image. — [OpenRouter](https://openrouter.ai/recraft/recraft-v4-vector); [Recraft docs](https://www.recraft.ai/docs/recraft-models/recraft-V4); [The Rundown](https://www.therundown.ai/tools/recraft-v4-1)

**Adobe: Firefly Image Model 5**
- Announced at Adobe MAX on 2025-10-28. It generates natively at up to 4 MP, improves photorealism and human rendering, adds Prompt-to-Edit, and has Layered Image Editing in development. Firefly Custom Models are available. — [Adobe newsroom](https://news.adobe.com/news/2025/10/adobe-max-2025-firefly); [TechCrunch](https://techcrunch.com/2025/10/28/adobe-firefly-image-5-brings-support-for-layers-will-let-creators-make-custom-models/)
- The Firefly app also hosts partner models from Google, OpenAI, BFL, Ideogram, Luma, Runway and others. — [Adobe newsroom](https://news.adobe.com/news/2025/10/adobe-max-2025-firefly)
- Commercial terms: Firefly is trained on Adobe Stock, openly licensed and public-domain content. Enterprise customers can get IP indemnification for most Firefly-powered workflows. It covers claims that the output itself directly infringes copyright, trademark, patent, publicity or privacy rights, and excludes claims arising from customer modifications or inputs. API access is through Firefly Services (enterprise). — [Adobe Firefly Legal FAQs (Enterprise)](https://www.adobe.com/content/dam/dx/us/en/products/sensei/sensei-genai/firefly-enterprise/Firefly_Legal_FAQs_Enterprise_Customers.pdf)

**Platforms with in-house models (mostly aggregators)**
- Higgsfield Soul 2.0 (released about 2026-02-01) is a fashion- and aesthetic-focused photo model with "Soul ID" character consistency. It is available through the Higgsfield API (open.higgsfield.ai). Higgsfield mainly wraps third-party models. — [Higgsfield API](https://open.higgsfield.ai/models/higgsfield-ai/soul/v2/standard/playground); [Design Offset](https://design-offset.com/20260220-higgsfield-soul-v2-0/)
- Krea 2, Krea's first in-house image model, launched May 2026 and generates 2K images in about 2 seconds. Krea also hosts 64+ outside models. — [Krea blog](https://www.krea.ai/blog/what-is-higgsfield-ai-pricing-free-plan-and-alternatives-in-2026)
- Leonardo.Ai (Canva-owned) has its own Lucid Origin and Phoenix models. — [Beebom](https://beebom.com/best-ai-image-generator/)

### Inferences
- For a new product, the default quality picks are GPT Image 2.5 (Flare for volume, Sunburst for hero shots). Cost-efficient alternatives are Nano Banana 2 / 2 Lite, Seedream 5.0 Pro, MAI-Image-2.6 and FLUX.2 [pro], at roughly $0.03–0.07 per image. Recraft is the clear choice for vector/SVG output and Ideogram for typography/design. Adobe Firefly is the choice where indemnity and training-data provenance matter most.
- Pricing is split. OpenAI, Google and Microsoft bill by token (cost varies with quality and resolution); BFL bills per megapixel; xAI, Ideogram, ByteDance and Recraft charge flat per image. Products should normalise to "cost per accepted image".
- Midjourney still cannot safely be built on without confirmed API terms.

### Gaps
- Could not read openai.com/api/pricing, ai.google.dev pricing or official model cards directly (blocked), so official per-image prices for gpt-image-2.5 and Gemini come from aggregators.
- Max resolutions were not confirmed for every model (e.g., GPT Image 2.5's exact maximum dimensions; Grok's 4K support).
- I found no 2025–26 sources on OpenAI, Google, xAI, BFL, ByteDance or Microsoft API indemnification terms. Earlier knowledge: OpenAI "Copyright Shield" and Google generative-AI indemnity cover enterprise/API customers. Both are **unverified for 2026**.
- No detailed content-policy differences were gathered. xAI is generally reported as more permissive, but I have no 2026 source.
- Leonardo, Krea 2 and Higgsfield Soul API prices were not found.

## 2. What are the current rankings on Artificial Analysis (T2I and Image Editing) and LMArena?

### Takeaway
OpenAI dominates text-to-image on both arenas: GPT Image 2.5 Sunburst is #1, Flare #2 and GPT Image 2 #3. Image editing is led by Microsoft's MAI-Image line on AA, with Reve 2.1 and GPT Image 2 close behind. Google has slipped to roughly #9 on LMArena T2I.

### Cited Findings
- **AA Text-to-Image (snapshot ~late Sept 2026):** 1. GPT Image 2.5 Sunburst (max) 1196; 2. GPT Image 2.5 Flare (max) 1190; 3. GPT Image 2 (high) 1171; 4. Grok Imagine Image 2.0 1157; 5. MAI-Image-2.6 1148. Models added in the past month: P-Image-Ideogram (Very High), Qwen-Image-2.1, Ming-Image-0.1-Design, Grok Imagine Image 2.0, and GPT Image 2.5 Sunburst and Flare. Best open-weights entries: Qwen-Image-2.1 (1035), then Ideogram 4.0 Quality (1010). AA has moved to a "v2.0" leaderboard ("AA-Image-T2I v2.0"), so Elo values are not comparable with older snapshots. — [AA T2I leaderboard (search snippet)](https://artificialanalysis.ai/image/leaderboard/text-to-image)
- **AA Image Editing ("AA-Image-Editing v2.0"):** MAI-Image-2.6-Preview leads at Elo 1286 in the newer snapshot. The older snapshot ranked MAI-Image-2.5-Pro 1272, Reve 2.1 1263, GPT Image 2 (high) 1256, MAI-Image-2.5 1256 and GPT Image 1.5 (high) 1250. The best open-weights editor is HunyuanImage 3.0 Instruct (1222). GPT Image 2.5's editing rank was not visible in snippets. — [AA editing leaderboard (snippet)](https://artificialanalysis.ai/image/leaderboard/editing)
- **LMArena (arena.ai) Text-to-Image, update of 2026-09-24:** 80 models and about 6.48M votes. 1. GPT Image 2.5 Sunburst 1424; 2. GPT Image 2.5 Flare 1401; 3. GPT Image 2 (medium) 1383; 4. MAI-Image-2.6 (89 points behind #1); 9. Nano Banana 2 (web search) 1261; 10–11 (tied). Seedream 5.0 Pro and Qwen Image 3.0 Pro at 1256. GPT Image 2 has about 88.7k votes and a ±4 confidence interval. — [flami.pro LMArena summary](https://flami.pro/en/blog/lmarena-text-to-image-leaderboard) *(secondary summary of [arena.ai leaderboard](https://arena.ai/leaderboard/text-to-image))*
- Grok Imagine Image 2.0 claimed the #2 spot on both arenas at its August 2026 launch, before GPT Image 2.5 pushed it down. — [Unite.AI](https://www.unite.ai/xai-ships-grok-imagine-image-2-0-with-precise-editing-and-a-top-arena-ranking/)
- GPT Image 2 set "the largest lead in Image Arena history" at its April 2026 launch. — [BuildFastWithAI](https://www.buildfastwithai.com/blogs/chatgpt-images-2-0-gpt-image-2-2026)

### Inferences
- Ranks 4–11 sit within about 40–100 Elo of each other, so mid-tier choices should depend on price, latency and features rather than rank.
- Ideogram 4.5 (released 2026-09-30) is probably not yet reflected in these rankings.

### Gaps
- Could not view full leaderboards (blocked). Ranks 6–15 on AA and 5–8 on LMArena, LMArena image-edit rankings, and per-category results (text rendering, photorealism) are missing.
- Midjourney V8.x does not appear in the arena snippets (Midjourney has historically not offered an API for arena inclusion).

## 3. Trends: native multimodal LLM image generation, instruction editing, multi-reference consistency

### Takeaway
The frontier is now "reasoning" image models built into multimodal LLMs. Examples are GPT Image 2/2.5 with Thinking mode, Gemini's Nano Banana line (which replaced Imagen) and Microsoft MAI-Image. Instruction-based multi-turn editing, many-image reference fusion and provenance watermarking have become standard expectations.

### Cited Findings
- Native LLM image generation: gpt-image-2 is OpenAI's first image model with native thinking. It reasons through image structure before generating and checks its own outputs. — [The New Stack](https://thenewstack.io/chatgpt-images-20-openai/). Google retired the standalone Imagen 4 diffusion API in favour of Gemini 3.1 Flash Image. — [byteiota](https://byteiota.com/imagen-4-shutdown-august-17-migrate-to-gemini-image-api-now/)
- Search-grounded generation is shipping: Nano Banana Pro has Search grounding, and Nano Banana 2 offers a web-search mode billed at $14 per 1K calls. — [OpenRouter](https://openrouter.ai/google/gemini-3.1-flash-image); [DataCamp](https://www.datacamp.com/tutorial/nano-banana-pro)
- Multi-reference consistency examples: Nano Banana Pro fuses 14 reference images, Seedream 5.0 Pro 10, Grok Imagine 2.0 accepts multiple references, GPT Image 2 makes up to 8 coherent images per prompt, and Higgsfield offers Soul ID. — sources cited above
- Instruction or multi-turn editing is the newest focus. Ideogram 4.5 targets less drift in multi-turn edits (2026-09-30), GPT Image 2.5 claims "more precise editing", Grok 2.0 adds region editing, and Firefly 5 adds Prompt-to-Edit and layers. — [OrcaRouter](https://www.orcarouter.ai/blog/ideogram-4-5-launch-precise-edit-model); [9to5Mac](https://9to5mac.com/2026/09/08/openai-releases-chatgpt-images-2-5-with-sharper-details-and-more-precise-editing/)
- Speed and cost tiering: every vendor now ships a fast/cheap variant (GPT-2.5 Flare, Nano Banana 2 Lite at $0.034, MAI-Image-2.6-Flash, FLUX.2 Klein, Ideogram Turbo). — sources above
- Provenance convergence: OpenAI and Google both now use C2PA plus SynthID (OpenAI since May 2026). — [c2paviewer](https://c2paviewer.com/articles/openai-google-c2pa-synthid-2026); [TechCrunch](https://techcrunch.com/2026/05/19/openai-is-making-it-easier-to-check-if-an-image-was-made-by-their-models/)
- Some 2026 entrants are blurring the open/closed line. Ideogram 4.0 is open weights with a commercial licence on the side, and BFL keeps Pro/Max closed while releasing Klein/Dev openly. — [AA on X](https://x.com/ArtificialAnlys/status/2065135515171709056)

### Inferences
- New vendors are entering at the top from the LLM labs (Microsoft MAI, xAI) rather than from image specialists. Leadership has changed hands about every 2–4 months in 2026: Nano Banana 2 (Feb), GPT Image 2 (Apr), Grok 2.0 (Aug, #2), GPT Image 2.5 (Sep). A product should abstract the model provider behind a router.
- Watermarking is becoming a default the customer cannot turn off on the major APIs. Products that need clean outputs should not expect to strip it.

### Gaps
- No authoritative source on whether GPT Image 2.5 or Nano Banana 2 allow turning SynthID off for enterprise (assumed no).
- No reliable comparative data on latency or per-model content-policy refusal rates.
