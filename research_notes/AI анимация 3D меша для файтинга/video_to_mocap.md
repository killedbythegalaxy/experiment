# Video-to-Mocap: AI / Markerless Video → 3D Skeletal Animation Tools (state as of 2026-10-27)

**Reading date for all vendor pages cited below: 2026-10-27**, unless a page carries its own publication date (noted inline). Anything labeled HISTORICAL is from 2024–2025 or earlier and may no longer hold.

**Overall source-quality warning for the report writer:** this market is drowning in SEO "best AI mocap tools 2026/2027" listicles that recycle each other's stale prices. I found *direct* official-page confirmation for Move One, Move API, Rokoko Vision, Plask, DeepMotion's credit mechanics, DeepMotion's ToS, Move AI's EULA, and Autodesk's own pricing guide. I could **not** get DeepMotion's actual dollar tiers, Move Pro's dollar tiers, RADiCAL's or Kinetix's current pricing from primary sources. Those are flagged as gaps, not guessed.

---

## Which tools exist and are actively maintained in 2026 (pricing, formats, cameras, clip length, res/fps)

### Takeaway
The single biggest structural change in 2026 is that **Epic acquired Meshcapade (Feb 2026), shut down Meshcapade's standalone platform (April 2026), and shipped the technology as a free MetaHuman Animator Markerless Motion Capture plugin for Unreal Engine 5.8 (June 17, 2026) — local processing, single camera, body + fingers, free for commercial use**. The second is Viggle's PINOC/JST-2 (Oct 2026) offering free browser video→FBX/GLB on a Mixamo/MetaHuman skeleton and a $0.05-per-source-second API. Established paid players (Move AI, DeepMotion, Rokoko Vision, Plask, Autodesk Flow Studio) are all still live, with Rokoko having *removed* features (dual-camera, webcam live, fingers) in Vision 3.0 and Autodesk having cut Flow Studio prices substantially.

### Cited Findings

**Move AI — Move One (consumer/single-phone), official docs**
- Move One plans: **Free $0** (30 one-time credits that don't expire, **max video length 30 seconds**); **Starter $18/month + VAT** (60 credits/mo, max 60 s); **Standard $48/month + VAT** (180 credits/mo, max 60 s); **Plus $225/month + VAT** (240 credits/mo, max 60 s); **Advanced $490/month + VAT** (700 credits/mo, max 60 s) — [Move docs, Move One Pricing](https://docs.move.ai/knowledge/move-one-pricing)
- Credit rates: **Gen 1 (s1) = 1 credit per person per second; Gen 2 (s2) = 2 credits per person per second**. Credits do not roll over and reset on the subscription anniversary day. Free accounts cannot buy extra credits without an active subscription; purchased credits are unusable after cancellation — [Move docs](https://docs.move.ai/knowledge/move-one-pricing)
- **Every Move One paid tier caps a single video at 60 seconds.** Overage rates exist but are published only at move.ai/pricing, which returned **HTTP 404 on 2026-10-27** — [Move docs](https://docs.move.ai/knowledge/move-one-pricing)
- Worked example of real cost for fight work: Gen 2 at 2 credits/person/second means a 10-second two-fighter clip = 40 credits. The $48 Standard tier's 180 credits ≈ **45 seconds of two-person Gen-2 capture per month**.

**Move AI — developer API (the only tier with published fps/resolution pricing)**
- Base per-second rates: **s1 $0.012/s; s2 $0.024/s** (single-camera); **m1 $0.024/s/camera; m2 $0.043/s/camera** (multi-camera) — [Move developer pricing](https://developers.move.ai/docs/pricing/)
- **Resolution multipliers:** ≤1,920 px long edge = 1.00x; 1,921–2,560 px = 1.15x; ≥2,561 px (4K) = 1.25x — [Move developer pricing](https://developers.move.ai/docs/pricing/)
- **FPS multipliers:** 24 fps = 0.92x; 30 = 1.00x; 45 = 1.20x; 60 = 1.40x; 90 = 1.80x; **120 = 2.20x** (formula `1.0 + (fps − 30) × 0.0133`). This is the only tool I found that documents a supported 120 fps path — [Move developer pricing](https://developers.move.ai/docs/pricing/)
- Camera-count multiplier (multi-camera only): 2 cams = 1.0x; 3 = 1.25x; 4 = 1.5x. Final price = base × fps × resolution × camera. Billed on processed video duration rounded to the nearest second; **minimum charge $0.10 per job** — [Move developer pricing](https://developers.move.ai/docs/pricing/)
- Additional models appear in the API docs navigation but are **unpriced**: `m2-xl`, `m2-light`, `s2-light`, `rt1`, `rt2` (the `rt` prefix suggests real-time/Move Live) — [Move developer pricing](https://developers.move.ai/docs/pricing/)
- Move Pro dollar pricing: **not obtainable.** The Move Pro desktop EULA says "the Usage Allowance applicable to your subscription will be clearly set out when you purchase your subscription and is also available at http://move.ai/pricing" — and that URL 404s — [search result citing Move Pro EULA](https://move.ai/move-pro-for-desktop-self-serve-cloud-processing-eula)
- **Conflicting third-party Move AI prices (treat as unreliable):** aggregators variously claim Free/$15/mo, $18/mo, "from $50/month", $99/mo starting, Personal $15/mo ($10.50 annual, non-commercial only, 2,250 credits), Standard $50/mo with commercial rights, Advanced $250/mo, Advanced $490/seat/mo ($392 annual) with "up to 4K and 120 fps", and a "$995 30-day paid trial with ~1,000 credits" — [AI Chat Daily](https://www.aichatdaily.com/tools/move-ai); [aichief](https://aichief.com/ai-3d-tools/move-ai/); [fitgap](https://us.fitgap.com/products/009160/move-ai). **Only the $18/$48/$225/$490 figures in Move's own docs should be reported as current.**

**DeepMotion Animate 3D**
- deepmotion.com/pricing is now a splitter page linking to **/pricing-saymotion** (text-to-3D-animation) and **/pricing-animate3d** (video-to-3D-animation) — [DeepMotion pricing](https://www.deepmotion.com/pricing)
- **Freemium:** no credit card, account doesn't expire, **up to 60 seconds of animation per month, personal/non-commercial only** — [DeepMotion Animate 3D pricing](https://www.deepmotion.com/pricing-animate3d)
- **Studio plan:** "UNLIMITED credits for an unlimited amount of animation creation," but **only the first 7,200 credits each subscription month get highest job priority**; beyond that, jobs run at low priority — [DeepMotion](https://www.deepmotion.com/pricing-animate3d)
- **Credit mechanics: 1 credit = 1 second of animation or 1 3D pose. Face tracking adds +0.5 credit/second. Hand tracking adds +0.5 credit/second.** So full body + hands + face = 2 credits/second. Monthly credits reset, no carryover. Rerun limits depend on subscription type — [DeepMotion](https://www.deepmotion.com/pricing-animate3d)
- **Input formats: MP4, MOV, AVI. Export formats: FBX, BVH, MP4, GLB** — GLB **only** for custom characters — [DeepMotion](https://www.deepmotion.com/pricing-animate3d)
- Commercial licensing requires a premium subscription; animations made from free credits do not change licensing — [DeepMotion](https://www.deepmotion.com/pricing-animate3d)
- **GAP / conflicting:** exact dollar tiers were not on the page I could read. Aggregators claim starter "from $9/mo", "~$12–17/mo", "$15/month with a commercial license", "$17/month", "Innovator $48/month", "Professional $117/month" — all unverified and mutually inconsistent — [Tech Jockey](https://www.techjockey.com/us/detail/deepmotion); [AI Chat Daily](https://www.aichatdaily.com/tools/deepmotion); [SoftwareSuggest](https://www.softwaresuggest.com/deepmotion). API and real-time SDK access is quote-only.

**Rokoko Vision 3.0 — official product page**
- **Starter: $0, free forever** — 30 seconds of Vision AI processing per month, FBX export, 1 month cloud storage — [Rokoko Vision](https://www.rokoko.com/products/vision)
- **Basic: $10/month billed annually, or $12/month billed monthly** — 600 seconds of Vision AI processing per month, BVH export, custom character import and retargeting, unlimited cloud storage — [Rokoko Vision](https://www.rokoko.com/products/vision)
- **Regressions in Vision 3.0 (important):** **single camera only going forward** — the dual-camera feature is "sunset approximately 30 days after the Vision 3.0 launch"; **webcam live capture has been removed** — all AI mocap is now upload-to-Rokoko-Create — [Rokoko Vision](https://www.rokoko.com/products/vision)
- **Vision 3.0 launched without finger tracking**, which Rokoko frames as a deliberate trade for body quality (vendor claim) — [Rokoko, Introducing Vision 3.0](https://www.rokoko.com/insights/introducing-rokoko-vision-3-0)
- Export: FBX (all plans), BVH (Basic). Skeleton presets include **HIK, Mixamo, UE5 Manny**. Stated compatibility: Blender, Unreal Engine, Unity, Cinema 4D, Maya, Houdini, MotionBuilder, iClone — [Rokoko Vision](https://www.rokoko.com/products/vision)
- **Not stated anywhere on the page:** max clip length, required frame rate, required resolution. Only the monthly processing allowance (30 s / 600 s) is given — [Rokoko Vision](https://www.rokoko.com/products/vision)
- Vendor acknowledges occlusion dependency: "Vision relies on a complete view of the performer, so occluded limbs or performers outside the frame track less well" — [Rokoko Vision](https://www.rokoko.com/products/vision)

**Plask Motion — official pricing page**
- **Free:** no subscription required, **15 seconds of motion capture per day**, 1 GB storage, exports **FBX, GLB, BVH** — [Plask pricing](https://plask.ai/pricing)
- **Standard: $18/month billed annually** — 10 minutes of mocap per month, 3 GB storage, FBX/GLB/BVH — [Plask pricing](https://plask.ai/pricing)
- **Pro: $50/month billed annually** — 1 hour of mocap per month, 10 GB storage, FBX/GLB/BVH — [Plask pricing](https://plask.ai/pricing)
- Monthly-billing prices, credit counts, and commercial-use terms are **not** on the pricing page — [Plask pricing](https://plask.ai/pricing)
- Plask handles **multiple people in one video** and is positioned for blocking/previs rather than final quality — [3dxdev comparison](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)

**Autodesk Flow Studio (formerly Wonder Studio / Wonder Dynamics)**
- Wonder Studio was folded into Autodesk Flow and renamed **Autodesk Flow Studio in March 2025** — [CGChannel](https://www.cgchannel.com/2025/03/wonder-studio-becomes-autodesk-flow-studio/)
- **Current prices from Autodesk's own September 28, 2026 pricing guide:** five standard tiers — Free, Lite, Standard, Pro, Enterprise — plus Education. **Lite $7/month, Standard $30/month, Pro $64/month**, each on a one-year term paid annually — [Autodesk M&E blog, Flow Studio pricing and subscription guide, 2026-09-28](https://blogs.autodesk.com/media-and-entertainment/2026/09/28/autodesk-flow-studio-pricing-and-subscription-guide/)
- **HISTORICAL (Aug 2025):** the freemium relaunch set Lite at $10/month, Standard at $45/month, Pro at $95/month, and *removed* some export options and the Wonder Tools from cheaper plans — [Animation Magazine](https://www.animationmagazine.net/2025/08/autodesk-launches-freemium-access-to-flow-studio-ai-solution/); [CGChannel](https://www.cgchannel.com/2025/08/autodesk-cuts-prices-of-flow-studio-subscriptions/); [Autodesk press release](https://adsknews.autodesk.com/en/pressrelease/autodesk-launches-freemium-access-to-autodesk-flow-studio-with-new-pricing-tiers-making-world-class-vfx-ai-more-affordable-to-all-creators/)
- Credit allotments (third-party rate card, updated 2026): Free 300, Lite 2,100, Standard 6,000, Pro 12,000, Enterprise 48,000 — [Usage Pricing](https://www.usagepricing.com/blueprint/wonder-dynamics)
- Export gating that *is* documented: **Lite no longer allows exporting character passes, PNG image sequences, or USD scenes**; Standard adds USD scene export and up to 4K output; **Free output is watermarked**, making it test-only — [CGChannel](https://www.cgchannel.com/2025/08/autodesk-cuts-prices-of-flow-studio-subscriptions/); [Digital Production](https://digitalproduction.com/2025/08/13/flow-studio-prices-drop-tiers-multiply-features-shift/)
- A low-authority review claims you can export "mocap, camera tracks, clean plates, and 3D scenes in USD, FBX, or directly into tools like Maya, Blender, Unreal Engine," but does not say which tiers include FBX — [TechShark](https://techshark.io/tools/flow-studio/). **Flag as unverified.**
- Wonder 3D asset generation was added in March 2026 with no subscription price change (third-party) — [Usage Pricing](https://www.usagepricing.com/blueprint/wonder-dynamics)
- Conflicting 2026 tier list: AI Chat Daily lists Free $0 / Lite $10 / Standard $45 / Pro $95 / Enterprise custom "as of August 2026" — [AI Chat Daily](https://www.aichatdaily.com/tools/wonder-dynamics). **Autodesk's own September 2026 guide ($7/$30/$64 annual) should take precedence.**
- No evidence of discontinuation; it remains an active freemium product.

**Epic Games: Meshcapade acquisition → free UE 5.8 MetaHuman Animator Markerless Motion Capture plugin (the most significant 2026 development)**
- **Epic acquired Meshcapade in mid-February 2026**; the Tübingen team joined Epic's AI Research group in "Cyber Valley". Purchase price undisclosed — [80.lv](https://80.lv/articles/ai-motion-capture-startup-meshcapade-now-part-of-epic-games); [heise](https://www.heise.de/en/news/Video-recording-animations-Epic-Games-buys-Tuebingen-based-company-Meshcapade-11182302.html); [GameSpot](https://www.gamespot.com/articles/fortnite-studio-buys-this-digital-human-company/1100-6538258/)
- **Meshcapade's standalone online platforms were shut down** — one tracker states "As of April 18th, the Meshcapade online platforms have been shut down" — [CGDive addons, Meshcapade (discontinued)](https://addons.cgdive.com/tools/meshcapade). **Meshcapade / Meshcapade ME should be reported as no longer purchasable as a standalone service.**
- **MetaHuman Animator Markerless Motion Capture plugin released June 17, 2026 alongside Unreal Engine 5.8.** Extracts **full-body animation including finger motion from a single mono camera** (webcam or phone) and applies it to a MetaHuman. **All processing runs locally, not in the cloud.** Built on Meshcapade technology. **Windows only. Requires UE 5.8. Free, including for commercial projects** — [CGChannel, 2026-06-18](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/)
- Dual-camera mode exists but is for *separating solves* — e.g. facial from a head-mounted camera, body from the main camera — not for multi-view triangulation of the body — [CGChannel](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/)
- Source footage can also come from the iOS Live Link Face app set to a non-TrueDepth camera — [CGChannel](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/)
- Unverified social-media reports put processing at roughly **one second of 1080p footage per under-a-minute** of compute — [CGChannel](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/) (CGChannel explicitly does not verify this)
- **Status conflict:** one source describes the UE 5.8 markerless capture feature as **"Experimental"** ("in July 2026 Unreal Engine 5.8 was released with an Experimental Markerless Motion Capture plugin") — [mimem.ai](https://www.mimem.ai/alternatives/meshcapade) (a competitor; treat with caution). CGChannel's launch article does not use the word "experimental." **Unresolved.**
- mimem.ai also claims the captured motion "can be exported as FBX from the editor," and that the plugin "captures from a single mono camera" and is "Windows-only at launch" — [mimem.ai](https://www.mimem.ai/alternatives/meshcapade). The FBX claim is from a competitor page and is **unverified**; the native output path is UE/MetaHuman.
- **HISTORICAL Meshcapade pricing** (now moot, from stale aggregators): paid plans from €99/month; a free tier at €0/month with 2,500 credits and a 10-second video limit; other pages said paid/commercial pricing was quote-only — [TechShark](https://www.techshark.io/tools/meshcapade/); [thetoolsverse](https://thetoolsverse.com/tools/me-meshcapade)

**Viggle — PINOC and JST-2 (newest entrant, Oct 2026)**
- PINOC launch announcement: "Upload a motion video, get skeleton animation as **.fbx/.glb**. Ready for Blender, Maya, whatever your pipeline is… **Free to use**" — [ViggleAI on X](https://x.com/ViggleAI/status/2054308068239040725?lang=en)
- Motion exports on a **65-bone, Mixamo-named skeleton** — [Viggle PINOC](https://viggle.ai/pinoc)
- **JST-2 launched October 25, 2026** — a "physics-native world foundation model" generating 3D scenes, single-image characters, and full-body motion. **Motion output: GLB or FBX from text or video, for Mixamo, MetaHuman, or MetaHuman Body skeletons** — [VP Land, 2026-10-25](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)
- **JST-2 API pricing:** scene from prompt **$0.06**; character **$0.25**; **video-to-motion $0.05 per source second**; text-to-motion **$0.10 per request**. Developers get 100 free credits, 1 credit = $0.01. The browser app (PINOC) is free — [VP Land](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)
- At $0.05/source-second, a 10-second fight clip costs **$0.50** — by far the cheapest per-second rate I found (vs Move s2 at $0.024/s before multipliers, but Move charges per *person*-second on Move One).
- **Critical caveat:** the generated *character* is a Gaussian splat (.vsplat), **not a mesh**, so "its characters therefore can't be exported as FBX or GLB meshes, and retargeting is needed." Scenes are .spz splats. Only the *motion* comes out as FBX/GLB — [VP Land](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)
- An MCP connection supports Codex, Claude Code, and other MCP-compatible agents — [VP Land](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)
- Viggle API docs describe a motion endpoint with download type "mixamo" returning a GLB URL, and a mocap endpoint returning a downloadable FBX animation from source footage — [Viggle API docs](https://docs.viggle.ai/v1/guides/quickstart-mocap)

**BlendCap (offline, one-time purchase — notable for studios that can't send footage to a cloud)**
- **$60 one-time**, offline after setup; tracks **body, hands, fingers, and face from one clip**; **Windows or Linux only**; **NVIDIA GPU strongly recommended**; setup downloads **~11 GB** — [3dxdev comparison](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)
- Review notes it ships built-in **FK-to-IK conversion tools** "so artists can quickly clean up tracked curves," but raises the open question "does curve cleanup take as long as hand keyframing?" — [3dxdev BlendCap review](https://3dxdev.com/assets/blendcap-review-offline-full-body-hand-facial-mocap-from-any-video/)

**Professional multi-camera markerless (Captury, Theia Markerless) — biomechanics-oriented, not game-animation-oriented**
- Theia Markerless licensing is split by customer type: "Non-profit, public sector or academic institutions can take advantage of a lifetime license agreement, while performance centers, apparel manufacturers and sports teams are required to pay an annual license fee." **No dollar amounts are published** — [Theia Markerless, Budgeting for a Markerless System](https://www.theiamarkerless.com/blog/budgeting-for-a-markerless-system)
- Theia "recommends a minimum of eight video cameras for most applications" — [Theia Markerless](https://www.theiamarkerless.com/blog/budgeting-for-a-markerless-system)
- **HISTORICAL (~5 years old):** a Vicon reseller estimated roughly **AUD 35,000 for eight cameras**, with a total of **AUD 40,000–50,000 before software** — [Logemas, Theia Markerless: A First Impression](https://logemas.com/theia-markerless-a-first-impression/)
- **The Captury: no published pricing found.** Described in third-party reviews as institution-priced, i.e. not a solo-creator option (qualitative) — [spybroski comparison](https://www.spybroski.com/blog/posts/best-markerless-motion-capture-tools-a-2026-comparison)
- Generic category figure: a 2026 market report puts professional entry-level multi-view optical systems (4–8 cameras) at approximately **$10,000 to $50,000** — [Valuates markerless motion capture market report](https://reports.valuates.com/market-reports/QYRE-Auto-4M17471/global-markerless-motion-capture-system)

**Suit-based baseline for cost comparison**
- Rokoko Smartsuit Pro II: about **$2,500–3,000**, with finger gloves adding about **$500–1,500**. Higher fidelity than camera-only tools, works outdoors, no occlusion problem — [3dxdev](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)
- Professional studio mocap sessions: **$3,000–$20,000 per day** — [MoCap Online, 2026-03-17](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026); another estimate puts a studio session at **$2,000–5,000** and outsourcing at **$3,000–8,000** — [StraySpark, Mar 2026](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)

**Cascadeur (not capture, but the standard cleanup/authoring companion)**
- Pricing **conflict**: MoCap Online says "Free tier; Pro $96/year" — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026); StraySpark says "Free for personal/indie use (under $100K revenue); Pro $200/year" — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison). **Neither is an official page; flag the conflict.**
- Common practitioner advice is to pair capture with Cascadeur: "professionals pair Cascadeur with a tool like Move.ai or Rokoko for best results" — [spybroski](https://www.spybroski.com/blog/posts/best-markerless-motion-capture-tools-a-2026-comparison)

### Inferences
- For a fighting game specifically, **Move One's universal 60-second-per-clip ceiling is a non-issue** (fight moves are 0.3–2 s), but its **per-person-per-second credit billing makes two-fighter capture twice as expensive**, and Gen 2 doubles it again — a four-times multiplier vs single-fighter Gen 1.
- The **free Epic UE 5.8 plugin plausibly displaces the paid single-camera cloud tools for Unreal-based projects**: it is free for commercial use, processes locally (no ToS questions about uploading footage, no per-second billing), and includes finger capture that Rokoko Vision 3.0 dropped. The trade-off is Windows-only, UE-native output, and a possibly-experimental status.
- Rokoko's removal of dual-camera, live webcam, and fingers in Vision 3.0 suggests Rokoko is retreating from markerless video toward its hardware suit business; that makes Vision a cheap-trial tool rather than a production path.
- Viggle's $0.05/source-second API is roughly an order of magnitude cheaper per second than Move One's effective rates and has no documented per-person surcharge, but it is three days old at the time of writing with zero independent evaluation.

### Gaps
- **DeepMotion's actual 2026 dollar tiers** — the official /pricing-animate3d content I could read had the credit mechanics but not the price table. Every third-party figure conflicts.
- **Move Pro and Move Live dollar pricing, camera counts, and clip limits** — move.ai/pricing returns 404 and the Move Pro help page content was unavailable.
- **RADiCAL's current pricing, clip limits, and whether it is still actively maintained.** The only 2026 mention is a qualitative one (below). Aggregator claims of "free tier, paid from ~$12/mo" come from a 2025-dated listicle — [pxz.ai](https://pxz.ai/blog/15-best-ai-dance-video-generators-in-2025-free-and-paid-tools-compared). Do not report as current.
- **Kinetix** — only aggregator mentions ("free or freemium, premium around $15/month") from the same stale listicle. I found **no 2026 primary source and no evidence of active maintenance**. I also found no evidence it shut down. State as unknown.
- **Motorica, Sloyd, Tripo, Hunyuan, Luma, Runway motion-export features** — no reliable sources surfaced in my searches tying any of these to a video→FBX/BVH skeletal-animation export path. Sloyd and Tripo are mesh/asset generators, not mocap. I will not assert they do video-to-mocap.
- **Adobe** — no Adobe video-to-mocap product surfaced. Mixamo remains a *library/auto-rigger*, not a capture tool; the only 2026 Mixamo-adjacent capture work I found is community tooling (below).
- **Autodesk's non-Flow offering, MotionMaker in Maya**, is an AI animation tool but **generates** motion rather than capturing from video — [Autodesk M&E blog, 2025-06-04](https://blogs.autodesk.com/media-and-entertainment/2025/06/04/meet-motionmaker/). Not in scope as video-to-mocap.
- **Max clip length for Viggle PINOC/JST-2 video input** — not stated in the VP Land coverage.
- Required **fps and resolution** for DeepMotion, Rokoko Vision, Plask, Viggle and the Epic plugin — none of these publish minimums.

---

## Measured/reported accuracy and visual quality; where single-camera breaks

### Takeaway
There is **no independent, standardized benchmark of any commercial video-to-mocap product**, and no 2026 consolidated EMDB/3DPW leaderboard. Academic monocular mesh recovery now sits around **43–47 mm EMDB-1 PA-MPJPE** for the best image-based methods, but *world-grounded* error is an order of magnitude worse (**W-MPJPE in the 200–300 mm range**), which is exactly the root-motion drift animators complain about. The only quantified commercial claim I found is third-party and unsourced: Move.ai at "roughly 85–90% of the positional accuracy of professional optical mocap for full-body locomotion," with the gap widening for fast/occluded/complex motion.

### Cited Findings

**Academic benchmark numbers (camera-frame accuracy — the "pose looks right" metric)**
- **CameraHMR: EMDB-1 PA-MPJPE 43.7 mm**, vs **TokenHMR 55.6 mm** and **HMR2.0a 61.5 mm** in the same comparison table — [Human3R](https://arxiv.org/pdf/2510.06219)
- **OnlineHMR (March 2026)** reports EMDB-1 PA-MPJPE at roughly **46–47 mm** across its ablation configurations, and uses 3DPW and EMDB-1 for camera coordinates and **EMDB-2 for world coordinates** — [OnlineHMR](https://arxiv.org/html/2603.17355v1)
- **MetricHMSR** on EMDB-1 ranks "second in PA-MPJPE and PVE and third in MPJPE," naming **PromptHMR as the top-performing method** — [MetricHMSR](https://arxiv.org/html/2506.09919v2)
- **GenHMR** (Dec 2024) reports EMDB alongside Human3.6M and 3DPW and claims it "consistently outperforms existing methods across key evaluation metrics—PA-MPJPE, MPJPE, and MVE"; its table lists HMR2.0 at 61.5 mm EMDB PA-MPJPE — [GenHMR](https://arxiv.org/pdf/2412.14444)
- **FMPose (arXiv 2601.16763)** 3DPW table: **WHAM 57.8 mm MPJPE, ExtPose 54.2 mm**. The authors caution that mesh-based baselines are "not directly comparable" to their joint-based model — [FMPose](https://arxiv.org/pdf/2601.16763)
- **FMPose3D (CVPR 2026):** MPJPE on Human3.6M decreases **from 49.3 mm to 47.3 mm** with 40 hypotheses — [FMPose3D, CVPR 2026](https://openaccess.thecvf.com/content/CVPR2026/papers/Wang_FMPose3D_monocular_3D_pose_estimation_via_flow_matching_CVPR_2026_paper.pdf)
- **MQ-HMR** (older) reported reducing EMDB PA-MPJPE by **9.4 mm** and MVE by **14.0 mm** — [EMDB-related](https://files.ait.ethz.ch/projects/emdb/main.pdf)
- EMDB dataset scale: **58 minutes / 105k frames** of "challenging 3D human motion recorded in diverse scenes" — [EMDB paper](https://files.ait.ethz.ch/projects/emdb/main.pdf)

**World-grounded / root-motion accuracy (the metric that matters for game root motion)**
- In one 2026 paper's table, **TRAM scored W-MPJPE 221.3 vs GVHMR's 292.7** on world-space evaluation (RICH and EMDB), with GVHMR using TRAM's estimated camera trajectories for fairness. **Single-source, own experimental setup — not a definitive ranking** — [Natural Human Motion Recovery by Aligning High-Order Temporal Dynamics](https://arxiv.org/pdf/2605.26879)
- **GVHMR** predicts world-grounded motion from monocular video using a gravity-aligned ("gravity-view") frame; poses are transformed back to world coordinates using camera rotations. SIGGRAPH Asia 2024, and the repo now lists a **TPAMI 2026** journal version — [GVHMR repo](https://github.com/zju3dv/GVHMR); [arXiv 2409.06662](https://arxiv.org/abs/2409.06662)
- **TRAM** uses SLAM to recover camera motion and the scene background to derive motion scale — a fundamentally different approach from GVHMR's gravity frame — [comparison in](https://arxiv.org/pdf/2605.26879)
- Note: **two-orders-of-magnitude gap** between camera-frame PA-MPJPE (~44 mm) and world-frame W-MPJPE (~220–290 mm) is the quantitative explanation for root drift in single-camera output.

**Commercial quality claims (all third-party or vendor, none independently measured)**
- **Move.ai: "roughly 85–90% of the positional accuracy of professional optical mocap for full-body locomotion. The gap widens for fast, occluded, or complex motion."** — [MoCap Online, 2026-03-17](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026). **Unsourced, and MoCap Online sells competing animation packs — flag the conflict of interest.**
- **RADiCAL: single-camera accuracy "lags" multi-camera setups significantly, and cloud processing can be slow** — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)
- **DeepMotion + Cascadeur cleanup: "roughly 80–85% of professional optical mocap quality"**, per the author's own estimate. DeepMotion body motion is good for locomotion and gestures; **hand and finger output is coarse** — [StraySpark, Mar 2026](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison). **Author sells Unreal and Blender MCP servers — biased source.**
- **Rokoko webcam body capture is "usable" but behind offline tools; its facial capture is solid. Move.ai with four or more cameras is the strongest non-specialized option** — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)
- Rokoko's own Vision 3.0 claim is that the monocular solver was "rebuilt from scratch," giving "cleaner animation and faster processing," and that the single-camera upload workflow is better than the dual-cam option it replaced — **vendor-only, unverified** — [Rokoko](https://www.rokoko.com/insights/introducing-rokoko-vision-3-0)
- One review says Rokoko Vision output "can be shaky and jumpy" — [spybroski](https://www.spybroski.com/blog/posts/best-markerless-motion-capture-tools-a-2026-comparison)
- Early hands-on on the Epic plugin: **Bryan Steagall (Area 505 Performance Capture & Animation)** reported "surprisingly good results, including finger capture at a distance and when fingers were occluded," noted **"expected floating feet,"** and said **all his tests used a static camera** and the plugin "did not perform as well with moving cameras" — [CGChannel](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/). This is the most useful named-practitioner data point I found.

**Documented failure modes (consolidated)**
- Single-camera generic: **depth ambiguity, temporal jitter, foot sliding, trouble with fast action, unusual poses, and occlusion** — [3dxdev](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)
- Practitioner list: **foot sliding, joint pops, limb interpenetration, root drift**; occlusion when the performer turns away from a single camera; poor or uneven lighting and loose clothing degrade tracking; **long continuous takes accumulate drift**; retargeting between mismatched proportions causes **arm clipping and foot-contact timing errors**; **finger tracking is weak without gloves or multi-camera setups**; Rokoko's CPU load can compete with the game engine — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)
- **Capture setting finding that matters for budget: "1080p is recommended; 4K does not meaningfully improve tracking and adds processing time."** No frame-rate requirement was stated — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)
- **GVHMR specifically "flattens fast limbs"** — the open-source `mixamo-llm-mocap` pipeline adds a keypoint-based fitting step explicitly to "restore a high kick to head height." **GVHMR produces no fingers at all**, so that pipeline reads fingers from zoomed hand crops with a separate detector — [mixamo-llm-mocap README](https://github.com/squall01337/mixamo-llm-mocap)
- Same project's documented fixes map one-to-one onto the failure list: contacts from GVHMR's detector + floor + image + physics with planted feet locked and no foot below the floor (foot skate); **hip pops** caught by an automated QA gate; **height drift removed continuously and horizontal drift corrected per footfall** — [mixamo-llm-mocap README](https://github.com/squall01337/mixamo-llm-mocap)
- Fingers are "notoriously difficult" for video AI; **contact-heavy actions like grappling and climbing with holds are poorly handled** — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)
- DeepMotion produces more artifacts on complex or rapid motion specifically named as **breakdancing, gymnastics, sharp direction changes** — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)
- **Foot sliding "persists even in high-quality commercial pipelines"**, per a physics-based refinement paper — [arXiv 2505.03154](https://arxiv.org/abs/2505.03154v1)
- **StableMotion** reports reducing motion pops by **68%** and frozen frames by **81%** — but this is a lab result on a soccer dataset, not a production time-saving measure — [arXiv 2601.19036](https://arxiv.org/html/2601.19036v1)
- **HISTORICAL (~2022, Reallusion forum)**: after fixing feet in DeepMotion or Plask, "you're left with rotating limbs and twisting torso." The poster's workaround: reduce keys, switch to step and linear curves for blocking and foot locks, then restore Bezier — "removed most of the shakes and slides." Anecdotal — [Reallusion forum](https://forum.reallusion.com/519932/Forum255.aspx)
- **Relevant if capturing from film/anime footage rather than live video:** in a Wan Animate test, "fast movements also contain smear frames (several positions drawn in one image to convey speed), and the model read these as extra limbs" — [joneshong blog](https://blog.joneshong.com/en/blog/ai-fix-anime-mocap-dance/)
- Viggle's own stated limitation for JST-2: **"Fast, full-body, strongly periodic motion is where Viggle sees the most room to improve."** Physics is only partly learned; character motion uses learned dynamics but scene collisions and object interactions rely on rule-based physics; hair and clothing move with the body but are not simulated — [VP Land](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)

### Inferences
- The academic numbers and the animator complaints are the same phenomenon viewed two ways: methods are optimized for **PA-MPJPE** (Procrustes-aligned, i.e. root and global orientation *removed*), so pelvis rotation and root translation — precisely what a game rig needs for root motion — are the least-optimized quantities. This is a defensible explanation of why "the pose looks right but the character slides."
- Because **no commercial vendor publishes benchmark numbers on EMDB/3DPW**, there is currently no way to rank Move.ai vs DeepMotion vs the Epic plugin on measured accuracy. Any report claiming such a ranking is relying on marketing or on single-author impressions.
- The practitioner finding that 4K does not improve tracking, combined with Move's 1.25x 4K price multiplier, means **shooting 4K for mocap is a pure cost increase**. Conversely, high **frame rate** is the variable that should matter for fast strikes — and Move prices up to 120 fps at a 2.2x multiplier, implying it genuinely processes it.

### Gaps
- **No independent benchmark of any commercial tool.** I found no third party that ran Move.ai / DeepMotion / Rokoko / the Epic plugin against a ground-truth dataset.
- **No consolidated 2026 EMDB or 3DPW leaderboard.** Papers rerun baselines under their own protocols, and **EMDB-1 (camera frame) and EMDB-2 (world frame) measure different things**, so the numbers above must not be merged into a single ranking.
- **NLF (Neural Localizer Fields)** did not appear in any benchmark table I could surface; I cannot place it relative to CameraHMR/PromptHMR/GVHMR/TRAM.
- No numbers at all for **jitter, foot-slide magnitude in cm, or limb interpenetration frequency** on commercial tools — these are described qualitatively everywhere.

---

## How much manual cleanup real production requires

### Takeaway
The only end-to-end, itemized time log I found is **~4–6 hours for a 45-second dialogue cutscene** through DeepMotion + Cascadeur + Blender + Unreal (single author, self-interested). The traditional-mocap benchmark is **2–4 hours of cleanup per minute of final body animation**. No source gives AI-specific cleanup hours for fight/combat motion, and everyone agrees AI cleanup tools have **not** replaced manual QA.

### Cited Findings
- **Itemized 45-second dialogue cutscene, total ~4–6 hours** — capture session 1–2 h; DeepMotion ~15 min active + 20–30 min processing; **Cascadeur cleanup 15–20 min per pass**; Blender retargeting ~20 min; **curve cleanup ~30 min**; body/face merge ~15 min; export ~5 min; Unreal assembly 1–2 h — [StraySpark, Mar 2026](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison). **Author sells Unreal/Blender MCP servers; the figures are self-reported and flattering to the workflow.**
- Same source's budget tiers: **~$20–50/month** (budget) and **$100–200/month** (professional) for an AI mocap stack, vs **3–5 days of manual animation** or **$3,000–8,000 outsourced** for the equivalent — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison)
- **Traditional body-mocap cleanup: 2–4 hours per minute of final animation** (≈2–4 minutes of cleanup per second of animation). Simpler locomotion cleans up faster at **1–2 hours per minute**; complex choreography takes longer — [MoCap Online, MoCap Data Cleanup Workflow](https://mocaponline.com/blogs/mocap-news/mocap-data-cleanup-workflow). **Vendor sells animation packs; this is not AI-specific.**
- Qualitative per-pipeline time-to-usable estimates: **Move.ai "hours to days (cleanup included)"; Cascadeur "days per animation set"; diffusion tools "hours + significant cleanup"; pre-made packs "hours (retargeting only)"; professional mocap "2–6 weeks with processing/cleanup"** — [MoCap Online, 2026-03-17](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)
- **"AI-assisted cleanup tools are improving but have not replaced manual QA."** Artistic decisions — how much to smooth a performance, fixing subtle foot contacts, handling edge cases like marker swaps — still require human judgment — [MoCap Online](https://mocaponline.com/blogs/mocap-news/mocap-data-cleanup-workflow)
- **Cascadeur AutoPhysics "cuts secondary motion work 40 to 60 percent in real-world testing."** No methodology is given and the article is vendor-adjacent — [MoCap Online, 2026-03-17](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026). **Treat as an unverified claim.**
- Practitioner consensus on *how* to clean: "selectively deleting or smoothing keyframes whilst leaving the key motor movements alone is always going to yield better results than a 'one button' process" — [Blender Artists](https://blenderartists.org/t/fine-tuning-jitter-removal-mocap-data/1477449)
- A 2026 Blender Artists tutorial describes the realistic workflow as "retargeting it to my character rig, and then cleaning up all the messy parts by hand" — no time estimate given — [Blender Artists](https://blenderartists.org/t/mocap-to-blender-how-to-clean-your-animation-fast/1654343)
- There is an active question of whether cleanup even saves time: a BlendCap review asks outright, "does curve cleanup take as long as hand keyframing?" — [3dxdev](https://3dxdev.com/assets/blendcap-review-offline-full-body-hand-facial-mocap-from-any-video/)
- Positioning rather than measurement: 3dxdev says to "expect a cleanup pass" and frames video mocap as "fast blocking that an animator then polishes," claiming this is much faster than keyframing from scratch but **giving no figures** — [3dxdev](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)

### Inferences
- Combining the two credible anchors: if traditional mocap cleanup is 2–4 h/min and the StraySpark log is ~4–6 h for 45 s (≈5–8 h/min including capture and engine assembly), **AI video mocap does not obviously beat traditional mocap on cleanup time per minute** — its advantage is eliminating the $2,000–20,000/day stage cost and the scheduling overhead, not the animator hours.
- For a fighting game the per-minute framing is misleading: fight libraries are hundreds of **0.5–2 second** clips. Per-clip fixed costs (retarget setup, foot-contact authoring, root-motion authoring) dominate, and none of the sources measure that regime.

### Gaps
- **No concrete cleanup time estimate anywhere for combat/martial-arts AI mocap.** Both time anchors are for dialogue/locomotion.
- My searches for **Reddit** threads (r/gamedev, r/blender, r/Unity3D, r/unrealengine, r/animation) returned **no Reddit results at all** through this search tool — the one search explicitly reported "The search didn't return any Reddit threads." Reddit, Polycount, 80.lv hands-on reviews and Discord testimony are therefore **not represented** in these notes. A follow-up pass with direct Reddit access is needed.
- No studio-scale testimony (an actual game team reporting shipped-animation cleanup budgets with AI video mocap).

---

## Fighting-game specifics: fast strikes, aerials, two-character contact

### Takeaway
**No independent, standardized benchmark of AI mocap on fast kicks exists** — the evidence is vendor claims, one hobbyist open-source pipeline, and anecdotal reviews. Every independent or semi-independent source names fast motion, occlusion, fingers and contact-heavy interaction as the *weakest* areas. The only tool with a documented high-frame-rate path is the Move API (priced to 120 fps). I could **not verify** any shipped fighting or martial-arts game using AI video mocap.

### Cited Findings
- **Direct vendor claim (flag as unverified marketing):** Viggle/PINOC says its motion capture handles "sprinting, spinning kicks, fast punch combos" and "holds tracking at full speed with no jitter or dropped frames" — [Viggle AI Motion Capture](https://viggle.ai/pinoc/ai-motion-capture). **This is contradicted by Viggle's own JST-2 limitation statement that "fast, full-body, strongly periodic motion is where Viggle sees the most room to improve"** — [VP Land](https://www.vp-land.com/stories/viggle-launches-jst-2-a-world-model-that-generates-3d-scenes-animation-ready-characters-an)
- **The closest thing to a fight-motion failure test is the open-source `mixamo-llm-mocap` project**, which turns locked-camera video (filmed or AI-generated) into FK animation on a Mixamo character, **including two-performer plates**, and runs in Blender — [GitHub README](https://github.com/squall01337/mixamo-llm-mocap)
  - Fast-limb fix: "**GVHMR flattens fast limbs**"; a keypoint-based fit "restores extremes, such as a **head-high kick**"
  - **Two-character contact:** "Mixamo characters are thicker than humans, so faithful retargets can collide. Clearance is added as a declared, measured offset."
  - Automated QA gate `qa_clip.py` checks **exploded bones, hip pops, foot skate, drifting roots, broken rest poses**
  - `compare_pair.py` checks **separation, strike reach, and intrusion for two performers**; a Blender BVH pass checks real mesh-vs-mesh collision
  - `eval_fidelity.py` measures limb and head angles against keypoints, **foot slide, and jitter**
  - **Operating frame rate: 30 fps.** Showcase content: "a 10-second showcase with nine punches, a slip, and a high side kick"; a duel clip with "four punches and a roundhouse from one performer"
  - Claims "**zero skate**" on planted feet (self-reported, no metric)
  - Requirements: **~8 GB VRAM (developer used an RTX 4080), Blender 5.1+, ~5 GB GVHMR checkpoints, SMPL-X body model (free research registration), user-supplied Mixamo FBX, Windows-proven install**
  - Repo metadata at read time: 413 stars, 83 forks, 28 commits
- **Fast-motion weakness, stated independently:** "a martial arts combo with tight hand positions and quick direction changes will have more noise"; "the accuracy gap grows for fast, occluded, or complex motions"; **grappling and climbing-with-holds are poorly handled**; fingers "notoriously difficult" — [MoCap Online, 2026-03-17](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)
- Move.ai is nevertheless the tool practitioners recommend for this content: it is "recommended for action sequences, fight choreography, and motion involving turns and floor contact" — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison); video-based mocap "suits action sequences, fight choreography, and motion that involves turning and floor contact" — same source
- **For aerial/ballistic moves, the repeated advice is to not capture at all:** Cascadeur's physics tools are recommended "for ballistic motion such as jumps, falls, and throws" — [StraySpark](https://www.strayspark.studio/blog/ai-mocap-indie-developers-2026-comparison); 3dxdev suggests Cascadeur "for impossible or dangerous motion" instead of capture — [3dxdev](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/); Cascadeur is rated "strong for combat and athletic motion" — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)
- **Frame rate:** the Move API is the only product with a published fps ladder, pricing **24 / 30 / 45 / 60 / 90 / 120 fps** at multipliers 0.92x / 1.00x / 1.20x / 1.40x / 1.80x / 2.20x — [Move developer pricing](https://developers.move.ai/docs/pricing/). A third-party listing also claims Move's Advanced tier supports "up to 4K and 120 fps" — [fitgap](https://us.fitgap.com/products/009160/move-ai) (unverified)
- **Multi-person:** Move.ai is claimed to support "single phone or synced multi-camera, up to 22 people" — [spybroski](https://www.spybroski.com/blog/posts/best-markerless-motion-capture-tools-a-2026-comparison), **unverified third-party**. Plask "handles several people in one video" — [3dxdev](https://3dxdev.com/ai-motion-capture-from-video-in-2026-every-tool-compared/)
- **Combat-motion generation as an alternative to capture:** AnimationGPT is an academic/AIGC tool "for generating game combat motion assets," generating "combat style character animations based on text," trained on a dataset derived from game assets — [AnimationGPT GitHub](https://github.com/fyyakaxyy/AnimationGPT)
- Industry skepticism about generative animation in games: a Game Developer piece on Tencent's genAI animation tools notes the demo in *The Hidden Ones* where the AI version fixed a sword clipping through a hat, but questions "whether it makes games more fun" — [Game Developer](https://www.gamedeveloper.com/art/tencent-s-genai-animation-tools-don-t-seem-to-make-games-more-fun)
- Commercial alternative that most fight projects actually use: professionally recorded martial-arts and combat mocap packs, "each movement recorded and optimized by professional martial artists and animators" — [Sidekick Animation](https://sidekickanimation.com/en/category/motion-capture-motions-release/martial-arts/); combat packs with "proper body mechanics and impact timing" — [MoCap Online combat animations](https://mocaponline.com/collections/combat-animations); itch.io also hosts paid and free combat packs in FBX and BVH — [itch.io fighting-game animation assets](https://itch.io/game-assets/tag-animation/tag-fighting)

### Inferences
- The one project that actually targets fight motion (`mixamo-llm-mocap`) had to **add a separate keypoint-refitting stage, a separate hand detector, a per-footfall drift corrector, a collision-clearance offset for two-character contact, and five distinct automated QA scripts** on top of a 2024/2026 SOTA estimator. That is a reasonable proxy for the real amount of engineering a fighting-game pipeline needs on top of any off-the-shelf tool.
- Since GVHMR (the backbone of much open tooling) "flattens fast limbs" and produces no fingers, and since Rokoko Vision 3.0 shipped with no fingers, **a fighting game's weapon grips and fist shapes will not come from video mocap in 2026** — they need hand-authoring or glove hardware.
- Multi-camera at high fps (Move `m2` at 120 fps) is the only configuration in this market whose pricing structure implies it is designed for the velocity and occlusion of two-fighter contact work; at $0.043/s/camera × 2.20 (120 fps) × 1.5 (4 cams) ≈ **$0.142/second/camera**, i.e. roughly **$0.57 per second** of 4-camera 120 fps capture — about **$34 per minute**, which is cheap relative to a stage day but assumes you can build a 4-camera synced rig.

### Gaps
- **No shipped fighting or martial-arts game verified as using AI video mocap.** A vendor blog credits *Sifu* with "using AI mocap for its martial arts combat animations" — [remocap blog](https://remocapp.com/blog/posts/518/ai-motion-capture-revolutionizes-indie-game-development) — but this is a **secondary vendor claim that I could not verify against Sloclap's own statements, and should not be reported as fact.**
- **No source states a minimum frame rate for capturing high-velocity strikes.** The only concrete data points are: Move prices up to 120 fps; `mixamo-llm-mocap` operates at 30 fps and needs an extra refitting stage to recover kick extremes at that rate. That is suggestive but not a requirement.
- **No tool documents aerial/airborne capture behavior** (whether the solver's floor/gravity prior fights a jumping spin kick). GVHMR's gravity-view formulation and the various "foot must not go below the floor" constraints imply risk here, but I found no test.
- **No data on two-fighter *contact* solve quality** (whether solvers resolve interpenetration between two tracked people). `compare_pair.py` exists precisely because nothing upstream does this.

---

## Legal / licensing: commercial game use and copyrighted source footage

### Takeaway
Two very different licensing postures: **DeepMotion grants a perpetual, royalty-free commercial license on paid plans but retains copyright in the output and forbids reselling retargeted animations**, while **Move AI's EULA goes further and states Move AI *owns* the Motion Data and Output, assigns your rights to them, licenses the output back to you for a "Permitted Purpose," forbids using the Output to train any AI system, and takes a perpetual license to train on your uploaded footage**. **Every tool whose terms I read forbids uploading copyrighted third-party footage you don't own or have permission for** — so capturing moves from a film or another game is a ToS breach regardless of the tool. Epic's UE 5.8 plugin is the cleanest: free, local, commercial use allowed.

### Cited Findings

**DeepMotion (terms of use, read 2026-10-27)** — [deepmotion.com/terms-of-use](https://www.deepmotion.com/terms-of-use)
- Freemium: "Our 'Freemium' Plan is for hobbyists, students, or non-commercial use."
- "**Commercial use is granted with a Paid Plan**" — paid plans grant "a perpetual, non-exclusive, fully-paid, royalty-free, worldwide **Commercial license to use, copy, modify**"
- **Restriction:** "you may not retarget the animations contained in the Products to a different character and sell" — i.e. in-game use is fine, **reselling as an animation pack is not**
- Ownership: "**We own all right, title, copyright, and other intellectual property rights and interest in the Products**"; "Products of the site are licensed, not sold." You keep "full ownership to your uploaded Content."
- Input restriction: "**You will not upload to or store in the Site Content that is copyrighted or subject to third party**" rights, "unless you are the owner of such rights or have the appropriate permission from their rightful owner." "You are solely responsible for the Content that you upload."

**Move AI (Move One API EULA, read 2026-10-27)** — [move.ai/move-one-api-eula](https://move.ai/move-one-api-eula)
- Ownership (cl. 6.1.2, 6.3): "**Move AI owns and shall retain ownership of all Intellectual Property Rights**" in the API, Documentation and related data, "**including Motion Data and Output**." **You assign to Move AI any rights in those items that would otherwise vest in you.** Move AI then grants you a licence to use the Output.
- Output licence (cl. 6.3): "worldwide, royalty-free, fully paid-up, non-exclusive transferable, sublicensable licence," subject to cl. 6.5, and tied to the "**Permitted Purpose**" — uploading video to extract motion data and importing the resulting Output into your or a third party's visualisation or ingestion engine.
- **Prohibited Uses (cl. 6.5):** building a product that competes with Move AI; **training any AI System**; **using Output as input to an AI System** — with a narrow exception for creating an original video work for your own use or commercialisation. **Breaching cl. 6.5 voids the Output licence.**
- Sublicensing requires a written agreement binding the sublicensee to terms at least as strict as the EULA.
- Move AI states it will not sell Output, Video Data, Motion Data or Materials.
- Input obligations (cl. 5.1.2, 6.1.1, 6.2, 6.4, 11.1–11.2): you and other Contributors keep ownership of the Video Data and Materials; **you must hold all necessary licences, clearances and consents, including for anyone appearing in them**; you warrant the Materials, Video Data and Output do not infringe "the rights (including Intellectual Property Rights) of any third party"; **business customers must indemnify Move AI**.
- **You grant Move AI a broad, perpetual, irrevocable, sublicensable licence to use your Video Data and Materials to provide the API and to develop, train, and improve its products and services.** Move AI may process personal data in the Materials and Motion Data for training and improvement.
- Move Pro desktop (self-serve cloud processing) has its own separate EULA — [move.ai/move-pro-for-desktop-self-serve-cloud-processing-eula](https://move.ai/move-pro-for-desktop-self-serve-cloud-processing-eula)

**Rokoko**
- Rokoko's Create FAQ states that "**all generated data from text-to-motion and video-to-motion can be used commercially**" — [Rokoko Create](https://create.rokoko.com/)
- The Vision product page links to "Studio, Preview and Vision — Terms of Use," which I **did not read**; the page itself says nothing about commercial use — [Rokoko Vision](https://www.rokoko.com/products/vision)

**Epic / UE 5.8 MetaHuman Animator Markerless Motion Capture**
- "**Free, including for use on commercial projects**" — [CGChannel, 2026-06-18](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/)
- Because **all processing is local**, no footage leaves the machine — this removes the upload-rights and training-licence questions that apply to every cloud tool — [CGChannel](https://www.cgchannel.com/2026/06/get-the-free-metahuman-animator-markerless-mocap-plugin/)

**Autodesk Flow Studio**
- "**Free output is watermarked**, making it suitable for testing or non-commercial uses" — [CGChannel](https://www.cgchannel.com/2025/08/autodesk-cuts-prices-of-flow-studio-subscriptions/)

**Open-source stack licensing note**
- The `mixamo-llm-mocap` pipeline requires the **SMPL-X body model**, obtained via "a free research registration" — [GitHub README](https://github.com/squall01337/mixamo-llm-mocap). SMPL/SMPL-X research registrations are commonly research-only; a commercial game would need to check the SMPL-X commercial licence terms. **I did not verify the current SMPL-X licence text — treat as an open risk.**

### Inferences
- **Move AI's EULA is the most restrictive of the ones I read for a game studio**, on three counts: (a) Move AI asserts ownership of the Motion Data/Output rather than assigning it to you; (b) the explicit ban on using Output "as input to an AI System" would block, for example, feeding captured clips into a motion-matching/learned-locomotion model or a motion diffusion model — a mainstream 2026 game-animation technique; (c) the perpetual training licence over your uploaded footage may be unacceptable for unannounced IP. A studio should get written clarification before committing a fight library to Move.
- **DeepMotion's "may not retarget and sell" clause** is aimed at animation-pack resale, not game shipping, so shipping a fighting game with DeepMotion-derived animations on a paid plan appears permitted — but DeepMotion retaining copyright in the Products is still worth legal review.
- **Capturing moves from copyrighted film or game footage is prohibited by the terms of the tools themselves**, before any question of whether the resulting motion data is a derivative work. The practical implication: film your own performer, or license a mocap pack.

### Gaps
- **Plask's commercial-use terms** — not on the pricing page; I did not locate the ToS.
- **Rokoko's full "Studio, Preview and Vision — Terms of Use"** — not read; only the Create FAQ claim.
- **Autodesk Flow Studio's per-tier commercial-use and output-licence terms** — not read beyond the watermark note.
- **Viggle PINOC / JST-2 terms of service** — not read at all. Given that PINOC is free and the JST-2 API is $0.05/source-second, the commercial-use terms are a material unknown.
- **The Epic plugin's EULA specifics** (beyond "free including commercial") — not read; the Unreal Engine EULA's usual terms presumably apply.
- **Whether motion data extracted from copyrighted footage is itself a derivative work** under copyright law — I found **no legal source addressing this**, and I will not speculate. This is genuinely unsettled and the report should say so rather than assert either way.
- **DeepMotion's ownership clause vs the user's licence**: I could not determine whether DeepMotion's retained copyright in "the Products" survives export into a shipped game build in any practically limiting way. Needs legal review, not research.
