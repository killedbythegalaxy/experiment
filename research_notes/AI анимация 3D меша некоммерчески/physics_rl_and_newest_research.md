# Physics/RL character animation (hobbyist-runnable) + verified catalogue of newest 2026 research

State of knowledge: **27 October 2026**. All verification verdicts below are explicit; anything unconfirmed is parked in Gaps.

---

## Q1. The physics/RL lineage: DeepMimic → AMP → ASE → CALM → PULSE → PHC → PHC+ → MaskedMimic → FARM, ProtoMotions, Isaac Lab, and 2026 successors

### Takeaway
The live, maintained center of gravity is **NVIDIA ProtoMotions** (Apache-2.0, 2.4k stars, MaskedMimic inside it), with **PHC/PULSE** (Zhengyi Luo) as the still-cited but older single-purpose repos. The honest hobbyist problem is not the code — it is the compute: ProtoMotions' own documented training recipes are quoted in **A100-GPU-hours (4× A100 for ~12 h on AMASS; 24× A100 for the BONES recipe)**, not consumer-GPU hours, and **no repo in this lineage ships a BVH or FBB exporter** — BVH is an *input* format only.

### Cited Findings
- **ProtoMotions** is NVlabs' GPU-accelerated framework for "training physically simulated digital humans and humanoid robots"; **2.4k stars, 394 forks, 46 watchers, 13 open issues, 76 commits on main**, source code **Apache-2.0**; NVIDIA pretrained models carry the separate **NVIDIA Open Model License** — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- ProtoMotions supported simulator backends: **NVIDIA Newton, MuJoCo (CPU), IsaacGym, IsaacLab**, with **IsaacSim 5.0+** for rendering; **Genesis** is community-contributed and explicitly marked "unverified" — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- ProtoMotions install is not a one-liner: the repo links an Installation Guide and ships **`Dockerfile.isaacgym` and `Dockerfile.newton`** plus **per-simulator requirements files** (Genesis, IsaacGym, IsaacLab, MuJoCo, Newton) — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- **Documented training cost**: full AMASS (40+ hours of motion) in **about 12 hours on 4× A100**; the multi-GPU BONES-SEED recipe uses **24× A100 with 13K motions per GPU**. The repo does not state a minimum consumer GPU — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- ProtoMotions **input** motion formats: **AMASS, PHUMA, SEED BVH, SEED G1 CSV, Kimodo outputs**, plus retargeting to robots via **PyRoki**. A dedicated **"SEED BVH Data Preparation (SOMA Skeleton)"** guide exists in the docs/README — [ProtoMotions docs](https://nvlabs.github.io/ProtoMotions/user_guide/experiments.html); [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- ProtoMotions **output/deployment** path is a **single ONNX model with observation computation baked in**. **BVH/FBX export is not mentioned anywhere on the repo page** — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- **MaskedMimic** is the named generative policy in ProtoMotions; it "formulates character control as a motion inpainting problem" so that "a single unified controller learns to synthesize full-body motions from partial observations", and it **requires a pre-trained Mimic expert model that you must train first** — [ProtoMotions Experiments docs](https://nvlabs.github.io/ProtoMotions/user_guide/experiments.html); [NVIDIA MaskedMimic project page](https://research.nvidia.com/labs/par/project/maskedmimic.html)
- ProtoMotions was announced by author **Chen Tessler** as containing "our recent work on MaskedMimic, but some additional pieces" — [Chen Tessler on X](https://x.com/ChenTessler/status/1838857687892713945)
- **PHC** = "Perpetual Humanoid Control for Real-time Simulated Avatars", ICCV 2023, official implementation at [GitHub ZhengyiLuo/PHC](https://github.com/ZhengyiLuo/PHC)
- **PULSE** = "Universal Humanoid Motion Representations for Physics-Based Control", **ICLR 2024 spotlight**, official implementation at [GitHub ZhengyiLuo/PULSE](https://github.com/ZhengyiLuo/PULSE). The PULSE repo itself states it "has a large amount of code overlap with PHC; PULSE is more focused on the generative tasks" — [GitHub ZhengyiLuo/PULSE](https://github.com/ZhengyiLuo/PULSE)
- PULSE positions itself as "a foundation model for control where downstream tasks ranging from simple locomotion, complex terrain traversal, to free-form motion tracking can all reuse this representation" — [PULSE ICLR 2024 paper](https://proceedings.iclr.cc/paper_files/paper/2024/file/fb19caf91f4966e426026685028cb73a-Paper-Conference.pdf)
- **FARM** = "Frame-Accelerated Augmentation and Residual Mixture-of-Experts for Physics-Based High-Dynamic Humanoid Control", arXiv **2508.19926** — it is a **2025 paper, not 2026**. It reports **42.8% reduction in tracking failure rate** and **14.6% lower global MPJPE** vs baseline on its own introduced HDHM dataset — [arXiv 2508.19926](https://arxiv.org/html/2508.19926)
- FARM's lineage statement places it downstream of PHC: "PHC advanced this by removing the RFC and introducing a progressive multiplicative control architecture" — [arXiv 2508.19926](https://arxiv.org/html/2508.19926)
- **2026 successor to PULSE — verified: Spherical Latent Motion Prior (SLMP)**, arXiv **2603.01294**, "Spherical Latent Motion Prior for Physics-Based Simulated Humanoid Control" (March 2026). It reports **higher success rate and lower MPJPE than PULSE**, approaching the expert controller — [arXiv 2603.01294](https://arxiv.org/pdf/2603.01294)
- Other 2026 names in this lineage surfaced in reference lists but **not individually verified**: **PhyGile** ("Physics-Prefix Guided Motion Generation for Agile General Humanoid Motion Tracking", [arXiv 2603.19305](https://arxiv.org/pdf/2603.19305)), **CLOT** ("Closed-Loop Global Motion Tracking for Whole-Body Humanoid Teleoperation", [arXiv 2602.15060](https://arxiv.org/pdf/2602.15060)), **OmniXtreme**, **OmniTrack**
- **PHUMA**, "Physically Reliable Humanoid Locomotion Dataset", [arXiv 2510.26236](https://arxiv.org/pdf/2510.26236) — listed as a supported ProtoMotions input dataset
- A ProtoMotions-adjacent **open-weights** motion model exists on Hugging Face: **`nvidia/Kimodo-SOMA-RP-v1.1`** (Kimodo outputs are a listed ProtoMotions input). Flagged as **unverified** in the source that surfaced it — [aiweekly summary](https://aiweekly.co/alerts/nvidia-jca-co-generates-3d-motion-and-video-for-any-rig)
- A **third-party MaskedMimic fork** exists ([GitHub DanielTruong99/MaskedMimic](https://github.com/DanielTruong99/MaskedMimic)) but it is a fork of the NVlabs repo and the searches showed no additional cleanup or export code in it

### Inferences
- The practical hobbyist entry point is **ProtoMotions with the MuJoCo (CPU) backend for inspection and IsaacGym/IsaacLab for training**, running **pretrained NVIDIA checkpoints** rather than training from scratch. Training MaskedMimic end-to-end on a single consumer GPU is effectively out of reach given the documented 4×A100/12 h figure is for the *expert* stage only and MaskedMimic needs that expert first.
- The Docker files are the lowest-friction install; IsaacGym in particular historically pins old Python/CUDA, which is why the repo ships images rather than a plain `pip install`.
- **ASE, CALM, AMP, DeepMimic and PHC+ are not part of the maintained ProtoMotions surface** — the repo page does not mention PHC, AMP or ASE at all. Treat them as reference implementations in their own (older, less maintained) repos.

### Gaps
- **Exact single-consumer-GPU training times (e.g. RTX 4090/5090) for any of these** — not published by any source found. The only numbers anywhere are A100 counts.
- **PHC+ as a distinct named release** — could not find a separate repo or paper under "PHC+"; it appears only as a variant name. Likely refers to the improved PHC released inside PULSE's codebase rather than a standalone artifact.
- **Minimum VRAM** for ProtoMotions inference/rollout — not stated on the repo page.
- Whether SLMP / PhyGile / CLOT have released code — not checked.
- CALM and ASE 2026 successors — not found.

---

## Q2. MaskedMimic / ProtoMotions as a motion *cleaner* (noisy AI mocap in → physically valid motion with correct foot contact out)

### Takeaway
**No one has published a documented MaskedMimic-as-cleaner recipe**, and ProtoMotions ships no cleanup mode. But the exact task **has been solved and published in 2026 by EA's SEED team** under the name *"The Last Mile to Production Readiness"* — per-sequence deep-RL refinement of commercial video-mocap output, explicitly targeting foot sliding, floating, penetration and phantom contact, with multi-character support. **Code release is not stated**, which is the catch.

### Cited Findings
- **ProtoMotions has no cleanup step**: searches of the docs and README surfaced motion *retargeting* (PyRoki-based) but **"nothing ... describes a dedicated motion cleanup or physics-based cleanup step"** — [ProtoMotions docs](https://nvlabs.github.io/ProtoMotions/user_guide/experiments.html)
- **The nearest-exact match, verified**: *"The Last Mile to Production Readiness: Physics-Based Motion Refinement for Video-Based Capture"* — **Tianxin Tao, Han Liu, Hung Yu Ling (Electronic Arts; SEED and EA Vancouver)**, **SIGGRAPH Asia 2025 Technical Communications**, Dec 2025, Hong Kong — [arXiv 2601.19036](https://arxiv.org/html/2601.19036); [ACM DOI 10.1145/3757376.3771379](https://dl.acm.org/doi/10.1145/3757376.3771379)
- Its stated input is **"imperfect vision-based capture data"** from **commercial vision-based mocap tools** — i.e. exactly "noisy AI mocap" — and output is refined physically plausible motion — [arXiv 2601.19036](https://arxiv.org/html/2601.19036)
- Method: **per-sequence deep RL motion imitation, "similar to DeepMimic"**; **a new policy is trained per motion clip**. Multi-character uses **MAPPO with individual policies and a shared critic** — [arXiv 2601.19036](https://arxiv.org/html/2601.19036)
- It contributes **artifact-aware evaluation, adaptive termination, and flexible reward shaping**, and targets **foot sliding, floating, penetration, and phantom contact** — [arXiv 2601.19036](https://arxiv.org/html/2601.19036)
- Foot-contact mechanism: **"physics-based simulators mitigate foot sliding by modeling ground friction to keep feet anchored"**; collision/penetration handled with **GJK and EPA** penetration-depth computation — [arXiv 2601.19036](https://arxiv.org/html/2601.19036)
- Multi-character: a **single shared policy worked in some cases but failed in scenarios with significant penetrations**, hence per-character policies — [arXiv 2601.19036](https://arxiv.org/html/2601.19036)
- **CVPR 2026 companion, RePHO** ("Recovering Physically Plausible Human-Object Interactions from Monocular Videos"): takes **"visually plausible but physically flawed"** monocular-video sequences and **reenacts them in a physics simulator using RL policies**, arguing that soft "physics-aware" losses "only enforce contact constraints softly and do not achieve true physical feasibility" — [papernotes CVPR2026](https://en.papernotes.org/CVPR2026/3d_vision/recovering_physically_plausible_human-object_interactions_from_monocular_videos/)
- **Plug-and-play alternative with a 2024 preprint**: *"A Plug-and-Play Physical Motion Restoration Approach for In-the-Wild High-Difficulty Motions"*, two-stage pipeline reported to keep ground penetration low and **heavily reduce foot sliding and floating while preserving the original movement pattern** — [arXiv 2412.17377 via Pith](https://pith.science/paper/2412.17377)
- The classic failure mode to expect is documented by Chentanez et al.: for **fast-moving or noisy mocap clips, "the agent can lose balance and fall down easily"** — [Chentanez et al., Physics-based Motion Capture Imitation with Deep RL](https://matthias-research.github.io/pages/publications/a1-chentanez.pdf)

### Inferences
- **A MaskedMimic-as-cleaner pipeline is architecturally straightforward but undocumented.** The construction is: (1) retarget your noisy clip to the ProtoMotions SMPL/SOMA humanoid using its **SEED BVH Data Preparation** path; (2) run it as a **Mimic / full-body tracking target** rather than a masked/partial one — MaskedMimic's "motion inpainting from partial observations" formulation means that if you feed *all* joints as constraints you are running pure tracking; (3) record the **simulated** joint states (not the reference) as the output. Because the simulated body obeys ground contact and friction, the recorded result has valid foot contact by construction. This is inference from the documented architecture, **not a procedure anyone has published.**
- The EA paper's **"a new policy is trained per motion clip"** is the honest cost statement for this whole approach: it is an optimization per clip, not a feed-forward filter. For a hobbyist this means minutes-to-hours of GPU per clip, not seconds.
- RePHO and the EA paper together establish that in 2026 the field's consensus answer to "can physics clean AI mocap?" is **yes, via per-sequence RL imitation in a simulator** — so the user's instinct is correct and current.

### Gaps
- **Concrete steps published by anyone for MaskedMimic-as-cleaner** — none found. No blog post, issue thread, or repo implementing this.
- **Code release for "The Last Mile to Production Readiness"** — the paper does not state it; no repo found.
- **GPU requirements and per-sequence refinement wall-clock time for the EA method** — the paper defers to "Supplementary Material D", which was not retrievable.
- **File formats for the EA method's input/output** — the paper does not specify them.

---

## Q3. Combat-specific physics work: MAAIP, two-character fighting, hit reactions, ragdoll blending

### Takeaway
**MAAIP is verified and published but no code repository could be found** — only an ACM supplemental archive of unknown contents. The more promising 2026 item is **"Physics-Based Motion Tracking of Contact-Rich Interacting Characters"** (boxing/pushing/grappling), and **SLMP** is the one paper found that implements actual **striking rewards, hit rewards tied to contact force, and knockdown bonuses/penalties**.

### Cited Findings
- **MAAIP — verified**: *"MAAIP: Multi-Agent Adversarial Interaction Priors for imitation from fighting demonstrations for physics-based characters"*, Younes, Kijak, Kulpa, Malinowski, Multon; submitted **4 Nov 2023**; published in **Proceedings of the ACM on Computer Graphics and Interactive Techniques, Vol 6, Issue 3, Article 32** — [arXiv 2311.02502](https://arxiv.org/abs/2311.02502); [ACM DOI 10.1145/3606926](https://dl.acm.org/doi/10.1145/3606926); author preprint at [HAL hal-04136868](https://hal.science/hal-04136868v1/file/SCA_2023_HAL.pdf)
- MAAIP was **tested on two fighting styles, boxing and a full-body martial art** (the QwanKiDo reference in the user's brief is consistent with "full-body martial art"), and trains using **MAGAIL with multi-agent PPO** — [arXiv 2311.02502](https://arxiv.org/pdf/2311.02502)
- **MAAIP code: could not find a GitHub repository or project page.** The ACM page offers a **supplemental files download of 64.22 MB**; whether it contains code is unknown — [ACM DOI 10.1145/3606926](https://dl.acm.org/doi/10.1145/3606926)
- **Two-character physics fighting, 2026 — verified**: *"Physics-Based Motion Tracking of Contact-Rich Interacting Characters"*, [arXiv 2604.07984](https://arxiv.org/html/2604.07984). It tracks **two humanoids through contact-heavy interactions including boxing, pushing, and grappling**, and argues single-character tracking methods do not transfer to two-character settings **because of force transfer and added complexity**
- **Striking/hit-reaction rewards — verified in SLMP**: the Spherical Latent Motion Prior paper uses **sparse rule-based rewards for striking, with a hit reward tied to contact force and a penalty when the agent's own head or torso is struck**, plus a **knockdown bonus and penalty, and episode termination on knockdown** — [arXiv 2603.01294](https://arxiv.org/pdf/2603.01294)
- **Non-RL hit reactions — verified**: Liu, *"Character hit reaction animations using physics and inverse kinematics"*, **Computer Animation and Virtual Worlds, 2023**. Combines physics simulation with IK for **real-time** hit reactions using an **impulse spread algorithm to distribute assault impulses across body parts** — [Wiley DOI 10.1002/cav.2170](https://onlinelibrary.wiley.com/doi/abs/10.1002/cav.2170)
- **Reaction synthesis**: *"PhysReaction: Physically Plausible Real-Time Humanoid Reaction Synthesis via Forward Dynamics Guided 4D Imitation"* — [arXiv 2404.01081](https://arxiv.org/pdf/2404.01081)
- **Earlier two-player competitive sports baseline**: Won, Gopinath, Hodgins-lineage work "Control strategies for physically simulated characters performing two-player competitive sports" — [ACM DOI 10.1145/3450626.3459761](https://dl.acm.org/doi/10.1145/3450626.3459761)
- **Cooperative two-human multi-agent RL, 2026**: *"Learning to Assist: Physics-Grounded Human-Human Control via Multi-Agent Reinforcement Learning"* — [arXiv 2603.11346](https://arxiv.org/pdf/2603.11346)
- **Ragdoll→animation blending, practical recipe (UE5)**: the simplest route, **`Set All Bodies Simulate Physics`, "produces an instant snap"**; the recommended approach is a **blend weight incremented from 0 to 1 over 0.2 seconds** in a timeline or tick function, with the hit impulse applied via **`Add Impulse` or `Add Force` on the relevant bone** — [MocapOnline ragdoll blending guide](https://mocaponline.com/blogs/mocap-news/ragdoll-physics-animation-guide)

### Inferences
- For a hobbyist fighting project, **the released-code path for combat physics is empty**. MAAIP has no findable repo; the 2026 two-character tracking paper's code status is unknown. The realistic route is to reproduce *reward design* ideas (SLMP's contact-force hit reward, knockdown termination) on top of ProtoMotions, which is a research project, not a weekend.
- The 0.2 s blend-weight ragdoll recipe in UE5 is the only item in this whole section that a hobbyist can implement the same day.

### Gaps
- **Whether the MAAIP ACM supplemental archive contains source code** — not determined; the 64 MB size is consistent with videos only.
- **Code/data release for arXiv 2604.07984** (contact-rich two-character tracking) — not checked.
- **Any released dataset of two-character fighting mocap** — none found. MAAIP's demonstration data availability is unknown.
- **UE5 Physics Control plugin / Chaos specifics** — no authoritative source retrieved (see Q5 gaps).

---

## Q4. The export problem: getting motion out of a physics simulator into a game-engine animation file

### Takeaway
**The user's premise is confirmed: there is no official NVIDIA exporter.** A GitHub issue on the Isaac Lab repo states NVIDIA does not provide one, and ProtoMotions' only export target is **ONNX (the policy, not the motion)**. The de facto route is scripted extraction → **BVH as intermediate** → Blender → FBX/GLB. The well-trodden direction is the *inbound* one (FBX/Mixamo → `.npz`), and there is a reusable repo for it.

### Cited Findings
- **No official exporter — verified**: an Isaac Lab GitHub issue states **"NVIDIA currently does not provide an official direct exporter from Isaac Sim that outputs AMP-ready .npz files for custom humanoid models"**, and that the usual approach is **"extracting motion from USD or other animation sources and converting it, which generally involves scripting and manual data handling"** — [IsaacLab issue #3870](https://github.com/isaac-sim/IsaacLab/issues/3870)
- **ProtoMotions' only documented export is ONNX** (the policy with observation computation included); **BVH appears only as an input format** — [GitHub NVlabs/ProtoMotions](https://github.com/NVlabs/ProtoMotions)
- The **Isaac Lab asset docs list FBX as a mesh *import* format** — this covers importing assets, not exporting motion — [Isaac Lab: Importing a New Asset](https://isaac-sim.github.io/IsaacLab/main/source/how-to/import_new_asset.html)
- **Inbound converter, released**: **`AsterisCrack/robot-motion-reference`** — "Extract and retarget FBX/Mixamo animations to robot joint trajectories for use as motion references in reinforcement learning." It extracts joint trajectories from FBX into **`.npz`** and includes a **preview script for Isaac Lab**; built around the **Mixamo → Isaac Lab** pipeline — [GitHub AsterisCrack/robot-motion-reference](https://github.com/AsterisCrack/robot-motion-reference)
- **GMR** is named in the same Isaac Lab issue thread as "a retargeting tool that can output npz files compatible with this algorithm" and that "can handle any SMPL or BVH motions for a custom robot" — [IsaacLab issue #3870](https://github.com/isaac-sim/IsaacLab/issues/3870)
- **BVH → FBX batch conversion via Blender headless** is a documented, long-standing script (import BVH, export FBX from the command line). Caveat from its own author's readers: **"I had to make a couple of minor changes due to Blender version changes"** — the script is from **2014** — [Batch Conversion of BVH to FBX](https://alastaira.wordpress.com/2014/04/25/batch-conversion-of-bvh-to-fbx-motion-capture-files/)
- **GUI route**: import the BVH, select the armature, export FBX — [MocapOnline BVH files guide](https://mocaponline.com/blogs/mocap-news/bvh-files-guide-convert-import)
- **BVH → Unity humanoid clips** has a documented community workflow — [extra-ordinary.tv, BVH to Humanoid Animation Clips for Unity](https://extra-ordinary.tv/2021/12/05/bvh-to-humanoid-animation-clips-for-unity/)
- Format landscape overview (FBX, BVH, VMD and others) — [QuickMagic export formats](https://www.quickmagic.ai/tools/3d-animation-export-formats)
- **Counter-example worth noting: ActionMesh exports straight to glTF.** It writes **per-frame `.glb` meshes**, a **single animated `.glb` (`animated_mesh.glb`, which requires Blender 3.5.1)**, and a rendered `.mp4` — no FBX/OBJ-sequence export — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh)

### Inferences
- The export pipeline a hobbyist must actually build is: **rollout the policy → dump per-frame root transform + joint rotations to a NumPy array → write BVH yourself (BVH is a plain-text hierarchy + channel block, trivial to emit) → `blender --background --python` to convert BVH→FBX/GLB → import to UE5/Unity.** Every published artifact covers the *reverse* direction, so the BVH writer is the piece you write.
- Because ProtoMotions' humanoid is SMPL/SOMA-skeleton-based, the BVH you emit will be on *that* skeleton, so a second retarget onto the user's own rig (Blender Rokoko/Auto-Rig Pro, or Mixamo) is unavoidable. **This is the structural reason the physics route is expensive for a custom-rig project** — it forces SMPL into the middle of the pipeline, which is exactly what the Part-2 "holy grail" question is trying to avoid.

### Gaps
- **A maintained, named "Isaac Gym/MuJoCo → BVH" script** — none found. Searches surfaced only the inbound direction. If one exists it is not discoverable by these queries.
- **Whether Isaac Sim's USD animation output can be round-tripped to FBX via USD tooling** — not investigated.
- **GMR's repository URL and current status** — named in a thread but not located.

---

## Q5. Simpler physics tools a hobbyist would actually use instead

### Takeaway
**Cascadeur is the clear effort/payoff winner**: it has shipped ragdoll since version **2024.3**, it runs on the *finished* animation rather than replacing authoring, and it is a desktop app with no training step. UE5's animation→ragdoll blend is a same-day implementation. Sources on Blender ragdoll addons and UE5's Physics Control plugin specifically were thin and I could not verify them.

### Cited Findings
- **Cascadeur has a documented Physics Tools panel**; its **Ragdoll** tool **"calculates motions like falling, getting hit etc."** — [Cascadeur help: Physics Tools](https://cascadeur.com/help/tools/physics_tools)
- Cascadeur's **Ragdoll arrived in release 2024.3** and is enabled from the **Physics Settings** panel — [Cascadeur help 2024.3](https://cascadeur.com/help/category/266)
- Critically for a cleanup workflow: Cascadeur's physics **"uses the final animation frames as input and simulates from that point"** — i.e. it is a post-process on existing animation, not a replacement authoring paradigm — [Cascadeur help: Physics Tools](https://cascadeur.com/help/tools/physics_tools)
- **Cascadeur's "Interaction with Environment" is Pro-tier and above only** — a real functional gate for a hobbyist on the free/indie tier — [Cascadeur help: Interaction with Environment](https://cascadeur.com/help/category/220)
- Cascadeur's team discussed its physics/AI/control design philosophy in a **February 2026** interview — [digitalproduction.com, Cascadeur on physics, AI and control](https://digitalproduction.com/2026/02/18/cascadeur-on-physics-ai-and-control/)
- Community discussion of Cascadeur ragdoll relative to a Blender workflow exists but is **anecdotal with no addon details** — [Blender Artists: Cascadeur adds Rag doll physics](https://blenderartists.org/t/cascadeur-adds-rag-doll-physics/1560268)
- **UE5 animation↔ragdoll blending, concrete**: `Set All Bodies Simulate Physics` **"produces an instant snap"**; the recommended approach is a **blend weight 0→1 over 0.2 s** driven by a timeline or tick, with impulse applied via **`Add Impulse`/`Add Force` on the hit bone** — [MocapOnline ragdoll blending guide](https://mocaponline.com/blogs/mocap-news/ragdoll-physics-animation-guide)
- One low-authority aggregator notes Cascadeur **"complements Blender/Maya but not replacing them, as it lacks modeling/rigging"** — flagged as low-authority — [yelzkizi.org](https://yelzkizi.org/cascadeur/)

### Inferences
- **Honest effort/payoff ranking for a hobbyist non-commercial project**, from the evidence gathered: (1) **UE5 blend-weight ragdoll** — hours, works, limited to hit reactions and deaths; (2) **Cascadeur ragdoll + physics tools** — days to learn, directly addresses "make this AI mocap look physically right", and the free tier covers everything except environment interaction; (3) **Blender rigid-body/ragdoll addons** — unverified, likely viable but no authoritative source found; (4) **ProtoMotions/MaskedMimic RL** — weeks-to-months plus A100-class compute plus a BVH exporter you must write yourself, for the payoff of a genuinely physically-valid full-body result.
- Cascadeur's "simulate from the final animation frames" design is the single most hobbyist-relevant fact in this whole document: it is the *same conceptual operation* as the EA SEED physics-refinement paper, available today as a GUI with no training.

### Gaps
- **Blender ragdoll addons by name** (e.g. Ragdoll Dynamics / Rigid Body Bones) — **no sources retrieved.** I could not verify any specific Blender ragdoll addon, its price, or its maintenance status.
- **UE5 Physics Control plugin and Chaos specifics** — **no sources retrieved.** The UE5 material found covers the classic `SimulatePhysics`/blend-weight approach, not the newer Physics Control component.
- **Unity ragdoll blending** beyond the general guide — not separately verified.
- Cascadeur free-tier limits (frame counts, export formats) — not retrieved.

---

## Q6. VERIFY "NVIDIA JCA" / "Joints, Camera, Action!"

### Takeaway
**VERIFIED — exists exactly as reported.** NVIDIA Research, arXiv **2610.28632**, submitted **20 October 2026**, eight NVIDIA authors. It co-generates video and 3D motion for arbitrary rigs and does video→rig-motion. **No code or weights found, and the project page does not state any release plan.**

### Cited Findings
- **Title/ID**: *"Joints, Camera, Action! Co-Generating 3D Motion and Video for Arbitrary Rigs"*, **arXiv:2610.28632**, cs.CV, submitted **20 Oct 2026** — [arXiv 2610.28632](https://arxiv.org/abs/2610.28632); [PDF](https://arxiv.org/pdf/2610.28632)
- **Authors (8, all NVIDIA Research)**: **Noam Rotstein, Yoad Tewel, Chen Tessler, Haggai Maron, Gal Chechik, Or Litany, Yoni Kasten, Yuval Atzmon** — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- Note the overlap with Q1: **Chen Tessler is the MaskedMimic/ProtoMotions author**, so JCA comes out of the same NVIDIA group as the physics work.
- **Architecture**: 3D motion lives in a **separate model supporting arbitrary skeletons**, linked to a **pretrained video model through cross-attention layers**; a **joint-localization head predicts where joints appear in video frames**, which is what lets the system **recover a camera and place the motion in the scene** — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- **Training data**: **~200K video+motion pairs**, plus motion-only data (which the authors note is easier to obtain than paired video); the pairs combine **human and robot video with synthetic edits made by a generative video editor that places animated 3D assets into scenes** — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/); [aiweekly summary](https://aiweekly.co/alerts/nvidia-jca-co-generates-3d-motion-and-video-for-any-rig)
- **Claimed capabilities, from the project page**: text-driven animation of diverse rigs (claimed **more faithful than video-first pipelines**); **scene-aware motion** responding to an input image and registerable in the 3D scene; **keyframe-based control of the motion trajectory**; **video→motion** (recovering a subject's 3D motion from video **plus the rig's rest pose**); **motion→video**, with or without a reference first frame — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- **Rig formats**: the page says **arbitrary skeletons / rigs of any topology** but **names no file formats (no FBX, no BVH)** — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- **Code/weights: not released as far as can be determined.** The only GitHub hit is **`jca-paper/jca-paper.github.io`**, which is a **supplementary-results website containing videos and web assets, not model code or weights** — [GitHub jca-paper/jca-paper.github.io](https://github.com/jca-paper/jca-paper.github.io)
- **Venue: none.** The BibTeX on the project page is a **`misc` entry, year 2026, primary category cs.CV**, with no conference or journal — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- **Caveat flagged by a secondary source**: the paper **"lists capabilities without quantitative comparisons, so the evidence for the claim is mainly qualitative."** Treat the "more faithful than video-first pipelines" claim as author-asserted — [secondary summary](https://aiweekly.co/alerts/nvidia-jca-co-generates-3d-motion-and-video-for-any-rig); see also [Ken Ashe writeup](https://kenashe.ai/blog/2026-10-21-nvidias-jca-turns-generated-video-into-rig-motion) and [GameDev News](https://gamedev.net/news/6594-joints-camera-action-co-generating-3d-motion-and-video-for-arbitrary-rigs/)

### Inferences
- JCA is **the single most on-target paper in existence for this user's task** — arbitrary rig, text→motion, video→motion on a supplied rest pose — and it is **one week old with no code**. For a project the user wants to actually build now, it is a signpost, not a tool.
- The requirement of "the rig's rest pose" for video→motion is the key design detail: it means the user's own rig genuinely is the target, not SMPL.

### Gaps
- **Whether code/weights are planned** — the project page is silent.
- **Quantitative benchmark numbers** — the paper reportedly does not provide comparisons; I did not read the PDF body to confirm this secondary claim.
- **Whether `nvidia/Kimodo-SOMA-RP-v1.1` on Hugging Face is related to JCA** — the one source linking them explicitly flagged it as unconfirmed.

---

## Q7. VERIFY "ActionMesh" (Meta)

### Takeaway
**VERIFIED, and it is the most downloadable item in this entire survey.** arXiv **2601.16148**, **CVPR 2026**, Meta Reality Labs + UCL. Code at **github.com/facebookresearch/actionmesh (447 stars)**, weights auto-download from **huggingface.co/facebook/ActionMesh**, runs on **12 GB VRAM in `--low_ram` mode**. But it outputs a **rig-free, fixed-topology deforming mesh — it cannot drive a game rig.**

### Cited Findings
- **Title/ID**: *"ActionMesh: Animated 3D Mesh Generation with Temporal 3D Diffusion"*, **arXiv:2601.16148**, cs.CV, first submitted **22 January 2026**, v2 dated **1 April 2026**, **CC BY 4.0** — [arXiv 2601.16148](https://arxiv.org/abs/2601.16148)
- **Venue: CVPR 2026** — confirmed by the CVF open-access proceedings PDF — [CVF CVPR 2026 proceedings](https://openaccess.thecvf.com/content/CVPR2026/papers/Sabathier_ActionMesh_Animated_3D_Mesh_Generation_with_Temporal_3D_Diffusion_CVPR_2026_paper.pdf)
- **Authors**: **Remy Sabathier, David Novotny, Niloy J. Mitra, Tom Monnier**; **Sabathier and Monnier at Meta Reality Labs** (Mitra is UCL; Novotny is Meta/FAIR-lineage) — [search result summary of the paper](https://huggingface.co/papers/2601.16148)
- **Inputs**: **monocular video, OR text, OR a 3D mesh plus a text prompt** — [arXiv 2601.16148](https://arxiv.org/abs/2601.16148)
- **Method**: extends 3D diffusion with a temporal axis ("**temporal 3D diffusion**"); a **temporal 3D autoencoder** maps a sequence of independent shapes onto **deformations of a pre-defined reference shape**, separating 3D generation from animation — [arXiv 2601.16148](https://arxiv.org/abs/2601.16148)
- **What it actually produces — the decisive answer**: **fixed-topology animated mesh, explicitly "rig-free" and "topology consistent"**. It is **not** a rigged skeleton and **not** a 4D volume — "production-ready 3D meshes" means deforming meshes — [arXiv 2601.16148](https://arxiv.org/abs/2601.16148)
- **Code — verified live**: **github.com/facebookresearch/actionmesh, public, 447 stars, 34 forks** — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh)
- **Install**: clone → `git submodule update --init --recursive` → `pip install -r requirements.txt` → `pip install -e .`; developed with **torch 2.4.0, CUDA 12.1, torchvision 0.19.0** — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh)
- **Weights — verified**: auto-download on first launch from Hugging Face: **`facebook/ActionMesh`**, plus **TripoSG, DINOv2-large, RMBG-1.4** — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh); [HF facebook/ActionMesh](https://huggingface.co/facebook/ActionMesh)
- **GPU**: **32 GB VRAM**, or **12 GB using `--low_ram` (e.g. a Colab T4)**. Optional **PyTorch3D** needed for video rendering and {video+3D}→4D inference — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh)
- **Speed**: **~75 s default, ~45 s with `--fast`, on an H100** — note this **contradicts** the "importable into standard 3D software in under a minute" marketing line, which only holds in fast mode on H100-class hardware — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh); contrasted with [HF paper page](https://huggingface.co/papers/2601.16148)
- **Outputs**: per-frame **`.glb`** (`mesh_000.glb`, …), a single animated **`.glb`** (`animated_mesh.glb`, requires **Blender 3.5.1**), and a rendered **`.mp4`**. **No FBX or OBJ-sequence export mentioned.** — [GitHub facebookresearch/actionmesh](https://github.com/facebookresearch/actionmesh)
- Also released: a benchmark called **ActionBench**; SOTA claimed on **video-to-4D benchmarks Consistent4D and Objaverse** — [HF paper page](https://huggingface.co/papers/2601.16148)
- **Project page** with code and videos: [remysabathier.github.io/actionmesh](https://remysabathier.github.io/actionmesh/); demos listed on **Replicate** and **Hugging Face Spaces**

### Inferences
- **Direct answer to "can its output drive a game rig?" — No, not as such.** The output is vertex-animated geometry with no skeleton and no skinning weights. To drive a game rig you would need a second stage that fits a skeleton and skinning weights to the mesh sequence — which is precisely what **GaussiAnimate/Skelebones (Q8)** does. **ActionMesh → GaussiAnimate is a plausible two-stage pipeline no one appears to have published.**
- Fixed topology across frames is nonetheless valuable: a topology-consistent GLB sequence can be imported as shape keys / vertex animation in Blender and baked, which is usable for props and creatures even without a rig.
- **This is the one item a hobbyist can install and run today on a 12 GB consumer GPU.**

### Gaps
- **License type** — the repo links LICENSE.txt but the content fetched did not name it. Given Meta's pattern, assume a research/non-commercial license; must be checked directly.
- Quality on human characters specifically (benchmarks cited are object-centric: Consistent4D, Objaverse).

---

## Q8. VERIFY "Motion2MixamoRig" and `squall01337/mixamo-llm-mocap`

### Takeaway
**Both are real and they are two separate projects, not a garbled reference to one.** `MaxLiu22/Motion2MixamoRig` is a real repo at **v0.1.7** with a **Blender 4.2+ extension**, and `squall01337/mixamo-llm-mocap` is a real, separate agent-driven skill. **Important licensing correction: despite Apache-2.0 being cited socially, the v0.1.7 release notes say non-commercial only.**

### Cited Findings
- **`MaxLiu22/Motion2MixamoRig` — verified**, with release **v0.1.7** — [GitHub release v0.1.7](https://github.com/MaxLiu22/Motion2MixamoRig/releases/tag/v0.1.7)
- **A fork/mirror exists**: **`AIPoses/Mocap_Motion2MixamoRig`** — "Retarget human motion from video to an Adobe Mixamo-rigged 3D character" — [GitHub AIPoses/Mocap_Motion2MixamoRig](https://github.com/AIPoses/Mocap_Motion2MixamoRig)
- **Pipeline**: mocap extraction via **SMPL-X / GVHMR** from a single-person full-body **video or a single photo**; **automatic retargeting onto the Adobe Mixamo standard skeleton** (e.g. Y_Bot); outputs **`run.json`, a 2×2 comparison video, and a `mixamo_character.glb` importable directly into Blender or Unity** — [AYi on X](https://x.com/AYi_AInotes/status/2109941018476892394)
- **Delivery form**: both a **Blender 4.2+ Extension** and a **command-line pipeline**; the project is described as **experimental**; it "copies the motion of a person in a video onto a Mixamo-rigged character and hands you a skinned GLB for Blender or Unity" — [Stefan 3D AI on X](https://x.com/Stefan_3D_AI/status/2109989762899570828)
- **License conflict — flag this**: Apache-2.0 is cited in social coverage, **but the v0.1.7 release notes say non-commercial use only, because of upstream model licenses** — [GitHub release v0.1.7](https://github.com/MaxLiu22/Motion2MixamoRig/releases/tag/v0.1.7); vs [AYi on X](https://x.com/AYi_AInotes/status/2109941018476892394). For this user's non-commercial project this is moot, but the discrepancy is real.
- **`squall01337/mixamo-llm-mocap` — verified, separate project**: turns any video into a Mixamo-rig animation using a **GVHMR estimator, spec-driven retargeting, and FK application in Blender via MCP**. It is an **open-source AI-agent skill for Claude Code, Codex CLI and ChatGPT**, with a **10-stage pipeline**, built to be driven end-to-end by an agent — [GitHub Signals: squall01337/mixamo-llm-mocap](https://githubsignals.io/repo/squall01337/mixamo-llm-mocap/); [skillsllm listing](https://skillsllm.com/skill/mixamo-llm-mocap); [themenonlab writeup](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation); [baeseokjae writeup](https://baeseokjae.github.io/posts/mixamo-llm-mocap-ai-agent-2026/)
- Additional third-party overview of Motion2MixamoRig — [aipings.io](https://aipings.io/content/motion2mixamorig-open-source-video-motion-capture)
- **The two projects share an estimator (GVHMR) and a target (Mixamo rig) but the searches showed no direct link between them** — they are convergent, not the same thing — [search result summary](https://githubsignals.io/repo/squall01337/mixamo-llm-mocap/)

### Inferences
- Both go **video → SMPL-X/GVHMR → Mixamo skeleton**, i.e. they do **not** skip SMPL; they just hide it and hard-code the target to the Mixamo bone naming convention. That makes them immediately useful if and only if the user's rig is Mixamo-compatible (or can be Mixamo-auto-rigged).
- `mixamo-llm-mocap` being an *agent skill* is notable for this user's context: it is designed to be invoked from Claude Code, so it is the lowest-friction practical pipeline found anywhere in this survey.

### Gaps
- **Star counts, last-commit dates and maintenance status for both repos** — not retrieved (I did not fetch the repo pages directly; the v0.1.7 release page and aggregators were the sources).
- Whether Motion2MixamoRig handles non-Mixamo rigs at all.

---

## Q9. VERIFY "GaussiAnimate" / "Skelebones"

### Takeaway
**VERIFIED, with code.** arXiv **2604.08547**, cited as a **SIGGRAPH Asia 2026 conference paper**, repo at **github.com/Net-Maker/GaussiAnimate**. The **Skelebones** mechanism is real and is exactly as described: it extracts free-form bones + smooth skinning weights from Gaussian **or mesh** sequences, then derives an inner kinematic skeleton. **Note the title discrepancy across sources.**

### Cited Findings
- **Repo — verified**: **`Net-Maker/GaussiAnimate`**, "official repo of the paper *GaussiAnimate: Reconstruct and Rig Animatable Categories with Level of Dynamics*" — [GitHub Net-Maker/GaussiAnimate](https://github.com/Net-Maker/GaussiAnimate)
- **Paper**: **arXiv 2604.08547** — [HTML v2](https://arxiv.org/html/2604.08547v2); [PDF](https://arxiv.org/pdf/2604.08547)
- **Title discrepancy — flag this**: the repo and some citations give *"**Reconstruct and** Rig Animatable Categories with Level of Dynamics"*; the arXiv listing renders as *"Rig Animatable Categories with Level of Dynamics"*. Same paper — [GitHub](https://github.com/Net-Maker/GaussiAnimate) vs [arXiv](https://arxiv.org/html/2604.08547v2)
- **Venue**: cited in another arXiv paper's reference list as a **SIGGRAPH Asia 2026 conference paper (J. Wang et al., 2026)**; also promoted with the **#SIGGRAPHAsia2026** hashtag by **Yuliang Xiu** — [Yuliang Xiu on X](https://x.com/yuliangxiu/status/2109278805903003749). Project page by author Jiaxin Wang: [cookmaker.cn/gaussianimate](https://cookmaker.cn/gaussianimate/)
- **Skelebones, stage 1 (outer bones)**: "**compress temporally-consistent Gaussian/Mesh sequences into free-form bones with smooth skinning weights**", using **linear blend skinning to approximate non-rigid deformation**; weights come from **SSDR**, which "computes the optimal skinning weights W∈ℝ^(N×B) and refines the bone transformations" — [arXiv 2604.08547](https://arxiv.org/html/2604.08547v2)
- **Skelebones, stage 2 (inner skeleton)**: extracts a **Mean Curvature Skeleton** from the canonical shape, projects skinning weights onto it, and detects **"joint candidates ... by identifying sharp changes in skinning weights along the curve skeleton"**; a **depth-first traversal** then builds the **kinematic tree** — [arXiv 2604.08547](https://arxiv.org/html/2604.08547v2)
- **Skelebones, stage 3 (binding)**: **Partwise Motion Matching** retrieves and blends outer-bone motion to drive a new inner pose — [arXiv 2604.08547](https://arxiv.org/html/2604.08547v2)
- **Output arrays, from the repo**: a **normalized outer-bone skinning weight matrix of shape [N, B]** and a **parent hierarchy for the inner skeleton** — [GitHub Net-Maker/GaussiAnimate](https://github.com/Net-Maker/GaussiAnimate)
- **Results**: **+17.3% PSNR over Linear Blend Skinning** and **+21.7% over Bag-of-Bones** on reanimation across unseen poses — [arXiv 2604.08547](https://arxiv.org/html/2604.08547v2)
- Author positioning: "**GaussiAnimate compresses a 4D capture into a rigged 3D asset while keeping its secondary dynamics. No physics simulation or learned deformable model.**" — [Yuliang Xiu on X](https://x.com/yuliangxiu/status/2109278805903003749)

### Inferences
- **The "or Mesh" in "Gaussian/Mesh sequences" is the load-bearing detail for this user**: it means GaussiAnimate accepts a topology-consistent *mesh* sequence, which is exactly ActionMesh's output format. The chain **ActionMesh (text/video → topology-consistent GLB sequence) → GaussiAnimate (mesh sequence → skeleton + [N,B] skinning weights + parent hierarchy)** is the only route found in this survey that goes from a prompt to an actual rigged asset using **released code at both stages**. Neither paper proposes this; it is my inference from the two I/O specs and should be presented as an untested idea, not a known-working pipeline.
- Because it emits a weight matrix and a parent hierarchy rather than an FBX, a Blender script would still be needed to assemble a real armature.

### Gaps
- **GPU requirements, runtime and install steps for GaussiAnimate** — not retrieved.
- **Whether weights/checkpoints are released** or only training code.
- **How the skinning weights are regularized beyond the SSDR fit** — the search explicitly noted this needs the full paper.
- Confirmation of SIGGRAPH Asia 2026 acceptance from the **official program** — only secondary citations and a tweet found (see Q10).

---

## Q10. Systematic sweep of the newest 2026 work: mesh-based motion transfer, rig-agnostic generation, skeleton-free retargeting, neural skinning/rigs, 4D→rigs, joint video+motion

### Takeaway
2026's genuinely new theme is **rig-agnostic / arbitrary-topology animation** — a cluster of papers (TopX, UniMate, ACT, AnyTop, SkelMo, JCA) that condition directly on a target skeleton's kinematic tree instead of going through SMPL. **This is a real paradigm shift, but almost none of it has released code**, and the papers with code (ActionMesh, GaussiAnimate, UniRig) each solve only one stage.

### Cited Findings — rig-agnostic / arbitrary-topology motion generation
- **TopX — verified, the single most on-target paper for "user-supplied rig, no SMPL"**: *"TopX: Text-to-Motion for Unseen Skeletons from the Skeleton File Alone"*, **arXiv 2610.34321**, submitted **25 October 2026** (two days before this writing), cs.CV. Authors: **Tengjiao Sun, Pengcheng Fang, Xiaoyu Zhan, Dongjie Fu, Xiaohao Cai, Hansung Kim** — [arXiv 2610.34321](https://arxiv.org/html/2610.34321)
- TopX details: **73M-parameter flow-matching transformer**; needs **"no training clips of the character, no template, no retargeting, and no fine-tuning"**; input is the skeleton's **kinematic tree, rest pose, and joint names**; output is **joint rotations and a root trajectory for any kinematic tree**. Motion is normalized by rest pose and skeleton size; attention is **factorized over joints and frames with a graph-based positional encoding** — [arXiv 2610.34321](https://arxiv.org/html/2610.34321)
- TopX benchmark/results: introduces **SkelShift**, **143,334 captioned clips on skeletons of 4 to 142 joints** with held-out test sets; vs **UniMate** it lowers **descriptor FID 5.73 → 4.58** and **kinetic FID 1.48 → 0.55**, with **fewer frames with feet below the ground** — [arXiv 2610.34321](https://arxiv.org/html/2610.34321)
- **TopX code/weights: not mentioned on the arXiv page** (only PDF, HTML and TeX source links). **Venue: none — arXiv preprint.** — [arXiv 2610.34321](https://arxiv.org/html/2610.34321)
- **UniMate** (TopX's baseline): *"UniMate: One Unified Model to Animate Diverse Skeletons"*, **arXiv 2609.05415** — [arXiv HTML](https://arxiv.org/html/2609.05415); [alphaXiv](https://www.alphaxiv.org/abs/2609.05415)
- **ACT — verified publication**: *"ACT: A Unified Framework for Rigging and Animating Characters with Arbitrary Topologies"*, **ACM TOG Vol 45, Issue 4, 2026** (Long, Wang, Zhao et al.). Combines **rigging, skinning and motion generation in one model**, using a **task-aware masking strategy for zero-shot rigging, text-guided motion generation, and motion completion**; a **VLM extracts semantic topological priors from arbitrary meshes** to condition a **DiT backbone**. **No official code repository found.** — [ACM DOI 10.1145/3811392](https://dl.acm.org/doi/10.1145/3811392)
- **AnyTop — has released PyTorch code**: *"AnyTop: Character Animation Diffusion with Any Topology"* — "generates realistic 3D motion sequences for diverse characters using **only their skeletal structure as input**" — [ACM DOI 10.1145/3721238.3730621](https://dl.acm.org/doi/full/10.1145/3721238.3730621)
- **SkelMo**: *"SkelMo: Universal Skeletal Motion Generation for 3D Rigged Shapes"*, **arXiv 2606.01518** — surfaced by title/reference only, method not verified — [arXiv 2606.01518](https://arxiv.org/pdf/2606.01518)
- **A Unified Conditional Flow for Motion Generation, Editing, and Intra-Structural Retargeting**, **arXiv 2604.13427** — positions itself against systems that "rely on explicit correspondences, IK, or optimization" — [arXiv 2604.13427](https://arxiv.org/pdf/2604.13427)
- **OmniSkel** (ICCV **2025**, earlier): *"Text-to-Any-Skeleton Motion Generation Without Retargeting"* — needs only text and **target bone lengths** to produce motion for a new character, skipping a separate retargeting stage — [CVF ICCV 2025](https://openaccess.thecvf.com/content/ICCV2025/papers/Liu_Text-to-Any-Skeleton_Motion_Generation_Without_Retargeting_ICCV_2025_paper.pdf)

### Cited Findings — skeleton-free / geometry-aware retargeting
- **Skinned Motion Retargeting via Artifact-driven Kinematic Prior Refinement**, **arXiv 2609.06517** (2026): "a learning-based geometry-aware motion retargeting framework that combines a **shared skeleton-agnostic motion prior** with explicit **motion-to-vertex Jacobian-based artifact-driven refinement**" using target-side mesh geometry to correct skinning artifacts — [arXiv 2609.06517](https://arxiv.org/html/2609.06517v1)
- **Semantic-Aware Motion Encoding for Topology-Agnostic Character Animation** (Zhang et al., submitted **May 2026**) — claims **zero-shot cross-species retargeting without paired data** — [GameDev News](https://gamedev.net/news/3570/)
- **HuMoT**: *"Human Motion Representation using Topology-Agnostic Transformers for Character Animation Retargeting"*, arXiv 2305.18897 (background) — [arXiv 2305.18897](https://arxiv.org/pdf/2305.18897)
- **Older skeleton-free baseline**: *"Skeleton-free Pose Transfer for Stylized 3D Characters"*, arXiv 2208.00790 — [arXiv 2208.00790](https://arxiv.org/pdf/2208.00790)
- **Important caveat recorded by the search**: none of the 2026 papers found is **strictly** skeleton-free. "Most of these methods still use a skeleton (a kinematic tree or bone lengths) as input"; truly skeleton-free mesh-deformation work appeared **mainly as older background** — [search synthesis over arXiv results](https://arxiv.org/html/2609.06517v1)

### Cited Findings — neural skinning, neural rigs, auto-rigging
- **A Geodesic Cut-Cell Prior for Neural Skinning**, **arXiv 2608.11272** — [arXiv](https://arxiv.org/pdf/2608.11272)
- **G-Skin: Learning to Bind 3D Gaussians with Generative Visual Priors**, **arXiv 2608.01726** — [arXiv](https://arxiv.org/html/2608.01726)
- **RigAnyFace: Scaling Neural Facial Mesh Auto-Rigging with Unlabeled Data**, **arXiv 2511.18601** — [arXiv](https://arxiv.org/pdf/2511.18601)
- **ViP-Rig: Visual-Prompted Controllable Rigging**, **arXiv 2607.27982** — [arXiv](https://arxiv.org/pdf/2607.27982)
- **Inverse Rig Optimization from Line Drawings**, **arXiv 2609.00732** — [arXiv](https://arxiv.org/pdf/2609.00732)
- **UniRig** (strong baseline, published): *"One Model to Rig Them All: Diverse Skeleton Rigging with UniRig"* — [ACM DOI 10.1145/3730930](https://dl.acm.org/doi/10.1145/3730930); [arXiv 2504.12451](https://arxiv.org/pdf/2504.12451)
- **Puppeteer: Rig and Animate Your 3D Models**, arXiv 2508.10898 — [arXiv](https://arxiv.org/pdf/2508.10898)
- **PhysRig: Differentiable Physics-Based Skinning and Rigging Framework for Realistic Articulated Object Modeling** — bridges Part 1 and Part 2 — [ResearchGate record](https://www.researchgate.net/publication/404941295_PhysRig_Differentiable_Physics-Based_Skinning_and_Rigging_Framework_for_Realistic_Articulated_Object_Modeling)
- **SkinCells: Sparse Skinning using Voronoi Cells**, arXiv 2506.14714 — [arXiv](https://arxiv.org/pdf/2506.14714)
- **RigPAPR: Rig-Based Animation of Static Neural Point Clouds from a Fixed-Viewpoint Video**, **arXiv 2606.06685** — [arXiv](https://arxiv.org/pdf/2606.06685)
- **RigGS: Rigging of 3D Gaussians for Modeling Articulated Objects in Videos**, arXiv 2503.16822 — [arXiv](https://arxiv.org/html/2503.16822v1)

### Cited Findings — 4D generation that yields rigs
- **SkelGen4D — verified**: *"SkelGen4D: Weakly-Supervised Skeleton-Based 4D Generation for Text-Driven Mesh Animation"*, **arXiv 2607.08246**, submitted **9 July 2026**. A **weakly supervised feed-forward framework** for text-driven mesh animation that **generates explicit skeleton motions without requiring per-frame skeleton annotations**, aimed at **standard industrial pipelines**, "which enables explicit control and editing" — [arXiv 2607.08246](https://arxiv.org/abs/2607.08246); [HTML](https://arxiv.org/html/2607.08246)
- **Rigel3D**: *"Rigel3D: Rig-aware Latents for Animation-Ready 3D Asset Generation"*, **arXiv 2605.13129** — generates **rigged meshes controllable through standard skeletal animation pipelines**, jointly modelling "the interdependent shape geometry and rig structure in an end-to-end generative framework" — [arXiv 2605.13129](https://arxiv.org/pdf/2605.13129)
- **RigMo**: *"RigMo: Unifying Rig and Motion Learning for Generative Animation"*, **arXiv 2601.06378** (January 2026) — premised on the fact that "rig and motion, the core structural and dynamic components of animation, are typically modeled as separate problems" — [arXiv 2601.06378](https://arxiv.org/abs/2601.06378)
- **AniGen: Unified S³ Fields for Animatable 3D Asset Generation**, **arXiv 2604.08746** — [arXiv](https://arxiv.org/pdf/2604.08746)
- **ViPS: Video-informed Pose Spaces for Auto-Rigged Meshes**, **arXiv 2604.17623** — [arXiv](https://arxiv.org/pdf/2604.17623)
- **Sketch2Motion: Text-driven 2D Sketch to 3D Animation via Diffusion-guided Skeleton Optimization**, **arXiv 2605.28394** — [arXiv](https://arxiv.org/pdf/2605.28394)
- **4DHumanDiff: Direct Text-to-4DGS Generation for Consistent 360-Degree Dynamic Humans**, **arXiv 2607.27634** — [arXiv](https://arxiv.org/html/2607.27634)
- **AnimateAnyMesh: A Feed-Forward 4D Foundation Model for Text-Driven Universal Mesh Animation**, arXiv 2506.09982 (2025, ActionMesh's closest prior) — [arXiv](https://arxiv.org/pdf/2506.09982)
- **AnimaMimic: Imitating 3D Animation from Video Priors**, arXiv 2512.14133 — [arXiv](https://arxiv.org/pdf/2512.14133)

### Cited Findings — joint video+motion generation
- **JCA (arXiv 2610.28632)** is the only joint video+3D-motion co-generation work found; see Q6 in full.

### Cited Findings — venue/program verification status
- **SIGGRAPH Asia 2026**: **Kuala Lumpur, Malaysia, 1–4 December 2026** (first time in KL); organizers report **1,303 Technical Paper submissions, ~18% up on the previous edition** — [SIGGRAPH Asia 2026 technical papers page](https://asia.siggraph.org/2026/submissions/technical-papers/); [3dvf writeup](https://3dvf.com/en/siggraph-2026-the-future-of-rigging-siggraph-asia-the-experience-hall-and-the-computer-animation-festival/)
- **I could not retrieve an official accepted-papers list for SIGGRAPH Asia 2026 technical papers.** Venue attributions above come from reference lists, author social posts, and ACM TOG issue metadata, not an official program — [search outcome](https://asia.siggraph.org/2026/submissions/technical-papers/)
- **SIGGRAPH 2026** has a live conference schedule with individual presentation pages (e.g. `papers_1544`) — [s2026.conference-schedule.org](https://s2026.conference-schedule.org/presentation/?id=papers_1544&sess=sess105)
- **CVPR 2026** proceedings are live on CVF open access (confirmed by the ActionMesh paper URL) — [CVF CVPR 2026](https://openaccess.thecvf.com/content/CVPR2026/papers/Sabathier_ActionMesh_Animated_3D_Mesh_Generation_with_Temporal_3D_Diffusion_CVPR_2026_paper.pdf)
- **Industry consensus note**: a 3DVF report on SIGGRAPH 2026 says "the emerging consensus favors **hybrid approaches in which AI enhances traditional rigs rather than replacing them**" — [3dvf.com](https://3dvf.com/en/siggraph-2026-the-future-of-rigging-siggraph-asia-the-experience-hall-and-the-computer-animation-festival/)

### The holy-grail question: does anything in 2026 do text/video → animation directly on a USER-SUPPLIED rig, skipping SMPL?

**Honest answer: yes, three papers claim exactly this — and none of them has downloadable code.**

- **TopX (arXiv 2610.34321, 25 Oct 2026)** — closest literal match. Text → motion for a skeleton it has never seen, from **the skeleton file alone** (kinematic tree + rest pose + joint names). No retargeting, no fine-tuning, no template. **Code not mentioned.** — [arXiv 2610.34321](https://arxiv.org/html/2610.34321)
- **JCA (arXiv 2610.28632, 20 Oct 2026, NVIDIA)** — text → video + 3D motion for arbitrary rigs, **plus video → motion given the rig's rest pose**. **No code or weights found.** — [NVIDIA project page](https://research.nvidia.com/labs/par/projects/jca/)
- **ACT (ACM TOG 45(4), 2026)** — one model doing zero-shot rigging + text-guided motion generation for **arbitrary topologies**. **No official code repository found.** — [ACM DOI 10.1145/3811392](https://dl.acm.org/doi/10.1145/3811392)
- **With code, but one stage only**: **AnyTop** (released PyTorch; motion from skeletal structure alone) — [ACM DOI](https://dl.acm.org/doi/full/10.1145/3721238.3730621); **UniRig** (rigging) — [ACM DOI](https://dl.acm.org/doi/10.1145/3730930); **ActionMesh** (rig-free animated mesh) — [GitHub](https://github.com/facebookresearch/actionmesh); **GaussiAnimate** (mesh/Gaussian sequence → skeleton + weights) — [GitHub](https://github.com/Net-Maker/GaussiAnimate)

### Inferences
- **The field crossed a line in Q3–Q4 2026.** Before 2026 the answer to "animate my own rig" was universally "go through SMPL and retarget". TopX, JCA, ACT, UniMate and SkelMo all abandon that. The user's instinct that this is the holy grail is correct, and the holy grail was reached **in papers, within the last 60 days, with no code.**
- **The practical 2026 answer for a hobbyist is a seam, not a single model.** The only all-released-code route found is **AnyTop (motion for your skeleton) or Motion2MixamoRig (video→Mixamo rig)** for human-ish rigs, and **ActionMesh → GaussiAnimate** for non-human/creature assets. Both are inferred assemblies, not published pipelines.
- **TopX's own metric is the one to watch**: "fewer frames with feet below the ground" shows that even the newest rig-agnostic generators still produce ground-penetration artifacts — which is precisely the defect the Part-1 physics route (EA's refinement, RePHO, MaskedMimic tracking) exists to fix. **The two halves of this survey are complementary, not competing**: generate on your rig with TopX/AnyTop-class models, then clean with a physics refinement pass.

### Gaps
- **Official accepted-paper lists for SIGGRAPH Asia 2026, NeurIPS 2026, ICCV/ECCV 2026** — not retrieved. Venue claims for GaussiAnimate, ACT and the neural-skinning cluster rest on secondary sources.
- **Code status for TopX, ACT, SkelGen4D, Rigel3D, RigMo, SkelMo, UniMate, arXiv 2604.13427, arXiv 2609.06517** — unverified; not individually checked on GitHub or Hugging Face.
- **Methods of SkelMo, AniGen, ViPS, Sketch2Motion, RigPAPR, G-Skin, the Geodesic Cut-Cell prior, ViP-Rig, RigAnyFace** — surfaced by title and ID only; abstracts not read. They are leads, listed here so the report-writer does not lose them, but **must not be described in detail without further checking.**
- **Whether any of the ECCV/ICCV 2026 programs contain additional rig-agnostic work** — not swept.
- **No Hugging Face sweep for released weights** in this area beyond `facebook/ActionMesh` and the unconfirmed `nvidia/Kimodo-SOMA-RP-v1.1`.
