# Still-Image Character / Body Swap: best pipeline (as of 2026-10-05)

Scope: replace a real, consenting person in a photo with a designed AI or fictional character. The character has to match the person's pose, proportions/contour, framing, perspective and lighting, and the real background stays untouched. Also covers deliberate silhouette, build and costume edits. Not covered: NSFW, face-swap-for-impersonation.

Method: web search snippets (about 25 calls). Many primary pages (arena.ai, artificialanalysis.ai, invideo.io, moda.app, orcarouter.ai) were **blocked by the egress proxy**, so the leaderboard and price figures come from search snippets and third-party aggregators. Tags: **[V]** = confirmed in a snippet from a vendor or a credible aggregator; **[U]** = unverified, conflicting or from memory. Re-check prices on the vendor pages before you commit.

---

## 0. TL;DR recommendation

**Hosted, best quality:** use a two-pass approach, "generative edit, then deterministic composite".
1. Build a **pose/depth/mask package** from the source photo yourself (SAM 3 mask, SDPose/DWPose skeleton, Depth Anything 3 depth).
2. Send source photo + character reference(s) to a frontier multi-reference editor with a role-numbered prompt ("image 1 = scene/pose, image 2 = character"):
   - Primary: **GPT Image 2 / GPT Image 2.5 Sunburst** (top of every edit arena in Sept 2026).
   - Alternatives: **Nano Banana Pro** (best per-character identity references) and **MAI-Image-2.6** or **Seedream 5.0 Pro** (cheap, strong).
   - **FLUX.2 Max/Pro** when you want BFL's multi-ref plus an open-weights fallback in the same family.
3. **Never trust the model's background.** Re-composite the original background pixels over the output, using the dilated union of the old-person mask and the new-character mask. Then run a harmonize/relight pass on the seam band only, and a detailer + upscale pass.

**Self-hosted, commercially clean:**
- **Qwen-Image-Edit-2511** (Apache-2.0), multi-image edit with the character ref + pose/depth control. Or **Qwen-Image + InstantX ControlNet-Union** (Apache-2.0) as a masked inpaint.
- **Z-Image-Turbo + Fun ControlNet Union 2.x** (inpaint + pose + depth) as a fast alternative.
- Supporting models: character LoRA trained on a turnaround set, SAM 3 masks, SDPose/DWPose, DA3 (Base/Large, Apache), SeedVR2 (Apache) upscale, and a compositing step.
- Avoid in a commercial product unless you pay for a license: FLUX.2 dev / Klein 9B (non-commercial), Qwen-Image-2.1 (research license), LBM relighting (CC BY-NC), DA3 Giant / Nested (CC BY-NC).

---

## 1. Hosted / closed editing models (status Oct 2026)

### Leaderboards (snippets, Sept 2026)
- **arena.ai Single-Image Edit** [V-snippet]:
  1. GPT Image 2.5-Sunburst 1522
  2. GPT Image 2.5-Flare 1478
  3. GPT Image 2 (medium) 1461
  4. MAI-Image-2.6 (#6) 1427
  5. Seedream-5.0-Pro (#9) 1394
  6. Nano Banana Pro 2k (#11) 1390
  7. Nano Banana 2 (#13) 1387

  Sources: https://arena.ai/leaderboard/image-edit (blocked; via search snippet). There is a separate Multi-Image Edit board at https://arena.ai/leaderboard/image-edit/multi-image-edit; it was blocked and I could not read its rankings **[U]**.
- **Artificial Analysis AA-Image-Editing v2.0**: GPT Image 2.5 Sunburst (max) is #1 at 1197. The best open model is about 161 points behind. Open-weights leaders: Qwen-Image-2.1 (1073), HunyuanImage 3.0 Instruct (1066), Qwen Image Edit Plus 2511 (1021) [V-snippet]. https://artificialanalysis.ai/image/leaderboard/editing/open-weights
- AA text-to-image arena: GPT Image 2 #1 with the largest #1–#2 gap ever recorded (Sept 2026) [V-snippet]. https://tech-insider.org/best-ai-image-generator-2026/

### Model-by-model

| Model | Date | Multi-ref | API / price (snippets) | Notes for character swap |
|---|---|---|---|---|
| **GPT Image 2** (`gpt-image-2`) | 2026-04-21 | up to 10 refs on edit endpoint (aggregator) | OpenAI API + Azure Foundry. Token priced: image in $8/1M, text in $5/1M. ≈$0.211 per 1024² high quality | Best instruction-following for "image 1 = scene, image 2 = character". Keeps composition well but **re-renders the whole frame** (subtle background drift), so composite the background back. Emits C2PA. |
| **GPT Image 2.5 Flare / Sunburst** | Sept 2026 (bifrost lists `gpt-image-2.5-flare-2026-09-08`) | same family | Aggregators: ≈$0.053 per 1024² high for both | **#1 edit arena (Sunburst = "precision tier for edits").** **[U] Conflict:** WaveSpeed says OpenAI's docs still list only gpt-image-2, with no 2.5 model page. Evolink and others resell 2.5. Confirm official API availability before building on it. |
| **Nano Banana Pro** (Gemini 3 Pro Image) | late 2025 | up to 6 object refs + **5 character refs** (identity slots) | Google API: $0.134 at 1K/2K, $0.24 at 4K | Best dedicated identity-reference handling. Strong "take the soul of image A, the skeleton of image B" pose transfer. SynthID + C2PA. Native 4K. |
| **Nano Banana 2** (Gemini 3.1 Flash Image) | 2026 | up to 10 object refs | $0.045 (0.5K) / $0.067 (1K) / $0.101 (2K) / $0.151 (4K) | Cheap iteration tier. Lite variant takes no character refs. |
| **Seedream 5.0 Pro** (ByteDance) | 2026-07-08 | up to 10 refs | $0.045 (≤2.36MP) / $0.09 (above). +$0.003 per extra ref | Good value, strong multi-ref fusion. Ranks just above Nano Banana Pro on the edit arena. |
| **MAI-Image-2.6 / 2.6-Flash** (Microsoft) | 2026-09-04 | up to 5 refs | Azure Foundry + OpenRouter. $5/1M text in, $8/1M image in, $38/1M image out (≈4¢/image per runtimewire). Flash: $1.75 / $2.50 / $19 | MAI-Image-2.5 (2026-06-02) was strong at "surgical, localized edits". 2.6 is #6 on the edit arena. Good for keep-everything-else-identical edits. |
| **FLUX.2 Max** (BFL) | 2025-26 | up to 8 refs (API) / family supports 10 refs, 4MP | $0.07 for the first output MP + $0.03 per additional MP; ref input $0.03/MP | Highest-quality FLUX. The **FLUX.2 Pro / Flex** price was not confirmed in snippets **[U]**. FLUX.1 Kontext Pro/Max (2025) are older but cheap single-ref editors. |
| **FLUX.2 Klein 4B** | Jan 2026 | multi-ref | $0.014 for the first MP | Apache-2.0 open weights too (see §2). |
| **Qwen-Image-Edit (2511) / Qwen-Image-2.1** | 2511: Dec 2025; 2.1: 2026-09-20 | 2511: 1–3 images optimal. 2.1: up to 10 refs + painted masks / circles | Alibaba Cloud, Replicate (`qwen-image-edit-plus`), Runware | 2.1 is the top open-weights editor, but its weights are a **research license**. Commercial use goes through the hosted API. |
| **HunyuanImage 3.0 Instruct** (Tencent) | Jan 2026 | multi-image | Tencent / third-party | #2 open-weights editor. Tencent community license: check territory exclusions (EU/UK/KR in prior Hunyuan licenses) **[U]**. |
| **Reve 2.1 edit** | 2026 | ref images | v2 Edit ≈150 credits ≈$0.20 (eesel). Atlas Cloud resells | Good aesthetics. Less evidence of being best at pose-locked swaps. |
| **Higgsfield** (face swap / character swap apps) | 2026 | 2 images (base + face) | Subscription. Free tier 5 swaps/day | **Face-level** swap, one face per operation. Reviewers note a "plastic" look and **jaw-edge halos** on hard frames. Not a full-body character replacement. Higgsfield also wraps third-party models (Nano Banana, Seedream, GPT Image) behind its apps and can serve as an aggregator. |

### Which handles "replace the person with this character, keep pose and background" best?
1. **GPT Image 2.5 Sunburst / GPT Image 2.** Best overall instruction-following and edit-arena Elo, and up to 10 refs. Weakness: re-renders the whole canvas (micro-drift and a slight color shift on the background), and gives limited hard mask control. Fix: composite.
2. **Nano Banana Pro.** Its explicit character-reference slots give the best identity lock on a *designed* character (costume details, face, palette). Also very good at pose transfer and native 4K. Nano Banana 2 for drafts.
3. **MAI-Image-2.6 / Seedream 5.0 Pro.** Strong value and multi-ref. MAI is notably good at localized edits.
4. **FLUX.2 Max / Pro.** Solid multi-ref, and you can share a prompt style / LoRA ecosystem with self-hosted FLUX.2.

Practical routing for a product: **run 2–3 models in parallel → auto-score (pose keypoint error vs source skeleton, mask IoU, background SSIM outside the mask, character-ID similarity vs ref) → pick the best → composite → polish.**

### Prompt pattern (works across GPT Image / Nano Banana / Seedream / MAI)
```
Image 1 is the scene. Image 2 (and 3) is the character reference.
Replace the person in image 1 with the character from image 2.
Keep exactly: the pose, limb positions, head angle, body scale and position in frame,
camera angle/lens perspective, and everything outside the person (background, objects,
other people) unchanged pixel-for-pixel. Match the scene's lighting direction, color
temperature, shadows and contact with the ground. Character's costume/silhouette:
<explicit spec, e.g. "broader shoulders, armored pauldrons, cape to knees">.
```
- Number the images and give each one a single role (GPT Image guidance).
- If proportions must change (a bulkier or taller character), say so explicitly and expect to re-plate the background where the old silhouette was larger. You need **inpaint/clean-plate the old body** before compositing (see §2 step 3).
- Optionally pass the skeleton or depth render as an extra reference image ("follow the pose in image 3"). Frontier models generally respect this, with less precision than a ControlNet **[U]**.

Sources: https://mcpservers.org/de/agent-skills/doany-ai/face-swap (GPT Image 2 edit, 10 refs, role numbering) · https://gate.ai/blog/gpt-image-2-openai-specs-pricing-api-use-cases · https://dreamina.capcut.com/resource/gpt-image-2-release-date · https://wavespeed.ai/blog/ai-news/gpt-image-2-5-what-we-know/ · https://evolink.ai/blog/gpt-image-2-5-release-date · https://www.getmaxim.ai/bifrost/model-library/compare/openai/gpt-image-2.5-flare-2026-09-08.md · https://www.atlascloud.ai/blog/guides/nano-banana-pro-vs-nano-banana-2 · https://magichour.ai/blog/nano-banana-2-vs-nano-banana-pro-we-benchmarked-both-on-the-same-5-prompts · https://chasejarvis.com/blog/change-the-pose-of-any-photo-with-nano-banana-weavy/ · https://empiriolabs.ai/models/seedream-5-0-pro · https://evolink.ai/blog/how-to-use-seedream-5-0-pro-multi-reference-image-generation · https://microsoft.ai/news/pushing-the-quality-cost-frontier-with-mai-image-2-6 · https://techcommunity.microsoft.com/t5/microsoft-foundry-blog/mai-image-2-6-and-mai-image-2-6-flash-quality-and-speed-at/ba-p/4550970 · https://runtimewire.com/article/microsoft-mai-image-2-6-foundry-four-cents-image · https://www.3daistudio.com/blog/mai-image-2-5-microsoft-image-model-explained · https://openrouter.ai/black-forest-labs/flux.2-pro/providers · https://api3.shopot.ai/black-forest-labs/flux.2-max · https://www.eesel.ai/blog/reve-2-1-pricing · https://www.atlascloud.ai/blog/guides/higgsfield-face-swap · https://llm-stats.com/leaderboards/best-ai-for-image-editing

---

## 2. Open-weight ComfyUI pipeline ("pose-aware masked replacement")

### Stage map

```
source photo ──► [1] analysis: SAM3 person mask, matting (BiRefNet), SDPose/DWPose skeleton,
                    DA3 depth (+normals), light estimate
             ──► [2] mask build: union(old person, planned new silhouette) → dilate 8–24 px → feather
             ──► [3] (if silhouette shrinks/changes) clean-plate the background (object-removal inpaint)
             ──► [4] generation: inpaint with character identity + pose/depth control
             ──► [5] identity/detail: face/hand detailers, character-LoRA second pass at low denoise
             ──► [6] relight/harmonize the character to the plate; contact shadows
             ──► [7] upscale (SeedVR2 / tiled) → [8] composite original background back → grade → C2PA sign
```

### [1] Segmentation, matting, pose, depth
- **SAM 3** (Meta, Nov 2025): text-prompted concept segmentation ("person"), also used as SAM3.1 in 2026 ComfyUI workflows. License: **SAM License** (royalty-free, allows derivatives; has a patent-litigation termination clause). Commercial use appears permitted, but have legal review it. SAM 2 is Apache-2.0. https://huggingface.co/facebook/sam3/blob/main/LICENSE · https://scancode-licensedb.aboutcode.org/sam-2025-11-19.html
- **GroundingDINO + SAM 2**: older text-to-box-to-mask chain (Apache). SAM 3 largely replaces it.
- **BiRefNet** (MIT): high-resolution matting for hair and fine edges. Use it to refine the alpha of the final composite edge.
- **Pose**: **DWPose** (Apache; 133-pt wholebody) is the ComfyUI default. **SDPose** (2025, diffusion-prior wholebody, 133 kpts) is more robust out-of-domain (costumes, stylized, occlusion); 2026 RunComfy workflows pair it with SAM3.1. **Sapiens** (Meta) gives pose + depth + normals + body-part seg at high resolution, but under a CC BY-NC-style license **[U: verify]**. OpenPose (CMU) is non-commercial unless licensed. https://huggingface.co/ViktorLin/SDPose-Wholebody · https://www.runcomfy.com/zh-TW/comfyui-workflows/character-pose-background-replacement-v3-comfyui-pose-character-background-swap
- **Depth**: **Depth Anything 3** (Nov 2025). **Small / Base / Mono-Large / Metric-Large are Apache-2.0. Giant and the recommended Nested Giant-Large are CC BY-NC 4.0.** Depth Anything V2 Small is Apache; Base/Large/Giant are CC BY-NC. For commercial use, choose DA3 Mono/Metric-Large. https://huggingface.co/depth-anything/DA3NESTED-GIANT-LARGE · https://comfyui-wiki.com/en/models/depth-anything/depth-anything-3
- Metric depth plus a ground-plane estimate lets you check scale/perspective (the character's feet must land on the same depth plane, and the head height must match the camera height).

### [2] Mask strategy (most quality is won here)
- New mask = union(original-person mask, target-silhouette mask). For a bigger or caped character, paint or extrude the target silhouette: scale the SAM mask, or draw it in the UI.
- Dilate 8–24 px (scale with image resolution), then feather/blur 4–12 px. Keep a **separate tight alpha** (BiRefNet on the *output*) for the final composite, so the generative seam sits inside the character's own matte.
- Use crop-and-stitch (e.g. ComfyUI "Inpaint Crop & Stitch" nodes) so the model works at native resolution around the person, then pastes back. This avoids degrading the full frame.

### [3] Clean plate when the silhouette shrinks or moves
If the old body extends beyond the new character (for example, swapping a large person for a slim elf), first remove the person and in-fill the background. Options: Qwen-Image-Edit "remove the person", FLUX Fill / Kontext, Z-Image inpaint, or LaMa/MAT for simple backgrounds. Then generate the character on the clean plate.

### [4] Generation backbones (pick one)
| Option | License | How |
|---|---|---|
| **Qwen-Image-Edit-2511** (20B MMDiT) | **Apache-2.0** | Multi-image edit: image1 = masked/cropped scene, image2 = character ref (1–3 inputs optimal). The 2509+ line has native ControlNet-style conditioning (depth, edge, keypoint map as an input image) and better person consistency. 2511 adds less drift, better multi-person consistency, and built-in popular LoRAs. GGUF quantizations exist (unsloth). **Best commercially-clean self-hosted choice.** https://huggingface.co/qwen/qwen-image-edit-2511 · https://www.alibabacloud.com/blog/qwen-image-edit-2511-improve-consistency_602762 · https://blog.comfy.org/p/wan22-animate-and-qwen-image-edit-2509 |
| **Qwen-Image + InstantX ControlNet-Union** (canny, soft-edge, depth, pose) + DiffSynth inpaint patch | Apache-2.0 | Classic masked inpaint with pose + depth control. Pair with a character LoRA trained on Qwen-Image. https://huggingface.co/InstantX/Qwen-Image-ControlNet-Union |
| **Z-Image-Turbo + Fun ControlNet Union 2.0/2.1** (alibaba-pai) | Z-Image Apache-2.0 **[U: check Fun-CN license]** | One ControlNet for canny/HED/depth/pose/MLSD **plus inpaint mode**. Very fast (few steps). Good for production throughput. https://cnb.cool/ai-models/alibaba-pai/Z-Image-Turbo-Fun-Controlnet-Union-2.1 |
| **FLUX.2 dev (32B) / Klein 9B** | **FLUX Non-Commercial** (outputs usable commercially, but *serving the model* in a product needs a paid BFL license) | Native multi-ref (up to 10) plus pose via ref image. Top open quality before Qwen-Image-2.1. https://canirun.ai/license/flux-non-commercial/ |
| **FLUX.2 Klein 4B** | **Apache-2.0** | Small, fast, multi-ref. Fine for drafts or a 2nd pass. https://help.bfl.ai/articles/8642316687-flux-2-klein-fast-generation-guide |
| **Qwen-Image-2.1** (7B, 2026-09-20) | **Qwen Research License (non-commercial)** | #1 open-weights editor. Up to 10 refs, painted-mask/circle region editing, native RGBA output (handy: it can output the character on transparent for compositing). Day-0 ComfyUI. Commercial use only via the API or a separate agreement. https://runtimewire.com/article/alibaba-qwen-image-2-1-transparent-editing-research-license · https://www.eesel.ai/blog/qwen-image-2-1 |
| **HunyuanImage 3.0 Instruct** | Tencent community license **[U]** | Very large (80B MoE). Heavy to self-host. |
| Legacy: **FLUX.1 Fill / Kontext dev, ACE++, In-Context LoRA** | FLUX.1-dev non-commercial. ACE++ / IC-LoRA inherit FLUX.1-dev | 2025-era reference inpainting: put the char ref beside the scene in a diptych, mask the right half. Superseded by native multi-ref editors, but still useful with FLUX.1 LoRA stacks. |

Recommended settings pattern: Qwen-Edit or Z-Image inpaint at denoise 1.0 inside the mask with pose ControlNet (strength 0.6–0.9, end at 0.6–0.8 of steps) + depth (0.3–0.5). Then run a second, low-denoise (0.25–0.4) pass with the character LoRA for identity. **[U: heuristics; tune per model]**

### [5] Identity / character consistency
- **Character LoRA** (strongest for a *designed* character; see §3). Train it on the same base you inpaint with (Qwen-Image, Z-Image, FLUX.2 Klein 4B for Apache-clean).
- **Multi-ref editors** (Qwen-Edit 2511, FLUX.2) often make adapters unnecessary.
- **Adapters** (mostly for FLUX.1/SDXL, so ecosystem age matters):
  - PuLID-FLUX (Apache code; depends on InsightFace antelopev2 models, which are **non-commercial** **[U]**).
  - IP-Adapter (Apache).
  - InfiniteYou (ByteDance, FLUX.1-dev based → non-commercial base).
  - Face-ID adapters target *real* faces; for a fictional character, a LoRA + ref image is better.
- **Detailers**: Impact-Pack FaceDetailer / hand detailer (YOLO face/hand detectors; Ultralytics YOLO is **AGPL**, so check before shipping). Re-inpaint the face and hands at higher resolution with the LoRA, plus MeshGraphormer or DWPose-hand depth for hands.

### [6] Relight / harmonize
- **IC-Light v1** (SD1.5, Apache code; lllyasviel). **IC-Light v2** (FLUX-based) is **not open-sourced**; it is available only via the HF Space and fal.ai API (custom "IC-Light V2 License"). https://comfyui-wiki.com/en/models/ic-light/ic-light-v2
- **LBM relighting** (Jasper AI, Latent Bridge Matching, 2025): fast one-step relight / harmonization, **CC BY-NC 4.0, so non-commercial only**. https://comfyui-wiki.com/en/tutorial/advanced/image/relighting/lbm-relighting
- Commercially-safe practice: let the generative inpaint itself do the lighting. Feed the scene context (crop with generous margin) and state the light direction and color temperature in the prompt. Then:
  - Match color in compositing: luminance/color histogram matching inside the character matte to a ring of background around it, LAB mean/std transfer, plus grain/noise and lens-blur matching.
  - Generate contact shadows with a low-denoise inpaint of a ground band under the feet. Or build an analytic shadow: project the matte along the estimated light direction onto the DA3 ground plane, then multiply-blend and blur.
- A frontier hosted model at low strength ("harmonize lighting of the character with the scene; change nothing else") also works as a harmonizer, followed by re-compositing the background.

### [7] Upscale
- **SeedVR2** (ByteDance-Seed, 3B/7B, **Apache-2.0**). One-step diffusion restoration and upscale for images and video, with native ComfyUI support. It is the current best faithful upscaler. https://docs.comfy.org/tutorials/utility/seedvr2
- **SUPIR**: strong, but SDXL-based with a non-commercial license on the SUPIR weights **[U]**.
- Tiled diffusion upscale (Ultimate SD Upscale with your base + LoRA at 0.2–0.35 denoise) for adding detail.
- Upscale **only the character crop**, then composite. Never re-upscale the real background (it should stay original).

### [8] Final composite (deterministic)
`out = bg_original * (1 - α) + gen * α`, where α = BiRefNet/SAM matte of the *new* character ∪ the shadow layer, feathered 1–3 px at the final resolution. Then apply a global grade match (film grain, chromatic aberration, vignette consistent with the source) and finally write C2PA.

### Related 2026 ComfyUI references
- RunComfy "Character & Pose & Background Replacement V3" (Wan2.2 Animate + SAM3.1 + SDPose; video, but the masks/pose are reusable on a single frame). https://www.runcomfy.com/zh-TW/comfyui-workflows/character-pose-background-replacement-v3-comfyui-pose-character-background-swap
- RunComfy MiniMax H3 character replacement (SAM3 detect → masks → ref-guided). https://www.runcomfy.com/comfyui-workflows/minimax-h3-character-replacement-comfyui-sam3-ref2va
- sam3 SmartInpainter node. https://floyo.ai/all-comfyui-nodes/sam3_smartinpainter-majidfida

### License cheat-sheet (commercial product, self-hosted serving)
- **OK (Apache/MIT):** Qwen-Image, Qwen-Image-Edit 2509/2511, InstantX Qwen ControlNet-Union, Z-Image(-Turbo), FLUX.2 Klein 4B, SAM 2, DA3 Small/Base/Large/Metric-Large, DWPose, BiRefNet, SeedVR2, IC-Light v1.
- **Check:** SAM 3 (SAM License), Z-Image Fun ControlNet, HunyuanImage 3.0 (territory), InsightFace models, Ultralytics (AGPL), SDPose and Sapiens weights.
- **Non-commercial unless licensed:** FLUX.2 dev, Klein 9B, FLUX.1 dev and derivatives (Kontext dev, Fill dev, ACE++, InfiniteYou), Qwen-Image-2.1, LBM, DA3 Giant / Nested, DA-V2 B/L/G, OpenPose, SUPIR.

---

## 3. Character reference preparation
- **Canonical sheet**: front / 3-4 / side / back full-body turnaround, neutral A-pose, flat even lighting, plain mid-gray background. Add a head/expression sheet and close-ups of costume details (emblems, materials). Write a text spec with a hex palette, height in heads (for example 7.5 heads), and build notes.
- Generate the sheet itself with Nano Banana Pro / GPT Image, or with **CharacterSheet** TripleView/QuadView edit-LoRAs (trained on about 300 sheets). Then **split the sheet into individual crops**; models respect separate ref images better than one collage **[U: practitioner consensus]**. https://huggingface.co/Alissonerdx/CharacterSheet
- **Proportion edits**: specify build relative to the source person ("same height and pose, 20% broader shoulders, heavier build"). Give the editor a silhouette or depth hint if the body changes a lot. For pose-locked bulk changes, a modified depth map (inflate the DA3 depth / mask in 2D) plus pose ControlNet is the most controllable open route.
- **LoRA dataset**:
  - 15–30 images for a focused character (20–40 for robustness; under 10 under-trains). For FLUX.2, 20–40 is optimal.
  - Shot mix: about 40–60% close-ups, 25–40% medium, 10–20% full-body.
  - Vary the poses, lighting and backgrounds. Remember the swap needs the character in *arbitrary photographic lighting*, so include relit and in-scene variants (generate them with the hosted multi-ref models, then curate).
  - Caption the variable attributes and use a unique trigger token.
  - Tools: ai-toolkit (Ostris), fal / Replicate FLUX.2 trainers, BFL Klein training docs.

  Sources: https://www.runcomfy.com/trainer/ai-toolkit/z-image-character-lora-dataset-guide · https://www.runcomfy.com/trainer/ai-toolkit/flux-2-dev-lora-training · https://docs.bfl.ai/flux_2/flux2_klein_training_example · https://www.runcomfy.com/comfyui-workflows/consistent-character-creator-4-0-comfyui-flux-2-dataset · https://tech-insider.org/train-custom-ai-image-lora-2026/
- Keep a **locked reference pack per character** (sheet crops + LoRA + prompt spec), versioned, so hosted and self-hosted paths use identical refs.

---

## 4. Quality checklist (automate what you can)
| Issue | Check | Fix |
|---|---|---|
| **Edge halos / fringing** (noted on Higgsfield jaw edges) | Diff the ring band around the matte; look for luminance spikes | Re-matte with BiRefNet on the output, defringe (erode alpha 1px + color decontaminate), feather 1–3px |
| **Ghost of the original person** (old silhouette visible where the new one is smaller) | Old-mask minus new-mask area should match the bg plate | Clean-plate step (§2 [3]) |
| **Background drift** (hosted models re-render everything) | SSIM / LPIPS outside the dilated mask vs the source | Always composite the original background back |
| **Pose fidelity** | DWPose/SDPose on the output vs the source skeleton (PCK / keypoint L2 normalized by torso) | Re-run with pose ControlNet or skeleton as an extra ref |
| **Scale / perspective** | Feet on the DA3 ground plane; head height vs horizon line; lens distortion consistent | Transform the matte or regenerate with a depth control |
| **Shadow / contact** | Shadow direction matches other objects; contact occlusion at the feet / seat | Shadow inpaint band or analytic projected shadow |
| **Color grading / light** | LAB stats of the character vs the surrounding ring; light direction from normals | Histogram / LAB match, relight pass, grain match |
| **Noise / grain / sharpness / DOF** | Noise-level estimate inside vs outside; blur kernel at the depth of the subject | Add matched grain; apply depth-based lens blur |
| **Hands / fingers** | Hand keypoint count, detector confidence | Hand detailer inpaint with pose + depth for the hands |
| **Identity / costume consistency** | CLIP/DINO similarity to the ref sheet; VLM checklist of costume items | Low-denoise LoRA pass; add detail-crop refs |
| **Occluders** (objects in front of the person: hands holding items, foreground railings) | SAM masks of foreground objects | Re-composite occluders over the character last |
| **Resolution mismatch** | Character sharper or softer than the plate | Upscale the character crop only; match the plate's MTF |

Use a VLM judge (or a human-in-the-loop) on a rubric. Pick the best of N across models and seeds.

---

## 5. Consent, likeness, provenance (brief)
- **Consent**: capture written, specific consent from the photographed person (use of their body/pose/photo, transformation into a character, distribution channels, revocation). Block identity-swap modes that put a *real third party's* face on someone. Here the target is a fictional character, but check that the "character" itself is not a real person or a protected IP character unless licensed. Keep the consent record linked to the asset ID.
- **EU AI Act Art. 50**: transparency obligations have applied since **2 Aug 2026**. "Deepfake" content (realistic manipulation of real persons or scenes) must be disclosed as AI-generated or manipulated, and generative providers must machine-mark outputs. The Code of Practice endorses **multi-layer marking**: signed C2PA metadata + an imperceptible watermark (e.g. SynthID) + optional fingerprinting. Fines run up to €15M / 3%. A swapped real photo with an untouched real background is a prime "manipulated image" case, so label it. https://www.cepic.org/post/the-transparency-obligations-of-the-ai-act-take-effect-on-2-august-2026-what-cepic-members-should-consider · https://c2paviewer.com/articles/eu-ai-content-labels-c2pa · https://blog.pebblous.ai/blog/eu-ai-content-labeling-article-50-provenance/en/
- **C2PA**: OpenAI and Google outputs carry C2PA (Google also carries SynthID). **Compositing / upscaling strips or invalidates upstream manifests.** Your pipeline must **re-sign** the final asset with your own C2PA manifest, listing ingredients (source photo, model outputs) and actions (`c2pa.edited`, digitalSourceType `compositeWithTrainedAlgorithmicMedia`). Self-hosted outputs need your own watermark as well (e.g. an open invisible watermark).
- US: state right-of-publicity and digital-replica laws, plus the federal NO FAKES Act proposals, concern unauthorized replicas. Consenting subjects + fictional characters is low risk, but log consent **[U: no fresh 2026 US status check done]**.

---

## Open uncertainties to verify
1. Official OpenAI API availability and IDs for GPT Image 2.5 Flare / Sunburst (conflicting reports, Sept 2026).
2. The arena.ai **multi-image** edit ranking (blocked), which is the most relevant board for ref + scene swaps.
3. FLUX.2 Pro / Flex per-MP prices; current FLUX.1 Kontext pricing.
4. Licenses for SAM 3 (commercial specifics), Sapiens, SDPose weights, Z-Image Fun ControlNet Union, HunyuanImage 3.0.
5. Whether Qwen-Image-Edit has a post-2511 Apache release in 2026 (none found; Qwen-Image-2.1 changed to a research license).
