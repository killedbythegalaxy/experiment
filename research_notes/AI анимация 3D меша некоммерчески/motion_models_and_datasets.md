# 3D Human Motion Generation Models and Motion Datasets for Non-Commercial / Personal Use (as of October 2026)

Scope note: licenses are RECORDED but never used to exclude an option. Non-commercial and research-only
assets are treated as fully usable here. Where a license is permissive that is noted as a bonus, not a
requirement.

---

## Key Question 1 — Text-to-motion and motion-generation models runnable locally on a consumer GPU

### Takeaway
NVIDIA **Kimodo** (released March 2026, updated through 15 Oct 2026) is by far the best-documented and most
practically runnable 2026 model: `pip install git+https://github.com/nv-tlabs/kimodo.git`, 282M params,
30 fps, 300-frame (10 s) clips, BVH export, and a documented path to run on a small card (~3 GB VRAM) by
forcing the text encoder to CPU. MoMask, MDM, MotionLCM and CondMDI remain the reliable older workhorses;
MotionMillion's 7B weights do appear to ship via a download script but with thin documentation, and
ViMoGen's weight-release status is self-contradictory in its own repo.

### Cited Findings

**NVIDIA Kimodo — full detail**
- Official repo: `https://github.com/nv-tlabs/kimodo` — "Official implementation of Kimodo, a kinematic motion diffusion model for high-quality human(oid) motion generation" — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo); [GitHub](https://github.com/nv-tlabs/kimodo)
- Project page, docs site, HF collection and live demo: project page `https://research.nvidia.com/labs/sil/projects/kimodo/`, docs `https://research.nvidia.com/labs/sil/projects/kimodo/docs/`, HF collection `https://huggingface.co/collections/nvidia/kimodo-v1`, demo Space `https://huggingface.co/spaces/nvidia/Kimodo` — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo)
- Architecture: kinematic motion **diffusion** model with a **two-stage transformer denoiser** that predicts root trajectory first, then body motion, specifically to reduce floating and foot-skating artifacts — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo)
- Parameter count: **282M** (HF metadata reports 0.3B params, F32 safetensors) — [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- Frame rate **30 fps**; **max 300 frames = 10 s per clip** — [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- Default skeleton: **30-joint SOMA** skeleton; model outputs root translation `[num_frames, 3]` and joint rotations `[num_frames, 30, 3, 3]` — [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- Training data: **~700+ hours of optical motion capture** from the proprietary "Bones Rigplay" dataset, with text descriptions. Code is open, training data is not released — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo); [Pebblous analysis](https://blog.pebblous.ai/blog/kimodo-text-to-motion/en/)
- Three skeleton families supported: **SOMA** (NVIDIA's parametric body model, 30 joints), **Unitree G1** humanoid robot (34 joints), and **SMPL-X body (22 joints)** — [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- **Full checkpoint list** (HF repo IDs, with release dates from the repo model table):
  - `nvidia/Kimodo-SOMA-RP-v1.2` — SOMA, Bones Rigplay 1 — 15 Oct 2026 (recommended default; corrected splits, recommended for benchmarking)
  - `nvidia/Kimodo-SOMA-RP-ArdyRep-v1.2` — SOMA, Bones Rigplay 1 — 15 Oct 2026
  - `nvidia/Kimodo-SOMA-RP-v1.1` — SOMA, Bones Rigplay 1 — 10 Apr 2026
  - `nvidia/Kimodo-SOMA-SEED-v1.1` — SOMA, BONES-SEED — 10 Apr 2026
  - `nvidia/Kimodo-SOMA-RP-v1` — SOMA, Bones Rigplay 1 — 16 Mar 2026
  - `nvidia/Kimodo-G1-RP-v1` — Unitree G1 — 16 Mar 2026
  - `nvidia/Kimodo-SOMA-SEED-v1` — SOMA, BONES-SEED — 16 Mar 2026
  - `nvidia/Kimodo-G1-SEED-v1` — Unitree G1, BONES-SEED — 16 Mar 2026
  - `nvidia/Kimodo-SMPLX-RP-v1` — SMPL-X (22-joint body) — 16 Mar 2026
  — [GitHub README](https://github.com/nv-tlabs/kimodo)
- Repo recommends the **Bones Rigplay ("RP") models by default** over the BONES-SEED models; checkpoints download automatically on first CLI/demo run (no manual download command) — [GitHub README](https://github.com/nv-tlabs/kimodo); [Quick Start docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/quick_start.html)
- **Exact install (package route, no clone):**
  ```
  conda create -n kimodo python=3.10
  conda activate kimodo
  pip install git+https://github.com/nv-tlabs/kimodo.git
  ```
  With interactive demo extras:
  ```
  pip install "kimodo[all] @ git+https://github.com/nv-tlabs/kimodo.git"
  ```
  — [Installation docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/installation.html)
- **Prerequisites:** Python 3.10 in the documented environment; PyTorch installed *before* Kimodo, "anything over PyTorch 2.0 is sufficient", GPU build recommended; developed/primarily tested on Linux — [Installation docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/installation.html)
- **Gated-model gotcha:** the text encoder uses the gated `meta-llama/Meta-Llama-3-8B-Instruct`. You must request access on HF, create a read token, and `hf auth login` (or paste the token into `~/.cache/huggingface/token`); `pip install --upgrade huggingface_hub` if `hf` is missing — [Installation docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/installation.html)
- **Source install:** `git clone https://github.com/nv-tlabs/kimodo.git && cd kimodo` then either venv/conda route or Docker; repo ships `Dockerfile`, `docker-compose.yaml`, `setup.py`, `pyproject.toml`; `pip install -e .` for development. Docs recommend the Docker source install if the pip install has compatibility problems — [Installation docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/installation.html); [GitHub](https://github.com/nv-tlabs/kimodo)
- **VRAM:** ~**17 GB** to run fully on GPU, dominated by the text embedding model. Setting `TEXT_ENCODER_DEVICE=cpu` (e.g. `TEXT_ENCODER_DEVICE=cpu kimodo_textencoder`) brings usage **under 3 GB** at a small speed cost. Tested on RTX 3090, RTX 4090, A100; other recent cards with enough VRAM should work. Supported on Ampere / Ada Lovelace / Blackwell, Linux and Windows — [Quick Start docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/quick_start.html); [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- **Entry points:** `kimodo_gen` (CLI generation, equivalently `python -m kimodo.scripts.generate`), `kimodo_textencoder` (background text-encoder service so repeated CLI calls share one encoder instance), `kimodo_demo` (Gradio demo at `http://localhost:7860` / `http://127.0.0.1:7860`), `kimodo_convert` (offline format conversion) — [Quick Start docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/quick_start.html); [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- **Minimal command:**
  ```
  kimodo_gen "A person walks forward." --model Kimodo-SOMA-RP-v1 --duration 5.0 --output output
  ```
  — [Quick Start docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/getting_started/quick_start.html)
- **Full CLI flag table** (defaults as documented): `prompt` (required positional); `--model` (default `Kimodo-SOMA-RP-v1`, though the page also says the latest SOMA-RP, currently v1.2, is used if omitted — docs are internally inconsistent here); `--duration` (default 5.0 s; for multi-prompt pass space-separated durations in one string); `--diffusion_steps` (default 100); `--num_samples` (default 1); `--num_transition_frames` (default 5, blending between prompts); `--constraints` (path to constraints JSON); `--output` (default stem `output`); `--save_example_dir`; `--bvh` (BVH export, **SOMA models only**); `--bvh_standard_tpose`; `--seed`; `--no-postprocess` (disables foot-skate cleanup and constraint optimization); `--input_folder` (folder with `meta.json` + optional `constraints.json`); `--cfg_type` (`nocfg` | `regular` | `separated`); `--cfg_weight` (one float for `regular`, two floats `[text, constraint]` for `separated`) — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- **Multi-prompt / sequenced prompts:**
  ```
  kimodo_gen "A person walks forward. A person is walking backwards." --duration "5.0 4.0"
  ```
  Constraints are injected at the start of the second prompt for continuity; `--num_transition_frames` controls the blend — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- **CFG examples:** `--cfg_type nocfg`; `--cfg_type regular --cfg_weight 2.5`; `--cfg_type separated --cfg_weight 2.0 1.5`; passing two values implies `separated` — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- **Output formats:** NPZ always written (Kimodo format); **AMASS NPZ** for G1 and SMPL-X models; **MuJoCo qpos CSV** for G1 and SMPL-X models; **BVH** for SOMA only via `--bvh`. FBX is not mentioned. Single sample → `stem.npz` / `stem.csv`; SMPL-X AMASS export is `stem_amass.npz`; multiple samples → a folder named after the stem containing `stem_00.npz`, `stem_01.npz`, … — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- **NPZ field layout:** `posed_joints [T,J,3]` (global joint positions), `global_rot_mats [T,J,3,3]`, `local_rot_mats [T,J,3,3]` (parent-relative), `foot_contacts [T,4]` (L heel, L toe, R heel, R toe), `smooth_root_pos [T,3]`, `root_positions [T,3]` (unsmoothed pelvis), `global_root_heading [T,2]` — [GitHub README](https://github.com/nv-tlabs/kimodo)
- **License:** code **Apache-2.0**; checkpoints licensed separately — SOMA and G1 checkpoints under the **NVIDIA Open Model License**, and `Kimodo-SMPLX-RP-v1` under the more restrictive **NVIDIA R&D Model License**. The SOMA model card states the model "is ready for commercial use" — [GitHub README](https://github.com/nv-tlabs/kimodo); [nvidia/Kimodo-SOMA-RP-v1 model card](https://huggingface.co/nvidia/Kimodo-SOMA-RP-v1)
- Repo activity: 54 commits on `main`, most recent news entry dated **2026-10-15** — [GitHub](https://github.com/nv-tlabs/kimodo)
- Kimodo also ships the **Kimodo Motion Generation Benchmark**, a curated test suite hosted on Hugging Face — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo)
- Kimodo outputs **kinematic** data, not robot controllers; it is positioned as an offline authoring tool feeding downstream physics pipelines such as ProtoMotions and GEAR-SONIC — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo)
- A third-party local-install walkthrough exists for low-VRAM cards (RTX 3060) with a custom text encoder — [Aero-Ex gist: Kimodo Local Installation Guide (with Custom Text Encoder)](https://gist.github.com/Aero-Ex/3affd23c4c9632dbff3045f4ae3655ec); [smeltcore RTX 3060 guide](https://smeltcore.com/recipes/kimodo-on-rtx-3060-text-to-3d-motion-generation-guide/)
- A ROBOTIS fork/integration exists (`ROBOTIS-GIT/kimodo`) documented under their AI Sapiens motion-generation stack — [ROBOTIS docs](https://docs.robotis.com/docs/systems/aisapiens/motion_generation_retargeting/kimodo/)

**MotionMillion / "Go to Zero" (7B)**
- Paper: "Go to Zero: Towards Zero-shot Motion Generation with Million-scale Data", ICCV 2025, arXiv 2507.07095 — [arXiv](https://arxiv.org/abs/2507.07095); [CVF open access PDF](https://openaccess.thecvf.com/content/ICCV2025/papers/Fan_Go_to_Zero_Towards_Zero-shot_Motion_Generation_with_Million-scale_Data_ICCV_2025_paper.pdf)
- Code: `https://github.com/VankouF/MotionMillion-Codes`, **license Apache-2.0** — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- Architecture: decoder-only transformer based on **LLaMA**, scaled to **7B parameters** — [arXiv HTML](https://arxiv.org/html/2507.07095)
- **Weights: yes, released.** README states the 7B model was "trained on all.txt" and performs best zero-shot; fetched with `bash prepare/download_pretrained_models.sh`. No explicit HF model repo ID is given in the README — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- Dataset: news section says "MotionMillion dataset is released" dated **2025/07/26**, hosted at `https://huggingface.co/datasets/InternRobotics/MotionMillion`. The Quick Start's dataset-preparation section still reads "Comming Soon!" — an internal contradiction in the repo — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- Dataset scale: **>2,000 hours, 2 million high-quality motion sequences** paired with text — [MotionMillion project page](https://vankouf.github.io/MotionMillion/)
- **Install:** `conda create python=3.8.11 --name motionmillion`; `pip install -r requirements.txt` (tested Python 3.8.11 / PyTorch 2.4.1); `conda install conda-forge::git-lfs`; download SMPL+H and DMPL body models into `./body_models/`; then `bash prepare/download_glove.sh`, `bash prepare/download_t2m_evaluators_on_motionmillion.sh`, `bash prepare/download_T5-XL.sh`, `bash prepare/download_pretrained_models.sh` — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- **Inference:** `bash scripts/inference/single_inference/test_t2m_7B.sh` (single) or `bash scripts/inference/batch_inference/test_t2m_7B.sh` (batch on MotionMillion-Eval). You must substitute `${resume-pth}` (tokenizer) and `${resume-trans}` (T2M model) paths. The script uses **LLaMA3.1-8B to rewrite prompts**; drop `${use_rewrite_model}` and `${rewrite_model_path}` to skip that — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- Motion representation: a `vector_272` format, with SMPL+H / DMPL body models; tokenizer was trained on 4× 80 GB GPUs — [GitHub](https://github.com/VankouF/MotionMillion-Codes)
- Motivation stated in the paper: prior text-to-motion models train on "barely 144 hours", so they overfit and mishandle long instructions — [Rohan Paul summary of the paper](https://x.com/rohanpaul_ai/status/1943541511884313018)

**ViMoGen (ICLR 2026)**
- Repo: `https://github.com/MotrixLab/ViMoGen` — "[ICLR 2026] Official Code for 'The Quest for Generalizable Motion Generation: Data, Model, and Evaluation'"; paper arXiv 2510.26794; project page `https://motrixlab.github.io/2026_iclr_vimogen/` — [GitHub](https://github.com/MotrixLab/ViMoGen); [arXiv](https://arxiv.org/abs/2510.26794)
- Diffusion Transformer handling text-to-motion and text/motion-to-motion; built on **Wan2.1, a 1.3B text-to-video foundation model** — [arXiv HTML](https://arxiv.org/html/2510.26794)
- Ships a **228K-sample dataset** (optical mocap + semantically annotated web-video motions + synthesized samples) and the **MBench** benchmark — [GitHub](https://github.com/MotrixLab/ViMoGen)
- **Weight status is contradictory:** the repo news section says pretrained DiT weights and the core inference pipeline were released in **December 2025**, while the repo's release plan still lists pretrained weights as pending — [GitHub](https://github.com/MotrixLab/ViMoGen)
- A distilled **ViMoGen-light** variant removes the video-generation dependency while retaining generalization — [arXiv HTML](https://arxiv.org/html/2510.26794)

**MoMask / MoMask++**
- MoMask (CVPR 2024): `https://github.com/EricGuo5513/momask-codes`. Repo states "**No GPU is required to use MoMask**"; paper timed inference on a single **2080Ti** averaged over 100 samples and positions MoMask favorably on the quality/efficiency tradeoff. No VRAM figure published — [GitHub](https://github.com/EricGuo5513/momask-codes); [MoMask paper](https://arxiv.org/pdf/2312.00063)
- **MoMask++** is introduced in the **SnapMoGen** paper (arXiv 2507.09122); code at `https://github.com/snap-research/SnapMoGen`, project page `https://snap-research.github.io/SnapMoGen/`. It converts motion to multi-scale token sequences and generates all tokens with a single generative masked transformer; reported SOTA on HumanML3D and SnapMoGen — [SnapMoGen paper](https://arxiv.org/pdf/2507.09122); [project page](https://snap-research.github.io/SnapMoGen/)
- A 2026 third-party paper (MUGEN, arXiv 2607.27581) measured **MoMask++ peak memory at 0.73 GB at batch size 32 on an NVIDIA L4** — the only concrete memory number I found for any of this model family — [MUGEN](https://arxiv.org/pdf/2607.27581)

**MDM / MotionLCM / StableMoFusion speed**
- MotionLCM (arXiv 2404.19759): one-step inference, **0.217 s per sentence**, versus **24.74 s for MDM with CFG**. Trained on an RTX 4090, tested on a Tesla V100. No VRAM figures reported — [MotionLCM paper](https://arxiv.org/pdf/2404.19759)
- StableMoFusion (arXiv 2405.05691): uses **FP16** at inference to accelerate, and describes parameter quantization to cut memory. No measured VRAM figure published — [StableMoFusion](https://arxiv.org/html/2405.05691)

**Broader 2025 model landscape (all with public repos, per the curated list)**
- The `awesome-text-to-motion` list enumerates 2025 releases with repos including: ReMoMask (`AIGeeksGroup/ReMoMask`), MoMask++/SnapMoGen (`snap-research/SnapMoGen`), GotoZero (`VankouF/MotionMillion-Codes`), MotionGPT3 (`OpenMotionLab/MotionGPT3`), Motion-R1 (`GigaAI-Research/Motion-R1`), ANT (`CCSCovenant/ANT`), ACMDM (`neu-vi/ACMDM`), MoMADiff (`zzysteve/MoMADiff`), Shape-Move (`shape-move/shape-move-public`), MG-MotionLLM (`CVI-SZU/MG-MotionLLM`), MixerMDM (`pabloruizponce/MixerMDM`), MotionReFit (`emptybulebox1/motionRefit`), LoRA-MDM (`haimsaw/LoRA-MDM`), SimMotionEdit (`lzhyu/SimMotionEdit`), MotionStreamer (`zju3dv/MotionStreamer`), Kinesis (`amathislab/Kinesis`), SALAD (`seokhyeonhong/salad`), PMG (`qinghuannn/PMG`), PersonaBooth (`Boeun-Kim/PersonaBooth`), MotionLab (`Diouo/MotionLab`), CASIM (`cjerry1243/casim_t2m`), ScaMo (`shunlinlu/ScaMo_code`), Light-T2M (`qinghuannn/light-t2m`), LS-GAN (`AmballaAvinash/motion-latent-diffusion`) — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- Additional 2025 entries listed **without** repo links (treat as paper-only / weights unconfirmed): X-MoGen, SASI, MOST, AttrMoGen, MOGO, PHYLOMAN, ReAlign, GENMO, DSDFM, UniPhys, MotionAnything, UniTMGE, FlowMotion, GenM3, sMDM, BioVAE, MoMug, Fg-T2M++, SPORT, MotionPCM, Free-T2M, FlexMotion, PackDiT, EnergyMoGen, EgoLM — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- 2026 papers exist that are not in that list, e.g. **LLaMo** (arXiv 2602.12370, "Scaling Pretrained Language Models for Unified Motion Understanding and Generation with Continuous Autoregressive Tokens") and **MotionVLA** (arXiv 2606.15142) — [LLaMo](https://arxiv.org/pdf/2602.12370); [MotionVLA](https://arxiv.org/html/2606.15142v1)

### Inferences
- Kimodo is the only model in this set where NVIDIA has published a *documented low-VRAM path*. The 17 GB figure is almost entirely the Llama-3-8B-based LLM2Vec text encoder, not the 282M diffusion model — so the practical consumer-GPU recipe is: run `kimodo_textencoder` with `TEXT_ENCODER_DEVICE=cpu` once as a persistent service, then hit it with many `kimodo_gen` calls. That keeps the GPU side under 3 GB and makes an 8 GB card viable.
- Kimodo's SOMA checkpoints are the ones to use for a hobby animation pipeline, because **BVH export is SOMA-only** (`--bvh`). The SMPL-X checkpoint gives you 22-joint AMASS NPZ instead, which is the right choice if you are feeding HumanML3D-style tooling or an SMPL-X mesh — but it carries the stricter NVIDIA R&D Model License.
- The gated `meta-llama/Meta-Llama-3-8B-Instruct` dependency is the single most likely install blocker for Kimodo: you must have HF access approved before anything works, and the docs note the transformers library prints unexpected/missing LLM2Vec layer warnings at startup that are expected and ignorable.
- MotionMillion's 7B is almost certainly impractical as a first choice on a consumer card: a 7B LLaMA-style generator *plus* a T5-XL and optionally LLaMA3.1-8B for prompt rewriting is a multi-tens-of-GB stack, and the repo publishes no inference VRAM figure. Its value for a hobbyist is more as a *dataset* source than as a model.
- Python 3.8.11 pinning in MotionMillion versus Python 3.10 in Kimodo means these cannot share one environment; plan separate conda envs per model.

### Gaps
- **No official Kimodo inference-speed number.** Search-result summaries attributed "about 2 to 5 seconds per prompt on an RTX 3090" to third-party/vendor-style sites (kimodo.net and blog aggregators), not to NVIDIA. The official Quick Start and CLI pages explicitly contain no timing figures. Treat the 2–5 s figure as unverified secondary reporting. The only speed lever documented is `--diffusion_steps` (default 100).
- Kimodo's parameter count is confirmed only for `Kimodo-SOMA-RP-v1` (282M). Whether v1.1/v1.2, the G1 and SMPL-X variants share that size is not stated.
- Kimodo checkpoint *file names* are not listed on the model card or README (download is automatic), so I cannot give exact `.safetensors` filenames.
- No published VRAM figures for MDM, MotionDiffuse, T2M-GPT, MotionGPT, OmniControl, MotionLCM, StableMoFusion, or Large Motion Model. I found no source measuring these; the one adjacent number is MoMask++ at 0.73 GB / batch 32 on an L4 from a third-party paper.
- I could not verify current repo state (last commit, open install issues) for MoMask, MDM, MotionDiffuse, T2M-GPT, MotionGPT, OmniControl, MotionLCM, StableMoFusion, or Large Motion Model — GitHub API access was not available to this session and I did not have tool budget to fetch each README.
- ViMoGen's actual current weight availability is unresolved due to the repo's own contradictory news-vs-release-plan statements.
- "Large Motion Model" and 2026-specific releases beyond Kimodo/LLaMo/MotionVLA were not individually verified.

---

## Key Question 2 — Motion in-betweening and keyframe-driven models: can you give it 3 poses and get a clean transition?

### Takeaway
Yes, two ways. **Kimodo** does it through its constraints JSON — you place `fullbody` constraints at chosen
`frame_indices` and it generates everything between them, with an interactive timeline editor to author the
keyframes and a "Save Constraints" button that exports the exact JSON the CLI consumes. **CondMDI**
(SIGGRAPH 2024, MIT license) is the dedicated in-betweening model and handles arbitrary dense-or-sparse
keyframes plus *partial* keyframes (only some joints specified), but its 2026 successors report it drifts
from keyframes and jitters in the second half of a clip.

### Cited Findings

**Kimodo in-betweening / keyframe control**
- The project page explicitly lists these control modes: text prompts (including sequences of prompts), **full-body keyframes (sparse keyframes)**, **in-betweening**, **sparse joint positions and rotations**, **end-effector constraints (hands, feet, or both)**, **2D waypoints** (SOMA and G1), **dense 2D paths**, and **mixed constraints** — [NVIDIA SIL project page](https://research.nvidia.com/labs/sil/projects/kimodo)
- **Constraints JSON schema:** the file is a JSON **array** of constraint objects; each object requires `type` (one of `root2d`, `fullbody`, `left-hand`, `right-hand`, `left-foot`, `right-foot`, `end-effector`) and `frame_indices` (0-based frame indices into the generated clip) — [Constraints docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/constraints.html)
- Per-type fields:
  - `fullbody` → requires `local_joints_rot [T,J,3]` (axis-angle, radians) and `root_positions [T,3]`; optional `smooth_root_2d [T,2]`
  - `end-effector` → requires `joint_names` plus the `fullbody` fields; optional `smooth_root_2d`
  - `left-hand` / `right-hand` / `left-foot` / `right-foot` → same fields as `fullbody`
  - `root2d` → requires `smooth_root_2d [T,2]`; optional `global_root_heading [T,2]` as `[cos θ, sin θ]` per frame (not a raw angle). A **dense 2D path** is just `root2d` with many frames.
  — [Constraints docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/constraints.html)
- Coordinate convention: **Y-up, meters**; locomotion on the XZ ground plane; constraints are relative to a canonical origin where the root starts at (0,0) at frame 0; Y is absolute hip height (~0.9 m standing). `local_joints_rot` constrains the **FK joint positions**, not the rotations directly. `joint_names` values are skeleton-dependent — see the skeleton's `expand_joint_names()` — [Constraints docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/constraints.html)
- Working example (2D root waypoints at frames 0/30/60):
  ```json
  [{"type": "root2d",
    "frame_indices": [0, 30, 60],
    "smooth_root_2d": [[0.0, 0.0], [0.5, 0.0], [1.0, 0.1]]}]
  ```
  Full-body keyframe example pins hips at x=0, y=0.96, z=1.5 at frame 60 with one `[x,y,z]` axis-angle per joint — [Constraints docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/constraints.html)
- Invocation with constraints:
  ```
  kimodo_gen "A person walks forward and picks something up from the ground." \
    --model Kimodo-SOMA-RP-v1 --duration 5.0 \
    --constraints kimodo/assets/demo/examples/kimodo-soma-rp/03_full_body_keyframes/constraints.json
  ```
  — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)
- Authoring workflow: the interactive demo (`kimodo_demo`) has a **timeline editor** for adding/editing keyframes and constrained intervals across several tracks, with constraint-type tracks for Full-Body, 2D Root (waypoints or full paths), and End-Effectors; click **Save Constraints** to export a valid JSON for `--constraints`. Per-model working examples live in `kimodo/assets/demo/examples` — [Constraints docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/constraints.html); [GitHub README](https://github.com/nv-tlabs/kimodo)
- Postprocessing includes **foot-skate cleanup and constraint optimization**, which can be disabled with `--no-postprocess` — [CLI docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/cli.html)

**CondMDI (Flexible Motion In-betweening with Diffusion Models)**
- Paper: Cohan, Tevet, Reda, Peng, van de Panne; arXiv 2405.11126; SIGGRAPH 2024 (ACM DOI 10.1145/3641519.3657414). Project page `https://setarehc.github.io/CondMDI/` — [arXiv](https://arxiv.org/pdf/2405.11126); [project page](https://xbpeng.github.io/projects/CondMDI/index.html); [ACM](https://dl.acm.org/doi/fullHtml/10.1145/3641519.3657414)
- Official code repo: `https://github.com/setarehc/diffusion-motion-inbetweening`, **license MIT** (dependencies CLIP/SMPL/SMPL-X/PyTorch3D and the datasets carry their own licenses) — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- Capability: "allows for arbitrary dense-or-sparse keyframe placement and partial keyframe constraints"; trained on randomly sampled keyframes with randomly sampled joints plus a mask indicating observed keyframes and features. Base model derives from GMD; text prompt goes through a CLIP-based embedder — [arXiv HTML](https://arxiv.org/html/2405.11126v2)
- **Install:** developed on Ubuntu 20.04, Python 3.7, CUDA 11.7, PyTorch 1.13.1. Steps: install ffmpeg → create virtualenv → `pip uninstall ffmpeg` → `pip install spacy` → `python -m spacy download en_core_web_sm` → `pip install git+https://github.com/openai/CLIP.git`; then run the `prepare/` scripts for SMPL files, GloVe, and T2M evaluators (plus recognition models for unconstrained use). Authors recommend installing from scratch to avoid version conflicts with GMD. Windows needs a separate ffmpeg guide — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- **Checkpoints:** three on Google Drive — CondMDI (random frames *and* joints), random-frames-only conditional, and unconditional. Unzip into `./save/`; commands expect `./save/condmdi_uncond/model000500000.pt` and `./save/condmdi_randomframes/model000750000.pt` — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- **In-betweening commands:**
  - Unconditioned model with imputation + reconstruction guidance:
    ```
    python -m sample.edit --model_path ./save/condmdi_uncond/model000500000.pt \
      --edit_mode benchmark_sparse --transition_length 5 --num_samples 10 \
      --num_repetitions 3 --imputate --stop_imputation_at 1 \
      --reconstruction_guidance --reconstruction_weight 20 \
      --text_condition "a person throws a ball"
    ```
  - Conditional model:
    ```
    python -m sample.conditional_synthesis --model_path ./save/condmdi_randomframes/model000750000.pt \
      --edit_mode benchmark_sparse --transition_length 5 --num_samples 10 \
      --num_repetitions 3 --text_prompt "a person throws a ball"
    ```
  - Omit the text flag (or pass `--no_text`) to condition on **keyframes only**. Other flags: `--edit_mode`, `--stop_imputation_at` (default 0), `--reconstruction_weight`, `--device`, `--seed`, `--motion_length` (**max 9.8 s**), `--progress`. The README is internally inconsistent on `--imputation` vs `--imputate` — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- **Output:** `results.npy` (text prompts + xyz joint positions), `sample##_rep##.mp4` stick-figure animations; optional SMPL mesh rendering via `visualize.render_mesh` → `sample##_rep##_smpl_params.npy` (thetas, root translations, vertices, faces) plus per-frame `.obj` files (uses SMPLify, needs a GPU) — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- **Custom keyframe poses are not documented.** The README documents no file or flag for passing your own keyframe poses; evaluation samples keyframes from ground-truth motion using root locations as conditions. An `--interactive` flag is listed as "in development" and would let you pick keyframes in a window, overriding the predefined pattern — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- Repo activity: 14 commits, first-release note dated **21 June 2024**, 8 open issues, 1 open PR; no VRAM figure stated — [GitHub](https://github.com/setarehc/diffusion-motion-inbetweening)
- **Known quality limits, per 2026 successor work:** CondMDI "gradually deviates from the direction of the keyframes" and shows "noticeable jittering in the latter half of the motion"; its "weak condition constraint results in significant keyframe errors" — [Generative Motion In-betweening by Diffusion over Continuous Implicit Representations, arXiv 2605.12778](https://arxiv.org/pdf/2605.12778)

**Other in-betweening / sparse-keyframe work**
- "Generative Motion In-betweening by Diffusion over Continuous Implicit Representations" (arXiv 2605.12778, 2026) — a direct CondMDI successor addressing keyframe drift — [arXiv](https://arxiv.org/html/2605.12778)
- "Less is More: Improving Motion Diffusion Models with Sparse Keyframes" (arXiv 2503.13859) — [arXiv](https://arxiv.org/html/2503.13859v1)
- `sMDM` (arXiv 2025) — sparse-keyframe conditioning; `PMG` (TCSVT 2025, `github.com/qinghuannn/PMG`) — conditions on text plus a few motion frames — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- `seokhyeonhong/long-mib` — long-horizon motion in-betweening; ships **pre-processed LaFAN1 and 100STYLE in NPZ** — [GitHub](https://github.com/seokhyeonhong/long-mib)
- `CrownLedger/hpp-condmdi` — third-party project combining HPP (Human-Perceptual Projection) with HumanML3D and pretrained CondMDI for "deterministic rough motion to natural motion"; its setup refers to a `diffusion-motion-inbetweening` directory — [GitHub](https://github.com/CrownLedger/hpp-condmdi)
- SceneAdapt (arXiv 2510.13044) — scene-aware adaptation of human motion diffusion — [arXiv](https://arxiv.org/pdf/2510.13044)

### Inferences
- **"Three poses → clean transition" is directly achievable in Kimodo**, and is the practically better route than CondMDI for a hobbyist: you get a GUI to author the three poses, an exported JSON, a BVH out the other end, and the model's two-stage root/body denoiser plus foot-skate postprocess specifically targets the artifacts CondMDI's successors complain about.
- Kimodo's docs do **not** define a distinct `in-betweening` or `sparse joint` constraint `type`, even though the project page advertises both. The schema makes the mechanism obvious though: in-betweening = `fullbody` constraints at a few sparse `frame_indices`; sparse joint = `end-effector` with a narrow `joint_names` list (or the dedicated `left-hand`/`right-foot`/etc. types). This is an inference from the schema, not a documented statement.
- Because `local_joints_rot` is constrained through forward kinematics rather than on the rotations directly, Kimodo keyframes behave as *positional* targets — expect the solver to honor where limbs end up more faithfully than exact joint twist.
- CondMDI's lack of a documented custom-keyframe input path is a real practical barrier: as shipped, its demos condition on keyframes sampled from ground-truth HumanML3D motions. Driving it from *your own* three poses means writing code against its internals, whereas Kimodo exposes this as a supported file format.
- Kimodo's 10 s / 300-frame ceiling and CondMDI's 9.8 s ceiling both mean a longer animation must be built as chained clips. Kimodo's multi-prompt mode with `--num_transition_frames` is the built-in answer; `long-mib` is the research answer for long horizons.

### Gaps
- No VRAM figure for CondMDI.
- Kimodo's documented `in-betweening` and `sparse joint` constraint types are advertised on the project page but absent from the constraints schema page; the mapping above is inferred. The docs point to a "Constraints key-concepts page" and the API reference, which I did not fetch.
- I did not verify whether `setarehc/diffusion-motion-inbetweening` has commits after June 2024, nor the contents of its 8 open issues (useful for install-blocker triage).
- No inference-time or quality comparison between Kimodo in-betweening and CondMDI exists in any source I found.

---

## Key Question 3 — Motion datasets fully usable for a non-commercial project (with a martial arts / combat hunt)

### Takeaway
AMASS (register, ~40–42 h, 15 merged mocap sets) and its derivatives HumanML3D (28.59 h, 20 fps, 44,970 text
descriptions) and BABEL remain the foundation; **Motion-X** (144.2 h, 81.1K clips, 30 fps, SMPL-X **with
hands and face**, Google-Form non-commercial access) is the best whole-body option; **MotionMillion**
(>2,000 h, 2M sequences, Apache-2.0, on Hugging Face) is now the largest. For martial arts specifically the
reliable free sources are **SFU Mocap** (Kendo, Wushu kicking), **CMU Mocap** (martial arts category),
**Bandai Namco Research's ~3,000 BVH moves** (includes fighting, CC-BY-NC-ND), and **Rokoko's 6 free martial
arts FBX animations** — all usable here.

### Cited Findings

**AMASS**
- Official site: `https://amass.is.tue.mpg.de/`; code/overview page `https://is.mpg.de/en/code/amass-dataset`; paper ICCV 2019 (arXiv 1904.03278) — [AMASS code page](https://is.mpg.de/en/code/amass-dataset); [ICCV paper](https://openaccess.thecvf.com/content_ICCV_2019/papers/Mahmood_AMASS_Archive_of_Motion_Capture_As_Surface_Shapes_ICCV_2019_paper.pdf)
- Scale: figures vary between paper versions — one gives **42 hours, 346 subjects, 11,451 motions**; the ICCV version lists **~40 hours, 344 subjects, 11,265 motions**. Derivative work cites "more than 40 hours" — [arXiv/ar5iv](https://ar5iv.labs.arxiv.org/html/1904.03278)
- Composition: AMASS **merges 15 existing mocap datasets**, with markersets from 37 to 91 markers unified into one format — [arXiv/ar5iv](https://ar5iv.labs.arxiv.org/html/1904.03278)
- Access: **registration required** on the dataset website(s) and agreement to the terms before downloading; released for academic research under custom licenses from the Max Planck Institute for Intelligent Systems — [AMASS code page](https://is.mpg.de/en/code/amass-dataset)
- License framing per a dataset comparison: "non-commercial scientific research, education, and artistic projects", with source-dataset terms also applying; >300 subjects, >11,000 motions — [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)
- Native format is SMPL-family with SMPL+H and DMPL tooling documented. **SMPL-X neutral body annotations from AMASS** are used by downstream work (e.g. RoHM), so SMPL-X versions exist, but SMPL-X is not the native release format — [RoHM paper](https://arxiv.org/pdf/2401.08570); [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)

**HumanML3D**
- Repo: `https://github.com/EricGuo5513/HumanML3D`. **28.59 hours, 14,616 motions, 44,970 text descriptions, 20 fps.** Standardized joint-motion representation. Repository access does not override AMASS or source-dataset terms — [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)
- Introduced CVPR 2022 — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)

**KIT Motion-Language (KIT-ML)**
- Download: `https://motion-annotation.humanoids.kit.edu/dataset/`. **11.23 hours, 3,911 motions, 6,278 annotations** (2016 release). Format **MMM XML**, MMM skeleton representation. Described as open; verify dataset and source terms — [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)

**BABEL**
- Site: `https://babel.is.tue.mpg.de/`. **~43 hours of AMASS motion, 28,055 sequence labels, 63,353 frame labels.** Uses the AMASS motion representation; academic research; AMASS access/license required for the motion itself — [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)

**Motion-X and Motion-X++**
- Repo: `https://github.com/IDEA-Research/Motion-X`; project page `https://motion-x-dataset.github.io/` (NeurIPS 2023) — [GitHub](https://github.com/IDEA-Research/Motion-X)
- Scale: **81.1K motion clips, 15.6M whole-body poses**; **144.2 hours** per a dataset comparison — [GitHub](https://github.com/IDEA-Research/Motion-X); [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)
- **Subset breakdown (clips / frames):** AMASS 26K / 5.4M; IDEA400 12.5K / 2.6M; Online Videos 32.5K / 6.0M; HAA500 5.2K / 0.3M; AIST++ 1.4K / 0.3M; BAUM 1.4K / 0.2M; GRAB 1.3K / 0.4M; EgoBody 1.0K / 0.4M; HuMMan 0.7K / 0.1M — [GitHub](https://github.com/IDEA-Research/Motion-X)
- Format: **SMPL-X parameters in `.npy`**, as a **322-dimensional vector per frame** split into root orientation, body pose, hand pose, jaw pose, face expression, face shape, translation, and betas. **All motions unified to 30 fps.** — [GitHub](https://github.com/IDEA-Research/Motion-X)
- **Fingers and face: yes, both included** — hand pose and hand text labels, jaw pose, facial expression motions, separate face and body motion folders — [GitHub](https://github.com/IDEA-Research/Motion-X)
- Text: frame-level whole-body pose descriptions **and** sequence-level semantic labels — [GitHub](https://github.com/IDEA-Research/Motion-X)
- **Download process:** request authorization via a **Google Form** (the form states authorization is for **non-commercial use**), then receive an email with download links. Original RGB videos and the original AMASS/GRAB/EgoBody motion data are **not redistributed** — only labels and annotations; you must separately register on the AMASS and GRAB sites and accept their licenses — [GitHub](https://github.com/IDEA-Research/Motion-X)
- License: Motion-X has its own license PDF; IDEA400 has a separate license document; AMASS and GRAB keep MPI custom licenses — [GitHub](https://github.com/IDEA-Research/Motion-X)
- **Motion-X++** extends Motion-X with more paired modalities: video, whole-body 2D keypoints, local and global SMPL-X, text, and audio (where video has audio). Hosted at `https://huggingface.co/datasets/YuhongZhang/Motion-Xplusplus` — [GitHub](https://github.com/IDEA-Research/Motion-X)

**MotionMillion**
- **>2,000 hours, 2 million** high-quality text-paired motion sequences; Hugging Face dataset `InternRobotics/MotionMillion`; code Apache-2.0; release announced 2025/07/26 — [project page](https://vankouf.github.io/MotionMillion/); [GitHub](https://github.com/VankouF/MotionMillion-Codes)

**100STYLE**
- Zenodo record: `https://zenodo.org/records/8127870`, **~15 GB total** — processed-data zip **14.8 GB** + original BVH zip **1.5 GB** — [Zenodo](https://zenodo.org/records/8127870)
- Captured with an **XSens** mocap system; BVH at **60 fps**. An FBX/BVH version retargeted to a skinned character is also available. Author page: `https://www.ianxmason.com/100style/` — [Zenodo](https://zenodo.org/records/8127870); [Ian Mason 100STYLE page](https://www.ianxmason.com/100style/)
- Associated paper: "Motion Capture Dataset for Practical Use of AI-based Motion Editing and Stylization" (arXiv 2306.08861) — [arXiv](https://arxiv.org/html/2306.08861)
- Retargeting helper: `https://github.com/orangeduck/100style-retarget` — "Retargeting of the 100STYLE dataset onto a common skeleton" — [GitHub](https://github.com/orangeduck/100style-retarget/)

**LAFAN1 (Ubisoft La Forge Animation Dataset)**
- Official repo: `https://github.com/ubisoft/ubisoft-laforge-animation-dataset`. Original release is a **BVH zip of ~137 MB**; **5 subjects, 77 sequences, 496,672 motion frames at 30 fps**; all sequences in BVH — [GitHub](https://github.com/ubisoft/ubisoft-laforge-animation-dataset); [OpenDataLab mirror](https://opendatalab.com/OpenDataLab/LaFAN1/download)
- Mirrors: Kaggle copy ~158 MB zip; Hugging Face retargeted Unitree G1 CSVs ~314 MB at `https://huggingface.co/datasets/lvhaidong/LAFAN1_Retargeting_Dataset` — [Kaggle](https://www.kaggle.com/datasets/star2578/lafan1); [HF LAFAN1 Retargeting Dataset](https://huggingface.co/datasets/lvhaidong/LAFAN1_Retargeting_Dataset)
- Pre-processed NPZ versions of **both LaFAN1 and 100STYLE** are provided by the `long-mib` project — [GitHub](https://github.com/seokhyeonhong/long-mib)

**MOSAIC (2026-era, permissive)**
- `https://huggingface.co/datasets/BAAI-Humanoid/MOSAIC_Dataset` — **45.9 GB**; human motion in AMASS-style files, Unitree G1 motion in NPZ; dataset card **CDLA-Permissive-2.0** (external tools and source data keep their own terms) — [Uthana dataset comparison](https://uthana.com/resources/human-motion-datasets)

**Martial arts / combat / sports motion — the hunt**
- **SFU Motion Capture Database** (`https://mocap.cs.sfu.ca/`): has martial arts entries including **Kendo and Wushu kicking** motions, in **BVH** among other formats. Free for research; data cannot be used in commercial products or resold — [SFU Mocap](https://mocap.cs.sfu.ca/)
- **CMU Graphics Lab Motion Capture Library** (`https://mocap.cs.cmu.edu/`): a secondary source states it has **over 2,500 motion sequences covering walking, running, sports, martial arts, gestures and more**. Official terms: "free for all uses", may be included in commercially-sold products, but **you may not resell this data directly, even in converted form** — [CMU Mocap](https://mocap.cs.cmu.edu/); [MocapOnline blog](https://mocaponline.com/blogs/mocap-news/bvh-animation-guide)
- **Bandai Namco Research**: ~**3,000 free mocap moves in BVH**, covering walking, running, **fighting** and dancing; tagged **CC-BY-NC-ND** per the reporting source (restricting commercial use and derivatives — irrelevant for this project). Fight content is described as limited — [CGChannel](https://www.cgchannel.com/2022/05/download-3000-free-mocap-moves-from-bandai-namco-research/); [3DArt](https://www.3dart.it/en/bvh-free-mocap-moves-from-bandai-namco/)
- **Rokoko**: **6 free martial arts animations** in **FBX** (not BVH); Rokoko states they can be used in any project, passion or commercial — [Rokoko](https://www.rokoko.com/resources/rokoko-mocap-6-free-martial-arts-animations)
- **motioncapturedata.com**: has a dedicated **"Mocap Boxing"** category with a free BVH sample from NaturalPoint, and a **"fighting"** category with karate and kicking BVH files. Licensing terms not stated on the category pages — [Mocap Boxing category](https://motioncapturedata.com/category/mocap-boxing/); [fighting category](https://motioncapturedata.com/category/fighting/)
- **HAA500** is a Motion-X subset (5.2K clips / 0.3M frames) — HAA500 is an atomic *action* dataset, so sports actions arrive via Motion-X's SMPL-X annotations — [GitHub Motion-X](https://github.com/IDEA-Research/Motion-X)
- **AIST++** is a Motion-X subset (1.4K clips / 0.3M frames) — dance, not combat — [GitHub Motion-X](https://github.com/IDEA-Research/Motion-X)
- A Japanese site offering **over 4,000 free mocap moves** was reported in 2010 — [CGChannel](https://www.cgchannel.com/2010/11/japanese-website-offers-over-4000-free-mocap-moves/)

### Inferences
- The dataset hour counts **cannot be summed**: AMASS underlies BABEL and HumanML3D, and AMASS is itself a Motion-X subset (26K of its 81.1K clips). Motion-X's genuinely new content is IDEA400 + Online Videos + HAA500, roughly 50K clips.
- For a hobbyist whose goal is *authoring a full animation set including combat*, the realistic data stack is: Motion-X (whole-body with hands and face, 30 fps, matches Kimodo's 30 fps) for semantics + variety; CMU/SFU/Bandai BVH for martial arts reference; 100STYLE for restyling; LaFAN1 for clean game-ready locomotion and in-betweening.
- Motion-X's 322-dim SMPL-X vector and 30 fps align naturally with Kimodo's SMPL-X checkpoint and its AMASS-NPZ export, so Kimodo-generated motion and Motion-X data can live in one pipeline. Kimodo's SOMA BVH export, by contrast, pairs better with CMU/LaFAN1/100STYLE BVH tooling.
- 100STYLE's 60 fps versus HumanML3D's 20 fps and Motion-X/Kimodo's 30 fps means resampling is unavoidable; the `long-mib` pre-processed NPZ and `orangeduck/100style-retarget` exist precisely to absorb that work.
- No single dataset is combat-dense. Martial arts has to be assembled from several small free sources, or sourced from MotionMillion's 2M sequences, which is the only corpus large enough that combat could be a meaningful slice.

### Gaps
- **The "23.7% martial arts" MotionMillion claim is unverified.** Neither the project page, the paper's abstract/HTML, nor the repo README that I fetched gives any motion-category breakdown or martial-arts percentage. Do not report this figure without checking the paper's dataset-statistics section or supplementary material.
- **No per-subset hour breakdown for AMASS's 15 constituent datasets.** The official pages I reached do not list it, and the two paper versions disagree on the totals (42 h / 11,451 motions vs ~40 h / 11,265 motions). ACCAD, Eyes Japan, SFU and CMU are AMASS subsets, but I could not source their individual hours.
- **No GB figures** for AMASS, HumanML3D, KIT-ML, BABEL, or Motion-X — the only sizes I could source are 100STYLE (~15 GB), LaFAN1 (137 MB), MOSAIC (45.9 GB), and the LAFAN1 mirrors.
- **Finger/face coverage is confirmed only for Motion-X.** For AMASS, HumanML3D, KIT-ML, BABEL I found no statement (AMASS's SMPL+H tooling implies hands, HumanML3D's 22-joint representation implies no fingers, but neither is sourced).
- **FPS unknown** for AMASS, KIT-ML, and BABEL from the sources I reached.
- **Not researched at all** for lack of tool budget: 100STYLE's motion-category list, CMU Mocap's exact file counts and subject counts, Human3.6M registration process and size, InterHuman/InterGen (my targeted query returned nothing on it), Trinity, ZeroEGGS, and BEAT. These remain open.
- The CMU "public domain" claim appears in a blog but **conflicts with CMU's own terms** ("may not resell... even in converted form"); rely on the official page.
- Bandai Namco's CC-BY-NC-ND tag comes from a reporting site, not the publisher's own page.

---

## Key Question 4 — Motion style transfer: can you restyle a captured or generated motion?

### Takeaway
Yes. **SMooDi** (ECCV 2024, `neu-vi/SMooDi`) is the reference implementation — it takes a content *text*
prompt plus a style *motion* sequence and produces stylized motion, built on MLD and trained on 100STYLE
retargeted to the SMPL skeleton; the repo also publishes that retargeted 100STYLE data. A dense 2025–2026
follow-up line (StyleMotif, MulSMo, SMooGPT, ClusterStyle, LoRA-MDM, SASI, PersonaBooth) all benchmark
against it.

### Cited Findings
- **SMooDi** official repo `https://github.com/neu-vi/SMooDi`; paper ECCV 2024. It adapts a pretrained text-to-motion model, **MLD**, using 100STYLE; generates motion from a **content text** plus a **style motion sequence**; authors report it surpasses baselines at generating stylized motion — [SMooDi ECCV paper](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/00147.pdf); [GitHub](https://github.com/neu-vi/SMooDi)
- SMooDi repo hosts the **retargeted 100STYLE dataset mapped to the SMPL skeleton, on Google Drive**. Setup: download the 100STYLE files into the HumanML3D dataset folder; **indices larger than 030000 are 100STYLE data** — [GitHub](https://github.com/neu-vi/SMooDi)
- SMooDi preprocessing pipeline (reproducible for your own motions): retarget with **Rokoko** to the SMPL skeleton in **BVH**, extract joint positions with **CharacterAnimationTools**, then derive HumanML3D features using HumanML3D's motion-representation notebook — [GitHub](https://github.com/neu-vi/SMooDi)
- SMooDi release status (as of the snapshot seen): inference code, pretrained models, evaluation code and training code were all still listed as **pending** on the repo TODO. Dependencies include SMPL, SMPL-X, PyTorch3D, HumanML3D and 100STYLE, each with its own license to adhere to — [GitHub](https://github.com/neu-vi/SMooDi)
- **StyleMotif** — "Multi-Modal Motion Stylization using Style-Content Cross Fusion" (arXiv 2503.21775), uses 100STYLE, compares to SMooDi — [arXiv](https://arxiv.org/pdf/2503.21775)
- **MulSMo** — "Multimodal Stylized Motion Generation by Bidirectional Control Flow" (arXiv 2412.09901). Notes that 100STYLE motions are **retargeted to the HumanML3D (SMPL) skeleton before preprocessing** — [arXiv](https://arxiv.org/pdf/2412.09901)
- **SMooGPT** — "Stylized Motion Generation using Large Language Models" (arXiv 2509.04058) — [arXiv](https://arxiv.org/html/2509.04058v2)
- **ClusterStyle** — "Modeling Intra-Style Diversity with Prototypical Clustering for Stylized Motion Generation" (arXiv 2512.02453) — [arXiv](https://arxiv.org/pdf/2512.02453)
- **LoRA-MDM** (arXiv 2025, `github.com/haimsaw/LoRA-MDM`) — low-rank stylization of MDM — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- **SASI** (SIGGRAPH 2025) — "Semantically Consistent Text-to-Motion with Unsupervised Styles" — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- **PersonaBooth** (CVPR 2025, `github.com/Boeun-Kim/PersonaBooth`) with the **PerMo** dataset — motion personalization — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- **MotionReFit** (CVPR 2025, `github.com/emptybulebox1/motionRefit`) with the **STANCE** dataset — motion editing/refinement — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- **SimMotionEdit** (CVPR 2025, `github.com/lzhyu/SimMotionEdit`) and **SALAD** (CVPR 2025, `github.com/seokhyeonhong/salad`) — motion editing — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)
- A curated cross-domain style-transfer paper collection including 3D motion: `https://github.com/00lostin00/Awesome-Style-Transfer` — [GitHub](https://github.com/00lostin00/Awesome-Style-Transfer)
- 100STYLE's own paper frames the dataset as being for "practical use of AI-based motion **editing and stylization**" — [arXiv 2306.08861](https://arxiv.org/html/2306.08861)

### Inferences
- "Make a walk aggressive" is the canonical SMooDi/100STYLE use case and is well-supported: 100STYLE's styles are exactly this kind of locomotion-adverb space, retargeted to SMPL and indexed into HumanML3D-compatible features.
- "Make a punch heavier" is less well-supported: 100STYLE is predominantly locomotion-style data, and the style-transfer literature I found benchmarks on it almost exclusively. Restyling a combat motion would likely need PerMo/PersonaBooth-style personalization or SASI's unsupervised styles rather than 100STYLE.
- SMooDi's documented preprocessing chain (Rokoko retarget → BVH → CharacterAnimationTools → HumanML3D notebook) is the generic recipe for getting *any* captured or Kimodo-generated BVH into the style-transfer ecosystem. Since Kimodo exports BVH for SOMA models, Kimodo → SMooDi is a plausible end-to-end restyling path.
- SMooDi's all-pending TODO list is the main practical risk; the retargeted 100STYLE data it publishes may be the more dependable deliverable from that repo than the model itself.

### Gaps
- **Motion Puzzle was not researched** — I had no tool budget left for it and found no incidental coverage. Its repo, license and runnability are open questions.
- SMooDi's *current* TODO/release state is unverified; the snapshot I saw showed inference code, weights, eval and training all pending, which may well have changed.
- No VRAM, install commands, or inference-speed numbers for any style-transfer model.
- Which of StyleMotif / MulSMo / SMooGPT / ClusterStyle / SASI actually have **public code and weights** was not verified — only arXiv PDFs were confirmed.

---

## Key Question 5 — Audio/music-driven motion (brief)

### Takeaway
Thin coverage in my results: **AIST++** is confirmed only as a 1.4K-clip / 0.3M-frame subset inside
Motion-X, and the curated text-to-motion list contains essentially no 2025 music-to-dance entries. I did not
independently verify EDGE or Bailando.

### Cited Findings
- **AIST++** appears as a Motion-X subset: 1.4K clips / 0.3M frames, unified to 30 fps SMPL-X — [GitHub Motion-X](https://github.com/IDEA-Research/Motion-X)
- **Motion-X++** adds **audio** as a paired modality where the source video has audio — relevant if you want audio-conditioned motion training data — [GitHub Motion-X](https://github.com/IDEA-Research/Motion-X)
- The curated text-to-motion list's only clear music-to-dance entry is **TM2D** (ICCV 2023, "Bimodality Driven 3D Dance Generation via Music-Text Integration"); the list contains **no 2025 music-to-dance work** — [awesome-text-to-motion](https://github.com/Zilize/awesome-text-to-motion)

### Inferences
- Audio-driven motion looks like a separate ecosystem from the text-to-motion repos surveyed here; the text-to-motion curated list is not a good index for it. A hobbyist wanting dance would be better served going directly to the EDGE and Bailando repos and the AIST++ official site.

### Gaps
- **EDGE and Bailando were not researched** (repos, licenses, VRAM, runnability all unknown) — deliberately deprioritized as "brief" per the assignment, and I exhausted tool budget on the higher-priority questions.
- AIST++'s official standalone download, size, and license were not verified.
- Gesture datasets **Trinity, ZeroEGGS and BEAT** were not researched at all.

---

## Key Question 6 — Pre-made free motion libraries a hobbyist can just download

### Takeaway
Three solid free pillars: **CMU Graphics Lab** (2,500+ sequences, raw BVH, "free for all uses" but no
reselling even converted), **Truebones' Gumroad free packs** (including a free Unreal Engine mocap library in
FBX *and* BVH, and a cleaned-up converted CMU set), and **Bandai Namco Research's ~3,000 BVH moves**. Mixamo
remains the easiest FBX starting point. SFU Mocap adds martial arts. All of these are fine for this project.

### Cited Findings
- **CMU Graphics Lab Motion Capture Library** `https://mocap.cs.cmu.edu/`: official terms say the dataset "is free for all uses"; the licensing section adds the data "may be included in commercially-sold products, but you may not resell this data directly, even in converted form." Data is **raw BVH**, and the official site points to external sites hosting conversions. A secondary source counts **over 2,500 motion sequences** covering walking, running, sports, martial arts and gestures — [CMU Mocap](https://mocap.cs.cmu.edu/); [MocapOnline BVH guide](https://mocaponline.com/blogs/mocap-news/bvh-animation-guide)
- **CMU in BVH on GitHub**: `https://github.com/una-dinosauria/cmu-mocap` — "The CMU mocap dataset in bvh format" — [GitHub](https://github.com/una-dinosauria/cmu-mocap)
- **Truebones (Gumroad)**: a **free Unreal Engine complete mocap library in FBX**; a separate free listing is a **fixed/converted CMU database with BVH for iClone, MotionBuilder and Maya**, free with the checkout code `truebonesfree`; a "500 free BVH and FBX mocap pak" is also promoted. One aggregator characterizes Truebones as a small library with limited coverage and variable quality — [Truebones free UE library](https://truebones.gumroad.com/p/new-free-unreal-engine-complete-mocap-library-in-fbx-format-only-from-truebones); [Truebones CMU conversion](https://truebones.gumroad.com/p/reminder-free-mixamo-conversion-to-i-clone-includes-bvh-for-3ds-max-only-from-truebones); [Truebones store](https://truebones.gumroad.com/l/eZRYS); [500 free pak video](https://www.youtube.com/watch?v=hHgmu0TzO5I)
- **Mixamo**: web-based library exporting **FBX**, generally treated as the easiest starting point; **not** a BVH source, so BVH conversion is a separate step. Terms are Adobe's — [MocapOnline free FBX guide](https://mocaponline.com/blogs/mocap-news/free-fbx-animations); [Mixamo alternatives 2026](https://mocaponline.com/blogs/mocap-news/mixamo-alternatives)
- **Bandai Namco Research**: ~**3,000 free mocap moves in BVH** (walking, running, fighting, dancing), reported as CC-BY-NC-ND — [CGChannel](https://www.cgchannel.com/2022/05/download-3000-free-mocap-moves-from-bandai-namco-research/)
- **SFU Motion Capture Database** `https://mocap.cs.sfu.ca/`: free for research purposes, no commercial products or resale; **BVH among other formats**; includes Kendo and Wushu kicking — [SFU Mocap](https://mocap.cs.sfu.ca/)
- **Rokoko**: 6 free martial arts animations in FBX, usable in any project — [Rokoko](https://www.rokoko.com/resources/rokoko-mocap-6-free-martial-arts-animations)
- **motioncapturedata.com**: free BVH categories including Mocap Boxing (NaturalPoint sample) and fighting (karate, kicking) — [Mocap Boxing](https://motioncapturedata.com/category/mocap-boxing/); [fighting](https://motioncapturedata.com/category/fighting/)
- A Blender-oriented educator page aggregating mocap sources — [The Educator: Motion Capture](https://sites.google.com/view/nward/blender-3d/motion-capture)
- **Practical warning:** sources describe CMU BVH files as needing cleanup — foot-plant fixes and loop trimming — before production use — [MocapOnline](https://mocaponline.com/blogs/mocap-news/free-motion-capture-animations)

### Inferences
- For a hobbyist the fastest path to a usable animation set is Mixamo FBX for the common verbs, Truebones' free converted-CMU BVH for breadth without CMU's cleanup pain, and SFU/Bandai/Rokoko for combat — then Kimodo for anything the libraries lack, with BVH export landing in the same pipeline as the library files.
- Truebones' *cleaned* CMU conversion is specifically valuable because it addresses the documented CMU cleanup burden (foot plants, loop trimming) that otherwise makes raw CMU BVH slow to use.
- MocapOnline is a paid-pack vendor, so its library rankings and "best alternatives" framing are marketing-influenced — useful as a pointer list, not as an evaluation.

### Gaps
- **ACCAD, Eyes Japan (Eyes, JAPAN Co. Ltd.), Human3.6M, and the Unity / Unreal free animation packs were not researched** — no tool budget remained. ACCAD and Eyes Japan are known AMASS constituents, but I have no sourced detail on their standalone download pages, sizes, or formats.
- Mixamo's **full catalogue size** (number of animations) and its current Adobe license text were not verified.
- Truebones' free-pack contents, exact animation counts, and current availability of the `truebonesfree` code were not verified against the store itself.
- Licensing on motioncapturedata.com's free BVH categories is not stated anywhere I could find.
- I found no sourced "GitHub collections of converted FBX mocap" beyond `una-dinosauria/cmu-mocap` (which is BVH, not FBX).

---

## Research-process notes for the report writer

- **Tool budget was exceeded** on Kimodo (the assignment flagged it as the centerpiece and requested exact
  install/control-mode detail), which came at the cost of per-repo verification for the older model set
  (MDM, MotionDiffuse, T2M-GPT, MotionGPT, OmniControl, MotionLCM, StableMoFusion, Large Motion Model) and
  for several datasets (Human3.6M, InterGen, Trinity, ZeroEGGS, BEAT) and Motion Puzzle. Those are the
  clearest candidates for a follow-up pass.
- **GitHub API was unavailable** to this session (`gh` returned HTTP 403: "GitHub access to this repository
  is not enabled for this session"), so last-commit dates and open-issue contents could not be checked
  programmatically. All repo-activity statements come from rendered GitHub pages via WebFetch.
- **Two internal contradictions worth flagging in the report:** (1) MotionMillion's repo says the dataset is
  released *and* says dataset preparation is "Comming Soon!"; (2) ViMoGen's repo says weights were released
  in Dec 2025 *and* lists weights as pending. (3) Kimodo's CLI docs give `--model` default as
  `Kimodo-SOMA-RP-v1` while also stating the latest SOMA-RP (v1.2) is used if omitted.
- **Unverified claim to not propagate:** MotionMillion being 23.7% martial arts. Nothing in the paper page,
  project page, or repo README I fetched supports it.
- **Unverified claim to attribute carefully:** Kimodo at "2–5 s per prompt on an RTX 3090" — third-party
  blogs only; NVIDIA's own docs publish no timing.
