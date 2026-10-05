# Video character replacement / motion control: research notes (as of 2026-10-05)

Scope: you have real footage of a consenting performer. You want to replace that person with a designed character who copies their body motion, gestures, facial expression and lip sync. The real background, lighting and camera move stay as shot.

Method and confidence: I researched this through web search on 2026-10-05. The egress proxy blocked direct fetches of most vendor and docs sites: alibabacloud, docs.comfy.org, huggingface.co, arxiv, atlascloud, runway help, aireiter, magichour, dualview and flowith. Only github.com (via raw.githubusercontent) and search snippets were reachable. Most numbers below therefore come from search-result snippets, often from third-party aggregator blogs. Tags used below:
- **[verified]**: read from a primary source I could actually load.
- **[snippet]**: taken from a search snippet.
- **[unverified]**: inference, or the sources conflict.

Re-check prices and limits on the vendor's own page before you build anything on them.

---

## 0. TL;DR recommendation

**The key distinction is replacement versus animation.**
- **Replacement** (also called "mix" or "swap"): the person is regenerated inside the original plate. The background, camera and lighting come from the source.
- **Animation** (also called "move" or "motion control"): a character image is animated with the performer's motion. The background comes from the *character image* or the prompt, not from the plate.

Most "motion control" products, including Kling Motion Control and Wan-Animate-2 as shipped in ComfyUI, are animation mode. If the real background must survive, use a replacement-mode tool, or composite the animated character back onto a clean plate yourself.

**Hosted, best quality for full-body replacement with the real background kept (Oct 2026):**
1. **Runway Aleph 2.0.** Released 2026-05-21. Edit-one-frame-and-propagate, up to 30 s at 1080p, available by API. It is the best general "edit the existing plate" tool: it preserves the camera and background by design. Weaker at exact facial performance. [snippet]
2. **Wan-Animate replacement mode** (Alibaba Model Studio `wan2.2-animate-mix`, plus many API resellers). It is purpose-built for character replacement, re-lights the character to match the scene and keeps the plate. It is cheap. 720p is native and shots longer than about 5 s need chaining. [snippet]
3. **Seedance 2.0 multimodal reference** (ByteDance). Give it a reference video plus character images and it keeps the source camera and motion while swapping in the subject, with no masking. Input is capped at 15 s total. [snippet]
4. **Higgsfield Recast**, a character-swap product that keeps lighting and camera, with voice. It wraps third-party models; the exact backend was not confirmed. [snippet]

**Hosted, best motion fidelity** (character animated *from* an image, background not kept): **Kling 3.0 Motion Control**. Released 2026-03-04/05. Up to 30 s in video-orientation mode, multi-image identity, API from about $0.10–0.16 per second. Use it together with a clean plate and compositing when the plate must survive. [snippet]

**Facial performance and lip sync:** **Runway Act-Two** (5 credits/s = $0.05/s by API). For dialogue close-ups, run a dedicated lip-sync pass afterwards (see §2.6).

**Open-weight, best quality pipeline:**
- **Wan2.2-Animate-14B in replace ("mix") mode** in ComfyUI, with:
  - SAM 3 / SAM 3.1 masks
  - DWPose or SDPose plus face crops
  - the Wan-Animate relight LoRA
  - a character LoRA
- Chain shots in segments of about 77 frames, carrying the last frames forward.
- Then lip-sync (InfiniteTalk video-to-video or LatentSync), SeedVR2 upscale, RIFE/GIMM interpolation, and a matte composite over the original plate (MatAnyone 2).
- **Wan-Animate-2** (open, 2026-08-07, Apache-2.0) is the newer animation-mode model. It needs no pose extractor and reportedly matches Kling Motion Control in user studies. Use it for animation-mode shots or as the "character render" in a composite pipeline. Its replacement-mode support is unconfirmed (see §2.1).

---

## 1. Hosted / closed tools

| Tool (version) | Mode | Keeps real plate? | Max length / res | API & price | Notes / limits |
|---|---|---|---|---|---|
| **Wan2.2-Animate** (Alibaba Tongyi, 2025-09-19; hosted as `wan2.2-animate-move` / `-mix` on Model Studio) | Animation ("move") **and** Replacement ("mix") | **Yes, in mix mode**. Re-lights the character to match scene lighting and tone | 720p @ 24 fps native. About 77-frame (~4.8 s) segments, chained for longer | Alibaba Model Studio plus resellers (muapi, fal, Replicate, etc.). Exact price not retrieved (Alibaba docs blocked) | Single main subject. Hands and fast motion are the usual failure points. Masks drive replacement quality. Sources: comfyui-wiki news 2025-09-19; humanaigc.github.io/wan-animate |
| **Wan-Animate-2** (Tongyi, open release 2026-08-07, arXiv 2608.06009) | End-to-end animation from the driving video. **Text-driven viewpoint control** (output camera decoupled from the driver). Lite / distilled variant for real-time streaming | Not documented as a replacement mode in the README or ComfyUI tutorial **[unverified]** | 720p (8×A800 default), 480p on 2×A800 | Open weights (Apache-2.0). Hosted availability not confirmed | Paper user study: beats open predecessors and roughly matches Dreamina and Kling-MotionControl [snippet: alphaxiv]. GitHub README **[verified]** |
| **Wan 2.7** (early 2026, 27B) | T2V, I2V, R2V, **Video Editing** | Editing mode possibly; not character-swap specific | n/a | Alibaba and resellers | General model; not a dedicated animate mode [snippet]. "Wan 3" is reportedly general T2V/I2V with no animate mode [snippet] |
| **Kling 3.0 Motion Control** (Kuaishou; launched 2026-03-04/05, Day-0 on Higgsfield. One snippet says Kling 3.0 launched "May 2026", which conflicts **[unverified]**) | Animation: character image plus motion video | **No**. Background comes from the character image. Composite to keep the plate | **Video-orientation mode: up to 30 s. Image-orientation mode: up to 10 s.** Standard 720p, Pro 1080p | Official API by credits: Std 5 credits/s (~$0.10/s), Pro 8 credits/s (~$0.16/s). Resellers from about $0.075–0.113/s. Credit packs from $9.80 to $4,200 | Multi-image identity (several angles of the character) improves face consistency over 2.6. "Element binding" only works when the orientation mode matches the video. **With 2+ people it follows the largest person.** Reference clip should be 3–30 s, single subject, no cuts and *no camera movement*. Match framing (full body to full body). Restores the face under occluders (hands, hats). Also on Runway and Vercel AI Gateway (`kling-v3.0-motion-control`) |
| **Kling 2.6 Motion Control** | Same idea, single reference image | No | Up to 30 s (video mode) | Cheaper | Face drifts on big head turns |
| **Runway Act-Two** | Performance transfer: driving video drives a character image *or video* (movement, expression, speech, gestures) | Partly. With a *character video* input, that video's scene is kept | ~30 s | API 5 credits/s; $0.01/credit means **$0.05/s** | Best for facial acting and dialogue. Weaker on full-body athletic motion |
| **Runway Aleph 2.0** (announced 2026-05-21, with "Edit Studio") | Video-to-video edit: text plus keyframe-guided. Edit one frame and it propagates | **Yes**. Designed to keep everything you don't edit, so camera, background and lighting stay | **30 s max, 1080p max** | Web app plus Runway API (also listed on OpenRouter as `runway/aleph-2`). **28 credits/s, about $0.28/s** | Replace subjects, outfits, relight, re-weather, outpaint. Strongest "replace a person in the plate" generalist. Exact lip-sync and microexpression fidelity are not guaranteed **[unverified]** |
| **Luma Ray3 Modify** (2025-12-18). **Ray3.14** (2026-01-26): native 1080p, 4× faster, 3× cheaper | Modify Video with start/end keyframes plus a character reference. Keeps the actor's motion, timing and eye-line | Yes, as a full re-render | **Modify up to 18 s**, 1080p | Dream Machine plus Luma API | Ray3.14 had "no character consistency feature" at launch [snippet: genra.ai]. Good for restyle and wardrobe; identity lock is weaker |
| **Seedance 2.0** (ByteDance, ~Feb 2026; Seedance 2.5 appeared around Sep–Oct 2026 on resellers, details not checked) | Multimodal reference: up to 9 images, up to 3 videos (2–15 s total), up to 3 audio clips plus text. **Character replacement keeps the source motion and camera, no masking** | Yes, by regeneration | ~15 s | BytePlus / Dreamina / ComfyUI API nodes / resellers | Strong identity from multiple refs. The plate is regenerated, not pixel-preserved, so fine background detail may drift **[unverified]** |
| **Higgsfield** (Recast / Character Swap; hosts Kling 3.0 MC, Wan Animate, etc.) | Aggregator plus in-house "Recast": full character swap with synced voice and motion, keeps lighting and camera | Yes (Recast) | Depends on the backend model | Subscription credits. Also MCP tools (`motion_control`, `hf_mult_motion_control`) | A good front-end, but quality depends on which backend model is used |
| **Viggle V4** (JST "3D world model") | Mix (replace a character in a video from an image), Move / Motion Control, multi-character swap, Viggle Live | Mix keeps the scene | Short clips | App plus API | Better non-humanoid shapes (robots, animals, armour) in V4. New controls: Character Refine, Smooth Motion, Foot Lock. Historically softer, more "stylised" quality than Kling or Wan |
| **Autodesk Flow Studio** (ex-Wonder Studio) | **CG** character replacement: tracks the actor, retargets onto *your 3D rig*, matches lighting. Outputs clean plates, alpha, camera track and mocap | **Yes**. Clean plate plus 3D render | Up to 4 actors per shot (Clean Plate tool) | Free / $10 / $45 (6,000 cr, 4K) / $95 (12,000 cr, Wonder Tools) / Enterprise. Wonder 3D gen added Mar 2026 | Needs a rigged 3D character. Deterministic and editable in DCC tools. Best when you need VFX-grade control or a stylised CG character rather than a photoreal generative one |
| **MiniMax Hailuo** (H3 Max / Hailuo 3.0 is the current default; Hailuo 2.3) | S2V subject reference. No dedicated motion-transfer or replace mode found | No | n/a | API | Not a primary tool for this task |
| **DeepMotion** (Animate 3D) | Video to 3D mocap (FBX/BVH), face and hand tracking | n/a (data only) | n/a | Subscription | Use it to feed a CG pipeline (Flow Studio / Blender / Unreal), not for generative replacement. Not re-researched for 2026 **[unverified]** |

**Ranking for "full-body replacement, real background preserved" (my judgement from the sources above):**
1. Aleph 2.0 (plate fidelity, 30 s, 1080p, API).
2. Wan-Animate mix (purpose-built replacement plus relighting; cheapest; open fallback).
3. Seedance 2.0 reference replacement.
4. Kling 3.0 MC plus a clean plate and compositing, when motion fidelity is paramount.

Act-Two is the pick for facial and dialogue performance. Flow Studio is the pick for CG and VFX pipelines.

**Common limits across tools:**
- One main subject. Kling takes the largest person; Flow Studio handles up to 4.
- 5–30 s per generation.
- 720–1080p.
- Typical failures: hands (finger count and contact), self-occlusion and crossing limbs, fast motion blur, props that cross the body, identity drift on big head turns, and camera motion in the *driver* (Kling recommends a locked-off camera).

---

## 2. Open-weight pipeline (ComfyUI)

### 2.1 Core models

**Wan2.2-Animate-14B** (2025-09-19, Apache-2.0). Native in ComfyUI; Kijai's WanVideoWrapper also supports it. [comfy docs via GitHub raw, verified]
- **Mix mode** = replacement. **Move mode** = animation.
- Inputs:
  - reference character image
  - driving video
  - DWPose skeleton
  - face crops (drive expression)
  - character mask (SAM-based; Points Editor node for clicks)
  - background video (the original plate with the person masked out)
- Files:
  - Wan2.2 Animate 14B in FP8 (Kijai) or BF16
  - CLIP Vision H
  - Wan2.1 VAE
  - UMT5-XXL fp8 text encoder
  - **LightX2V I2V distill LoRA** (4-step)
  - **WanAnimate relight LoRA** (`wananimate-relight-lora-fp16`), which matches the character to the plate's lighting in replace mode
- Dimensions must be multiples of 16.
- **Each extension segment is about 77 frames (~4.8 s).** Extend by chaining the "video extend" groups: the batch outputs plus a frame offset carry the last frames forward as motion context.
- Custom nodes: ComfyUI-KJNodes, comfyui_controlnet_aux.

**Wan-Animate-2** (2026-08-07, Apache-2.0, `Wan-AI/Wan2.2-Animate-2-14B`, GitHub Wan-Video/Wan-Animate-2). [verified README]
- End-to-end: it consumes the driving video directly, with **no pose or skeleton extractor**.
- Uses an LLM caption of character appearance and background (Qwen3.7-Plus is suggested).
- Base model takes 40 steps. The distilled model takes 10 steps at CFG 1.0. A Lite variant targets real-time streaming.
- Text-driven viewpoint control.
- Runs at 720p on 8×A800 by default; 480p on 2×A800 was tested.
- Diffusers pipeline is available. ComfyUI files: `wan_animate_2_int8_convrot` / `_bf16` / `_distill_*`, plus the lightx2v LoRA.
- The ComfyUI tutorial describes only "motion transfer from driving video to static character" (animation). **Replacement mode is not confirmed.** For plate preservation, composite its output, or keep Wan2.2-Animate mix.

**Wan VACE (2.1/2.2 Fun VACE):**
- An all-in-one control and inpaint model. Masked video inpainting with pose or depth control plus a reference image is the classic open "replace a person in the plate" route.
- Use it as an alternative or a fix-up pass where Animate fails, for example partial-region repaint of hands.

**Others:**
- UniAnimate-DiT and MimicMotion (older, SD/SVD-era, lower quality).
- One-to-All Animation (alignment-free pose transfer, long-video token replacement).
- SteadyDancer.
- HunyuanVideo-based options: HunyuanCustom / HunyuanVideo-Avatar.
- MoCha (Meta; talking characters).

As of Oct 2026, Wan2.2-Animate and Wan-Animate-2 are the open state of the art. Treat the others as legacy or niche. **[snippet / unverified ranking]**

### 2.2 Pre-processing
- **Segmentation:**
  - **SAM 3** (Meta, Nov 2025): text-prompted concept segmentation and tracking in video.
  - **SAM 3.1** (2026-03-27): "Object Multiplex", shared-memory multi-object tracking.
  - A RunComfy "Character & Pose & Background Replacement V3" workflow already pairs **Wan2.2 Animate + SAM 3.1 + SDPose**.
  - SeC (Segment Concept) masks are an alternative in community "Animate V2" workflows.
- **Matting** (for final compositing, soft edges and hair):
  - **MatAnyone 2**: uv/CLI/HF support added Mar 2026; also in Sammie-Roto 2 from 2026-03-08.
  - **BiRefNet** for per-frame high-res mattes.
- **Pose:** DWPose (default in ComfyUI Animate), **SDPose**, ViTPose, or Sapiens (Meta; best for hands and dense keypoints, but heavy).
- **Face:** the Animate preprocessor crops face images for expression. MediaPipe / InsightFace landmarks for lip-sync masks.
- **Depth:** Depth Anything V2 / Video Depth Anything for VACE control or relighting.
- **Clean plate** (when you composite an *animation-mode* output): Flow Studio Clean Plate, or video inpainting such as Wan VACE / ProPainter / DiffuEraser. Clean plates are needed when the new character's silhouette is smaller than the performer's.

### 2.3 Post-processing
- **Lip sync:**
  - **InfiniteTalk**: audio-driven video-to-video of unlimited length. Re-syncs lips *and* head and body to the audio while keeping identity. Best open option for dialogue.
  - **LatentSync** (ByteDance): mouth-region only, conservative.
  - Hosted: sync.so lipsync-2-pro.
  - Apply it to the face region only and mask it back, so the body motion stays.
- **Relighting:** the WanAnimate relight LoRA in-model; Aleph 2.0 or IC-Light-style passes for shot-level fixes.
- **Temporal consistency:**
  - overlap segments and cross-fade the overlap frames
  - fixed seed and the same reference across segments
  - a character LoRA
  - a low-denoise V2V refine pass (Wan 2.2 low-noise model) to unify texture
  - a deflicker node
- **Upscale:** **SeedVR2** (ByteDance, one-step diffusion restoration; native ComfyUI, "v2.5" workflows), or Topaz Video (Starlight / Proteus) for commercial use.
- **Interpolation:** RIFE (fast) or GIMM-VFI (better on large motion), 16 → 24/48 fps.
- **Compositing:** composite the generated character with its MatAnyone 2 matte over the *original* plate in Nuke, Resolve/Fusion or AE. That keeps background pixels exactly original, including grain. Add re-grain and match the black level.

### 2.4 Hardware / VRAM / length
- Wan2.2-Animate-14B on an RTX 4090 (24 GB) is the practical floor at 720p with FP8 plus block-swap. 480p fits more easily. GGUF Q4/Q5 runs on 10–16 GB at a quality cost (Q3_K at about 6 GB is the bare minimum).
- Production setup: an A100, H100 or RTX 6000-class GPU with 48–80 GB, BF16, no block-swap.
- Accelerators:
  - lightx2v 4-step LoRA: about 4–10× faster, with a slight loss of fine detail. Use full steps for hero shots.
  - SageAttention.
  - FP8.
- Wan-Animate-2 at 720p is tuned for 8×A800 multi-GPU (xDiT / FSDP). Single-GPU use needs the INT8 ComfyUI builds. Speed figures not verified.
- **Long shots:**
  1. Native extension: 77-frame segments with the last N frames carried forward. Quality decays slowly, so re-anchor with the reference image each segment.
  2. Context windows (WanVideoWrapper context options: uniform windows of 81 frames with an overlap of about 16).
  3. Cut on action. Keep each generated shot under about 10–15 s and edit them together.

  Hosted ceilings: Kling 30 s, Aleph 30 s, Luma 18 s, Seedance 15 s.

---

## 3. Character consistency
- **Reference image:** one clean, front-facing, full-body, neutral (A-pose) image, evenly lit, on a plain or neutral background, at the *same framing* as the driving footage. For replace mode, match the performer's proportions and silhouette (height, limb length, bulk). Big mismatches cause warping or leftover performer pixels.
- **Turnaround / multi-view:**
  - Generate a turnaround sheet (front, 3/4, side, back) plus face close-ups and expression sheets with Nano Banana Pro, GPT-Image, FLUX.2 Kontext or Qwen-Image-Edit.
  - Kling 3.0 and Seedance 2.0 (up to 9 images) accept multiple references directly.
  - Wan-Animate takes one image, so use a LoRA for multi-view identity.
- **Character LoRA for Wan 2.2:**
  - Tools: **musubi-tuner** (kohya) or **ostris AI-Toolkit**; both support Wan 2.2 T2V/I2V 14B, with a ComfyUI Musubi node available.
  - Data: 15–40 captioned stills (turnaround plus expressions plus lighting variants). A few short clips help motion. Clip lengths follow 4n+1 frames (e.g. 81).
  - Settings: rank 16–32, on a 24 GB GPU or cloud H100.
  - For Wan 2.2's MoE (high-noise and low-noise experts), train or apply on both, or at least on low-noise for identity.
  - Load the LoRA alongside the Animate model. Check compatibility per model: Animate is a separate fine-tune, so test the strength.
- Keep a locked "character bible": hex colours, materials, the reference images, and a seed per shot.

## 4. Shooting the source footage
- **Lighting:** soft, even key light with motivated direction that matches the plate. Avoid hard, strobing or coloured mixed light on the performer. Keep the face lit, with no deep eye-socket shadows, because the expression extractor needs the eyes and mouth.
- **Camera:**
  - Locked-off or smooth moves work best. Kling explicitly wants a static driver camera; replace-mode tools handle moves better.
  - High shutter speed and 1/1000-type settings for fast action, to reduce motion blur that ruins pose and hands.
  - 24–30 fps, 1080p or above. Avoid heavy stabilisation warp and rolling shutter.
- **Framing:** keep the whole body in frame for full-body shots, with headroom and foot room. Keep the same framing as the character reference. One performer per shot, or clearly separated performers.
- **Wardrobe and silhouette:**
  - Fitted clothing in a colour that contrasts with the background. No flowing capes or skirts unless the character has them.
  - Match the character's silhouette (add padding or props of similar volume).
  - Avoid logos and fine patterns (moiré).
  - Hair tied back if the character has a different hairstyle.
- **Occlusion:** avoid crossing arms in front of the face, hands over the mouth, or props passing in front of the body. Keep hands visible and spread, because closed fists and overlapping fingers cause errors. Minimise contact with other people and objects.
- **Audio:** record clean dialogue audio, with a lav and a slate, for later lip-sync passes.
- **Clean plate:** shoot a clean plate at the end of each set-up (same move if motion control or repeatable). This is essential for compositing a slimmer character.
- **Takes:** shoot in 5–15 s beats to match model segment lengths.

## 5. Consent, likeness, provenance (brief)
- **Consent:** get written, informed consent from the performer covering AI transformation, the specific uses, duration and territory, and any voice use. Also cover rights in their *motion and performance*, not only their face. Union productions are bound by SAG-AFTRA digital replica / "digital alteration" terms (consent plus description of intended use plus compensation).
- **Character:** the replacement character must not resemble a real person without that person's rights cleared. Many hosted tools' terms (Kling, Runway, Higgsfield) prohibit real-person impersonation and may apply face filters.
- **Provenance:**
  - Embed **C2PA Content Credentials**. Runway, Adobe and others already sign outputs.
  - Add invisible watermarking (SynthID-style or a vendor watermark).
  - Disclose AI alteration where law or platforms require it: the EU AI Act Art. 50 deepfake transparency obligations apply from Aug 2026; there are US state laws, plus YouTube/TikTok/Meta labelling.
  - Log the inputs (source footage, consent IDs) to support audit.

---

## Sources (accessed 2026-10-05; most via search snippets)
- Wan-Animate (2.2) announcement: https://comfyui-wiki.com/en/news/2025-09-19-wan22-animate ; project page https://humanaigc.github.io/wan-animate/ ; paper https://arxiv.org/pdf/2509.14055
- Wan-Animate-2: https://github.com/Wan-Video/Wan-Animate-2 (README verified) ; https://huggingface.co/Wan-AI/Wan2.2-Animate-2-14B ; https://www.alphaxiv.org/abs/2608.06009
- ComfyUI docs (via GitHub raw, verified): https://raw.githubusercontent.com/Comfy-Org/docs/main/tutorials/video/wan/wan2-2-animate.mdx and .../wan-animate-2.mdx
- Relight LoRA: https://comfy.org/p/supported-models/wananimate-relight-lora-fp16.md
- SAM3.1 + SDPose + Animate workflow: https://www.runcomfy.com/zh-CN/comfyui-workflows/character-pose-background-replacement-v3-comfyui-pose-character-background-swap
- Animate V2 community workflow (SeC mask, continue motion): https://aistudynow.com/comfyui-wan-2-2-animate-v2-sec-mask-continue-motion-detail-enhancer-workflow/
- Wan 2.7: https://blog.picassoia.com/wan-27-latest-alibaba-ai-video-model ; https://gptproto.com/news/what-is-wan-2-7
- Kling MC: https://kling.ai/quickstart/motion-control-user-guide ; https://vercel.com/ai-gateway/models/kling-v3.0-motion-control/faq ; https://higgsfield.ai/blog/kling-motion-control-3 ; https://www.atlascloud.ai/blog/guides/kling-ai-motion-control ; pricing https://anycap.ai/page/en-US/news/kling-ai-review-2026 , https://www.atlascloud.ai/blog/guides/kling-3.0-review-features-pricing-ai-alternatives
- Runway Aleph 2.0: https://runway.com/fr/product/aleph-2 ; https://openrouter.ai/runway/aleph-2/api ; https://postium.ru/runway-vypustila-aleph-2-0/ ; pricing https://creatify.ai/blog/runway-pricing-(2026)-plans-credits-and-what-you-ll-actually-pay
- Luma Ray3 Modify / Ray3.14: https://www.businesswire.com/news/home/20251218399664/en/ ; https://lumalabs.ai/learning-hub/ray314-user-guide ; https://genra.ai/blog/luma-ray3-complete-guide-review
- Seedance 2.0: https://help.scenario.com/articles/7140699840-seedance-2-0-the-complete-guide ; https://blog.comfy.org/p/seedance-20-is-now-available-in-comfyui ; https://www.3daistudio.com/VideoStudio/seedance-2
- Hailuo: https://help.scenario.com/articles/8094765856-minimax-hailuo-video-the-essentials
- Viggle V4: https://viggle.ai/blog/everything-you-need-is-in-viggle-v4
- Higgsfield Recast: https://geo.higgsfield.ai/blog/best-tool-character-morphing-elemental-transformations-ai-video
- Flow Studio: https://www.cgchannel.com/2025/08/autodesk-cuts-prices-of-flow-studio-subscriptions/ ; https://usagepricing.com/blueprint/activity/wonder-dynamics-2026-03-05-launch ; https://digitalproduction.com/2025/03/18/wonder-studio-rebrands-now-flowing-with-autodesk/
- Comparisons: https://magichour.ai/blog/best-ai-tools-to-replace-characters-in-videos ; https://www.dualview.ai/blog/ai-tools/ai-video-editing-models.html ; https://flowith.io/blog/10-best-viggle-ai-alternatives-character-animation-2026/
- SAM 3: https://arxiv.org/pdf/2511.16719 ; MatAnyone 2 / Sammie-Roto 2: https://github.com/Zarxrax/Sammie-Roto-2
- SeedVR2: https://docs.comfy.org/tutorials/utility/seedvr2.md ; InfiniteTalk V2V: https://www.runcomfy.com/models/community/infinite-talk/fast/video-to-video
- LoRA training: https://www.runcomfy.com/trainer/ai-toolkit/wan-2-2-i2v-14b-lora-training ; https://www.runcomfy.com/comfyui-nodes/comfyUI-Realtime-Lora/musubi-wan-lora-trainer ; https://www.spheron.network/blog/fine-tune-flux2-wan-lora-cost-gpu-cloud-2026/

## Open questions / to verify
- Does Wan-Animate-2 have a replacement mode, and is it available hosted on Alibaba Model Studio? What are its prices?
- Current Alibaba `wan2.2-animate-mix` per-second price and maximum duration.
- Kling 3.0 launch-date conflict (March versus May 2026).
- Seedance 2.5 capabilities for reference-video replacement.
- Whether Act-Two has a successor in 2026.
