# Generative 3D Motion Models: Text-to-Motion, Motion-to-Motion, In-betweening, and Physics-Based Synthesis (state as of October 2026)

**Scope note:** all findings below concern models that output 3D skeletal animation (joint rotations / SMPL-family parameters / BVH-like skeletons), not video. Everything dated 2022–2024 is labelled historical context. Research-only arXiv preprints are flagged as such; vendor claims are flagged separately from independently verified results.

**Methodological caveat for the report writer:** this research pass hit real limits. Papers-with-Code-style leaderboards for HumanML3D did not surface a canonical 2026 ranking, and a large share of 2026 material is arXiv preprints whose peer-review status I could not confirm. Practitioner/postmortem evidence on shipped games using *generative* motion is essentially absent (see "Quality reality check"). I have not silently filled those holes.

---

## Q1. Leading open-source / open-weight text-to-motion models as of 2026; lineage and current SOTA

### Takeaway
The MDM → T2M-GPT → MoMask → MotionLCM lineage (2022–2024) is now explicitly treated as *baseline*, not SOTA; the 2025–2026 frontier moved to (a) million-scale web-video-derived datasets with multi-billion-parameter autoregressive models (MotionMillion / "Go to Zero", 7B), (b) video-generation-assisted and retrieval-augmented hybrids (ViMoGen, ReMoMask-2), and (c) production-mocap-trained, constraint-controllable diffusion from industry labs (NVIDIA Kimodo). The field is large and fragmented — one curated list counts roughly 70 models labelled 2025 alone — and no single consensus SOTA exists. Critically, **open code is common, open *weights* are less common, and genuinely commercial-use-clean weights are rare to nonexistent** because almost everything inherits SMPL and/or AMASS terms.

### Cited Findings

**Lineage / baseline status (historical context, 2022–2024)**
- MDM (Human Motion Diffusion Model) is the canonical diffusion baseline, arXiv 2209.14916 — [arXiv](https://arxiv.org/pdf/2209.14916)
- MoMask (CVPR 2024, generative masked modeling with residual VQ) official repo is **MIT licensed**, but the repo explicitly warns: "our code depends on other libraries, including SMPL, SMPL-X, PyTorch3D, and uses datasets which each have their own respective licenses that must also be followed" — [GitHub: EricGuo5513/momask-codes](https://github.com/EricGuo5513/momask-codes); paper — [arXiv 2312.00063](https://arxiv.org/pdf/2312.00063)
- MoMask's masked decoding converges "typically within around 10 iterations," which is the source of its speed advantage — [arXiv 2312.00063](https://arxiv.org/pdf/2312.00063)
- MotionLCM (ECCV 2024, latent consistency model for real-time controllable generation) has a LICENSE file in its official repo, but I could **not** confirm its contents; a curated list places "MIT license" adjacent to it ambiguously — [GitHub: Dai-Wenxun/MotionLCM](https://github.com/Dai-Wenxun/MotionLCM); [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- A curated survey repo lists ~150 models from 2020–2024 (including MotionGPT, MDM, T2M-GPT, MoMask, MotionLCM, MotionDiffuse) and **~70 models labelled 2025**, plus 14 datasets labelled 2025. The repo itself is MIT; it does not state licenses for the individual papers/code/datasets it links. Its scope is explicitly **single-person motion without human-object or scene interaction** — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)

**2025 model/dataset wave (names, venues, from the curated list)**
- 2025 datasets: UniMo4D, FineMotion, SnapMoGen, **MotionMillion (ICCV)**, HumanAttr, GBC-100K, STANCE (CVPR), PerMo (CVPR), TMD, **Motion-X++**, MotionPercept (ICLR), **HumanML3D++ (ICCV)**, MotionLib (ICML), Inter-MT2 (ICCV) — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- 2025 models include: X-MoGen, **ReMoMask**, SASI (SIGGRAPH), **MoMask++**, GotoZero (ICCV), MotionGPT3, Motion-R1, MOGO, ACMDM, GENMO, DSDFM (CVPR), MixerMDM (CVPR), **UniPhys (ICCV)**, MotionReFit (CVPR), **LoRA-MDM**, SimMotionEdit (CVPR), MotionStreamer (ICCV), **Kinesis**, SALAD (CVPR), PersonaBooth (CVPR), MotionAnything, Fg-T2M++ (IJCV), **MotionLab (ICCV)**, MotionPCM, Free-T2M, FlexMotion, EnergyMoGen (CVPR), ScaMo (CVPR), **Light-T2M (AAAI)**, MARDM (CVPR), MaskControl (ICCV), LaMP (ICLR), DART (ICLR), Being-M0 (ICML), Motion-Agent (ICLR), ReinDiffuse (WACV), MoLA — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)

**Million-scale scaling line: MotionMillion / "Go to Zero" (ICCV 2025)**
- MotionMillion is described by its authors as the largest human motion dataset to date: **>2,000 hours, 2 million** high-quality text-paired motion sequences, "20× larger" than prior resources; clips mined from web video via a multi-stage pipeline with VLM-generated captions — [arXiv 2507.07095](https://arxiv.org/pdf/2507.07095); [project page](https://vankouf.github.io/MotionMillion/); [ICCV 2025 PDF](https://openaccess.thecvf.com/content/ICCV2025/papers/Fan_Go_to_Zero_Towards_Zero-shot_Motion_Generation_with_Million-scale_Data_ICCV_2025_paper.pdf)
- Model is a **7B-parameter** network, scaled progressively from a 1B base, with claimed emergent zero-shot capability; a reviewer summary states the 7B model beats prior methods on human-judged text alignment while only *matching* them on smoothness and physical plausibility — [alphaXiv](https://www.alphaxiv.org/abs/2507.07095); [Pith review](https://pith.science/paper/2507.07095)
- Dataset hosted at [HuggingFace: InternRobotics/MotionMillion](https://huggingface.co/datasets/InternRobotics/MotionMillion); code at github.com/VankouF/MotionMillion-Codes (per the paper's code link)
- I found **no source explicitly confirming the 7B weights are publicly released** — public code + dataset are the only openness evidence. Treat "open-weight 7B text-to-motion" as unverified.
- Follow-on: Open-UniMo extends the paradigm to MotionMillion, citing "substantially richer motion dynamics and more diverse language descriptions" — [arXiv 2609.14615](https://arxiv.org/pdf/2609.14615)

**Video-assisted generation: ViMoGen (arXiv 2510.26794, Oct 2025)**
- Proposes **ViMoGen-228K**: 228,236 clips / 369.4 hours = 171,542 optical-mocap clips + 41,971 in-the-wild video-derived + 14,723 synthetic-video-derived (171.5K text-motion pairs, 56.6K text-video-motion triplets) — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Architecture: flow-matching diffusion transformer with **Text-to-Motion and Motion-to-Motion branches** plus an adaptive gate; `ViMoGen-light` is a distilled variant that runs without video generation at inference — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Authors state their data comes from "publicly available optical MoCap datasets released under research-friendly licenses" — no license is stated for the released dataset itself — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Self-reported MBench "Motion Generalizability" scores (authors' own benchmark, not independent): **T2M-GPT 0.38, MoMask 0.44, MDM 0.51, MotionLCM 0.55, ViMoGen-light 0.55, ViMoGen 0.68** — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Text-encoder ablation: CLIP 0.35 vs T5-XXL 0.44 on generalizability — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Authors **excluded 13 of 30** candidate optical mocap datasets for poor text-motion consistency and for high-speed motion such as dancing (kept 17) — a direct signal that fast/athletic motion is the hard case — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Their own video-derived motions "carry jitter and inaccurate global translation," mitigated by post-processing — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)

**Retrieval-augmented masked generation: ReMoMask-2 (arXiv 2609.08365, Sept 2026)**
- Single-stage (mask-only) redesign reportedly cuts per-sample inference from ~**0.14s to 0.05s** and surpasses the full mask+residual conference version on HumanML3D FID — [arXiv 2609.08365](https://arxiv.org/html/2609.08365)
- Speed context: MoSa reports average inference time **0.045s vs MoMask 0.062s** — [arXiv 2511.01200](https://arxiv.org/pdf/2511.01200)
- MoMask++ has **public checkpoints** and was independently run by third-party benchmarkers (MUGEN) on a **single NVIDIA L4 GPU** at conditioning scale 5 with 18 masked-decoding steps — the strongest concrete evidence of "runnable locally on modest VRAM" in this lineage, though the license was not stated in the snippet — [arXiv 2607.27581](https://arxiv.org/pdf/2607.27581)

**Industry-lab diffusion with real production mocap: NVIDIA Kimodo (released March 16, 2026)**
- Kinematic motion diffusion model from NVIDIA Spatial Intelligence Lab; generates 3D human **and robot** motion from text + kinematic constraints — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- Trained on **>700 hours of production-quality optical mocap** (Bones Rigplay dataset, with text descriptions); some variants on the public BONES-SEED dataset; G1 robot data retargeted with the SOMA retargeter — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- Two-stage transformer denoiser that **decomposes root and body prediction** specifically to reduce artifacts; the motion representation is designed to minimize "floating and foot skating" — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- Controllability modes: text prompts (including prompt *sequences*), **full-body keyframes and in-betweening**, sparse joint positions/rotations, end-effector constraints on hands and feet, 2D waypoints and dense 2D root paths, and mixed constraints — this is the single most game-pipeline-shaped control surface found — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- Outputs on the SOMA and G1 skeletons; **exportable to ProtoMotions and MuJoCo to train physics-based policies**, and usable as demonstrations for GEAR-SONIC — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- Concrete specs for the published checkpoint `nvidia/Kimodo-SMPLX-RP-v1`: **282M parameters** (0.3B F32); **max 10 seconds = 300 frames at 30 fps**; output = root translation (N×3) + joint rotations (**N×22×3×3**) at 30 fps; PyTorch runtime; tested on RTX 3090/4090/5090, A100, L40S, L4, RTX 6000 Ada, RTX A6000; Ampere/Lovelace/Blackwell; Linux + Windows; released **March 16, 2026** — [HuggingFace model card](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1)
- **License: "NVIDIA Internal Scientific Research and Development Model License" — "This model is for non-commercial research use only."** Commercial use not permitted under the stated terms — [HuggingFace model card](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1)
- No VRAM figure is published on either page — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/); [HuggingFace model card](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1)

**Other 2026 text-to-motion claims (all self-reported, protocols not comparable)**
- MSCoT claims a **48% FID improvement and ~10× faster inference** on HumanML3D, "state-of-the-art controllable text-to-motion" — surfaced via search; treat as unverified vendor-of-itself claim
- MoLingo (motion-language alignment) evaluates on HumanML3D test set with the MARDM-67 protocol over 20 generation runs with confidence intervals — [arXiv 2512.13840](https://arxiv.org/pdf/2512.13840)
- DT3DPE (dual-transformer + residual VQ, January 2026 journal issue) claims to outperform SOTA on HumanML3D and KIT-ML — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S0010448525001526)
- Reconstruction-Anchored Diffusion — [arXiv 2601.14788](https://arxiv.org/pdf/2601.14788); "Embracing Aleatoric Uncertainty" diversity work — [arXiv 2508.20604](https://arxiv.org/html/2508.20604v1); "Think Before You Move" latent motion reasoning — [arXiv 2512.24100](https://arxiv.org/pdf/2512.24100); "Open the Motion Door" atomic motion decomposition (CVPR 2026) — [CVPR 2026 PDF](https://openaccess.thecvf.com/content/CVPR2026/papers/Fan_Open_the_Motion_Door_Atomic_Motion_Decomposition_and_Recomposition_for_CVPR_2026_paper.pdf)
- Lightweight line for local inference: Light-T2M (AAAI 2025) — [arXiv 2412.11193](https://arxiv.org/pdf/2412.11193)

### Inferences
- The *canonical* output format of this entire family is still a **22-joint skeleton at 20–30 fps in SMPL/SMPL-X parameter space (HumanML3D representation)** — not a game rig. Kimodo's published spec (N×22×3×3 rotations + root translation @30fps, 10s cap) is a precise, citable instance of what the whole class emits. Every pipeline therefore needs a retarget step onto the actual character rig, and fingers are typically absent.
- The 10-second / 300-frame cap on Kimodo and the short-clip orientation of HumanML3D-trained models mean these tools are **clip generators, not sequence authoring tools**. Prompt-sequencing (Kimodo) and in-betweening are the workarounds.
- Inference cost is no longer the bottleneck for the masked/consistency-model family: 0.045–0.14s per sample on a single mid-range GPU (L4-class) is reported. The bottleneck is **quality and licensing**.
- There is a clear bifurcation: the models with the best data (Kimodo, 700h production mocap) are explicitly non-commercial, and the models with permissive code (MoMask, MIT) are trained on non-commercial data. **No path to a clean commercial open-weight text-to-motion model was found.**

### Gaps
- Could not obtain a current Papers-with-Code / canonical HumanML3D leaderboard for 2026; no single verified SOTA can be named.
- VRAM figures are essentially unpublished across the board. Only one hard data point (MoMask++ on a single L4, 24GB class) was found, and that was incidental to a third-party benchmark.
- MotionLCM's actual license text was not retrieved. MotionMillion 7B weight availability unconfirmed. ReMoMask/MoMask++ licenses unconfirmed.
- Could not verify OmniControl, PriorMDM, FineMoGen, StableMoFusion, Large Motion Model (LMM), or MotionCraft current status/licenses individually — they appear in the historical portion of the curated list but were not separately sourced in this pass.
- No sourced data on motion *length* limits for anything other than Kimodo.

---

## Q2. Datasets and licensing limits; can AMASS-derived output ship commercially?

### Takeaway
**No — not by default, and the trap is worse than most practitioners assume.** The AMASS license prohibits commercial use outright *and separately prohibits using the dataset to train methods/algorithms/neural networks for commercial use of any kind*. SMPL itself carries the same dual prohibition and is patented. Motion-X is CC BY-NC-SA and contains AMASS as a sub-dataset. HumanML3D is a re-annotation of AMASS. This means the entire mainstream text-to-motion stack is non-commercial at the data layer, independent of whatever MIT license sits on the code. The only clean datasets found are **100STYLE (CC BY 4.0)** and **MotionFix annotations (CC BY 4.0, but the underlying AMASS motion is still governed by AMASS terms)**.

### Cited Findings

**AMASS — the central trap**
- Official license page: use is permitted "only for non-commercial purposes. Any other use, in particular any use for commercial purposes, is prohibited." Commercial licensing: email ps-license@tue.mpg.de — [AMASS license](https://amass.is.tue.mpg.de/license.html)
- **Training restriction (the part people miss):** the license "prohibits the use of the Dataset to train methods/algorithms/neural networks/etc. for commercial use" — [AMASS license](https://amass.is.tue.mpg.de/license.html)
- Redistribution: the Dataset "shall not be copied, shared, distributed, re-sold," may not be sublicensed, and may not be modified or passed to third parties without prior written permission; the sole exception is "one copy for archive purposes only" — [AMASS license](https://amass.is.tue.mpg.de/license.html)
- The AMASS software/loader repo is likewise under a non-commercial scientific research license — [GitHub: nghorbani/amass](https://github.com/nghorbani/amass)
- Confirmed again by the Motion-X project page, which describes AMASS (and GRAB) as "released for academic research only" and free to use "for non-commercial purposes at educational or research institutions" — [Motion-X](https://motion-x-dataset.github.io/)

**SMPL / SMPL-X — a second, independent blocker**
- SMPL-Model license limits use to non-commercial research, education, or artistic projects, and explicitly excludes "incorporation in a commercial product, use in a commercial service, or production of other artefacts for commercial purposes"; it *also* "prohibits the use of the Software to train methods/algorithms/neural networks/etc. for commercial use of any kind" — [SMPL model license](https://smpl.is.tue.mpg.de/modellicense.html)
- The SMPL-Model specification is covered by patents held by the Max Planck Society (**US10395411B2**) — [SMPL-Body license](https://smpl.is.tue.mpg.de/license.html)
- Commercial licensing routes through **Meshcapade**, covering the adult model variants (SMPL+H, SMPL-X, STAR); SMIL (infant) and SMAL (animal) are expected to be separate licenses — [Meshcapade wiki](https://github.com/Meshcapade/wiki/blob/main/wiki/SMPL.md)
- Pricing has never been public; one developer reports you email sales and negotiate — [clad.you blog](https://clad.you/blog/posts/body-pipeline/)
- **Important nuance:** SMPL-Body (a subset excluding the shape blendshapes) is under **CC BY 4.0**, and the SMPL-Body FAQ indicates the SMPL-Model license is needed to *create* bodies while SMPL-Body permits free distribution of created meshes and poses. Read literally, a game could ship *exported animated meshes/poses* under CC BY 4.0 with attribution, while *generating* new bodies from shape parameters still needs the commercial license — [SMPL-Body license](https://smpl.is.tue.mpg.de/license.html); [SMPL made simple FAQs](https://files.is.tue.mpg.de/black/talks/SMPL-made-simple-FAQs.pdf)
- A third-party blog reports **Epic Games acquired Meshcapade (announced February 2026, closing April 2026)**, which would make the future of SMPL commercial licensing unclear. **I could not confirm this from an official source — flag as unverified** — [clad.you blog](https://clad.you/blog/posts/body-pipeline/)
- Alternatives cited by that same (unverified) source: **Naver's Anny** and **Meta's MHR**, both claimed to be permissively licensed; licenses not independently verified — [clad.you blog](https://clad.you/blog/posts/body-pipeline/)

**Motion-X / Motion-X++**
- "All data is distributed under the **CC BY-NC-SA** (Attribution-NonCommercial-ShareAlike) license"; sub-datasets fall under their original licenses, and annotated results are provided only with approval from the original institution — [Motion-X](https://motion-x-dataset.github.io/)
- Scale: **15.6M SMPL-X whole-body pose annotations across 81.1K motion sequences**, with 15.6M frame-level pose descriptions and 81.1K sequence-level semantic labels — [Motion-X](https://motion-x-dataset.github.io/)
- Sources: ~15K online videos, AIST++ and NTU-RGBD120 (multi-view), GRAB and EgoBody (human-scene), HAA500 and HuMMan (action recognition), **AMASS** (body-only mocap). HuMMan is under the S-Lab License v1.0 — [Motion-X](https://motion-x-dataset.github.io/)
- **No labelled sports/games/martial-arts subset** on the Motion-X page; some online-video credits point to kung fu channels, but that is not a labelled category — [Motion-X](https://motion-x-dataset.github.io/)
- Third-party critique: Motion-X "focuses predominantly on sports and gaming" and uses **pseudo-ground-truth** motion labels (vs HumanML3D's ground truth) — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)

**HumanML3D / KIT-ML / MotionMillion scale and critique**
- HumanML3D: 14,616 clips / **28.6 hours** / indoor / ground-truth labels / no video; optical mocap "limited in scale" — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Motion-X: 81,084 clips / 144.2 hours / in-the-wild / pseudo-GT / with video — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- MotionMillion: 2M clips / >>2000 hours / in-the-wild / pseudo-GT / with video — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- HumanML3D originates at CVPR 2022; KIT at Big Data 2016 — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- "Models trained solely on these datasets tend to overfit to a small set of indoor, locomotion-heavy actions"; common test prompts are "dominated by simple, indoor actions" — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)

**100STYLE and LAFAN1 (style transfer / in-betweening datasets)**
- **100STYLE is CC BY 4.0** on Zenodo (100STYLE.zip 1.5 GB; labelled data 14.8 GB; record dated May 2022) — [Zenodo 8127870](https://zenodo.org/records/8127870)
- The widely used `orangeduck/100style-retarget` derivative is also CC BY 4.0 ("licensed under the same terms as the original dataset"), but notes **"all animations in this dataset are missing finger motion,"** and the accompanying skinned character mesh is separately free for *non-commercial research use only* — [GitHub: orangeduck/100style-retarget](https://github.com/orangeduck/100style-retarget/)
- **LAFAN1 license: not determined.** The Robust Motion In-betweening paper states the dataset is made public along with extraction code, but none of the sources found state a license — [arXiv 2102.04942](https://arxiv.org/pdf/2102.04942)
- MotionFix (SIGGRAPH Asia 2024) is offered for **commercial/research** purposes under **CC BY 4.0** — but that license covers MotionFix's *annotations and metadata*, not the underlying AMASS motion, so AMASS terms still bind the motion data — [MotionFix license](https://motionfix.is.tue.mpg.de/license.html)

**Commercially licensed alternatives**
- Commercial mocap libraries are the practical substitute; MoCap Online's standard license covers game use, with its FAQ limiting the personal tier to fewer than 1 million end users and requiring a separate commercial license above that — [MoCap Online standard license](https://mocaponline.com/pages/standard-license)
- Reallusion sells a boxing/martial-arts pack ("18 dynamic motions") as licensed content — [Reallusion content store](https://www.reallusion.com/contentstore/pack/iclone/3d-animation/martial-arts-boxing)
- Uthana publishes a comparison of 3D human motion datasets aimed at exactly this licensing question — [Uthana](https://uthana.com/resources/human-motion-datasets)

### Inferences
- The license stack is **three-layered and each layer can independently block shipping**: (1) code license (often MIT — fine), (2) body model license (SMPL — non-commercial + patented), (3) training data license (AMASS/HumanML3D/Motion-X — non-commercial *and* no commercial training). An MIT badge on a GitHub repo is close to meaningless for commercial viability here.
- Because the AMASS and SMPL licenses both bar *training for commercial use*, the exposure is not limited to shipped motion files — it extends to the model weights themselves. A studio that fine-tunes MoMask on HumanML3D and ships the resulting weights (or even output) is, on a plain reading, in breach of two licenses.
- The SMPL-Body/CC-BY-4.0 carve-out is the single most interesting ambiguity for a game team: it plausibly permits shipping *baked* animated meshes/poses. But it does not cure the AMASS training restriction, and this needs actual legal review, not a research note.
- 100STYLE (CC BY 4.0) is the only clean, substantial, commercially usable motion dataset surfaced in this pass — which is why it dominates style-transfer work. Its lack of finger motion is a direct problem for fighting games (fist formation, guard, grabs).
- A possible Epic/Meshcapade acquisition, if real, is strategically significant: it would put SMPL commercial licensing inside the Unreal ecosystem. Unverified, so treat as a watch item, not a finding.

### Gaps
- LAFAN1's actual license terms — unresolved and important, since LAFAN1 is the standard in-betweening benchmark (Ubisoft-originated).
- Community discussion (Reddit/Discord/forum threads) specifically about shipping AMASS-derived motion commercially: I found **no** such threads. The evidence here is all primary license text, which is arguably better, but it means I cannot report how studios actually behave in practice or whether enforcement has ever occurred.
- Whether Max Planck's commercial AMASS license exists in practice, what it costs, or whether anyone has obtained one.
- Epic/Meshcapade acquisition unconfirmed by any primary source.
- Licenses for MotionMillion (HuggingFace dataset), ViMoGen-228K, HumanML3D++ , MotionLib, and SnapMoGen were not retrieved.

---

## Q3. Quality reality check — what these models actually produce; any shipped games?

### Takeaway
There is a genuine evidence vacuum here: **I found no published studio postmortem, GDC talk, or practitioner evaluation that quantifies the quality of generative text-to-motion output in a shipped game, and no shipped game using text-to-motion generated animation.** What exists is (a) researchers' own admissions of specific artifacts, (b) models designed explicitly to fight foot skating (which is itself evidence the artifact is pervasive), and (c) vendor marketing. The one genuinely shipped, verified instance of ML-driven animation in a AAA title is **EA Sports FC 26's RL goalkeeper positioning** — which is reinforcement learning driving selection/positioning, *not* generative motion synthesis.

### Cited Findings

**Artifacts acknowledged in the literature**
- NVIDIA's Kimodo names **"floating and foot skating"** as the artifacts its motion representation is explicitly built to minimize, and decomposes root/body prediction "to reduce artifacts" — an industry lab designing around these problems in 2026 — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)
- An ACM paper on AI-assisted animation prototyping in Unity states: "controllable characters require animations that are constrained in multiple ways in order to blend together and avoid artifacts such as feet sliding," and notes authoring/iterating such animation is costly — [ACM DL 10.1145/3681757.3697051](https://dl.acm.org/doi/10.1145/3681757.3697051)
- ViMoGen authors admit their video-derived motion "carries jitter and inaccurate global translation," requiring post-processing; and they **dropped 13 of 30 candidate mocap datasets** partly because of high-speed motion such as dancing — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- The same paper: FID-style distribution metrics "compress performance into one score and often diverge from human judgment" — a direct statement from researchers that paper metrics do not track perceived quality — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- Qualitative failure example: T2M-GPT produces "implausible or generic motions" for novel prompts such as "body surfing" — [arXiv 2510.26794](https://arxiv.org/html/2510.26794)
- MotionMillion's 7B model beats prior methods on **human-judged text alignment** but only **matches** them on **smoothness and physical plausibility** — i.e. scale buys semantics, not physics — [Pith review of 2507.07095](https://pith.science/paper/2507.07095)
- Native frame rates in the current generation are **30 fps** (Kimodo output spec; MAAIP downsampled to 30 Hz) — [HuggingFace Kimodo card](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1); [arXiv 2311.02502](https://arxiv.org/pdf/2311.02502)
- Clip length cap of **10 s / 300 frames** on the published Kimodo checkpoint — [HuggingFace Kimodo card](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1)
- Retargeting quality is itself a major error source: a general-motion-retargeting study measured mean global position error of **104.1 mm for GMR vs 247.8 mm for PHC and 139.7 mm for ProtoMotions**, with **PHC at 0% success on "Dance 1" and "Dance 2" due to ground-penetration artifacts** — [arXiv 2510.02252 (alphaXiv)](https://www.alphaxiv.org/abs/2510.02252)

**Shipped ML animation in games — the one verified case**
- EA Sports **FC 26** ships RL-driven goalkeeper positioning, built by EA SPORTS, **Frostbite** (model training integration + runs the model during gameplay) and **SEED** (EA's applied research group) — [EA news](https://www.ea.com/news/fc-26-goalkeepers)
- Scope is deliberately narrow: "RL Positioning is only active in certain situations, and is only used to position the Goalkeeper. The system is not active when making a save or performing a catch" — [EA news](https://www.ea.com/news/fc-26-goalkeepers)
- Training framework described by a SEED research scientist as data-efficient; deep RL sped up training and produced more human-like behaviour than traditional methods — [EA news](https://www.ea.com/news/fc-26-goalkeepers)
- GDC 2026 session: "Reinforcement Learning in 'FC26': Shipping Human-Like Goalkeepers with a Designer-First Approach," describing an approach that lets developers train agents overnight — [GDC Vault](https://gdcvault.com/play/1035700/Reinforcement-Learning-in-FC26-Shipping)
- Alessandro Sestini (EA SEED) is credited with the RL-based movement system of the FC 26 goalkeeper — [EA SEED](https://www.ea.com/seed/news/seed-gameplay-innovation)
- Related research: a sample-efficient RL goalkeeper paper exists but evaluates **FC 25**, so it is a predecessor to the shipped system — [arXiv 2510.23216](https://arxiv.org/pdf/2510.23216)
- Broader 2026 context: "Augmenting Game AI with Deep Reinforcement Learning" — [arXiv 2606.20210](https://arxiv.org/pdf/2606.20210)

**Practitioner sentiment (weak sources, mostly about 2D/sprite AI, included for honesty about the evidence base)**
- A DEV Community piece argues AI-generated sprite sheets break once animated — characters change size between frames, "the feet jump around," movement feels wrong — and cites an r/aigamedev thread acknowledging misaligned sprites — [DEV Community](https://dev.to/gamelabstudio/ai-can-code-your-game-why-are-ai-characters-and-animations-still-such-a-mess-19ad)
- itch.io reviewer on a paid asset pack: animations "fluid" but suspected "at least partially if not fully AI generated without proper disclosure," with some animations "cut off due to size restraints and certain ones not looping properly" — [itch.io](https://itch.io/post/11415837)
- A video-to-animation vendor's own guide attributes foot sliding to unclear footage / camera movement / occlusion, and its remedy is manual post-export cleanup: "adjust root motion and foot contact settings in your engine or animation editor" — [Cinevva guide](https://app.cinevva.com/guides/ai-animation-generators-3d)
- Industry adoption claim (aggregator, unverified): roughly 50% of studios have integrated AI into production pipelines, mostly for targeted tasks rather than replacing artists — [MindStudio](https://www.mindstudio.ai/blog/ai-image-video-gaming-concept-art-trailers)
- GDC 2026 reporting notes **52% of devs are unhappy** about AI in game development (headline figure from a conference write-up; underlying survey not verified) — [Sean Kim blog](https://blog.imseankim.com/gdc-2026-ai-game-development-npcs-procedural-content-voice-acting/)
- Tencent's **VISVISE** claims to cut certain game-art production from "days or months to minutes" — explicitly a **vendor claim** — [Wikipedia: VISVISE](https://en.wikipedia.org/wiki/VISVISE)
- Ubisoft demoed AI-powered game characters at GDC (conversational NPCs, "the game world actually listens") — not animation synthesis — [VGC](https://www.videogameschronicle.com/news/ubisoft-shows-off-ai-powered-game-characters-at-gdc-the-game-world-actually-listens/)

### Inferences
- The honest 2026 answer to "is text-to-motion output shippable in a game?" is: **not as final animation**. The combination of 30 fps native, 10-second clips, 22-joint fingerless skeletons, pseudo-GT training data, acknowledged jitter/foot-skate/floating, and research-only licensing points to **prototyping, blockout, and reference** — which matches the one academic use case found (prototyping in Unity).
- The FC 26 case is instructive for the report's framing: the AAA industry shipped ML in animation in 2026, but as a **controller/selector over hand-authored animation**, with a hard scope boundary drawn around the moments that matter most visually (saves, catches). That is a strong signal about where studios trust ML and where they do not.
- The absence of practitioner evaluations is itself a finding: if generative 3D motion were usable in production, there would be postmortems. There are not.
- Because paper FID "often diverges from human judgment" by researchers' own admission, benchmark-score-based claims in this field should carry low weight in the final report.

### Gaps
- **No shipped game using text-to-motion generated 3D animation was found.** I cannot say none exists, only that no source confirms one.
- No quantitative practitioner study of foot skating, physical implausibility, or cleanup cost for AI-generated 3D motion.
- No r/gamedev or r/MachineLearning threads surfaced despite targeted querying; no Twitter/X animation-TD threads; no 80.lv articles. The practitioner-discussion dimension of the assignment is substantially unmet.
- No sourced data on "mushy/averaged motion" or "limited style" as measured phenomena, only as general critique of dataset narrowness.

---

## Q4. Combat / martial arts specifically, and two-character interaction generation

### Takeaway
Martial-arts motion data exists but is fragmented, small, and mostly unreleased or non-commercial; the single largest martial-arts corpus found is **MotionMillion, whose HuggingFace listing is 23.7% "Martial Arts"** — a genuinely significant number for a fighting game, though it is web-video-derived pseudo-GT. Two-character interaction generation is an active and fast-moving 2025–2026 area (InterGen is now the *baseline*), but the published work is overwhelmingly about conversational/social dyads and co-manipulation, with **contact constraints identified as a known weakness** — exactly the property combat requires.

### Cited Findings

**Martial arts / combat datasets**
- **MotionMillion's HuggingFace listing breaks material down by category including Martial Arts 23.7%**, alongside Fitness, Performance, and Dance — [HuggingFace: InternRobotics/MotionMillion](https://huggingface.co/datasets/InternRobotics/MotionMillion)
- **MAAIP** (Multi-Agent Adversarial Interaction Priors, imitation from fighting demonstrations for physics-based characters) is the closest fit for physics-based fight imitation: **boxing and QwanKiDo** mocap, Qualisys optical system, **22 cameras, 46 anatomical landmarks**, downsampled to **30 Hz**, retargeted to the simulated character. Authors said they planned to share the datasets upon acceptance; **release not confirmed** — [arXiv 2311.02502](https://arxiv.org/pdf/2311.02502)
- **Kickboxing combat mocap** recorded with an **Xsens suit at 180 Hz** from one kickboxing volunteer, covering variations in speed and target height, for physics-based humanoid control — [arXiv 2603.01294](https://arxiv.org/pdf/2603.01294)
- **HKMALA-Motion:** 134 real traditional Chinese martial-arts sequences converted to a common **SMPL-H** representation (not boxing-specific) — [arXiv 2609.19927](https://arxiv.org/pdf/2609.19927)
- **KungFu-Fiesta / MagicFight:** synthetic *video* dataset for personalized two-person combat video generation — **>500 videos, ~10 s each at 60 fps**, each sample containing a combat video, two character-ID reference images, and a **pose map sequence**; listed on HuggingFace as `MingfuYAN/KungFu-Fiesta` — [arXiv 2601.02107](https://arxiv.org/pdf/2601.02107)
- **Boxing punch IMU data (Zenodo):** wrist-mounted sensors at 200 Hz for jabs, hooks, uppercuts — **not full-body**, poor fit for motion generation — [Zenodo 14965635](https://zenodo.org/records/14965635)
- **MADS (Martial Arts, Dancing and Sports)** dataset from City University of Hong Kong — [MADS](http://visal.cs.cityu.edu.hk/research/mads/)
- Karate-specific mocap analysis of highly-skilled athletes (templates/matching/visualization) — [PMC5713128](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5713128/)
- Commercial option: Reallusion's "Martial Arts - Boxing" iClone pack, 18 dynamic motions, licensed content rather than research data — [Reallusion](https://www.reallusion.com/contentstore/pack/iclone/3d-animation/martial-arts-boxing)

**Two-character interaction generation (2025–2026)**
- **InterGen** (Liang et al., 2024) is now the standard baseline and the standard *evaluator* for interaction work — [ICLR 2026 paper using an InterGen-trained evaluator](https://proceedings.iclr.cc/paper_files/paper/2026/file/a99f732df9b668284b449da0214a3286-Paper-Conference.pdf)
- **Known weakness directly relevant to combat:** "InterGen employs a Transformer with cross-attention to improve the naturalness of dual-human actions, but it lacks contact constraints with objects, which leads to hand–object misalignment" — [arXiv 2604.20336](https://arxiv.org/html/2604.20336v1)
- **InterCompose / InterActor (ICLR 2026):** builds text-to-interaction data by *composing single-person motions* — "many interactions can be composed from single-person motion primitives when composition is text-grounded and constrained by inter-agent geometry and timing" — [ICLR 2026](https://proceedings.iclr.cc/paper_files/paper/2026/file/a99f732df9b668284b449da0214a3286-Paper-Conference.pdf)
- **Distance-aware text-to-interaction generation**, IEEE TVCG Vol 32 Issue 3, March 2026 — keeps plausible inter-character distances while matching text — [IEEE TVCG](https://doi.org/10.1109/tvcg.2026.3651382)
- **MIME** (Multimodal Interactive Motion Encoder) for two-person interactive motion: stream-based co-attention with explicit interaction features and curriculum-based contrastive training — [arXiv 2607.22702](https://arxiv.org/html/2607.22702v1)
- **InterEdit:** text-guided dyadic motion *editing* benchmark with paired source-target motions plus edit instructions — [arXiv 2603.13082](https://arxiv.org/html/2603.13082v2)
- **Two-in-One:** interaction VAE compresses two-person sequences into a single latent token stream; diffusion transformer generates latents from text; reports improvements over two-branch methods on R-Precision, FID, MM Dist, Diversity, and **~4× faster inference** — [arXiv 2412.16670](https://pith.science/paper/2412.16670)
- **It Takes Two:** real-time *co-speech* two-person interaction via reactive auto-regressive diffusion — conversational, not combat — [arXiv 2412.02419](https://arxiv.org/pdf/2412.02419)
- **Multi-Person Interaction Generation from Two-Person Motion Priors** — scaling dyadic priors to groups — [arXiv 2505.17860](https://pith.science/paper/2505.17860)
- **Object-guided human-human co-manipulation** (stability-driven) — [arXiv 2604.20336](https://arxiv.org/html/2604.20336v1)
- Interaction dataset from the 2025 wave: **Inter-MT2 (ICCV 2025)** — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- Note: the main curated text-to-motion survey repo explicitly **excludes** human-object and scene interaction from scope, so interaction work is tracked separately and is less consolidated — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)

### Inferences
- For a fighting game, the most promising *data* route found is MotionMillion's martial-arts slice (23.7% of 2M clips ≈ hundreds of thousands of clips, >400 hours implied) — but it is web-video-derived pseudo-GT, so expect jitter, bad global translation, and no reliable contact; and its license was not verified.
- The structural problem for combat is **contact**. Interaction models optimize inter-character *distance* and *naturalness*, not impact timing or contact correctness. The co-manipulation paper's critique of InterGen (no contact constraints → misalignment) generalizes directly: a generated punch will land near, not on, the opponent. This is why the physics-based line (Q5) matters more for combat than the kinematic line.
- MAAIP remains the most conceptually correct approach for combat (adversarial multi-agent imitation from fight mocap, physics-based) but its data release is unconfirmed, which limits reproducibility.
- KungFu-Fiesta is video+pose-map, not 3D skeletal — useful as reference/eval, not as animation source.
- Fingers are a recurring absence (100STYLE explicitly; 22-joint HumanML3D representation generally), which is a real problem for fists, guards, and grabs.

### Gaps
- MotionMillion license and whether its martial-arts slice is usable/clean; no verification of its quality in that category.
- MAAIP dataset availability.
- No dedicated two-character *combat* interaction model found — all 2025–2026 dyadic work surfaced is conversational, co-manipulative, or generic-text. InterHuman and Duolando were not separately sourced in this pass.
- No evaluation of how well any interaction model handles strikes, blocks, clinches, or throws.

---

## Q5. Physics-based and RL motion synthesis; what's runnable in 2026 and at what cost

### Takeaway
This is where the real 2025–2026 momentum is, driven largely by humanoid *robotics* rather than games — which is good news (fast progress, open frameworks: ProtoMotions, Isaac Lab, MuJoCo) and bad news (objectives are robot-shaped, and retargeting/export to a game rig is an unsolved plumbing problem with no official FBX path). The lineage is explicitly documented as DeepMimic → UHC → PHC → PHC+ → MaskedMimic → FARM. Physics does fix foot sliding by construction, but measured retargeting errors are large and some methods fail outright on dynamic motion.

### Cited Findings

**Lineage, stated in the 2026 literature**
- A 2026 paper traces the field from **DeepMimic through UHC and PHC to PHC+ and MaskedMimic, with FARM as the latest high-dynamics extension**; notes PHC "removed external force modules and achieved more stable tracking on large-scale datasets" — [arXiv 2603.01294](https://arxiv.org/pdf/2603.01294)
- PHC is characterized in 2026 work as "a large-scale tracker that scales to thousands of clips" — [arXiv 2608.03227](https://arxiv.org/pdf/2608.03227)

**Measured quality / failure modes**
- General Motion Retargeting (GMR) study: mean global position error **104.1 mm (GMR) vs 247.8 mm (PHC) vs 139.7 mm (ProtoMotions)**; **PHC scored 0% success on "Dance 1" and "Dance 2" due to ground-penetration artifacts** — [arXiv 2510.02252](https://www.alphaxiv.org/abs/2510.02252) (note: this paper predates 2026)

**2026 physics-based work**
- **Spherical Latent Motion Prior** for physics-based simulated humanoid control; includes the kickboxing Xsens dataset — [arXiv 2603.01294](https://arxiv.org/pdf/2603.01294)
- **GenTrack:** physical alignment for robot-native motion generation and zero-shot humanoid tracking; builds on **fixed ProtoMotions and SONIC checkpoints**, targets Unitree G1; results show the ProtoMotions branch improves out-of-distribution coverage and key-body accuracy with split-dependent trade-offs — [arXiv 2608.01410](https://arxiv.org/html/2608.01410v2)
- **PhyGile:** physics-prefix guided motion generation for agile general humanoid motion tracking — [arXiv 2603.19305](https://arxiv.org/pdf/2603.19305)
- **BFMTrack:** latent sequence optimization for physics-based motion tracking with behavioral foundation models — [arXiv 2606.25056](https://arxiv.org/pdf/2606.25056)
- **PhysMoDPO:** "Physically-Plausible Humanoid Motion with Preference Optimization," PyTorch implementation public — [GitHub: Mael-zys/PhysMoDPO](https://github.com/Mael-zys/PhysMoDPO)
- **CLOT:** closed-loop global motion tracking for whole-body humanoid teleoperation — [arXiv 2602.15060](https://arxiv.org/pdf/2602.15060)
- **PFM-HR:** pose flow matching for humanoid robots — [arXiv 2608.03227](https://arxiv.org/pdf/2608.03227)
- **UniPhys (ICCV 2025)** and **Kinesis (2025)** appear in the curated model list as physics-adjacent entries — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- Scalable motion in-betweening via diffusion **and physics-based character adaptation** — [arXiv 2504.09413](https://arxiv.org/pdf/2504.09413)
- **Skeleton2Humanoid:** animating simulated characters for physically-plausible motion in-betweening (historical context) — [arXiv 2210.04294](https://arxiv.org/pdf/2210.04294)

**Isaac Lab / tooling and the game-export gap**
- **Isaac Lab 3.0 General Availability targeted for end of October 2026**; highlights include Isaac Lab Mimic and SkillGen demonstration-generation workflows and LEAPP policy export/deployment commands — [IsaacLab releases](https://github.com/isaac-sim/IsaacLab/releases); [NVIDIA Isaac Lab](https://developer.nvidia.com/isaac/lab)
- Isaac Lab 2.3 added dictionary observation spaces, Automatic Domain Randomization, and Population Based Training; docs note developers are expected to have **pre-collected data** for humanoid environments — [NVIDIA blog](https://developer.nvidia.com/blog/streamline-robot-learning-with-whole-body-control-and-enhanced-teleoperation-in-nvidia-isaac-lab-2-3/); [Isaac Lab docs](https://docs.nvidia.com/isaaclab/features/examples-data-generation-and-imitation-learning-for-humanoids)
- Isaac Lab framework paper (Sept 2025) — [PDF](https://d1qx31qr3h6wln.cloudfront.net/publications/Isaac%20Lab,%20A%20GPU-Accelerated%20Simulation%20Framework%20for%20Multi-Modal%20Robot%20Learning.pdf); [arXiv 2511.04831](https://arxiv.org/html/2511.04831v1)
- **FBX import exists for geometry only:** "Omniverse Kit includes the mesh converter tool that uses the ASSIMP library to import assets from various mesh formats (e.g. OBJ, FBX, STL, glTF, etc.)" — this covers meshes, not motion data for imitation — [Isaac Lab docs](https://isaac-sim.github.io/IsaacLab/main/source/how-to/import_new_asset.html)
- **The motion-data gap, from a maintainer-facing issue:** "NVIDIA currently does not provide an official direct exporter from Isaac Sim that outputs AMP-ready .npz files for custom humanoid models." The typical route is extracting motion data — usually from USD stage animations or other 3D animation sources — and converting it to the .npz format Isaac Lab requires. The thread points to **GMR retargeting, which "allows you to retarget any SMPL or BVH motions to your own robot,"** implying **BVH is the workable intermediate** and an FBX→BVH step is needed — [IsaacLab issue #3870](https://github.com/isaac-sim/IsaacLab/issues/3870)
- **Kimodo → physics bridge:** Kimodo motions "can be exported to ProtoMotions and MuJoCo for training physics-based policies, and they can serve as demonstrations for GEAR-SONIC" — the clearest documented kinematic-generation → physics-tracking handoff found — [NVIDIA project page](https://research.nvidia.com/labs/sil/projects/kimodo/)

**Combat-relevant physics work**
- **MAAIP** is the explicit physics-based fighting imitation method (multi-agent adversarial interaction priors from boxing/QwanKiDo demonstrations), in the AMP/ASE lineage — [arXiv 2311.02502](https://arxiv.org/pdf/2311.02502)

### Inferences
- Physics-based tracking is the right tool for the stated combat problem (foot sliding, contact-correct strikes) because contact and non-penetration are enforced by the simulator rather than learned. The Kimodo → ProtoMotions/MuJoCo → policy path, plus MAAIP's adversarial fight-imitation framing, is the most technically credible 2026 route to contact-correct combat animation.
- But the **game-ready pipeline does not exist as a product**. There is no official simulate-then-bake-to-FBX path. A studio would have to build: generate/retarget → BVH/npz → Isaac Lab or MuJoCo policy training → rollout capture → retarget to game rig → FBX. Every step is documented in pieces; nothing is turnkey.
- Cost: unquantified in sources, but the architecture implies GPU-hours of RL training per behaviour class, which is why the FC 26 team highlighted "train agents overnight" as a selling point — overnight-per-behaviour is the state of the practical art.
- The field's center of gravity shifted to humanoid robots (Unitree G1 targets everywhere). This means objectives optimize for real-robot feasibility, not stylized exaggerated combat readability — a real mismatch for a fighting game, where animation is deliberately non-physical (impact frames, hold poses, anticipation).

### Gaps
- No sourced VRAM/GPU-hour/$ figures for training or running any physics-based motion policy in 2026.
- Isaac Lab 3.0 GA shipping status unconfirmed.
- Could not source current status of AMP/ASE/CALM/PULSE individually, or any Tencent/NetEase physics-animation work beyond the VISVISE vendor claim.
- No examples found of a game studio actually baking simulation-trained motion to FBX for shipping.

---

## Q6. Runtime neural animation in games (motion matching, learned motion matching, neural state machines)

### Takeaway
Classic motion matching remains the production standard; **learned motion matching's memory savings came at a runtime performance cost**, and no source confirms a specific 2026 shipped game using it. The verified 2026 shipped neural-animation instance is again FC 26's RL goalkeeper (positioning only), plus a GDC 2026 session on neural skeletal physics from LightSpeed Studios.

### Cited Findings
- Learned Motion Matching (Holden et al. 2020, historical context) "uses neural networks to compress the database and speed up selection" — [ResearchGate](https://www.researchgate.net/publication/343616124_Learned_motion_matching)
- **The tradeoff:** a later paper states the 2020 method "reduced memory requirements, but it resulted in increases in performance costs," and proposes a sparse mixture-of-experts approach with Lipschitz continuity to address it (SIGGRAPH/MIG-track, ACM) — [ACM DL 10.1145/3623264.3624442](https://dl.acm.org/doi/10.1145/3623264.3624442)
- Production status: "Motion Matching remains an essential animation tool in many AAA game productions" — [ACM DL 10.1145/3623264.3624442](https://dl.acm.org/doi/10.1145/3623264.3624442)
- An open-source learned-motion-matching implementation exists — [GitHub: pau1o-hs/Learned-Motion-Matching](https://github.com/pau1o-hs/Learned-Motion-Matching)
- Adjacent 2025–2026 runtime work: **Environment-aware Motion Matching** — [arXiv 2510.22632](https://arxiv.org/pdf/2510.22632); **MotionBricks:** scalable real-time motions with a modular latent generative model and "smart primitives" — [arXiv 2604.24833](https://arxiv.org/pdf/2604.24833); **Half Pound Filter** for real-time animation blending — [arXiv 2602.21702](https://arxiv.org/pdf/2602.21702); **MOCHA:** real-time motion characterization via context matching — [arXiv 2310.10079](https://arxiv.org/pdf/2310.10079); **Generative Motion Matching** (example-based synthesis) — [arXiv 2306.00378](https://arxiv.org/pdf/2306.00378)
- GDC Vault historical talks: "Character Control with Neural Networks and Machine Learning" — [GDC Vault](https://www.gdcvault.com/play/1025389/Character-Controlwith-Neural-Networks); "A New Era of Performance Capture with Machine Learning" — [GDC Vault](https://www.gdcvault.com/play/1026328/A-New-Era-of-Performance)
- Daniel Holden (Ubisoft La Forge) has given GDC talks on data-driven animation systems and on a neural network for automatically cleaning motion capture data — **years unconfirmed in the sources found** — (surfaced via GDC schedule search; no stable citation obtained)
- **GDC 2026 Machine Learning Summit** included "SoftMax: Neural Simulation of Skeletal Physics" from **LightSpeed Studios** — the closest 2026 neural-animation session found — [GDC schedule](https://schedule.gdconf.com/search/ai)
- **GDC 2026 ML Forum: Ask the Experts** (March 9, 10:30–11:30am) panel included Alessandro Sestini (EA SEED) and Alexis Rolland (Ubisoft La Forge), with Embark, Blizzard, Orion, and Google DeepMind — a panel, not an animation talk — [GDC schedule](https://schedule.gdconf.com/search/ai)
- Alexis Rolland's own GDC 2026 ML Summit talk was about **image generation**, with La Forge building its own image models — not animation — [Invisible Friends GDC 2026 account](https://www.invisiblefriends.net/gdc-2026-a-personal-account/)
- A vendor blog claims "Ubisoft's locomotion systems work internally this way" (learned motion matching) but names no game and offers no verification — **flag as unverified vendor claim** — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)

### Inferences
- Runtime neural animation in 2026 shipped titles is narrow and decision-layer-shaped (RL policy choosing/positioning among authored animation) rather than generative-pose-shaped. Learned motion matching appears to have stalled commercially on the memory-vs-CPU-cost tradeoff that the 2023 MoE paper identifies.
- For a fighting game specifically, motion matching is a poor fit anyway (fighting games need frame-exact, designer-authored timing windows), so this line is less relevant to the stated use case than the physics or generation lines.

### Gaps
- No confirmed 2026 shipped game using learned motion matching.
- Could not obtain stable citations for the Ubisoft La Forge animation talks or confirm their years.
- No EA SEED / Sony / NVIDIA / Tencent / NetEase neural-animation tech-blog posts from 2025–2026 surfaced beyond the EA goalkeeper material.
- Neural state machines (Starke et al. lineage) — not sourced in this pass.

---

## Q7. Commercial products wrapping these capabilities, and 2026 pricing

### Takeaway
NVIDIA has gone genuinely open on facial animation (**Audio2Face, MIT license for the stack; NVIDIA Open Model License for the newer model weights**) while keeping body-motion models research-only (Kimodo). Consumer-facing text-to-motion products exist (DeepMotion **SayMotion**, Cascadeur's AI tools) but **2026 pricing could not be established from primary sources** — every figure found came from aggregator blogs that contradict each other. Free tiers are consistently **non-commercial and length-capped**, which is the key fact for a shipped game.

### Cited Findings

**NVIDIA**
- NVIDIA released **Audio2Face as open source under an MIT license**, "releasing the full stack to foster community contributions and optimizations"; the Audio2Face-3D SDK and the **Unreal Engine 5 plugin** are also MIT-licensed per NVIDIA's own repo table — [VideoCardz](https://videocardz.com/newz/nvidia-releases-audio2face-as-open-source-for-game-developers); [GitHub: NVIDIA/Audio2Face-3D](https://github.com/NVIDIA/Audio2Face-3D); [GitHub: NVIDIA/Audio2Face-3D-Samples](https://github.com/NVIDIA/Audio2Face-3D-Samples)
- **The model weights are not MIT:** newer Audio2Face-3D models ship as open weights in ONNX-TRT format under the **NVIDIA Open Model License** — [HuggingFace: nvidia/Audio2Face-3D-v3.0](https://huggingface.co/nvidia/Audio2Face-3D-v3.0)
- ACE for Games overview / docs — [Omniverse docs](https://docs.omniverse.nvidia.com/audio2face/latest/overview.html); [NVIDIA ACE for Games](https://developer.nvidia.com/ace-for-games); [NVIDIA digital humans](https://www.nvidia.com/en-gb/use-cases/digital-humans)
- NVIDIA made AI avatar creation free — [Datamation](https://www.datamation.com/artificial-intelligence/nvidia-avatar/)
- **Inworld AI** is named as an ACE partner that integrated Audio2Face; NVIDIA also describes a Streamlabs + Inworld streaming assistant — partnership/integration, not a licensing deal — [Datamation](https://www.datamation.com/artificial-intelligence/nvidia-avatar/)
- Commentary: the ACE platform "remains tied to Nvidia GPUs, so widespread adoption of free software drives hardware demand" — [GamingBench](https://gamingbench.net/nvidia-open-sources-audio2face-a-game-changer-for-real-time-facial-animation/)
- **Body motion remains closed for commercial use:** `nvidia/Kimodo-SMPLX-RP-v1` is "for non-commercial research use only" — [HuggingFace](https://huggingface.co/nvidia/Kimodo-SMPLX-RP-v1)

**Cascadeur**
- **AutoPhysics is included in the free tier but in limited form; the free tier is non-commercial with exports capped at 30 seconds** — [AIChief](https://aichief.com/ai-3d-tools/cascadeur/)
- Indie plan reported at **~€12/month, or $8/month billed annually**, for developers under a revenue cap — [VisionVix](https://visionvix.com/best-ai-animation-tool/); [AIJet](https://aijet.cc/item/cascadeur)
- **Contradicting figures:** other sources list Indie at **$15 or $19/month**, and Pro anywhere from **~$25 to $49/month** depending on source and billing term — [AICentralResources](https://www.aicentralresources.com/compare/deepmotion-vs-cascadeur); [VisionVix](https://visionvix.com/best-ai-animation-tool/)
- Sources note Cascadeur's tiers have changed more than once — [AIJet](https://aijet.cc/item/cascadeur)

**DeepMotion**
- **Text-to-motion is DeepMotion's SayMotion product, not Animate 3D** (which is video mocap) — [NiraAnimates](https://niraanimates.com/blog/best-ai-3d-animation-software)
- Animate 3D is **credit-based at roughly one credit per second**, free tier **60 seconds/month, non-commercial** — [Cinevva](https://app.cinevva.com/guides/ai-animation-generators-3d)
- Paid entry reported as **$9/month (Starter, annual)** in one source and **$15/month** in others; higher tiers **$17–$50/month**; Studio **~$83/month** — [AICentralResources](https://www.aicentralresources.com/compare/deepmotion-vs-cascadeur)
- DeepMotion markets gen-AI figure-skating animation with the claim that "what previously required weeks of meticulous manual work now takes mere minutes" — **vendor claim, and the page does not discuss foot skating** — [DeepMotion blog](https://www.deepmotion.com/post/create-and-animate-figure-skaters-using-gen-ai-in-minutes)
- A vendor video-to-animation guide's remedy for foot sliding is manual engine-side cleanup of root motion and foot contact settings — [Cinevva](https://app.cinevva.com/guides/ai-animation-generators-3d)

**Other**
- Tencent **VISVISE** claims to reduce certain game-art production from days/months to minutes — **vendor claim** — [Wikipedia](https://en.wikipedia.org/wiki/VISVISE)
- Uthana publishes dataset-licensing comparisons aimed at commercial motion needs — [Uthana](https://uthana.com/resources/human-motion-datasets)
- A 2026 vendor roundup of AI animation tools for game developers — [MoCap Online](https://mocaponline.com/blogs/mocap-news/ai-game-animation-tools-2026)

### Inferences
- The commercially safest AI-animation capability in 2026 is **facial/lipsync** (Audio2Face, MIT stack + NVIDIA Open Model License weights, UE5 plugin), not body motion. That asymmetry is worth stating plainly in the report: a studio can ship NVIDIA facial animation today; it cannot ship NVIDIA body motion.
- Commercial text-to-motion products are the practical way around the AMASS/SMPL licensing trap, because the vendor assumes the data-rights problem — but only on paid tiers, since every free tier found is explicitly non-commercial.
- Cascadeur's position is different in kind from the generative tools: AutoPhysics is a *physics assist on animator-authored keys*, which sidesteps both the licensing trap and the quality trap. For a fighting game needing exaggerated-but-plausible combat, this is the most defensible commercial tool surfaced.
- The free tiers' caps (30 s Cascadeur export, 60 s/month DeepMotion) confirm these are evaluation tiers, not production tiers.

### Gaps
- **No primary-source 2026 pricing was obtained for any vendor.** All figures above are from aggregator blogs that contradict each other. The report should present these as unverified ranges and recommend checking cascadeur.com and deepmotion.com/saymotion directly.
- SayMotion's own pricing not found.
- **Motorica: no information found at all** — no 2026 announcements, pricing, or licensing surfaced.
- **Anything World: not sourced** in this pass.
- No 2026 ACE release notes or licensing changes found; the Audio2Face open-sourcing material appears to be 2025-dated and should be labelled accordingly.
- No pricing for Inworld.
