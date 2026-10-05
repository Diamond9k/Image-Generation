# Legal, Licensing, Safety and Provenance Landscape for AI Image and Video Generation (as of 5 Oct 2026)

> Not legal advice. Most items come from web search snippets on 2026-10-05; several primary sources (EU Commission site, Paul Weiss, docket blogs) were blocked by the network proxy, so some details are second-hand. Items marked "[unverified this session]" come from background knowledge with a known URL but were not re-checked here.

## Copyright: lawsuits, Copyright Office guidance, rulings and licensing deals

### Takeaway
As of Oct 2026 no US court has ruled on the merits of whether training an image or video model is fair use. The big image and video cases (Disney/Universal/WBD v. Midjourney, the studios v. MiniMax, Getty v. Stability in N.D. Cal., Andersen) are all in discovery or pretrial, and each has survived dismissal. In the UK, Getty mostly lost at first instance but is now appealing on whether a model can be an "infringing copy." The practical risk for a launcher is now the **outputs** (characters and trademarks generated on demand) as much as training. Studios have shown they will license, as with Disney–OpenAI, but those deals are fragile.

### Cited Findings
**Disney/Universal (+WBD) v. Midjourney (C.D. Cal., 2:25-cv-05275, Judge Kronstadt)**
- Consolidated case; studios allege Midjourney generates Marvel, Star Wars, Minions and WB characters on demand; Midjourney's defense is fair use. — [CourtListener docket](https://www.courtlistener.com/docket/70513159/disney-enterprises-inc-v-midjourney-inc/); [Forensis summary](https://www.forensisgroup.com/resources/expert-legal-witness-blog/disney-and-universal-v-midjourney-u-s-generative-ai-copyright-litigation-over-image-training-and-outputs)
- WBD sued Midjourney in Sept 2025 over Superman, Bugs Bunny and other characters. — [MarketBeat](https://www.marketbeat.com/articles/warner-bros-sues-midjourney-for-ai-generated-images-of-superman-bugs-bunny-and-other-characters-2025-09-05)
- On 15 June 2026, Magistrate Judge Richlin denied Midjourney's broad discovery into the studios' own AI use. Midjourney objected. Reported schedule: discovery closes Sept 2026, summary judgment motions due 23 Nov 2026, and a jury trial likely in 2027 if the case is not resolved earlier. The schedule comes from a secondary tracker. — [Variety](https://variety.com/2026/film/news/midjourney-studios-ai-copyright-discovery-1236800902/); [The Art Newspaper, 9 Jul 2026](https://www.theartnewspaper.com/2026/07/09/midjourney-demands-hollywood-AI-secrets); [ailawsuittracker](https://ailawsuittracker.com/blog/disney-v-midjourney-explained/)

**Disney/Universal/WBD v. MiniMax (Hailuo AI, a video model; C.D. Cal.)**
- Filed Sept 2025. The complaint says Hailuo produced Darth Vader, Minions and Wonder Woman videos and was marketed as a "Hollywood studio in your pocket." It seeks an injunction, disgorgement and statutory damages. — [Business Standard](https://www.business-standard.com/world-news/disney-warner-bros-discovery-sue-china-s-minimax-over-copyright-violation-125091601400_1.html)
- Around May 2026 the court refused to dismiss and found direct and secondary infringement claims plausible. — [Bloomberg Law](https://news.bloomberglaw.com/ip-law/disneys-copyright-suit-against-chinese-ai-developer-advances)

**Getty Images v. Stability AI**
- UK: in the Nov 2025 High Court judgment, the secondary copyright claim failed (the model was not an "infringing copy"). Trademark infringement was found only for limited, historic watermark outputs from early Stable Diffusion versions. In Dec 2025/Jan 2026 Getty got permission to appeal the secondary infringement point, called "novel and important." Stability was refused permission to appeal on trademarks. — [IPKat, Jan 2026](https://ipkitten.blogspot.com/2026/01/permission-to-appeal-granted-in-getty.html); [Wiggin](https://www.wiggin.co.uk/insight/high-court-grants-permission-to-appeal-in-getty-images-v-stability-ai/); [Taylor Wessing](https://www.taylorwessing.com/en/insights-and-events/insights/2026/01/next-steps-for-getty-v-stability-why-has-permission-to-appeal-been-granted)
- US: Getty dropped the 2023 Delaware case and refiled in N.D. Cal. in Aug 2025. On 24 Apr 2026, Judge Trina Thompson denied most of Stability's motion to dismiss. Copyright (alleged copying of over 12M photos), trademark, dilution and unfair competition claims proceed; 1 of 7 counts was dismissed. — [Bloomberg Law](https://news.bloomberglaw.com/ip-law/gettys-ai-copyright-suit-survives-stabilitys-bid-for-dismissal); [Bloomberg Law, refiling](https://news.bloomberglaw.com/ip-law/getty-drops-stability-ai-copyright-suit-refiles-in-california); [Loeb, Apr 2026](https://www.loeb.com/en/insights/publications/2026/04/getty-images-us-inc-v-stability-ai-ltd)

**Andersen v. Stability AI / Midjourney / DeviantArt / Runway (N.D. Cal., Judge Orrick)**
- Surviving claims: direct infringement for training; induced infringement (Stability, Runway) for distributing Stable Diffusion weights, the "model-as-copy" theory; and Lanham Act trade dress claims over "in the style of" outputs. — [ailawsuittracker](https://ailawsuittracker.com/cases/andersen-v-stability-ai-ltd-3-23-cv-00201/); [Copyright Alliance](https://copyrightalliance.org/andersen-v-stability-ai-copyright-case/)
- **Sources conflict on timing.** One blog (6 Sept 2026) says a jury trial begins 8 Sept 2026 — [Sigma Law Group](https://sigmalawgroup.com/blog/2026-09-06-andersen-stability-ai-jury-trial/). A later post (28 Sept 2026) says Judge Orrick extended the schedule by 3 months and summary judgment won't be resolved before late 2027 — [ChatGPTiseatingtheworld](https://chatgptiseatingtheworld.com/2026/09/28/sarah-andersens-copyright-case-schedule-gets-extended-by-3-months). The later docket-focused source is more likely accurate. No verdict found.

**OpenAI Sora / Disney**
- Sora 2 launched Oct 2025 and drew backlash over copyrighted characters and celebrity deepfakes. On 11 Dec 2025, Disney and OpenAI announced a 3-year license for 200+ Disney/Marvel/Pixar/Star Wars characters. It excluded actor likenesses and voices, came with a planned $1B Disney investment, and carried responsible-use commitments. Disney also sent Google a cease-and-desist letter. — [The Register](https://www.theregister.com/2025/12/11/disney_openai_video_image_generation_deal/); [EU IP Helpdesk](https://intellectual-property-helpdesk.ec.europa.eu/news-events/news/disney-signs-agreement-openai-and-sends-google-cease-and-desist-notice-over-ai-belgian-court-rejects-2026-01-14_en)
- On 24 Mar 2026 OpenAI shut down the Sora app and web platform, and Disney exited the deal with no money transferred. — [Engadget](https://engadget.com/ai/openai-is-shutting-down-its-sora-video-generation-app-211023358.html); [WinBuzzer](https://winbuzzer.com/2026/03/25/openai-kills-sora-video-app-billion-dollar-disney-deal-xcxwbn/)
- I found no specific copyright lawsuit against Sora/OpenAI over video. Secondary sources mention "70+ lawsuits worldwide" over training in general. — [WinBuzzer](https://winbuzzer.com/2026/03/25/openai-kills-sora-video-app-billion-dollar-disney-deal-xcxwbn/)

**US Copyright Office and authorship**
- Part 3 (Generative AI Training), a pre-publication version released 9 May 2025, says some training uses go beyond fair use, especially where outputs compete in the market. Its authority is clouded because Register Perlmutter was removed right after release. — [Mishcon](https://www.mishcon.com/news/us-copyright-office-report-part-3-generative-ai-training); [Wiley](https://www.wiley.law/alert-Copyright-Office-Issues-Key-Guidance-on-Fair-Use-in-Generative-AI-Training); [Epstein Becker Green](https://www.ebglaw.com/insights/publications/unpacking-copyright-offices-ai-report-amid-admin-shakeups)
- Part 2 (Copyrightability, Jan 2025): prompts alone are not enough for authorship. Human selection, arrangement and modification can be protected. [unverified this session] — [USCO Part 2 PDF](https://www.copyright.gov/ai/Copyright-and-Artificial-Intelligence-Part-2-Copyrightability-Report.pdf)
- The Supreme Court denied cert in *Thaler v. Perlmutter* on 2 Mar 2026, so the human-authorship requirement stands. — [Baker Botts](https://www.bakerbotts.com/thought-leadership/publications/2026/march/supreme-court-denies-petition-on-copyright-authorship-by-ai); [SCOTUSblog](https://www.scotusblog.com/cases/case-files/thaler-v-perlmutter)
- *Allen v. Perlmutter* (D. Colo.), on whether Midjourney-assisted work is copyrightable, is pending. I found no 2026 ruling. — [Justia docket](https://dockets.justia.com/docket/colorado/codce/1:2024cv02665/237436)

### Inferences
- Output-side risk for famous characters and trademarks is the most immediate exposure for a video/image launcher. The MiniMax and Midjourney complaints rest on on-demand character generation, and courts found these claims plausible. Prompt and output filters for protected characters and logos are standard mitigation.
- Because generated outputs can't be copyrighted without human authorship, terms of service should not promise users copyright in raw outputs.
- The Sora collapse shows that studio licenses can vanish quickly. Don't build products that depend on a single licensor.

### Gaps
- No US merits ruling on fair use for image or video training. The Bartz/Kadrey (text) 2025 fair-use rulings were not re-checked here.
- Actual Andersen trial status could not be confirmed (docket blog blocked).
- Other video-model suits (Runway, Google Veo, Kling) and studio licensing deals in 2026 (e.g., Runway–Lionsgate, other stock deals) were not verified.

## Regulation: EU, US federal and state, UK, China

### Takeaway
The EU AI Act Article 50 transparency duties apply from **2 Aug 2026**. Systems already on the market before that date get until **2 Dec 2026** for the machine-readable marking duty. A final voluntary Code of Practice expects at least two marking techniques (signed metadata plus imperceptible watermark). California SB 942, as amended by AB 853, also became operative **2 Aug 2026**. The US TAKE IT DOWN Act's 48-hour removal duty has been enforced by the FTC since **19 May 2026**. NO FAKES has not passed. The UK is criminalizing nudification tools, and Ofcom is using the Online Safety Act against generative AI. China has required explicit and implicit labels since **1 Sept 2025**.

### Cited Findings
**EU AI Act Article 50**
- Providers of generative AI producing image, video, audio or text must mark outputs in a machine-readable, detectable way. Deployers must disclose deepfakes. Obligations apply from 2 Aug 2026; systems placed on the market before then have until 2 Dec 2026 for marking and detection. — [EU Commission FAQ](https://digital-strategy.ec.europa.eu/en/faqs/transparency-obligations-under-article-50-ai-act); [artificialintelligenceact.eu guide](https://artificialintelligenceact.eu/transparency-rules-article-50/)
- The Commission published a final Code of Practice on marking and labelling AI-generated content. It calls for at least two machine-readable techniques, such as digitally signed, tamper-evident metadata plus imperceptible watermarking. Signing is voluntary, but Art. 50 is binding. — [Paul Weiss](https://www.paulweiss.com/insights/client-memos/eu-finalises-transparency-rules-for-ai-generated-content); [Commission signing FAQ](https://digital-strategy.ec.europa.eu/en/faqs/signing-code-practice-transparency-ai-generated-content); [nicfab analysis](https://www.nicfab.eu/en/posts/code-of-practice-transparency-ai-content/)
- The GPAI Code of Practice (July 2025) covers copyright policy, opt-out compliance and a training-data summary template for GPAI model providers. GPAI obligations started 2 Aug 2025, with enforcement from Aug 2026. [unverified this session] — [EU Commission GPAI Code](https://digital-strategy.ec.europa.eu/en/policies/contents-code-gpai)

**US federal**
- TAKE IT DOWN Act: since 19 May 2026, covered platforms must provide a notice-and-removal process and remove non-consensual intimate imagery (including AI-generated) and known copies within 48 hours. Penalties are up to $53,088 per violation, and the FTC has sent letters to major platforms. — [FTC press release, May 2026](https://www.ftc.gov/news-events/news/press-releases/2026/05/ftc-begins-enforcing-take-it-down-act); [Finnegan](https://www.finnegan.com/en/insights/articles/the-take-it-down-act-is-now-in-full-effect-what-platforms-need-to-know.html)
- NO FAKES Act of 2026 (S.4591 / H.R.8915) was introduced 20 May 2026. Senate Judiciary advanced it unanimously on 22 June 2026, but it is not law. It would create a federal digital-replica right with DMCA-style notice and counter-notice and safe harbors. — [Congress.gov S.4591](https://www.congress.gov/bill/119th-congress/senate-bill/4591); [Holland & Knight](https://www.hklaw.com/en/insights/publications/2026/06/senate-judiciary-committee-advances-legislation-to-protect-name); [Manatt](https://www.manatt.com/insights/newsletters/client-alert/congress-reintroduces-the-no-fakes-act-what-s-new-in-the-2026-bill)

**US states**
- California SB 942 (AI Transparency Act), as amended by AB 853 (signed 13 Oct 2025), became operative 2 Aug 2026. It covers providers with more than 1M monthly users. They must offer a free detection tool plus visible (optional) and latent disclosures in image, video and audio. AB 853 extends duties to large platforms, hosting platforms and capture-device makers on later dates. — [Orrick](https://infobytes.orrick.com/2025-10-17/california-delays-its-ai-transparency-act-and-passes-new-content-laws/); [NatLawReview](https://natlawreview.com/article/californias-ongoing-ai-regulation-key-deadlines-arriving-2026-and-beyond)
- Tennessee's ELVIS Act and many state election-deepfake and intimate-deepfake laws exist. The specific 2026 changes were not verified this session (see Gaps).

**UK**
- Ofcom opened a formal Online Safety Act investigation into X over Grok-generated sexualized images in Jan 2026. The government is legislating, via Crime and Policing Bill amendments, to criminalize creating or supplying AI "nudification" tools. There are concerns it covers only single-purpose tools. — [Taylor Wessing](https://www.taylorwessing.com/de/insights-and-events/insights/2026/01/rd-ofcom-investigates-xs-grok-as-scrutiny-of-deepfakes-and-nudification-tools-increases); [The Register](https://www.theregister.com/2026/01/14/uk_government_nudification_ban/); [Simmons & Simmons](https://www.simmons-simmons.com/en/publications/cmkfjc1xl0030v4tklthq0jn3/grok-generative-ai-and-the-uk-online-safety-act)

**China**
- The CAC Measures for Labeling AI-Generated Synthetic Content and mandatory national standard GB 45438-2025 took effect 1 Sept 2025. They require explicit (visible) labels on text, images, audio, video and virtual scenes, implicit labels in file metadata, and labels retained on download or export. Platforms must detect and label content. — [Loeb & Loeb](https://www.loeb.com/en/insights/publications/2025/03/chinas-ai-labeling-measures-and-mandatory-national-standards-take-effect-september-1)

### Inferences
- A single design can satisfy the EU, California and China: C2PA manifest, invisible watermark, a visible label option, and a public detection endpoint. China additionally requires visible labels by default.
- Any product hosting user-shared outputs is a likely "covered platform" under TAKE IT DOWN. It needs a 48-hour NCII takedown workflow with hash-based duplicate removal.

### Gaps
- The EU Digital Omnibus proposals (late 2025) may have adjusted AI Act timelines. I could not confirm whether Art. 50 dates changed, because the EU site was blocked. The dates above come from the Commission FAQ snippet.
- Final Art. 50 Commission guidelines status is unknown.
- State laws (NY digital replica and synthetic performer disclosure, Texas, Minnesota election deepfake litigation, Colorado AI Act delay) were not verified.

## Provenance: C2PA, SynthID, watermarking, platform labeling

### Takeaway
The de facto stack is C2PA Content Credentials (signed manifest, v2.2+) **plus** an imperceptible watermark that survives metadata stripping. Google's SynthID does this per frame for Veo. The EU Code expects two layers, and California requires latent disclosures plus a detector.

### Cited Findings
- C2PA 2.2 is a cryptographically signed, tamper-evident metadata standard ("Content Credentials"). Google released Credentio, an open-source C++ C2PA library supporting spec versions 2.2 and 2.4. — [Google Developers Blog](https://developers.googleblog.com/introducing-credentio-open-source-c-library-for-c2pa-content-credentials-from-google/)
- SynthID embeds imperceptible watermarks in text, image, audio and video. Veo video is watermarked frame by frame, and detection goes through Google's SynthID Detector portal (proprietary). — [Trust Insights, Apr 2026](https://www.trustinsights.ai/blog/2026/04/ai-watermarks/); [traeai overview of SynthID, Stable Signature and C2PA](https://learn.traeai.com/t/ai-engineering/phases/18-ethics-safety-alignment/23-watermarking-synthid-stable-signature-c2pa)
- Watermarks can be removed: commercial "Veo watermark remover" services exist, so watermarking alone is not robust. — [example remover site](https://www.gptcleanup.com/veo-video-watermark-remover)
- Platform labels: YouTube requires creators to disclose realistic altered or synthetic content ("altered or synthetic content" label); Meta applies "AI info" labels using C2PA/IPTC signals; TikTok auto-labels content carrying C2PA Content Credentials. [unverified this session] — [YouTube Help](https://support.google.com/youtube/answer/14328491); [Meta, Apr 2024](https://about.fb.com/news/2024/04/metas-approach-to-labeling-ai-generated-content-and-manipulated-media/); [TikTok newsroom, May 2024](https://newsroom.tiktok.com/en-us/partnering-with-our-industry-to-advance-ai-transparency-and-literacy)

### Inferences
- Open-source watermark options include Meta's Stable Signature/Video Seal and the C2PA SDKs (c2pa-rs). Commercial options include Digimarc, Steg.AI, Truepic and IMATAG. Pick one that survives video transcoding and compression.

### Gaps
- No independent 2026 robustness benchmarks for video watermarks were retrieved.
- Current status of C2PA spec 2.3/2.4 features for video (e.g., soft-binding, live video) was not verified.

## Safety: CSAM, NSFW, likeness and voice consent, moderation tooling

### Takeaway
The baseline is Thorn/All Tech is Human "Safety by Design" practice:
- clean training data of CSAM
- classify prompts, inputs and outputs, including sampled video frames
- hash-match known CSAM
- report to NCMEC (a legal duty for US providers under 18 U.S.C. §2258A)
- block sexualized depictions of real people

Regulators are now acting against "nudify" capability (the Grok/Ofcom case). Likeness and voice consent flows are becoming expected ahead of NO FAKES.

### Cited Findings
- Thorn and All Tech is Human launched Safety by Design for Generative AI in April 2024. Google, Meta, OpenAI and others committed to preventing AIG-CSAM across the lifecycle, and Thorn published a progress update in October 2025. The principles are being integrated into NIST and IEEE standards. — [Thorn principles PDF](https://info.thorn.org/hubfs/thorn-safety-by-design-for-generative-AI.pdf); [Thorn one-year progress](https://www.thorn.org/blog/safety-by-design-one-year-of-progress/)
- Companies report apparent CSAM to NCMEC; Google describes its classifier and hash-matching approach for generative AI. — [Google child-safety update](https://blog.google/innovation-and-ai/technology/safety-security/an-update-on-our-child-safety-efforts-and-commitments/)
- Grok's "spicy" image features triggered Ofcom's investigation and UK government criticism ("deepfake creation a premium service"). This is a concrete enforcement example. — [Express & Star, Jan 2026](https://www.expressandstar.com/uk-news/2026/01/09/no-10-grok-changes-insulting-and-make-deepfake-creation-a-premium-service); [Katten](https://quickreads.ext.katten.com/post/102megi/ofcoms-investigations-into-ai-platforms-the-online-safety-acts-framework)
- The Disney–OpenAI license excluded actor likeness and voice, showing that likeness rights are handled separately from character IP. — [The Register](https://www.theregister.com/2025/12/11/disney_openai_video_image_generation_deal/)

### Inferences
- Moderation tooling options:
  - Thorn Safer (hash and CSAM classifier)
  - Microsoft PhotoDNA
  - Google Content Safety API
  - Hive and AWS Rekognition (NSFW, celebrity detection)
  - OpenAI omni-moderation (images plus text, free)
  - Open models: Llama Guard 4 (multimodal), ShieldGemma 2 (image), LAION/Falconsai NSFW detectors
  - Video: sample frames per second and run image classifiers on them, plus audio/voice checks

  These are from background knowledge, not re-verified.
- For image-to-video and face-swap, add consent verification for uploaded faces (liveness or self-verification). Sora 2's "cameo" opt-in model was the reference design.

### Gaps
- No 2026 NCMEC statistics on AI-generated CSAM reports were retrieved.
- Status of the US ENFORCE Act and other AI-CSAM bills was not found.

## Model licenses and vendor indemnification

### Takeaway
Indemnity is available mainly from vendors with licensed data or large balance sheets:
- Adobe Firefly (enterprise)
- Getty/Shutterstock generators
- Google Cloud (Imagen/Veo on Vertex)
- OpenAI enterprise/API

It is always conditional: you must keep filters on, it excludes trademark claims and user-supplied inputs, and caps apply. Midjourney offers no indemnity, and open-weight licenses carry revenue thresholds and use restrictions.

### Cited Findings
- Adobe Firefly is trained on Adobe Stock and public-domain content, and IP indemnity is offered to eligible enterprise customers. — [Computerworld](https://www.computerworld.com/article/1628682/adobe-offers-copyright-indemnification-for-firefly-ai-based-image-app-users.html)
- Google Cloud's generative AI indemnity covers generally available models such as Imagen. Getty's generator advertises indemnity (reported $50k per image). Shutterstock offers enterprise indemnity. OpenAI excludes claims tied to disabled safety features or trademark issues. Midjourney requires Pro or Mega for companies with more than $1M revenue and offers no indemnity. — [Mike Chambers, Sept 2025](https://www.mikechambers.com/blog/post/2025-09-24-generative-ai-commercial-safety/); [internetandtechnologylaw.com on OpenAI](https://www.internetandtechnologylaw.com/openai-generative-ai-indemnity-infringement-claims/)
- In Andersen, induced-infringement claims target Stability and Runway for *distributing* model weights. Anyone hosting or redistributing open weights inherits this theory's risk. — [ailawsuittracker](https://ailawsuittracker.com/cases/andersen-v-stability-ai-ltd-3-23-cv-00201/)

### Inferences
- Open-weight license gotchas, from background knowledge and not re-verified:
  - Stability Community License: free under $1M revenue, enterprise license above that.
  - FLUX.1 [dev] and Kontext [dev]: non-commercial license for the weights, with a commercial license from Black Forest Labs. FLUX.1 [schnell] is Apache-2.0.
  - Wan 2.x and some Hunyuan/LTX video models use Apache or custom licenses. Tencent Hunyuan's license excludes the EU, UK and South Korea.
  - OpenRAIL-style use restrictions must be passed downstream.
- Check each license's territorial exclusions and acceptable-use flow-down.

### Gaps
- Current (2026) indemnity terms for Runway, Luma, Google Veo via Gemini API vs. Vertex, and OpenAI image API were not verified from primary terms.
- 2026 studio/stock licensing deals (e.g., Runway–Lionsgate, AMC, Getty/Shutterstock merger status) were not verified.
