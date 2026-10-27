# Generative Video Models as a Source of 3D Skeletal Animation for Game Characters (state: October 2026)

**Scope note on source quality.** The generative-video space in 2026 is heavily polluted by SEO/affiliate blogs that re-publish each other's spec sheets. Below I mark each item as:
- **[VERIFIED-PRIMARY]** — I read the vendor's own page, official docs, the Hugging Face model card, or the GitHub README.
- **[SECONDARY]** — only third-party blogs/comparison sites; treat numbers as indicative.
- **[NOT FOUND]** — could not corroborate; likely wrong name, hallucinated, or unreleased.

---

## Key Question 1: Verifying the five specific claims the user was given

### Takeaway
Four of the five product names are **real**, but every one of them is garbled in some load-bearing detail — most importantly the **dates are shifted by a year** (these are 2026 releases, not 2025) and the **"open weights" claim is wrong for Wan**. The single most consequential correction: **Wan 2.5 and Wan 3.0 are closed API-only models**, so "cheaper open motion models" does not describe the current Wan line; Wan 2.2 (2025, Apache-2.0) is still the last open Alibaba video flagship.

### Cited Findings

**Claim (1): "MiniMax H3, open-weight, released 2025, 'reference-to-video'/'Ref2VA' mode."** — **Mostly real, date wrong by a year.**
- The Hugging Face repo `MiniMaxAI/MiniMax-H3` **exists**. The model card describes H3 as a "general-purpose, omni-modal generative system" that understands text, images, video and audio and generates video with stereo audio — [MiniMaxAI/MiniMax-H3 model card](https://huggingface.co/MiniMaxAI/MiniMax-H3) **[VERIFIED-PRIMARY]**
- The card lists exactly two open BF16 checkpoints: **Base FL2VA** (text-to-audio-video and first/last-frame-to-audio-video) and **Base Ref2VA** (reference-to-audio-video). So **"Ref2VA" is a genuine official checkpoint name**, not an invention — [MiniMax-H3 card](https://huggingface.co/MiniMaxAI/MiniMax-H3) **[VERIFIED-PRIMARY]**
- Ref2VA accepts **up to 9 reference images, up to 3 reference video clips, up to 3 reference audio clips**; each clip 2–15 s; total video and total audio each capped at 15 s; 12 files total across all types — [MiniMax-H3 card](https://huggingface.co/MiniMaxAI/MiniMax-H3) **[VERIFIED-PRIMARY]**
- Open checkpoints output **768p default, 4–15 s clips, 24 FPS, 32 kHz stereo**. **H3-Context-IR** (preprocessing) and **H3-Regenerate-2K** (the 2K path) are **not open-sourced** — API only — [MiniMax-H3 card](https://huggingface.co/MiniMaxAI/MiniMax-H3) **[VERIFIED-PRIMARY]**
- License: **MiniMax H3 Community License Agreement**, with a separate application form required for users in the USA, EU, UK and South Korea — [MiniMax-H3 card](https://huggingface.co/MiniMaxAI/MiniMax-H3) **[VERIFIED-PRIMARY]**
- Branding: H3 is marketed as **Hailuo 3.0** — [HF community blog](https://huggingface.co/blog/ResterChed/minimax-h3-hailuo-3-0) **[SECONDARY]**
- Dates: launch reported as **July 31, 2026**; open weights reported variously as **Aug 2, Aug 3, or Aug 5 2026** by different blogs — [HF blog](https://huggingface.co/blog/ResterChed/minimax-h3-hailuo-3-0); [llm-stats](https://llm-stats.com/blog/research/minimax-h3-launch); [OpenClaw](https://openclawlaunch.com/blog/minimax-h3-open-weight-video-model) **[SECONDARY, conflicting]**
- Hailuo's own X account posted "H3 is now officially open-weight" — [@Hailuo_AI](https://x.com/Hailuo_AI/status/2084284120625225750) **[VERIFIED-PRIMARY-ish, no date captured]**
- Secondary sources describe the base checkpoint as a **33B dense transformer** and claim ComfyUI serving support — [dreampixelforge](https://www.dreampixelforge.com/blog/minimax-h3); [OpenClaw](https://openclawlaunch.com/blog/minimax-h3-open-weight-video-model) **[SECONDARY]**

**Claim (2): "Kling 3.0 Motion Control exists and is the most realistic motion transfer."** — **Feature is real; version is now one generation stale.**
- Wikipedia's Kling timeline: Kling **3.0 in early 2026**; **Kling 4.0 Flash early-access Sept 28, 2026**; **full Kling 4.0 globally launched October 2026**, with native 30-second generation, native 4K with 10-bit HDR, stereo audio and expanded keyframe editing. **Wikipedia does not mention Motion Control at all** — [Kling AI (Wikipedia)](https://en.wikipedia.org/wiki/Kling_AI) **[VERIFIED-PRIMARY for timeline]**
- Motion Control exists as a named Kling feature on both **2.6** and **3.0** tiers. Reported v3 additions: "Element Binding" for multi-angle face consistency, scene source control, up to 30-second orientation, 10×+ faster inference — [motioncontrolai guide](https://motioncontrolai.com/blogs/how-to-use-kling-motion-control); [Alici.AI](https://alici.ai/blog/kling-motion-control-guide) **[SECONDARY]**
- Reported release date for v3 Motion Control conflicts: **March 5, 2026** (Standard + Pro), **March 4, 2026**, or **May 2026** — [motioncontrolai](https://motioncontrolai.com/blogs/how-to-use-kling-motion-control); [Atlas Cloud](https://www.atlascloud.ai/blog/tips/kling-ai-motion-control) **[SECONDARY, conflicting]**
- Kling 2.6 Motion Control remains the cheaper tier: **480p/580p/720p at 24 fps**; driving-clip limits **3–30 s for "Match Video", up to 10 s for "Match Image"**; "Keep original sound" toggle on by default — [Atlabs guide](https://www.atlabs.ai/blog/the-ultimate-user-guide-to-kling-2.6-motion-control); [oakgen](https://oakgen.ai/blog/kling-motion-control-guide) **[SECONDARY]**
- **No source confirms a Motion Control feature on Kling 4.0.** Gap.

**Claim (3): "Higgsfield Genjutsu does 'performance recast'."** — **Product is real; the feature name is wrong.**
- Genjutsu is a real Higgsfield product with a dedicated page and API docs. Its two modes are **Motion Transfer** and **Object Swap** — **not** "performance recast" — [Higgsfield Genjutsu](https://higgsfield.ai/genjutsu); [Higgsfield API docs: Genjutsu motion-transfer](https://docs.higgsfield.ai/docs/models/genjutsu/motion-transfer) **[VERIFIED-PRIMARY]**
- Independent confirmation of the internal naming: Higgsfield's own MCP server instructions in this session state "Higgsfield Genjutsu routes through generate_video: motion transfer -> `hf_mult_motion_control`; object replacement -> `hf_mult_replace_object`" — **[VERIFIED-PRIMARY, vendor tooling]**
- Motion Transfer is **video-to-video**: it carries the motion of a source clip onto a new character while **keeping the original camera and timing**, rebuilding the scene from reference images — [Layer.ai model docs](https://layer.ai/docs/models/higgsfield-genjutsu-motion-transfer); [higgsfield.ai/genjutsu](https://higgsfield.ai/genjutsu) **[VERIFIED-PRIMARY]**
- Limits: source clip **4–30 s** (longer input is trimmed to 30 s), **minimum 4 s**, prompt optional; **up to 8 reference images** per Layer's docs vs **up to 30 reference images** per Higgsfield's own generator UI (characters, products, wardrobe); output **480p or 720p** per Layer, with a 1080p tier in Higgsfield's price list — [Layer.ai](https://layer.ai/docs/models/higgsfield-genjutsu-motion-transfer); [Higgsfield docs](https://docs.higgsfield.ai/docs/models/genjutsu/motion-transfer) **[conflicting between the two]**
- Pricing **per second of input video**: **$0.318 @ 480p, $0.681 @ 720p, $1.632 @ 1080p**. A **Genjutsu Lite** variant is faster at roughly **one-third the cost**, supporting up to 10 reference images — [Higgsfield listing via search](https://higgsfield.ai/genjutsu) **[SECONDARY — number appeared in listing text, not re-verified on a live pricing page]**
- A conflicting listing gives **$0.025–$0.10/sec** for a "Genjutsu Light Motion Transfer" — [Mitte](https://mitte.ai/node/higgsfield-genjutsu) **[SECONDARY, conflicts]**
- API endpoint for new integrations: `higgsfield/genjutsu/motion-transfer/v1.0`; the legacy misspelled `higgsfiled/...` path is still callable but hidden from discovery — [Higgsfield API docs](https://docs.higgsfield.ai/docs/models/genjutsu/motion-transfer) **[VERIFIED-PRIMARY]**
- Launch date reported as **September 1, 2026** — [Kapwing](https://www.kapwing.com/resources/5-best-higgsfield-genjutsu-alternatives-in-2026/) **[SECONDARY]**

**Claim (4): "Seedance 2.5 exists."** — **Real, but availability is murky.**
- **Seedance 2.0** launched **February 12, 2026** per Reuters-sourced reporting and Baidu Baike (SitePoint and DataCamp say Feb 10) — [eWeek](https://www.eweek.com/news/bytedance-seedance-2-ai-video-race/); [Baidu Baike](https://baike.baidu.com/en/item/Seedance/1457750); [DataCamp](https://www.datacamp.com/blog/seedance-2-0) **[SECONDARY, 2-day conflict]**
- Seedance 2.0 specs: **4–15 s clips at up to 1080p**, with text, image, audio and video inputs; native audio-visual sync and multi-shot narrative — [invideo](https://invideo.io/blog/seedance-2-0-overview/); [Baidu Baike](https://baike.baidu.com/en/item/Seedance/1457750) **[SECONDARY]**. Some sites claim 4K/20 s/60 s; **DataCamp explicitly warns ByteDance has published no official English release note**, so spec sheets may just echo Chinese press — [DataCamp](https://www.datacamp.com/blog/seedance-2-0) **[SECONDARY]**
- **Seedance 2.5** announced by **Volcano Engine at its FORCE conference on June 23, 2026**; headline specs **up to 30 s and up to 50 reference inputs**. As of July 20, 2026 one source still described it as closed beta; later comparisons say it is live via Dreamina and is listed on Higgsfield's paid plans — [kie.ai](https://kie.ai/blog/seedance-2-5-release-deep-dive); [origami](https://origami.sa/en/blog/seedance-2-5-vs-veo-kling-sora/); [trezalabs](https://www.trezalabs.com/blog/seedance-2-5-vs-veo-3-1-vs-sora-2-comparison) **[SECONDARY]**
- Volcano Engine opened a Seedance **enterprise public beta on April 2, 2026** — [Baidu Baike](https://baike.baidu.com/en/item/Seedance/1457750) **[SECONDARY]**
- **No weights.** No source indicates any Seedance checkpoint is open.

**Claim (5): "Wan 2.2 / 3.0 exist as cheaper OPEN motion models."** — **Half wrong, and this is the most important correction.**
- **Wan 3.0 is closed, API-only. No weights on Hugging Face, GitHub or ModelScope.** It entered **public beta on 6 August 2026** on Alibaba Cloud Model Studio and Qwen Cloud — [wan27.org: Is Wan 3.0 Open Source?](https://wan27.org/blog/wan-3-open-source); [jxp.com](https://www.jxp.com/wan/blog/is-wan-3-0-open-source); [imagera](https://imagera.ai/blog/is-wan-3-0-real-what-alibaba-shipped-2026) **[SECONDARY but mutually corroborating]**
- **Wan 2.5 is also closed**: released as a commercial API, pre-announced as open, **weights never appeared** — [wan27.org: Is Wan 2.5 Open Source?](https://wan27.org/blog/wan-2-5-open-source-guide) **[SECONDARY]**
- **Wan 2.2 (2025, Apache-2.0) remains Alibaba's last open video flagship** — [wan27.org](https://wan27.org/blog/wan-3-open-source) **[SECONDARY]**
- Several SEO pages on the same domain contradict each other, one claiming Wan 3.0 weights are up on `Wan-AI` / `Wan-Video` / ModelScope, another calling it only a Q3-2026 rumor. I could not verify any open Wan 3.0 release against an official page — **[CONFLICTING, treat open-weight Wan 3.0 as false until proven]**

**Bonus correction the user was not given, but needs:**
- **OpenAI Sora is gone.** The Sora app was shut down **April 26, 2026** and API access ended **September 24, 2026** — [elser.ai](https://www.elser.ai/news/seedance-2-5-vs-veo-3-1-vs-sora-2-pro); [trezalabs](https://www.trezalabs.com/blog/seedance-2-5-vs-veo-3-1-vs-sora-2-comparison) **[SECONDARY, two independent sites agree]**. Any 2026 plan built on Sora is dead on arrival.

### Inferences
- The other AI's answer looks like a 2025-knowledge model extrapolating: it got the *names* roughly right (H3, Ref2VA, Genjutsu, Seedance 2.5, Kling Motion Control all exist) but back-dated the whole cohort by a year and assumed the Chinese labs kept open-sourcing their flagships, which they stopped doing after Wan 2.2.
- "Performance recast" is most likely a paraphrase the other AI generated, not a product feature. If the user searches that term in Higgsfield's UI they will find nothing; the control is called **Motion Transfer**.

### Gaps
- **Could not verify the Bilibili viral-clip provenance claim at all.** No Chinese-language source surfaced tying viral Bilibili motion-transfer clips specifically to MiniMax H3 Ref2VA. Treat that attribution as unsupported — the same visual result is achievable with Kling Motion Control, Genjutsu, Viggle or Wan, and viral clips are rarely labelled.
- No live pricing page captured for Kling Motion Control or Seedance 2.5 (credits-based, region-dependent).
- Whether Kling 4.0 ships Motion Control is unknown.

---

## Key Question 2: The Oct 2026 landscape of video motion-transfer / character-replacement models

### Takeaway
Motion transfer from a driving video is now a **standard, commoditized feature** across the closed Chinese and US video platforms (Kling Motion Control, Higgsfield Genjutsu Motion Transfer, MiniMax H3 Ref2VA, Runway Act-Two, Viggle), priced at roughly **$0.3–$1.6 per second of output**. **The open-weight side has regressed**: Alibaba and MiniMax both now withhold their best paths (Wan 2.5/3.0 entirely, H3's 2K and Context-IR modules), leaving Wan 2.2 and HunyuanVideo 1.5 as the practical local options.

### Cited Findings

| Model / product | Current ver. (Oct 2026) | Motion transfer from driving video? | Open weights? | Price | Max duration |
|---|---|---|---|---|---|
| **Kuaishou Kling** | **4.0** (global, Oct 2026); 4.0 Flash preview Sept 28 2026 — [Wikipedia](https://en.wikipedia.org/wiki/Kling_AI) | Yes, **Motion Control** on 2.6 / 3.0 tiers (unconfirmed on 4.0) — [SECONDARY](https://motioncontrolai.com/blogs/how-to-use-kling-motion-control) | No | Credits; not captured | 30 s native on 4.0 — [Wikipedia](https://en.wikipedia.org/wiki/Kling_AI) |
| **MiniMax Hailuo** | **H3 / Hailuo 3.0** (Jul 31 2026) — [SECONDARY](https://huggingface.co/blog/ResterChed/minimax-h3-hailuo-3-0) | Yes, **Ref2VA** (≤3 reference videos) — [PRIMARY](https://huggingface.co/MiniMaxAI/MiniMax-H3) | **Yes, partially** — FL2VA + Ref2VA BF16 at 768p; Context-IR & 2K closed — [PRIMARY](https://huggingface.co/MiniMaxAI/MiniMax-H3) | Weights free under community license (US/EU/UK/KR need application) | 4–15 s — [PRIMARY](https://huggingface.co/MiniMaxAI/MiniMax-H3) |
| **ByteDance Seedance** | **2.5** (announced Jun 23 2026); 2.0 Feb 12 2026 — [SECONDARY](https://kie.ai/blog/seedance-2-5-release-deep-dive) | Video input accepted; up to 50 reference inputs on 2.5 — [SECONDARY](https://origami.sa/en/blog/seedance-2-5-vs-veo-kling-sora/) | No | Volcano Engine / Dreamina / Higgsfield plans | 2.0: 4–15 s; 2.5: up to 30 s — [SECONDARY](https://invideo.io/blog/seedance-2-0-overview/) |
| **Alibaba Wan** | **3.0** public beta Aug 6 2026 — [SECONDARY](https://wan27.org/blog/wan-3-open-source) | Video-to-video paths exist | **No for 2.5 / 3.0.** Last open = **Wan 2.2, Apache-2.0, 2025** — [SECONDARY](https://wan27.org/blog/wan-2-5-open-source-guide) | API | 30 s claimed for 3.0 — [SECONDARY](https://morphic.com/resources/models/wan-3-0) |
| **Tencent HunyuanVideo** | **1.5** (8.3B, Nov 2025); I2V / Avatar / Foley variants — [SECONDARY](https://vantaige.io/ai-tool/hunyuanvideo) | **Weak** — general T2V/I2V + audio-driven avatar, no dedicated motion-transfer mode — [SECONDARY](https://vantaige.io/ai-tool/hunyuanvideo) | **Yes** — open-weight family, runs on consumer GPUs (~75 s on a single RTX 4090) — [SECONDARY](https://vantaige.io/ai-tool/hunyuanvideo) | Free locally | n/a |
| **Higgsfield Genjutsu** | **v1.0** (launched ~Sep 1 2026) — [PRIMARY](https://docs.higgsfield.ai/docs/models/genjutsu/motion-transfer) | **Yes — the dedicated product.** Motion Transfer + Object Swap — [PRIMARY](https://higgsfield.ai/genjutsu) | No | **$0.318/s @480p, $0.681/s @720p, $1.632/s @1080p**; Lite ≈⅓ cost — [SECONDARY listing](https://higgsfield.ai/genjutsu) | **4–30 s input**, trimmed at 30 — [PRIMARY](https://docs.higgsfield.ai/docs/models/genjutsu/motion-transfer) |
| **Runway** | **Act-Two** — **no evidence of Act-Three** — [SECONDARY](https://magichour.ai/blog/viggle-ai-alternatives) | Yes — transfers a driving performance onto a character image or video; image input gives gesture control, video input preserves the character video's scene | No | **5 credits/s**, 3-second minimum charge — [SECONDARY](https://magichour.ai/blog/viggle-ai-alternatives) | up to 30 s — [SECONDARY](https://magichour.ai/blog/viggle-ai-alternatives) |
| **Viggle** | current | Yes — purpose-built character motion transfer onto a still, from video or text, on its own "video-3D foundation model" — [SECONDARY](https://tooldirectory.ai/tools/viggle-ai); [vendor](https://viggle.ai/vs/runway) | No | not captured | not captured |
| **Google Veo** | **3.1** (Preview in Gemini API) — no version beyond 3.1 found — [SECONDARY](https://www.trezalabs.com/blog/seedance-2-5-vs-veo-3-1-vs-sora-2-comparison) | Not a motion-transfer product | No | Gemini API | — |
| **OpenAI Sora** | **RETIRED.** App shut **Apr 26 2026**, API ended **Sep 24 2026** — [SECONDARY](https://www.elser.ai/news/seedance-2-5-vs-veo-3-1-vs-sora-2-pro) | n/a | n/a | n/a | n/a |
| **Luma / Pika** | — | — | — | — | — |

- **ComfyUI support:** ComfyUI is where the open models land. Confirmed video→motion node packs (see Q3) run GVHMR inside ComfyUI, and secondary sources claim MiniMax H3 serving support in ComfyUI — [OpenClaw](https://openclawlaunch.com/blog/minimax-h3-open-weight-video-model) **[SECONDARY]**

### Inferences
- The **cheapest route to a photoreal motion-transfer clip** at Oct 2026 is Genjutsu Lite (~$0.1/s at 480p) or Kling 2.6 Motion Control; the cheapest *free* route is Wan 2.2 or HunyuanVideo 1.5 locally, at a real quality cost.
- Every commercial motion-transfer product bills **per second of video**, which matters for a fighting game: a 60-move moveset at ~1.5 s per move is ~90 s of video, i.e. **$30–$150 of generation per character** at 720p–1080p before any iteration. Iteration multiplies that 5–20×.

### Gaps
- **Luma and Pika**: no current-version, pricing or motion-transfer data surfaced in my searches. Unresolved.
- Viggle pricing and duration limits not captured.
- No reliable 2026 benchmark comparing motion-transfer *fidelity* across these products; vendor-claimed "success rates" (e.g. Viggle's) are not backed by any benchmark I could find — [tooldirectory](https://tooldirectory.ai/tools/viggle-ai) notes this.

---

## Key Question 3: Can 3D motion be extracted FROM generated video? (generated video → HMR → SMPL → game rig)

### Takeaway
**Yes, and this is the route that actually works today.** It is not theoretical: at least two public projects explicitly accept AI-generated video as input, run **GVHMR** to get SMPL-X, and retarget onto a **Mixamo** rig — one of them demos **punch-and-kick and two-fighter duel** sequences. The hard constraint is not generated-frame weirdness but a much more boring one: **the camera must be locked**, and fast limbs get flattened.

### Cited Findings

**The working pipeline (strongest finding in this whole report):**
- **`squall01337/mixamo-llm-mocap`** README: "Turn any locked-camera video — **filmed or AI-generated** — into a clean FK animation on any Mixamo character." Handles one or two performers — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- Method: **GVHMR** estimates SMPL-X, then a **SMPLify-style refit** against the video's 2D keypoints (**ViTPose**), plus heel/toe keypoints (**MediaPipe**) and zoomed hand crops for fingers — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- Output: joint rotations transferred to the rig and keyed in headless Blender; **hips are the only translating bone, everything else is quaternions at 30 fps** — explicitly Mixamo-shaped — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- **It is built around AI-generated input**: the showcase uses an AI-generated source clip and the repo ships **a guide to writing generation prompts that survive retargeting** — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- **Fighting-game relevance, directly:** examples include "a punch-and-kick sequence and a two-fighter duel, with blocks and ducks," two characters retargeted with their own proportions, plus a Blender mesh-collision pass — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- A notable concrete failure they hit and fixed: **faithful retargeting made near-misses actually collide, because Mixamo characters are thicker than humans**; they add a declared, measured clearance offset — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- Foot skating: the support ankle is **pinned through each stance** so planted feet solve to ground height "with zero skate"; height drift removed continuously, horizontal drift corrected one footfall at a time — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY, vendor claim]**
- 10-stage breakdown and QA tooling (`eval_fidelity.py` for jitter and foot slide, `qa_clip.py` for explosions/pops, `compare_reference.py` frame-vs-source, `compare_pair.py` for separation/reach/intrusion, mesh-vs-mesh collision check) — [The Menon Lab, pub. 2026-08-30](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation) **[VERIFIED-PRIMARY]**

**What breaks (reported limitations, from the practitioners themselves):**
- **GVHMR "flattens fast limbs"** — the single most relevant defect for a fighting game; the SMPLify-style refit exists specifically to correct it — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- **GVHMR has no fingers** → requires separate zoomed hand crops — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- **Locked camera only; no tracking shots** — [Menon Lab](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation) **[VERIFIED-PRIMARY]**
- Needs **full body visible**; **cloth and props confuse the estimator**; **poor lighting and shadows confuse it** — [Menon Lab](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation) **[VERIFIED-PRIMARY]**
- **Mixamo rigs are FK-only, no IK** — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**
- Two-performer tracking: **tracker IDs swap on contact**, so tracks are split by screen side — a direct problem for grapples/clinches in a fighter — [Menon Lab](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation) **[VERIFIED-PRIMARY]**
- Hardware: **~8 GB VRAM** (developed on an RTX 4080), Blender 5.1+, ~5 GB GVHMR checkpoint, SMPL-X research registration required — [GitHub](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**

**ComfyUI node packs that implement this route:**
- **`PozzettiAndrea/ComfyUI-MotionCapture`** — runs GVHMR on video to estimate SMPL params and 3D skeletal motion; example pipeline is video load → **SAM3** person segmentation → `LoadGVHMRModels` → `GVHMRInference` → SMPL params; also includes an **SMPL-to-BVH** pipeline. Listing shows a **2026-03-27** update — [GitHub](https://github.com/PozzettiAndrea/ComfyUI-MotionCapture); [node listing](https://www.floyo.ai/all-comfyui-nodes/comfyui-motioncapture-pozzettiandrea) **[VERIFIED-PRIMARY]**
- **`AKASubaz/ComfyUI-Video2MotionCapture`** — WIP, also GVHMR-based, extracts SMPL params and **exports rigged FBX**, with **retargeting to Mixamo or the Unreal mannequin** via a bundled Blender setup; installer pulls ~3.5 GB of GVHMR + ViTPose + SMPL models — [GitHub](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture) **[VERIFIED-PRIMARY]**
- `HY-Motion NPZ to SMPL_PARAMS` converts existing NPZ motion to SMPL params (not raw video); updated 2026-05-27 — [runcomfy](https://www.runcomfy.com/comfyui-nodes/ComfyUI-HyMotion/hy-motion-npz-to-smpl-params) **[SECONDARY]**
- `Fannovel16/ComfyUI-MotionDiff` (4DHuman/MotionGPT/MDM/MotionDiffuse/ReMoDiffuse) is the older option, ~16 months stale, and its SMPL conversion works from motion data not video — [GitHub](https://github.com/Fannovel16/ComfyUI-MotionDiff) **[SECONDARY]**

**Other video→motion options:**
- **`mehulcode12/vid2anim`** — three tracking engines; GVHMR path outputs SMPL-X as the "heavy studio option", MediaPipe path runs CPU-only; ships a Blender add-on targeting **Mixamo, VRM, Rigify and UE5** — [GitHub](https://github.com/mehulcode12/vid2anim) **[VERIFIED-PRIMARY-ish]**
- **Uthana** — commercial hosted video-to-motion; reconstructs single-person video as skeletal animation and retargets to compatible bipedal characters — [Uthana](https://uthana.com/models/video-to-motion) **[SECONDARY]**
- Established markerless video-mocap field: **Move.ai, Rokoko Vision, Plask Motion, RADiCAL, DeepMotion, Vmotionize, Cascadeur**; Rokoko Vision and DeepMotion have free tiers — [Film Threat roundup](https://filmthreat.com/features/10-best-ai-tools-for-turning-video-into-3d-character-motion/) **[SECONDARY]**

**State of the HMR research that this route depends on (2026):**
- **MetricHMSR (CVPR 2026)** — metric-scale human mesh *and scene* recovery from a single image; authors claim SOTA on both — [CVPR 2026 open access](https://openaccess.thecvf.com/content/CVPR2026/html/Song_MetricHMSR_Metric_Human_Mesh_and_Scene_Recovery_from_Monocular_Images_CVPR_2026_paper.html) **[VERIFIED-PRIMARY]**. (Metric scale matters: it is the thing that fixes "changing proportions / no camera ground truth".)
- **arXiv 2603.20391** — *training-free* test-time multi-view refinement on top of pretrained single-view HMR models; **26.9 MPJPE / 20.6 PA-MPJPE on Human3.6M** with a CameraHMR backbone, beating prior SOTA HeatFormer (29.5 / 22.4) — [arXiv](https://arxiv.org/abs/2603.20391) **[VERIFIED-PRIMARY]**
- **DanceHMR (arXiv 2605.18102, Jun 2026)** — hand-aware whole-body SMPL-X from monocular video, improved hands with competitive body accuracy. Dance is the closest published analogue to fight choreography — [arXiv](https://arxiv.org/pdf/2605.18102) **[SECONDARY, abstract only]**
- **Anny-Fit (2605.04728)** all-age HMR and **PEAR (2601.22693)** pixel-aligned expressive HMR also exist — **[SECONDARY, results not read]**
- **Kirin (arXiv 2609.01823)** — animal motion generation from in-the-wild video via SMAL, relevant only if the fighter is non-human — **[SECONDARY]**

### Inferences
- The route **works, with caveats that are specific and controllable**, and crucially the hardest caveats are things you control at *generation* time: **generate with a locked camera, full body in frame, clean lighting, no loose cloth or props**. The repo shipping a prompt-writing guide for exactly this is strong evidence the authors found generation controllable enough to be worth systematizing.
- The failure modes the user's question anticipated (temporal inconsistency between generated frames, hallucinated limbs) are **not** what practitioners report as the blocker. The reported blockers are **fast-limb flattening** (a GVHMR property, not a generated-video property), **camera motion**, and **ID swaps on contact**. That is a meaningfully different and more tractable problem list.
- For a **fighting game specifically**, the two serious risks are (a) fast-limb flattening hitting exactly the frames that matter — the extended punch, the kick apex — and (b) ID swaps during contact, which affects throws and clinches. Both argue for generating **one fighter at a time against empty space**, not two fighters interacting.
- GVHMR appears to be the de-facto 2026 standard for this task: it is the engine in `mixamo-llm-mocap`, `vid2anim`'s studio path, `ComfyUI-MotionCapture` and `ComfyUI-Video2MotionCapture` alike. Any pipeline design should assume GVHMR plus a 2D-keypoint refit.

### Gaps
- **No quantitative quality numbers for any of these pipelines.** Both `mixamo-llm-mocap` and the Menon Lab post ship QA *tooling* that produces numeric reports but publish **no benchmark scores and no demonstration that output meets production quality** — the Menon Lab write-up says so explicitly. All quality claims (notably "zero skate") are **design-level vendor claims, unverified by a third party**.
- **I found no Reddit, r/StableDiffusion, r/comfyui or r/gamedev practitioner report** on generated-video→mocap. My search for community discussion returned nothing relevant. This is a genuine evidence gap: the only reported results come from the two project authors themselves.
- No 2026 HMR survey or leaderboard found; the CVPR/ICCV/ECCV 2026 proceedings and EMDB/3DPW trackers would be needed for a real SOTA picture.
- **WHAM, TRAM, SMPLer-X, NLF** specifically: did not surface in 2026 results. GVHMR has apparently displaced them in practitioner tooling, but I cannot confirm they are worse.

---

## Key Question 4: Alternative routes that skip video — 4D / animated-3D generation

### Takeaway
**The 4D-generation literature is still not a route to a game character.** The 2026 papers overwhelmingly output **dynamic geometry or Gaussian sequences without a controllable rig** — a "4D asset" you can replay but cannot pose, loop, blend or drive from game input. The one genuinely interesting 2026 development is **GaussiAnimate**, which goes the *other* direction: it takes a 4D sequence and **extracts a rig from it**.

### Cited Findings
- **GaussiAnimate (arXiv 2604.08547, submitted Apr 9 2026, revised Sep 7 2026, accepted SIGGRAPH Asia 2026)** proposes "**Skelebones**": (1) **Bones** — compress Gaussian *or mesh* sequences into bones with skinning weights; (2) **Skeleton** — extract and refine a skeleton; (3) **Binding** — synthesize new bone motions via non-parametric Partwise Motion Matching. Result described as "compact skelebones that are simultaneously controllable and expressive." Inputs: **temporally consistent Gaussian or mesh sequences + a canonical shape**. Reports reanimation gains over linear blend skinning, Bag-of-Bones, and GRU/MLP baselines — [arXiv 2604.08547](https://arxiv.org/abs/2604.08547) **[VERIFIED-PRIMARY]**
- A survey-style vendor post states X-to-4D systems generate geometry and motion **together in one pass**, unlike traditional rigging (mesh first, then skeleton); names **Align4D, SV4D 2.0, STP4D** as producing visible 4D output but says **production use is not yet practical** — [Neural4D blog](https://blog.neural4d.com/neural4d/x-to-4d-generation/) **[SECONDARY, vendor]**
- The rig-controllability gap is stated in the literature: a 2026 paper notes that methods like **ActionMesh (arXiv 2601.16148)** and **Motion 3-to-4 (arXiv 2601.14253)** produce dynamic geometry but **do not resolve rig controllability** — [via GaussiAnimate/related](https://arxiv.org/html/2604.08547v2) **[SECONDARY, as cited in a reference discussion]**
- Other 2026 entries in the space: **Helix4D** complex 4D mesh generation (2605.26109), **MORPHOS** autoregressive 4D with temporal structured latents (2606.02491), **AniGen** unified S³ fields for animatable 3D asset generation (2604.08746), **SkelMo** universal skeletal motion generation for already-rigged 3D shapes (2606.01518), **RegHead** non-humanoid head blendshapes (2607.12206), **RigMo** (learns rigs from raw mesh sequences without annotations) — **[SECONDARY, titles/abstract-level only]**
- **AnimateAnyMesh (arXiv 2506.09982, ICCV 2025)** — feed-forward 4D foundation model for **text-driven universal mesh animation** — **[SECONDARY]**
- **R-DMesh (arXiv 2605.13838)** — video-guided 3D animation via rectified dynamic mesh flow; demonstrates **motion transfer from generated videos to a target 3D character without per-identity fine-tuning**, but **does not use SMPL** — [arXiv](https://arxiv.org/pdf/2605.13838) **[SECONDARY, abstract-level]**

### Inferences
- **The critical distinction to hold onto:** a *4D asset* (Gaussian splat sequence, or a baked dynamic mesh) is a **recording**. It has no joints, no blend tree, no root motion, no state machine hooks. You cannot make a fighting game out of it — you cannot interrupt it on hit, blend it into a block, mirror it, or scale it to a different character. A *rigged skeletal animation* (FBX/GLB with a joint hierarchy whose rotations you key) is the only thing a game engine can treat as a character animation. Almost all 4D generation output falls on the wrong side of this line.
- **GaussiAnimate and RigMo are the papers that matter** precisely because they attack the rig-extraction step. But GaussiAnimate requires *temporally consistent* Gaussian or mesh sequences as input — which generated video does not give you. So it is not a shortcut; it is a tool for cleanly-captured 4D data.
- **SkelMo is on the wrong side of the problem for this user**: it generates motion for *already-rigged* shapes. That is useful if the user has a rig and wants synthetic motion — arguably a competing route worth separate investigation — but it is not video-to-animation.
- **R-DMesh is the most intriguing lead in this section** for the user's goal: video-guided mesh animation driven by *generated* video, onto a target 3D character. It deserves a direct read.

### Gaps
- My searches mostly returned **reference lists** rather than paper bodies, so for AniGen, ActionMesh, Helix4D, MORPHOS, R-DMesh and SkelMo I have **titles and one-line characterizations only**. I did **not** confirm whether AniGen takes image input or emits a rigged mesh, despite it being the most on-target title.
- Did **not** find 2026 status for the specific older works the user named — **Animate3D, DreamGaussian4D, L4GM, 4Diffusion, Stable Virtual Camera**. They did not appear in 2026 results, which weakly suggests they have been superseded, but I cannot confirm that.

---

## Key Question 5: Products advertising "video in → rigged animated 3D game character out"

### Takeaway
**Several products advertise it; none of the evidence I found establishes production-grade output quality.** All claims here come from vendor pages and listicles, and **no independent quality evaluation surfaced for any of them.** Note the architectural split: these products do **video → animation on a rig** (which is the achievable thing), not **video → a novel rigged character + its animation** in one shot.

### Cited Findings
- **V2Fun** — browser workflow combining generate-or-upload a character image, **automatic keypoint-based rigging**, and applying motion from a **single-camera video**, in one place. Reported to have hit **Product Hunt Product of the Day in July 2026** — [V2Fun blog](https://v2fun.art/en/blog/best-game-character-animation-tools-2026) **[SECONDARY, vendor]**
- **Sloyd** — upload a short full-body video clip; it captures the motion and maps it onto the character. **Video-to-animation is a Pro-tier feature** — [Sloyd animations](https://www.sloyd.ai/animations) **[SECONDARY, vendor]**
- **Krikey AI** — animation from text or video input, plus **auto-rigging** and lip-sync — [Krikey character rigging](https://www.krikey.ai/create/character-rigging) **[SECONDARY, vendor]**
- **DeepMotion** — cloud video-to-3D motion capture; free tier available — [Film Threat](https://filmthreat.com/features/10-best-ai-tools-for-turning-video-into-3d-character-motion/) **[SECONDARY]**
- **3D AI Studio** — pitched as generating a character and getting it rigged and animation-ready in one place; **Mixamo** is cited as the free auto-rig baseline — [3daistudio](https://www.3daistudio.com/blog/best-ai-tools-for-ai-3d-animation-2026) **[SECONDARY, vendor]**
- **ComfyUI-Video2MotionCapture** is the open equivalent and is explicit about the deliverable: **SMPL params → rigged FBX → Mixamo or Unreal mannequin** — [GitHub](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture) **[VERIFIED-PRIMARY]** (but **WIP** by its own description)

### Inferences
- **The honest answer to "does a product do video in → rigged animated game character out" is: the *animation* half is a solved commodity, the *character* half is a separate pipeline.** The reliable shape is: get a rigged mesh separately (Mixamo auto-rig, or an image→3D mesh tool), then drive it with video-derived motion. Products that bundle both are bundling two independently-available steps, not doing something new.
- For a fighting game, the **open ComfyUI route is probably preferable to the hosted products** despite being WIP, because fighting-game animation needs frame-level cleanup in Blender/Maya anyway, and the hosted tools give you less access to the intermediate SMPL data.

### Gaps
- **No independent quality evaluation of V2Fun, Sloyd, Krikey or 3D AI Studio output.** Every claim in this section is vendor marketing or a listicle repeating it. I could not verify 2026 release details or pricing for any of them. This is the weakest-evidence section of the report.

---

## Key Question 6: What game studios actually say about generative video in the animation pipeline (2025–2026)

### Takeaway
**Generative video is being used for cinematics, trailers and marketing — not for in-game character animation.** The production uses that are actually documented are 2D/VFX-adjacent (rotoscoping, plate cleanup, style variation), while in-game animation still runs on markerless mocap and auto-rigging. Meanwhile developer sentiment has turned actively hostile, and some clients now **contractually ban** generative AI from outsourced work.

### Cited Findings
- **VFX, not animation:** major VFX houses including **DNEG** have built **ComfyUI workflows into Nuke** for rotoscoping and plate cleanup — [GenAI in VFX & Animation whitepaper 2026](https://www.maacjayanagar.com/genai-vfx-guide) **[SECONDARY]**
- **Epic / Fortnite:** art teams use generative tools to produce **lighting and style variations from artist-made sketches and 3D blockouts** — i.e. look-dev, not animation — [same whitepaper](https://www.maacjayanagar.com/genai-vfx-guide) **[SECONDARY]**
- **Animation tooling that is actually established** is mocap, auto-rigging and lip-sync: **NVIDIA Audio2Face**, **Reallusion iClone**; **Ninja Theory's AI-driven facial capture on Hellblade II** is the cited earlier precedent — [gamineai ranking](https://gamineai.com/blog/25-ai-animation-tools-ranked-game-studios-2026) **[SECONDARY]**
- **Generative video's real home is cinematics and trailers.** **Utopai Studios** released a multi-shot video model in **March 2026** and reported **$11M ARR within sixty days**; smaller outfits like AI25.Studio make game cinematics and trailers with generative video — [Utopai (Wikipedia)](https://en.wikipedia.org/wiki/Utopai_Studios); [Versely](https://www.versely.studio/blog/ai-video-studios-for-games-and-entertainment) **[SECONDARY]**
- **Developer sentiment is hostile:** GDC's survey shows **rising developer skepticism toward generative AI, largely over job losses**, and **some clients now write contract clauses banning generative AI from outsourced production** — [State of AI in Video Games 2026](https://www.athena-productions.com/read/state-of-ai-2026-246) **[SECONDARY, citing GDC survey]**
- **Cost concern:** commentary notes AI integration brings **variable running costs**, with token consumption growing sharply under agent-based workflows — [same](https://www.athena-productions.com/read/state-of-ai-2026-246) **[SECONDARY]**

### Inferences
- The absence of any studio claiming generative-video-derived in-game animation, in a year where the tech clearly exists, is itself a signal: either quality is insufficient for shipped character animation, or the legal/sentiment risk outweighs the savings, or both.
- The **contract-clause finding is a practical warning**: if the user ever intends to outsource art or sell/license the game's assets, generative-video-derived animation may be contractually problematic, independent of its quality.

### Gaps
- **I found no named studio statement specifically about generative video → 3D animation.** All the "generative AI in games" sourcing I found is vendor blogs, SEO roundups and whitepapers; several carry unverified figures (e.g. a "20% margin leakage" claim) that I have not reproduced here. The GDC survey is cited second-hand and I did not read it directly.

---

## Bottom line for the user's goal (usable fighting-game character animation)

### Takeaway
**The viral video-model motion-transfer pipeline CAN be converted into usable 3D game animation, and the route is already built and public.** It is: **(driving video or text prompt) → motion-transfer or plain generation with a LOCKED camera → GVHMR → SMPL-X → SMPLify refit against ViTPose 2D keypoints → retarget to Mixamo/UE mannequin in Blender → FBX.** Both `mixamo-llm-mocap` and `ComfyUI-Video2MotionCapture` implement it end-to-end, and the former explicitly demos punches, kicks, blocks and ducks.

### Cited Findings
- The route ends in a **rigged, animatable asset**: quaternion keys on a Mixamo-compatible joint hierarchy at 30 fps, hips as the only translating bone — [mixamo-llm-mocap](https://github.com/squall01337/mixamo-llm-mocap) **[VERIFIED-PRIMARY]**; rigged **FBX** export with Mixamo/Unreal-mannequin retargeting — [ComfyUI-Video2MotionCapture](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture) **[VERIFIED-PRIMARY]**
- The **4D-generation route does not** end in a riggable asset, by the admission of the literature itself (dynamic geometry without rig controllability) — [via GaussiAnimate discussion](https://arxiv.org/html/2604.08547v2) **[SECONDARY]**

### Inferences
- **Practical recommendation ordering:** (1) generate single-fighter clips, locked camera, full body, clean lighting, tight clothing, no props; (2) run GVHMR + 2D refit; (3) retarget to the actual game rig, not Mixamo, if the game rig differs in proportion — the collision-clearance problem the repo hit is a proportion mismatch artifact; (4) budget for manual cleanup of fast-limb apex frames, which is where GVHMR is documented to fail and where a fighting game is most readable.
- **Generate one fighter at a time.** Two-performer tracking ID swaps on contact are documented, and throws/clinches are exactly where that breaks.
- **Do not plan around Sora (retired) or open-weight Wan 2.5/3.0 (does not exist).** For a local, free motion source use **Wan 2.2 (Apache-2.0)** or **HunyuanVideo 1.5**; for quality-per-dollar on motion transfer use **Genjutsu Lite** or **Kling 2.6 Motion Control**; for an open reference-to-video model with real driving-video support use **MiniMax H3 Ref2VA** at 768p.
- **The unresolved risk is quality, and nobody has measured it.** Every quality claim in the working-pipeline section is the project author's own. The user should expect to run the pipeline on one test move and judge the result themselves before committing to a moveset.
