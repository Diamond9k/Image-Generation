# Closed (Proprietary) AI Video Generation Models and APIs — State as of October 2026

Research date: 2026-10-05. Method note: direct page fetches to artificialanalysis.ai, ai.google.dev, and several vendor/aggregator sites were blocked by the network egress proxy, so most findings come from web-search result extracts (from vendor pages, trade press, and aggregator blogs). Prices and leaderboard numbers marked "(aggregator)" should be checked against the vendor page before they are used for budgeting. Image models and open-weight models are out of scope. Open-weight models are mentioned only where they affect closed-model rankings.

## Q1. Which closed video models lead today, and what has changed in 2025–2026?

### Takeaway
As of October 2026, the closed video field has rotated away from Western labs. Google's Gemini Omni Flash (I/O 2026), ByteDance's Seedance 2.0/2.5, Alibaba's Wan 3.0 (API-only) and HappyHorse, Kuaishou's Kling 3.0, and MiniMax H3 sit at or near the top of the arenas. OpenAI has left the field: the Sora app shut on 2026-04-26 and the Sora 2 API on 2026-09-24. Veo 3.1 is still Google's developer API line but now ranks mid-table. Runway (Gen-4.5, Aleph 2.0), Luma (Ray3.14/Ray3.2), and xAI (Grok Imagine 1.5) remain viable API options.

### Cited Findings

**Google: Veo 3.1 family (developer API) and Gemini Omni (new, 2026)**
- Pricing for Veo 3.1 on the Gemini API (2026). Standard is $0.40/s at 720p/1080p and $0.60/s at 4K. Fast is $0.10/s (720p), $0.12/s (1080p) and $0.30/s (4K). Lite is $0.05/s (720p) and $0.08/s (1080p), with no 4K. — [Puter Gemini API pricing (Sep 2026)](https://developer.puter.com/tutorials/gemini-api-pricing/); [Gemini API pricing page](https://ai.google.dev/gemini-api/docs/pricing) (official page; fetch blocked, figures taken from search extract); [AtlasCloud Veo 3.1 pricing](https://www.atlascloud.ai/blog/tips/veo-3.1-api-pricing)
- Veo 3.1 Lite launched in preview on the Gemini API on 2026-03-31 and went GA on 2026-04-23. It makes 4, 6 or 8 s clips at 24 fps, 16:9 or 9:16, 720p/1080p, T2V and I2V, with a SynthID watermark. Cost is under 50% of Veo 3.1 Fast. — [AlternativeTo news (Apr 2026)](https://alternativeto.net/news/2026/4/google-launches-veo-3-1-lite-a-low-cost-video-model-for-developers); [CometAPI](https://www.cometapi.com/what-is-google-veo-3-1-lite/)
  - CONFLICT: sources disagree on whether Veo 3.1 Lite has native audio. One extract says "native synchronized audio" and another says it "does not support 4K output, scene extension, or native audio generation." This was not resolved. — [CometAPI](https://www.cometapi.com/what-is-google-veo-3-1-lite/); [Puter](https://developer.puter.com/ai/google/veo-3.1-lite/)
- Gemini Omni was announced at Google I/O 2026 (May 2026). It is a DeepMind multimodal family that generates and edits video from any mix of text, image, audio and video inputs. The first model, Gemini Omni Flash, rolled out the same day to the Gemini app and Google Flow (AI Plus/Pro/Ultra) and to YouTube Shorts / YouTube Create for free. — [The Next Web](https://thenextweb.com/news/google-gemini-omni-flash-video-model-io-2026); [Technobezz](https://www.technobezz.com/news/google-launches-gemini-omni-flash-model-that-generates-video-with-synchronized-audio)
- Omni Flash makes clips of up to 10 s with synchronized audio and is SynthID-watermarked. It can use an existing video as the base for a new one (V2V / conversational editing). An "avatar mode" and audio editing were held back at launch. It replaces Veo as the video model inside the Gemini app, while Veo stays the developer-API line. — [WaveSpeed](https://wavespeed.ai/blog/posts/gemini-omni-flash-shipped-what-actually-launched/); [The Next Web](https://thenextweb.com/news/google-gemini-omni-flash-video-model-io-2026); [geotoolbox](https://geotoolbox.ai/blog/gemini-omni)
- Gemini Omni 1.1 Flash API pricing (aggregator). Input is $1.50/1M tokens, text output $9.00/1M, and video output $17.50/1M. Video is billed at about 5,792 tokens/s, so roughly $0.10/s at 720p. The price is the same on the Gemini API and Vertex AI, which was renamed "Gemini Enterprise Agent Platform" in April 2026. There is no batch discount. — [eesel.ai](https://www.eesel.ai/blog/gemini-omni-1-1-flash-pricing)

**OpenAI: Sora 2 / Sora 2 Pro (SUPERSEDED, API retired)**
- On 2026-03-24 OpenAI told developers that the Videos API and all Sora model aliases would be removed on 2026-09-24. The retired aliases are sora-2, sora-2-pro, sora-2-2025-10-06, sora-2-2025-12-08 and sora-2-pro-2025-10-06. The deprecations page lists no replacement. The Sora app shut on 2026-04-26. Sora 2 generation is reported to remain inside ChatGPT paid tiers, but there is no programmatic access. — [Pondero (2026-09-24)](https://pondero.ai/news/2026-09-24-openai-sora-api-shutdown/); [Wikipedia: Sora](https://en.wikipedia.org/wiki/Sora_(text-to-video_model)); [heydev](https://heydev.us/blog/openai-model-shutdowns-september-2026-audit-your-app)
- Historical pricing before shutdown, for reference only. sora-2-pro cost $0.30/s (720p), $0.50/s (1792×1024) and $0.70/s (1080p). The Batch API was half price. — [OpenRouter Sora 2 Pro](https://openrouter.ai/openai/sora-2-pro); [ecorpit](https://ecorpit.com/sora-2-videos-api-shutdown-migration-cost-2026/)

**ByteDance: Seedance 2.0 / 2.5 (Dreamina; API via BytePlus / Volcano Engine)**
- Seedance 2.0 on BytePlus costs $0.04–$0.78/s depending on variant and resolution. 720p is about $0.15/s ($0.76 per 5 s) and 1080p about $0.37/s ($1.87 per 5 s). Clips run 4–15 s at 480p/720p/1080p/4K. Audio is included at no extra charge. — [anikuku](https://anikuku.com/blog/seedance-2-api-pricing-guide-2026); [AtlasCloud](https://www.atlascloud.ai/blog/case-studies/seedance-2.0-pricing-full-cost-breakdown-2026); [OpenRouter Seedance 2.0](https://openrouter.ai/bytedance/seedance-2.0)
- Seedance 2.5 was announced at Volcano Engine FORCE on 2026-06-23 and released on 2026-07-31. It makes up to 30 s of audio+video in one pass, with up to 50 references (30 images, 10 videos, 10 audio clips). It adds frame-local / timestamp-level editing and multi-round extension. One extract says the API supports 4–30 s at 480p/720p. — [Kie.ai](https://kie.ai/blog/seedance-2-5-release-deep-dive); [seedance2-video.com](https://seedance2-video.com/seedance-2-5) (unofficial fan/aggregator sites; verify with BytePlus)

**Kuaishou: Kling 3.0 (and Kling O3 / Omni)**
- Kling 3.0 was released on 2026-02-06. It adds native multilingual audio (EN/ZH/JA/KO/ES), multi-shot storyboarding, 4K ("ultra-HD") output, stronger character consistency and Motion Brush. Native audio uses about 50% more credits. — [AtlasCloud Kling 3.0 review](https://www.atlascloud.ai/blog/tips/kling-3.0-review-features-pricing-ai-alternatives); [virse](https://www.virse.ai/blog/kling-3-0-pricing)
- Official Kling 3.0 API pricing (aggregator-reported) is $0.112/s at 720p with audio, $0.140/s at 1080p with audio, and $0.420/s at 4K. Third-party resellers charge different rates: PiAPI is $0.15/s (720p+audio) and $0.20/s (1080p+audio). — [AtlasCloud](https://www.atlascloud.ai/blog/tips/kling-3.0-review-features-pricing-ai-alternatives); [PiAPI](https://piapi.ai/kling-api)

**Alibaba: Wan 3.0 (closed, API-only) and HappyHorse 1.0**
- Wan 3.0 entered public beta on 2026-08-06 on Alibaba Cloud Model Studio / Qwen Cloud as `wan3.0-video` and was formally released on 2026-08-24. It is closed and API-only. Alibaba's open weights stop at Wan 2.2 (Apache 2.0); Wan 2.5, 2.6 and 2.7 were commercial API models. It makes up to 30 s in a single pass from text, image, audio, video or documents (PPT/PDF/DOC/XLS). Pricing is $0.05/s (480p), $0.10/s (720p) and $0.20/s (1080p). — [Alibaba Cloud blog](https://www.alibabacloud.com/blog/wan3-0-30-second-ai-video-generation-from-any-input_603452); [The Next Web](https://thenextweb.com/news/alibaba-wan3-video-model-after-share-sale); [Winbuzzer (2026-08-25)](https://winbuzzer.com/2026/08/25/alibaba-launches-wan3-0-for-30-second-ai-video-from-documents-xcxwbn/); [anikuku](https://anikuku.com/blog/wan-3-0-release-date-api-pricing-2026)
- HappyHorse-1.0 comes from Alibaba's Alibaba Token Hub unit and is led by Zhang Di, formerly of Kuaishou/Kling. It is a 15B-parameter model producing 1080p video with synchronized audio in one pass, with lip-sync in 6 languages. It reached #1 on the AA Video Arena for T2V (Elo 1333) and I2V (Elo 1392) at some point in 2026. It is available via fal and others. Some press calls it "open-source," so its open/closed status is UNCLEAR and should be verified. — [fal.ai HappyHorse](https://fal.ai/happyhorse); [Tullahoma News press release](https://www.tullahomanews.com/?p=103570); [24-7 Press Release](https://www.24-7pressrelease.com/press-release/533608/happy-horse-10-storms-the-video-arena-mystery-dark-horse-tops-the-global-leaderboard) (press releases; treat as promotional)

**MiniMax: Hailuo / H3 (open-weight; noted for ranking context only)**
- MiniMax H3 (Hailuo 3.0) was released around 2026-07-29/31. It is open-weight and makes 4–15 s clips at 24 fps with native stereo audio, in 768p or 2K. The hosted API costs $0.08/s (768p) and $0.13/s (2K), plus $0.04 per reference image after the first 5. It is out of scope as an open-weight model, but it tops several arena boards (see Q3). — [OpenRouter H3](https://openrouter.ai/minimax/hailuo-3); [llm-stats](https://llm-stats.com/blog/research/minimax-h3-launch); [minimax-ai.chat](https://minimax-ai.chat/models/minimax-h3/)

**Runway: Gen-4.5, Aleph 2.0**
- Gen-4.5 has been on the Runway API since 2026-02-10. It does T2V and I2V with clips of 2–10 s. It gained native audio generation and audio editing in May 2026. — [Runway API changelog](https://docs.dev.runwayml.com/api-details/api_changelog/); [veo4.dev review](https://veo4.dev/runway-gen-4-5)
- API pricing is $0.01 per credit with a $10 minimum top-up. Gen-4.5 costs 12 credits/s ($0.12/s) and Gen-4 Turbo 5 credits/s ($0.05/s). Gen-4 Aleph (V2V editing) was removed from the API on 2026-07-30 and replaced by Aleph 2.0 at 28 credits/s ($0.28/s). — [apiframe Runway guide](https://apiframe.ai/guides/runway-api-guide); [eesel Runway pricing](https://www.eesel.ai/blog/runway-ai-pricing); [Runway pricing](https://runway.com/pricing); [Runway API changelog](https://docs.dev.runwayml.com/api-details/api_changelog/)
- No Runway "Gen-5" was found in sources as of early October 2026.

**Luma AI: Ray3 / Ray3.14 / Ray3.2 (Dream Machine)**
- Ray3 shipped in September 2025, billed as the first "reasoning" video model with native HDR. Ray3.14 (January 2026) raised native resolution to 1080p and is about 3x cheaper and 4x faster than launch Ray3. The API (Ray3.2, "Luma Agents API") is priced per second: about $1.20 for a 5 s 1080p SDR clip, about 2x that for HDR, and about 3x for HDR+EXR. Consumer plans are Plus $30, Pro $90 and Ultra $300 per month. — [pasqualepillitteri review](https://pasqualepillitteri.it/en/news/4026/luma-dream-machine-ray3-review-2026); [magichour Luma pricing](https://magichour.ai/blog/luma-dream-machine-pricing); [apiframe Luma guide](https://apiframe.ai/guides/luma-api-guide)

**xAI: Grok Imagine (video)**
- The API charges about $0.05/s with native audio included, so a 6 s clip is $0.30 and a 15 s clip $0.75. `grok-imagine-video-1.5` costs $0.08/s. Durations are 6, 10 or 15 s, with Video Extend up to 30 s total. Aspect ratios are 1:1, 16:9, 9:16, 4:3, 3:2 and 2:3. Consumer access is through SuperGrok (about $10 Lite, about $30 full). Latent Space headlined the API as "the #1 Video Model, Best Pricing and Latency." — [apiframe Grok guide](https://apiframe.ai/guides/grok-video-api-guide); [Latent Space AINews](https://www.latent.space/p/ainews-spacexai-grok-imagine-api); [felloai](https://felloai.com/grok-imagine-video-generation/)
- CONFLICT: one source says Grok Imagine Video has no native audio, while others say audio is automatic and included in billing. — [apiframe](https://apiframe.ai/guides/grok-video-api-guide)

**Others (sparse data)**
- Vidu Q3 (Shengshu) adds native audio and is described as a budget/API contender strong at reference-to-video. — [teamday.ai (Sep 2026)](https://www.teamday.ai/blog/best-ai-video-models-2026); [kingy.ai](https://kingy.ai/news/best-ai-video-generator-2026/)
- Midjourney Video V1 has no public API, does image-to-video only, and has no audio. — [kingy.ai](https://kingy.ai/news/best-ai-video-generator-2026/)
- Adobe Firefly acts as a multi-model hub. Firefly Boards (worldwide since 2025-09-24) offers Runway Aleph, Moonvalley Marey, Luma Ray3/Ray2, Veo 3, Pika 2.2 and others alongside Adobe's own Firefly Video model. The April 2026 update added Quick Cut and unlimited generations on some plans. — [Adobe blog (2025-09-24)](https://blog.adobe.com/en/publish/2025/09/24/firefly-boards-launches-globally-now-with-runway-aleph-moonvalley-marey-models-new-powerful-ideation-features-flexible-offers); [Adobe HelpX partner models](https://helpx.adobe.com/firefly/web/create-mood-boards/firefly-boards/partner-models-to-generate-videos.html); [novareviewhub](https://www.novareviewhub.com/news/adobe-firefly-update-2026)
- Pika's lower plans are cited as budget options. — [kingy.ai](https://kingy.ai/news/best-ai-video-generator-2026/)

**Avatar / talking-head APIs**
- HeyGen API: Avatar IV/V costs about $0.05/s ($3/min). Photo Avatar is about $3/min (720p/1080p), and Digital Twin / Studio Avatar about $4/min ($4–5/min at 4K). On the web plan, Avatar IV uses 20 credits/min, which works out to about $0.97/min on the $29 Creator plan. Real-time "LiveAvatar" is billed separately. — [realtimeavatar.ai](https://realtimeavatar.ai/blog/heygen-api-pricing-explained); [aitoolanalysis HeyGen review (May 2026)](https://aitoolanalysis.com/heygen-review/); [creatify](https://creatify.ai/blog/heygen-pricing-(2026)-plans-and-what-you-ll-actually-pay)
- Synthesia: Starter is about $18–29/month and Creator about $64–89/month. API access is on Creator and is capped at 360 minutes per year. A 5-minute video costs about $14.50 on Starter, against about $4.83 on HeyGen Creator. — [knowlify](https://www.knowlify.com/articles/synthesia-pricing); [creatify comparison](https://creatify.ai/blog/heygen-vs-synthesia-(2026)-pricing-avatars-and-which-one-fits-your-team)
- Tavus: $1 per generated minute and $0.37 per real-time conversation minute. Paid plans start at $22/month. — [veed.io talking-head APIs](https://www.veed.io/learn/best-talking-head-video-apis); [aiagentrank](https://aiagentrank.io/compare/synthesia-vs-tavus)
- Hedra Character-3: Basic is $15/month for 1,500 credits (about 4 min, about $3.60/min). Creator is $30/month for about 15 min (about $2/min). — [HeyGen blog comparison](https://www.heygen.com/blog/best-talking-avatars-video-maker) (competitor-authored); [arcade](https://www.arcade.software/post/heygen-pricing)

### Inferences
- For a new product, the closed APIs with the best published price-to-quality ratio are Wan 3.0 (about $0.10–0.20/s, 30 s clips), Kling 3.0 (about $0.11–0.14/s with audio), Seedance 2.0/2.5 (about $0.15–0.37/s, audio included), Gemini Omni Flash (about $0.10/s at 720p), and Grok Imagine (about $0.05–0.08/s). Veo 3.1 Standard ($0.40/s) and the former Sora 2 Pro ($0.70/s) are premium-priced outliers.
- The Sora retirement is a concrete vendor-risk example: deprecation was announced only 6 months before shutdown, and no replacement was named. Building on a multi-provider aggregator (fal, Replicate, OpenRouter, AtlasCloud) reduces lock-in.
- Chinese vendors (ByteDance, Alibaba, Kuaishou, MiniMax) now dominate both quality rankings and price. US buyers should weigh data-residency and compliance terms for them.

### Gaps
- Official per-vendor rate limits (RPM / concurrent jobs) were not retrievable for any vendor because vendor docs were blocked.
- Commercial-use / IP indemnity terms were not verified. Adobe historically markets its Firefly models as commercially safe, but I found no 2026 primary source to cite.
- No 2026 primary data was found for Pika, Higgsfield's own models (Higgsfield mainly aggregates third-party models), Moonvalley Marey pricing/API, Captions (Mirage), or Utopai X beyond its leaderboard listing.
- Veo 3.1 Standard/Fast release dates and max duration/extension specs were not re-verified in this session. Veo 3.1 itself dates to October 2025 according to my training knowledge, which is unverified here.
- Kling 3.0 maximum duration and fps were not found.

## Q2. Capability details per model (duration, resolution, audio, controls, editing, watermarking)

### Takeaway
Native synchronized audio is now standard across leading closed models; the exceptions are Midjourney and possibly Veo 3.1 Lite. Maximum single-pass length has moved from 8–10 s (2025) to 15–30 s (2026: Seedance 2.5, Wan 3.0, Grok via extend). Multi-reference conditioning and in-place editing (V2V, frame-local edits) are the new differentiators.

### Cited Findings
| Model (date) | Max duration | Resolution | Native audio | Notable controls / editing | API price |
|---|---|---|---|---|---|
| Veo 3.1 Std/Fast | n/v | 720p/1080p/4K | Yes (per Google positioning; not re-verified) | — | $0.40–0.60/s Std; $0.10–0.30/s Fast — [Puter](https://developer.puter.com/tutorials/gemini-api-pricing/) |
| Veo 3.1 Lite (GA 2026-04-23) | 8 s, 24 fps | 720p/1080p | Conflicting | T2V, I2V; no extension; SynthID — [CometAPI](https://www.cometapi.com/what-is-google-veo-3-1-lite/) | $0.05–0.08/s |
| Gemini Omni Flash (I/O, May 2026) | 10 s | 720p priced | Yes | Any-input, conversational V2V editing; SynthID — [WaveSpeed](https://wavespeed.ai/blog/posts/gemini-omni-flash-shipped-what-actually-launched/) | ~$0.10/s — [eesel](https://www.eesel.ai/blog/gemini-omni-1-1-flash-pricing) |
| Seedance 2.0 | 15 s | 480p–4K | Yes, free | Multimodal refs — [anikuku](https://anikuku.com/blog/seedance-2-api-pricing-guide-2026) | $0.04–0.78/s |
| Seedance 2.5 (2026-07-31) | 30 s | 480p/720p (API) | Yes | 50 refs, frame-local edit, extension — [Kie.ai](https://kie.ai/blog/seedance-2-5-release-deep-dive) | n/v |
| Kling 3.0 (2026-02-06) | n/v | up to 4K | Yes (5 langs) | Multi-shot storyboard, Motion Brush, character consistency — [AtlasCloud](https://www.atlascloud.ai/blog/tips/kling-3.0-review-features-pricing-ai-alternatives) | $0.112–0.42/s |
| Wan 3.0 (2026-08-24) | 30 s | 480p–1080p | Yes (audio input; output audio not confirmed) | Doc-to-video, any-input — [Alibaba Cloud](https://www.alibabacloud.com/blog/wan3-0-30-second-ai-video-generation-from-any-input_603452) | $0.05–0.20/s |
| Runway Gen-4.5 (API 2026-02-10) | 10 s | 1080p (per review) | Yes, since May 2026 | Aleph 2.0 V2V ($0.28/s) — [Runway changelog](https://docs.dev.runwayml.com/api-details/api_changelog/) | $0.12/s |
| Luma Ray3.14 / 3.2 | n/v | 1080p, HDR/EXR | n/v | "Reasoning," HDR — [magichour](https://magichour.ai/blog/luma-dream-machine-pricing) | ~$0.24/s (1080p SDR) |
| Grok Imagine 1.5 | 15 s (30 s w/ extend) | 480p/720p | Disputed | 6 aspect ratios — [apiframe](https://apiframe.ai/guides/grok-video-api-guide) | $0.05–0.08/s |
| Sora 2 Pro (RETIRED 2026-09-24) | — | up to 1080p | Yes | — | was $0.30–0.70/s — [OpenRouter](https://openrouter.ai/openai/sora-2-pro) |

(n/v = not verified in this session)

- Watermarking: Google applies SynthID to Veo 3.1 Lite and Gemini Omni Flash outputs. — [CometAPI](https://www.cometapi.com/what-is-google-veo-3-1-lite/); [WaveSpeed](https://wavespeed.ai/blog/posts/gemini-omni-flash-shipped-what-actually-launched/)

### Inferences
- The Luma per-second figure (about $0.24/s) is derived from "$1.20 per 5 s 1080p SDR."
- Products that need clips longer than 15 s in one pass have closed options in Seedance 2.5 and Wan 3.0. Others need extension or stitching.

### Gaps
- Watermark / C2PA policies for Kling, Seedance, Wan, Runway, Luma and xAI were not verified.
- fps values were not found for most models other than Veo 3.1 Lite and H3 (24 fps).

## Q3. Leaderboard standing (Artificial Analysis Video Arena, LMArena video)

### Takeaway
The leaderboards are volatile, and the snapshots I found conflict. Each has a different version (AA moved to "AA-Video-T2V v2.0") and an audio/no-audio split, so Elo values cannot be compared across snapshots. The recurring leaders in mid-to-late 2026 are Gemini Omni Flash, MiniMax H3 (open-weight), Seedance 2.0/2.5, Wan 3.0, HappyHorse and Kling 3.0. Veo 3.1 has fallen to around #11, and Sora is absent.

### Cited Findings
- AA-Video-T2V v2.0, latest snapshot from search (around Sep/Oct 2026): 1. Alibaba Wan 3.0 (1157±9). 2. Utopai Studios Utopai X (1150±10). 3. ByteDance Dreamina Seedance 2.5 (1143±9). 4. MiniMax H3 768p (1138±9). Also listed are "FLUX 3" (1128), Seedance 2.0 (1118), Gemini Omni Flash 1.1 (1114) and SkyReels V4 (1074). — [Artificial Analysis T2V leaderboard](https://artificialanalysis.ai/video/leaderboard/text-to-video) (search extract; direct fetch blocked)
- AA-Video-I2V v1.0, October 2026 per extract: 1. MiniMax H3 Max (1195). 2. MiniMax H3 (1181). 3. Gemini Omni Flash (1178). 4. Dreamina Seedance 2.0 720p (1176). 5. HiDream-O1-Video-1.0 (1175). — [Artificial Analysis I2V leaderboard](https://artificialanalysis.ai/video/leaderboard/image-to-video)
- AA T2V with audio, earlier 2026 snapshot (around August): Gemini Omni Flash 1245, MiniMax-H3 1242, Seedance 2.0 1225. Veo 3.1 was #11 (1098, August 2026), below all four Kling 3.0 variants, and Kling 3.0 1080p Pro was #9 (1105). — [ocdevel](https://ocdevel.com/mlg/mla-26); [invideo (Aug 2026)](https://invideo.io/blog/best-ai-video-model/)
- HappyHorse-1.0 was reported #1 on AA for T2V (1333) and I2V (1392), beating Seedance 2.0 by 60 and 37 points. The date was earlier in 2026 and comes from a press release. — [Tullahoma News](https://www.tullahomanews.com/?p=103570)
- LMArena / arena.ai text-to-video: one extract says "Kling v3" leads with a score of 1934, followed by Happy Horse 1.0 (1816) and Seedance 2.0 Fast (1747), as of October 2026. This is low confidence because the scale differs from other arenas and the aggregator extract conflated LMArena with AA. — [arena.ai T2V leaderboard](https://arena.ai/leaderboard/text-to-video); [llm-stats](https://llm-stats.com/leaderboards/best-ai-for-video-creation)
- Sora no longer appears on the leaderboards. — [ocdevel](https://ocdevel.com/mlg/mla-26)

### Inferences
- These conflicts most likely reflect board versions (v1 vs v2), audio vs no-audio splits, and different dates, not true contradictions. The report should present them as ranges or tiers rather than one ranking.
- Western closed models (Veo 3.1, Runway, Luma) no longer top any arena found. Google's top-tier entry is now Gemini Omni Flash.

### Gaps
- I could not access a single authoritative, dated AA snapshot because the site was blocked. Exact current ranks and prices per minute from AA are unverified.
- Utopai X details (creator, API, open or closed) were not found.
- "FLUX 3" appearing on the AA video board is unverified and may be an extraction error.

## Q4. Trends: native audio, longer clips, world models, real-time

### Takeaway
2026 trends: (1) audio+video in one pass is now table stakes; (2) single-pass length has jumped to 30 s; (3) any-input, conversational editing (Gemini Omni, Seedance 2.5, Wan 3.0 doc-to-video); (4) interactive real-time world models are still consumer previews without APIs (Genie 3); (5) prices are falling fast, with sub-$0.10/s tiers (Veo 3.1 Lite, Grok, Wan 480p, Runway Turbo); (6) a Western lab has exited (Sora).

### Cited Findings
- Genie 3 (DeepMind, announced August 2025) generates interactive worlds in real time at 720p, 24 fps, with about 1 minute of memory. Project Genie gave access to US Google AI Ultra subscribers from 2026-01-29. There is no public API. — [Wikipedia: Genie](https://en.wikipedia.org/wiki/Genie_(world_model)); [Wikipedia: Project Genie](https://en.wikipedia.org/wiki/Project_Genie_(website)); [DeepMind Genie 3](https://deepmind.google/models/genie/); [fenxi.fr](https://fenxi.fr/en/blog/genie-3-google-deepmind-world-model-2/)
- Real-time avatars are a priced API product: Tavus charges $0.37 per conversation minute, and HeyGen sells LiveAvatar separately. — [veed.io](https://www.veed.io/learn/best-talking-head-video-apis); [realtimeavatar.ai](https://realtimeavatar.ai/blog/heygen-api-pricing-explained)
- 30 s single-pass models arrived: Seedance 2.5 (2026-07-31) and Wan 3.0 (2026-08). — [Kie.ai](https://kie.ai/blog/seedance-2-5-release-deep-dive); [Alibaba Cloud](https://www.alibabacloud.com/blog/wan3-0-30-second-ai-video-generation-from-any-input_603452)
- Runway added native audio and audio editing to Gen-4.5 in May 2026. Kling 3.0 added it in February 2026. — [veo4.dev](https://veo4.dev/runway-gen-4-5); [AtlasCloud](https://www.atlascloud.ai/blog/tips/kling-3.0-review-features-pricing-ai-alternatives)

### Inferences
- A new product should design for audio-on-by-default, 10–30 s clips, reference-image or character consistency, and an editing loop rather than one-shot generation.
- Interactive world models are not yet buildable through closed APIs as of October 2026.

### Gaps
- No verified data on real-time / streaming video generation APIs other than avatars (for example, low-latency generative video from Decart or Odyssey was not researched).
