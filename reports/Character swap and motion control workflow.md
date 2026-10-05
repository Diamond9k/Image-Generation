# Character swap and motion control workflow

*Best-quality pipelines as of October 2026, for still images and video. Based on `research_notes/Character swap and motion control/`.*

> **Read first.** The network proxy blocked most vendor sites during research, so prices, version numbers and limits mostly come from search snippets and third-party resellers. The notes tag each claim as verified, snippet or unverified. Check vendor pages before you budget or build. This guide assumes the person being replaced has consented, and it does not cover adult-content uses.

## Terms

| Term | Meaning | Where it applies |
|---|---|---|
| Body / character swap | Replace a real person in a photo with a designed character, matching their pose, build and lighting, and keeping the real background | Still image |
| Replacement mode | Regenerate only the person inside the original footage; the real background, camera move and light stay | Video |
| Animation mode / motion control | Animate the character image using the performer's motion; the background comes from the character image | Video |
| Pose-aware inpainting | The technique underneath: mask the person, extract skeleton and depth, regenerate inside the mask under those constraints | Both |

The rule behind both pipelines: **no model output is final.** The real background is always restored from the original pixels by compositing. The model only supplies the character.

---

## Part 1: still image (body / character swap)

### Hosted route (best quality)

1. **Prepare the character.** Make 4–8 clean reference images (front, ¾, side, back, face close-up). Export them as separate files, not a single turnaround collage.
2. **Extract controls from the source photo:**
   - **Person mask:** SAM 3, with BiRefNet for hair and edges.
   - **Pose:** DWPose or SDPose skeleton, including hands and face.
   - **Depth:** Depth Anything 3 (Small, Base or Large; these sizes are Apache-licensed).
   - **Mask expansion:** expand the mask by 8–24 px and feather it. If the character is slimmer than the person, fill the vacated background area first.
3. **Generate with 2–3 editors in parallel.** Send the source photo, the character references and optionally the pose image. Number the images by role, for example "Image 1 = scene, Images 2–5 = character; replace the person in Image 1 with the character, keep the exact pose, framing and lighting".

   | Editor | Notes | Approx. price |
   |---|---|---|
   | GPT Image 2 / 2.5 Sunburst | #1 on the single-image edit arena (Sep 2026). Confirm 2.5 is available on OpenAI's own API | ~$0.05–0.21/img |
   | Nano Banana Pro | Strongest identity lock; 5 character-reference slots | $0.134 (1K/2K), $0.24 (4K) |
   | MAI-Image-2.6 | Precise localized edits; Azure Foundry preview | ~$0.04/img |
   | Seedream 5.0 Pro | Up to 10 references | $0.045–0.09/img |

4. **Score the results automatically and keep the best:**
   - pose error against the source skeleton
   - background similarity outside the mask (SSIM/LPIPS)
   - character similarity to the references (embedding)
5. **Composite.** Paste the original background pixels back in, using the mask as a soft matte. Editors redraw the whole frame, so the background always drifts slightly.
6. **Finish:**
   - Relight or blend the seam, and add a contact shadow.
   - Run face and hand detailer passes.
   - Color-match to the plate, and add grain or noise to match the camera.
   - Upscale only the character region (SeedVR2).
   - Re-sign the file with C2PA. Compositing breaks the vendor's original manifest.

### Self-hosted route (ComfyUI, licensed for commercial use)

- **Edit model:** Qwen-Image-Edit-2511 (Apache-2.0), given the character references plus pose and depth maps.
  - Alternative: Qwen-Image with the InstantX ControlNet-Union.
  - Fast alternative: Z-Image-Turbo with Fun ControlNet Union.
- **Identity:** train a character LoRA on the same base model with musubi-tuner or ai-toolkit. Use 15–30 images: about 40–60% close-ups, 10–20% full-body, and a range of real-scene lighting.
- **Preprocessing:** SAM 3, BiRefNet, DWPose/SDPose and Depth Anything 3, as in the hosted route.
- **Post-processing:** face and hand detailers, SeedVR2 upscale (Apache), then the same compositing step.
- **Avoid without a paid license:**
  - FLUX.2 dev and Klein 9B: serving them needs a BFL license. Klein 4B is Apache and fine.
  - Qwen-Image-2.1: research-only license.
  - LBM relighting, and Depth Anything 3 Giant/Nested: CC BY-NC.
  - OpenPose and SUPIR: commercial licenses required.

---

## Part 2: video (motion control / character replacement)

### Shoot the source footage right (quality is mostly decided here)

- **Light:** soft, even light, with the face and eyes well lit.
- **Camera:** locked-off or smooth movement, with a fast shutter to limit motion blur.
- **Framing:** one performer per shot, full body in frame, hands visible, and nothing crossing the face or body.
- **Clothing:** fitted and contrasting with the background, with a silhouette close to the character's.
- **Clean plate:** record one for every set-up (the empty shot).
- **Takes:** 5–15 s long, matching the models' segment lengths.
- **Sound:** record clean dialogue audio for lip sync.

### Pick the right mode

- **Real background must stay:** use a replacement-mode tool, or animation mode plus a composite over the clean plate.
- **Character's own world:** animation mode is enough.

### Hosted route

| Tool | Mode | Best for | Limits / price (approx.) |
|---|---|---|---|
| **Runway Aleph 2.0** | Replacement (v2v edit) | Strongest general full-body replacement; edit one frame and it propagates | ≤30 s, 1080p, API ~$0.28/s; lip/face fidelity not exact |
| **Wan2.2-Animate (replace/"mix" mode)** | Replacement | Built for this; relights the character to the scene; cheap | 720p, ~77-frame (~4.8 s) segments you chain |
| **Seedance 2.0** | Reference-video | Keeps motion and camera without masks; up to 9 character images | Input ~15 s |
| **Kling 3.0 Motion Control** | Animation | Most faithful body motion; accepts several character images | ≤30 s; ~$0.10/s 720p, $0.16/s 1080p; largest person only, wants a static camera |
| **Runway Act-Two** | Performance transfer | Facial acting and dialogue | ~$0.05/s |
| **Autodesk Flow Studio** | CG pipeline | Rigged 3D characters, up to 4 actors; outputs plates, mattes, camera tracks and mocap | $10–95/mo |
| Higgsfield Recast / Viggle V4 / Luma Ray3 Modify | Various | Quick consumer-grade swaps | Higgsfield's base model unknown; Viggle softer; Luma weak on consistency |

**Recommended hosted chain:**

1. Generate with Runway Aleph 2.0 or Wan-Animate replacement mode. Or generate with Kling 3.0 Motion Control and composite onto the clean plate.
2. Fix lip sync on the face region only.
3. Upscale and interpolate.
4. Composite with a MatAnyone 2 matte over the original footage.

### Self-hosted route (ComfyUI)

1. **Preparation:**
   - SAM 3.1 masks per frame.
   - DWPose/SDPose skeletons and face crops for expression.
   - A clean plate, needed when the character is slimmer than the performer.
2. **Generation:** Wan2.2-Animate-14B in **replacement mode**, with:
   - the Wan-Animate relight LoRA
   - a character LoRA (musubi-tuner or ai-toolkit; 15–40 captioned stills; rank 16–32)
   - the 4-step lightx2v LoRA for drafts only; render final shots at full steps.
   - Wan-Animate-2 (open weights, Aug 2026, Apache-2.0) gives stronger motion, but it is documented only in animation mode. Use it as the character render inside a composite.
3. **Long shots:** chain 77-frame segments, carrying the last frames forward and re-supplying the character reference each segment. Or use 81-frame context windows with about 16 frames of overlap. It is better to cut on action and keep shots to 10–15 s or less.
4. **Post-processing:**
   - Lip sync on the face region only: InfiniteTalk (v2v, any length) or LatentSync.
   - A light v2v refine and deflicker pass.
   - Upscale with SeedVR2 (or Topaz).
   - Interpolate with RIFE or GIMM-VFI.
   - Final composite with a MatAnyone 2 matte over the original plate, so background pixels and grain stay exactly as shot.
5. **Hardware:** a 24 GB RTX 4090 at FP8 with block-swap is the floor for 720p. Production uses 48–80 GB GPUs. GGUF Q4/Q5 runs on 10–16 GB with some quality loss.

---

## Quality checklist (both)

- **Edges:** no halos or color fringing; hair edges clean.
- **Scale and perspective:** the character matches the scene's horizon and camera height.
- **Grounding:** contact shadows and foot placement look right; reflections are present where expected.
- **Look:** color grade, noise or grain and sharpness match the plate.
- **Hands and face:** finger count is right; nothing warps; eye lines follow the original.
- **Video only:**
  - no flicker or identity drift across segment joins
  - lip sync stays within ±1 frame
  - motion blur matches the shutter

## Consent and provenance (required, not optional)

- **Performer consent:** written consent from the performer covering face, voice and performance, linked to each asset. Union productions also need SAG-AFTRA digital-replica terms.
- **Character design:** don't design the character to resemble a real person without that person's consent.
- **Labeling:** EU AI Act Art. 50 has applied since 2 Aug 2026, and California SB 942 / AB 853 applies too. Embed signed C2PA Content Credentials plus an invisible watermark, and re-sign after compositing.
- **Records:** keep audit logs of inputs, models and prompts.

## Open questions

- Does Wan-Animate-2 have a replacement mode, and is there hosted pricing?
- What is Alibaba's price and maximum length for Wan-Animate replacement?
- When did Kling 3.0 Motion Control launch? (March vs May 2026)
- What does Seedance 2.5 add for this task?
- Is the official GPT Image 2.5 API live?
- Licenses for SAM 3, Sapiens, SDPose and the Z-Image ControlNet still need confirming.
