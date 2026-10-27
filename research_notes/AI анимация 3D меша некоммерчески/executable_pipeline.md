# Executable Pipeline: Arbitrary 3D Mesh → Full Skeletal Animation Set, Fully Local (state as of 2026-10-27)

Scope note for the report writer: everything below is sourced. Where I could not verify something
I have put it in **Gaps** rather than guessing. Several items the assignment asked about
(`vid2anim`, `Motion2MixamoRig`) appear not to exist as named; see the first section.

---

## Q1. Known complete open-source end-to-end pipelines: exact repos, versions, install steps, outputs, open issues

### Takeaway
Exactly one of the named projects is a genuinely complete, documented, local, end-to-end
video→rigged-animation pipeline with a real install sheet: **`squall01337/mixamo-llm-mocap`**
(GVHMR → SMPL-X → Mixamo rig → Blender, Windows-proven, Blender 5.1+, Python 3.10, CUDA 12.1,
~8 GB VRAM, ~5.3 GB of checkpoints). The two ComfyUI nodes are thinner wrappers around the same
GVHMR core; `bedlam2_retargeting` is an **Unreal-side batch retargeter**, not a capture pipeline;
`vid2anim` and `Motion2MixamoRig` could not be found at all. Every path in this family depends on
registration-walled SMPL/SMPL-X body models that cannot be scripted.

### Cited Findings

**Repo-name verification**
- `squall01337/mixamo-llm-mocap` is real and resolves: README, `docs/INSTALL.md` and `docs/PITFALLS.md` all fetch from `raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/` — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md); it is also mirrored as an agent "skill" at [skillsllm.com](https://skillsllm.com/skill/mixamo-llm-mocap). Star counts in secondary sources conflict (204 stars/44 forks on one blog vs 53 on a skills directory), so do not quote popularity — [blog](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation/); contradicted by [skillsllm](https://skillsllm.com/skill/mixamo-llm-mocap).
- **"Motion2MixamoRig": no such repository found.** A GitHub repository search and web search surfaced nothing by that name. The plausible real referents are `mixamo-llm-mocap`'s own `pipeline/lift_to_mixamo.py` stage and `ComfyUI-Video2MotionCapture`'s `RetargetSMPLToMixamo` node — [ComfyUI-Video2MotionCapture node list](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture). Treat the name as garbled.
- **"vid2anim": no GitHub repo found.** A targeted search returned only `video2chars` (ASCII-art video converter, PyPI), AnimateAnyone, and hosted Wan-2.2-Animate — all 2D video-to-video, none producing a skeletal animation — [search result set](https://pypi.org/project/video2chars), [AnimateAnyone](https://github.com/humanaigc/animateanyone).

**`squall01337/mixamo-llm-mocap` — the real build sheet**
- Prerequisites verbatim: **Python 3.10** for the GVHMR venv (created with `uv`); **CUDA 12.1 wheels (cu121) with torch 2.3.0** for pytorch3d; NVIDIA GPU **~8 GB VRAM minimum** (author used RTX 4080 16 GB); **Blender 5.1 or newer** (developed on 5.1.2); plus `uv`, `git`, `ffmpeg` — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- GVHMR install, verbatim — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md):
  ```
  git clone https://github.com/zju3dv/GVHMR tools/GVHMR
  uv python install 3.10
  uv venv --python 3.10 tools/GVHMR/.venv
  uv pip install --python tools/GVHMR/.venv/Scripts/python.exe -r docs/requirements_gvhmr_windows.txt
  uv pip install --python tools/GVHMR/.venv/Scripts/python.exe --no-deps -e tools/GVHMR
  uv pip install --python tools/GVHMR/.venv/Scripts/python.exe yacs
  ```
- pytorch3d is installed from a prebuilt third-party wheel because it will not build: `uv pip install --python tools/GVHMR/.venv/Scripts/python.exe --no-deps "https://github.com/MiroPsota/torch_packages_builder/releases/download/pytorch3d-0.7.6/pytorch3d-0.7.6%2Bpt2.3.0cu121-cp310-cp310-win_amd64.whl"` — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- MediaPipe goes in **any** Python, not the GVHMR venv: `pip install mediapipe opencv-python` — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- Checkpoints total **~5.3 GB**, pulled from a HuggingFace mirror because "Google Drive blocks scripted downloads" — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md):
  ```
  BASE=https://huggingface.co/camenduru/GVHMR/resolve/main
  CK=tools/GVHMR/inputs/checkpoints
  curl -L $BASE/gvhmr/gvhmr_siga24_release.ckpt   -o $CK/gvhmr/gvhmr_siga24_release.ckpt --create-dirs
  curl -L $BASE/yolo/yolov8x.pt                   -o $CK/yolo/yolov8x.pt --create-dirs
  curl -L $BASE/vitpose/vitpose-h-multi-coco.pth  -o $CK/vitpose/vitpose-h-multi-coco.pth --create-dirs
  curl -L "$BASE/hmr2/epoch%3D10-step%3D25000.ckpt" -o "$CK/hmr2/epoch=10-step=25000.ckpt" --create-dirs
  ```
  `dpvo.pth` is only needed for moving cameras and can be skipped for locked plates.
- **SMPL-X is the hard gate**: free registration at https://smpl-x.is.tue.mpg.de/, download SMPL-X v1.1 (NPZ+PKL), copy `SMPLX_NEUTRAL.npz` to `tools/GVHMR/inputs/checkpoints/body_models/smplx/`. "GVHMR asserts on this file at startup." — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- MediaPipe models (`pose_landmarker_heavy.task`, `hand_landmarker.task`) self-download to `tools/models/` on first use — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- Blender add-on setup: **Edit → Preferences → System → Network → Allow Online Access must be turned on — it is off by default and the MCP server will not start without it**; add-on from https://www.blender.org/lab/mcp-server/; server autostarts ~1 s after launch on `localhost:9876` — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- MCP registration command, with a mandatory pin: `claude mcp add blender -s user -- uvx --from "git+https://projects.blender.org/lab/blender_mcp.git#subdirectory=mcp" --with "mcp[cli]<2" blender-mcp`. **`mcp[cli]<2` is required because the 2.x SDK removed `mcp.server.fastmcp`.** The MCP route can be skipped entirely via `pipeline\blender_exec.py <script.py> [timeout]` — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- Rig prep is already headless: `"C:\Program Files\Blender Foundation\Blender 5.1\blender.exe" --background --python pipeline\setup_rig.py -- --fbx ybot.fbx --out ybot_rest.blend`. Set `GVHMR_ROOT` if GVHMR lives elsewhere — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- The **SMPL-X path (default, 5 stages)**: `estimate_pose_gvhmr.py --video … --out … --smplx-only` (GVHMR→SMPL-X params, refined on the plate's own 2D keypoints, heels/toes, zoomed hands) → `pipeline\retarget_smplx.py --spec action_specs\<name>.json` (rotations onto the Mixamo rig, floor, foot locks, camera-true trajectory) → `bl_motion.py` run under `blender -b` with `apply --motion … --save` → `pipeline\render_review.py --spec … --clip …` (2×2 review video) → `pipeline\eval_fidelity.py --spec … --clip …` (limb/head angles vs keypoints, foot slide, jitter) — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- The **landmark path (legacy, still supported)** runs through the GVHMR venv interpreter explicitly: `tools\GVHMR\.venv\Scripts\python.exe pipeline\analyze_landmarks.py --landmarks plates\<name>\landmarks.json`, then `… pipeline\lift_to_mixamo.py --spec action_specs\<name>.json`, then `python pipeline\run_in_blender.py all action_specs\<name>.json`, then `qa_clip.py`, `compare_reference.py`, `render_preview.py … --showcase`. This path **requires** the Blender MCP add-on; the SMPL-X path does not — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- Formats at each hop: input `.mp4` **locked-camera** plate; rig source = Mixamo character downloaded as **FBX Binary, T-pose**; action spec = JSON with required fields `name`, `action_name`, `clip_dir`, `landmarks`, fps (optional `fists`, rest blends, `arm_overrides`); intermediates `landmarks.json`, `rig_profile.json`, `motion.npz`; scene `.blend` (e.g. `ybot_rest.blend`); outputs `preview.mp4`, `showcase.mp4`. **Motion is 30 fps quaternion FK with hips as the only translating bone.** — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- Stated limitations: GVHMR "flattens fast limbs" (hence the SMPLify-style refit); GVHMR has **no fingers**, so hands are cropped and read separately; the plate must have a locked camera; Mixamo rigs are **FK-only so hip height is solved rather than using IK**; GVHMR itself is not vendored — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- Directory layout the scripts assume (`ybot.fbx`, `ybot_rest.blend`, `rig_profile.json` at repo root; `pipeline/`, `docs/`, `tools/GVHMR/.venv`, `tools/GVHMR/inputs/checkpoints/{gvhmr,yolo,vitpose,hmr2,body_models/smplx}`, `tools/models/`) — [docs/INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).

**`PozzettiAndrea/ComfyUI-MotionCapture`**
- Manual install (README calls it "most reliable") — [repo](https://github.com/PozzettiAndrea/ComfyUI-MotionCapture):
  ```
  cd ComfyUI/custom_nodes
  git clone https://github.com/PozzettiAndrea/ComfyUI-MotionCapture.git
  cd ComfyUI-MotionCapture
  pip install -r requirements.txt --upgrade
  python install.py
  ```
  Manager alternative: search `MotionCapture`, install the highest version, "if that doesn't work, try nightly"; or Install-via-Git-URL with the clone URL.
- It "uses experimental package `comfy-env` to attempt a one click isolated install," which downloads and uses the **pixi** package manager — a known source of install breakage — [repo](https://github.com/PozzettiAndrea/ComfyUI-MotionCapture).
- It is GVHMR-based and lists ViTPose, HMR2 (4D-Humans), SMPL and SMPL-X as components. Sample filenames `smpl_30fps.mp4` and `smpl_to_bvh_30fps.mp4` imply MP4 + **BVH** output, but the README does not state formats explicitly. Repo shows **16 open issues / 3 open PRs**. No Python version, CUDA version or VRAM figure is published — [repo](https://github.com/PozzettiAndrea/ComfyUI-MotionCapture).

**`AKASubaz/ComfyUI-Video2MotionCapture`**
- Clone into `ComfyUI/custom_nodes/`, `pip install -r requirements.txt` (ComfyUI Portable: use the `python_embeded` env or the Manager), then `python install.py` to download models. **The README's own `cd` example says "ComfyUI-MotionCapture" but the folder is "ComfyUI-Video2MotionCapture" — a copy/paste bug that will break a literal follow-along** — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- Stated deps: **Python 3.10+**, **Blender 4.0+** recommended for retargeting, used via **`bpy` as a pip wheel** — "usually automatic on Windows; on Linux/Mac you may need a system Blender install or a `bpy` build." Person tracking uses **SAM3** segmentation, with no YOLO dependency — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- `install.py` downloads **~3.5 GB** to `ComfyUI/models/motion_capture`; components are GVHMR, ViTPose, SMPL body models, HMR2 checkpoints — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- Node names: `LoadGVHMRModels`, `LoadMotionData`, `GVHMRInference`, `SaveSMPLAsRiggedFBX`, `RetargetSMPLToMixamo`, `RetargetsMixamoToUEMannequin`, `SaveSMPLAsMesh`. Outputs: **rigged FBX with automatic axis-orientation fixes**; retargeting to Mixamo, UE Mannequin or custom rigs; **OBJ mesh sequence, one file per frame**; `.npz` intermediates. BVH is not mentioned — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- Documented errors + fixes: **`RuntimeError: Operator bpy.ops...`** → needs a valid display or a correct `bpy` install (nodes now use "context-safe selection" to avoid it); **"Duplicate Bone Mapping"** → the retarget node handles duplicate bone names automatically, otherwise check target rig hierarchy. Marked **WIP**, 6 open issues. README says MIT while the repo page shows GPL-3.0 (ignore per constraints, but note the inconsistency as a sign of churn) — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).

**`PerceivingSystems/bedlam2_retargeting` — not a capture tool; an Unreal batch retargeter**
- Branch selection is literal: `git checkout 5.3  # or 5.4`. Requires **Unreal Engine 5.3 or 5.4** and **SMPL-X Blender add-on build 20241129 or later**. Needs the UE **Python plugin, Python Foundation Packages (numpy, for `.npz`), and Python Remote Execution** plugins. Config lives in `paths.json`. Driven from `Widgets/HumanEngineWidget` via "Run Editor Utility Widget" — [README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- Reads `.npz` (SMPL-X params), `.fbx` (source animations and target bodies), `.csv` (source/target pairs), `.uasset`. Writes `.uasset` (imported + retargeted Animation Sequences), `.fbx` (with or without mesh), `.npz` (with or without betas), and floor-adjusted `.npz` into `{input_dir}_floor_adjusted` — [README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- Steps: (1) `make_fbx_files.py` converts `.npz`→`.fbx` (use `--tpose` for bodies), `make_csv_file.py` builds the pairs CSV, files organised into `animations/` and `bodies/`; (2) `import_batch.py --animation` or the widget's Import; (3) `retarget_batch.py --pool_dir … --csv_path_retargeting … --num_batches … --processes …`, output to `{working_dir}/retargeting/`; (4) export Animation Sequences as `.fbx` and/or `.npz` from the widget; (5) `adjust_floor_npz.py --input_dir <input_npz_dir>`, which needs `SMPLX_NEUTRAL.npz` in a `body_models` directory and specifically the **head-bun-removed** SMPL-X models — [README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- Three hard rules that double as the known failure modes: **keep IK disabled in the IK Retargeter (FK only)**; **all animations and skeletal meshes must sit at floor level**; a reference **SMPL-X IK Rig** is provided (credited to Joachim Tesch) and chain names must be mapped manually in the IK Retargeter when source and target chain names differ. No joint-level mapping table is published — [README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).

**SMPL→FBX converters and the SMPL/SMPL-X Blender add-ons (Meshcapade-era tooling)**
- **`Meshcapade/SMPL_blender_addon` is discontinued**; the page points to the successor `smplx_blender_addon` on MPI's GitLab. It imports `.npz` (shape + motion, e.g. AMASS) for licensed users, with an up-axis choice in the import dialog; it imports `.fbx` but imported files need a **"Fix Blendshape Ranges"** click because **Blender up to and including LTS 3.6.1 clamps imported FBX blendshapes to 0 and 1**. It documents **no `.npz`/`.fbx` export** — only "Write Pose" to console or `.json` in Rodrigues notation. **No headless/Python API is documented; it is a GUI add-on.** Pose correctives are not recalculated automatically — you must click the update button after pose changes — [Meshcapade/SMPL_blender_addon](https://github.com/Meshcapade/SMPL_blender_addon).
- The live successor is **`gitlab.tuebingen.mpg.de/jtesch/smplx_blender_addon`**; its README states **Blender 3.6+, tested with 4.2.3**, and its changelog mentions an option to **import animation onto a grounded rest-pose armature** — [GitLab commit](https://gitlab.tuebingen.mpg.de/jtesch/smplx_blender_addon/-/commit/4f5d7a52a14e79d6a10af51e7f58b4c559fa7672), [MPI project page](https://ps.is.mpg.de/code/smpl-x-for-blender-and-unity).
- A scriptable AMASS-`.npz`→Blender route that is *not* the add-on's import button: load the `.npz` with NumPy, then `import_smplx("neutral")` followed by `set_amass_animation(data)` — [GenMotion docs](https://genmotion.readthedocs.io/en/latest/tutorial/render/blender/AMASS.html).
- **No dedicated, maintained SMPL-npz→FBX repo surfaced.** Closest working routes: (a) the Blender script posted in **ROMP issue #193**, which builds a keyframed skinned SMPL mesh and exports FBX or glTF — but takes `.pkl` not `.npz`, supports only male/female, and runs from CLI or the Blender text editor — [ROMP#193](https://github.com/Arthur151/ROMP/issues/193); (b) **`SlimeVRX/smpl2bvh`**, which converts SMPL motion to **BVH** from `.npz` input that must contain `rotations` and `trans` keys, then import the BVH into Blender and export FBX — [smpl2bvh](https://github.com/SlimeVRX/smpl2bvh).

**Auto-rigging the unrigged mesh (the step before any of the above, if your mesh has no skeleton)**
- **`VAST-AI-Research/UniRig`** (SIGGRAPH'25, Tsinghua + Tripo) is the current open-source answer. Install verbatim — [README](https://raw.githubusercontent.com/VAST-AI-Research/UniRig/main/README.md):
  ```bash
  conda create -n UniRig python=3.11
  conda activate UniRig
  git clone https://github.com/VAST-AI-Research/UniRig
  cd UniRig
  python -m pip install torch torchvision
  python -m pip install -r requirements.txt
  python -m pip install spconv-{you-cuda-version}
  python -m pip install torch_scatter torch_cluster -f https://data.pyg.org/whl/torch-{your-torch-version}+{your-cuda-version}.html --no-cache-dir
  python -m pip install numpy==1.26.4
  ```
  **Python 3.11**, PyTorch "tested with version >=2.3.1", **≥8 GB VRAM for inference**; checkpoint auto-downloads from HuggingFace `VAST-AI/UniRig`. The `numpy==1.26.4` pin at the end is load-bearing.
- Run commands — [README](https://raw.githubusercontent.com/VAST-AI-Research/UniRig/main/README.md):
  ```bash
  bash launch/inference/generate_skeleton.sh --input examples/giraffe.glb --output results/giraffe_skeleton.fbx
  bash launch/inference/generate_skin.sh     --input examples/skeleton/giraffe.fbx --output results/giraffe_skin.fbx
  bash launch/inference/merge.sh --source results/giraffe_skin.fbx --target examples/giraffe.glb --output results/giraffe_rigged.glb
  ```
  `--input_dir`/`--output_dir` for batches; `--seed 42` for reproducibility. Inputs `.obj`, `.fbx`, `.glb`, `.vrm`; outputs FBX from the two prediction stages and `.glb` from merge. Optional VRM add-on: `python -c "import bpy, os; bpy.ops.preferences.addon_install(filepath=os.path.abspath('blender/add-on-vrm-v2.20.77_modified.zip'))"`.
- UniRig's own listed breakages — [README](https://raw.githubusercontent.com/VAST-AI-Research/UniRig/main/README.md): "**there is a high chance that you will encounter flash_attn installation error**" (defers to the flash-attention repo's guide); skinning "may degrade significantly if the skeleton is inaccurate" (missing tail/wing bones) so refine the skeleton first; **merging `*_skeleton.fbx` produces no skinning — you must merge the `*_skin.fbx`**; switch the voxel skin backend to `open3d` to dodge pyrender issues; bone-attribute prediction is still "Coming Soon"; the released checkpoint is trained on Articulation-XL2.0, not the full Rig-XL/VRoid set; training code "may be a bit messed up." The README names **SkinTokens** as UniRig's successor.
- `ComfyUI-UniRig` wraps the same workflow as ComfyUI nodes — [RunComfy node page](https://www.runcomfy.com/comfyui-nodes/ComfyUI-UniRig/uni-rig-auto-rig), [ComfyUI-Wiki writeup](https://comfyui-wiki.com/en/news/2025-04-12-unirig-one-model-to-rig-them-all).

### Inferences
- The two ComfyUI nodes and `mixamo-llm-mocap` are three front-ends over the **same GVHMR + ViTPose + HMR2 + SMPL/SMPL-X stack**, so their VRAM floor (~8 GB) and checkpoint set are effectively shared; debugging knowledge transfers between them.
- `mixamo-llm-mocap` is the only one of the three that publishes Python/CUDA/VRAM/Blender numbers and a pitfalls document, which makes it the right spine for a build sheet even if you only borrow its install steps and run the retarget elsewhere.
- Because `bedlam2_retargeting` consumes `.npz`/`.fbx` and emits `.fbx`/`.npz`, it slots in **after** any SMPL-X-producing capture step as the "retarget to a game skeleton in bulk" stage — provided you have UE 5.3/5.4 and are willing to run the editor. It is not an alternative to the capture tools.
- `ComfyUI-Video2MotionCapture`'s `bpy`-as-pip-wheel dependency is the single biggest Linux risk in that path; the documented `RuntimeError: Operator bpy.ops...` is the symptom. On Linux prefer a system Blender driven by `--background --python`, which is what `mixamo-llm-mocap` does.

### Gaps
- **`vid2anim`**: no repository of that name found. Either it does not exist, is private, or the name is garbled. Report as unverifiable.
- **`Motion2MixamoRig`**: no repository found; almost certainly a garbled reference (see above).
- `requirements.txt` contents, Python version, CUDA version and VRAM for `ComfyUI-MotionCapture` are not published on its README; I could not read the file itself.
- Individual open-issue titles/bodies for both ComfyUI repos (16 and 6 open issues respectively) were not retrievable through the tooling available here — the GitHub MCP server in this session is scoped to a single unrelated repository, and the rendered repo pages do not include issue text.
- `docs/PIPELINE.md` and `docs/requirements_gvhmr_windows.txt` in `mixamo-llm-mocap` (the actual pinned dependency list) were not fetched.
- No **Linux** install recipe for `mixamo-llm-mocap`: every command in `INSTALL.md` is Windows (`\` paths, `.venv/Scripts/python.exe`, a `win_amd64` pytorch3d wheel). The README calls the path "Windows-proven."

---

## Q2. Blender headless/scripting: rigging, retargeting, baking, FBX/GLB export with no GUI

### Takeaway
Headless Blender is the only reliable glue in this pipeline, and the two API calls that matter are
`bpy.ops.nla.bake(visual_keying=True, clear_constraints=True, …)` to turn constraint-driven
retargeting into real keyframes, and `bpy.ops.export_scene.fbx(…)` with the armature/scale flags set
deliberately. Add-on Blender-version compatibility is the dominant breakage: the live SMPL-X add-on
is validated to 4.2.3, `mixamo-llm-mocap` demands **Blender 5.1+**, `ComfyUI-Video2MotionCapture`
wants 4.0+, and the popular Mixamo root-motion add-ons are stuck at 2.8x–4.2 and are reported broken
above 5.0.

### Cited Findings

**Headless invocation pattern (verified in the wild)**
- `blender --background --python <script.py> -- --arg value` is the working pattern, e.g. `"C:\Program Files\Blender Foundation\Blender 5.1\blender.exe" --background --python pipeline\setup_rig.py -- --fbx ybot.fbx --out ybot_rest.blend` — [mixamo-llm-mocap INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- The same repo ships `pipeline\blender_exec.py <script.py> [timeout]` as a generic "run this script in Blender" shim, explicitly offered as the alternative to the MCP add-on — [INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- `bl_motion.py` is run as `blender -b` with subcommand `apply`, `--motion <motion.npz>` and `--save`, i.e. the repo's own "bake and write the .blend" stage — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).

**Baking constraints to keyframes — `bpy.ops.nla.bake`**
- `visual_keying` = "Keyframe from the final transformations (with constraints applied)"; it first appears in the 2.69 API and is present through 4.x/5.x — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html), [4.5](https://docs.blender.org/api/4.5/bpy.ops.nla.html).
- `clear_constraints` removes constraints from the keyed bones/objects, and the docs state **"To get a correct bake with this setting Visual Keying should be enabled."** In the 5.3 docs the label reads **"Clear Local Constraints."** — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html).
- `only_selected` limits keying to selected bones; `use_current_action` = "Overwrite Current Action, Bake animation into current action, instead of creating a new one (useful for baking only part of bones in an armature)" — available in 4.x and 5.x — [bpy.ops.nla 4.4](https://docs.blender.org/api/4.4/bpy.ops.nla.html), [5.0](https://docs.blender.org/api/5.0/bpy.ops.nla.html).
- `clear_parents` exists from 2.67 on (bakes object animation before clearing parents); `clean_curves` and `channel_types` appear only in 4.x and later — [bpy.ops.nla docs across versions](https://docs.blender.org/api/5.0/bpy.ops.nla.html).
- **API-shape breakage**: in Blender 5.x/5.3 the arguments are **keyword-only** — `bpy.ops.nla.bake(*, frame_start=…, visual_keying=True, clear_constraints=True)`. Older versions accept them positionally. A script written for 3.x can therefore raise a TypeError on 5.x — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html).
- The accepted retarget-bake idiom is `visual_keying=True` together with `clear_constraints=True` over an explicit frame range — [bpy.ops.nla docs](https://docs.blender.org/api/5.3/bpy.ops.nla.html).

**FBX export: full parameter list with defaults (current Blender API)**
From [bpy.ops.export_scene docs](https://docs.blender.org/api/current/bpy.ops.export_scene.html):
```
filepath=''                         check_existing=True             filter_glob='*.fbx'
use_selection=False                 use_visible=False               use_active_collection=False
collection=''                       global_scale=1.0 (0.001..1000)  apply_unit_scale=True
apply_scale_options='FBX_SCALE_NONE'                                use_space_transform=True
bake_space_transform=False
object_types={'ARMATURE','CAMERA','EMPTY','LIGHT','MESH','OTHER'}
use_mesh_modifiers=True             use_mesh_modifiers_render=True  mesh_smooth_type='OFF'
colors_type='SRGB'                  prioritize_active_color=False   use_subsurf=False
use_mesh_edges=False                use_tspace=False                use_triangles=False
use_custom_props=False
add_leaf_bones=True                 primary_bone_axis='Y'           secondary_bone_axis='X'
use_armature_deform_only=False      armature_nodetype='NULL'
bake_anim=True                      bake_anim_use_all_bones=True    bake_anim_use_nla_strips=True
bake_anim_use_all_actions=True      bake_anim_force_startend_keying=True
bake_anim_step=1.0                  bake_anim_simplify_factor=1.0
path_mode='AUTO'                    embed_textures=False            batch_mode='OFF'
use_batch_own_dir=True              use_metadata=True
axis_forward='-Z'                   axis_up='Y'
```
`apply_scale_options` enum: `FBX_SCALE_NONE`, `FBX_SCALE_UNITS`, `FBX_SCALE_CUSTOM`, `FBX_SCALE_ALL`.
`object_types` enum: `EMPTY`, `CAMERA`, `LIGHT`, `ARMATURE`, `MESH`, `OTHER`.
- **Multiple animations in one FBX** is exactly `bake_anim_use_all_actions=True` (default True) plus `bake_anim_use_nla_strips`; this is the knob that decides whether you get one clip or the whole library per file — [bpy.ops.export_scene docs](https://docs.blender.org/api/current/bpy.ops.export_scene.html).
- **Engine-accepted settings, as practitioners state them**: default bone axes are **Primary Y / Secondary X**, and changing them "is the usual cause of 'my bones import rotated 90 degrees'"; keep **Forward `-Z`, Up `Y`** — [Blender→Unreal export checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist).
- For Unreal scale, there are two mutually exclusive strategies and mixing them is the bug: either set **Scene Properties → Units → Unit Scale = 0.01** (and Length = Centimeters) and model at Unreal size, **or** keep Unit Scale 1.0 and export with **Apply Scalings = `FBX_SCALE_ALL`** so the file header carries the conversion and Unreal's Convert Scene handles it — [Blender→Unreal export checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist), [UE forum](https://answers.unrealengine.com/questions/617946/view.html).

**Add-on / Blender version compatibility matrix (the breakage table)**
- `mixamo-llm-mocap`: **Blender 5.1+ required**, developed on 5.1.2 — [INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- `smplx_blender_addon` (MPI GitLab, the live one): **Blender 3.6+, tested with 4.2.3** — [GitLab](https://gitlab.tuebingen.mpg.de/jtesch/smplx_blender_addon/-/commit/4f5d7a52a14e79d6a10af51e7f58b4c559fa7672).
- `Meshcapade/SMPL_blender_addon`: **discontinued**; documents a blendshape-clamping bug in "Blender up to and including LTS 3.6.1" — [repo](https://github.com/Meshcapade/SMPL_blender_addon).
- `bedlam2_retargeting`: needs **SMPL-X Blender add-on build 20241129 or later**; does not state a Blender version — [README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- `ComfyUI-Video2MotionCapture`: **Blender 4.0+** recommended, via pip `bpy` — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- `enziop/mixamo_converter` (adds a root bone to Mixamo animations, built for UE4): needs **Blender 2.80+**, but a user reports "using a blender version thats too new with that particular plugin will cause the models and animations to be messed up," fixed by reverting to **Blender 2.8.3 LTS** — [repo](https://github.com/enziop/mixamo_converter), [Adobe community thread](https://community.adobe.com/t5/mixamo-discussions/animations-are-off-on-uploaded-mixamo-model-that-has-been-modified/td-p/13872592).
- The **"Import Mixamo - Root Motion"** Blender extension is listed as supported on **4.2 LTS and unsupported above 5.0** — [extension versions page](https://extensions.blender.org/add-ons/import-mixamo-root-motion/versions/), [guide](https://app.cinevva.com/de/guides/mixamo-to-blender-2026).
- `RichardPerry/Mixamo-Root` "failed on several Blender versions"; a 2026 Godot forum thread is still asking "What is currently the most reliable addon or method in 2026" — [Godot forum](https://forum.godotengine.org/t/blender-addon-for-mixamo-root-motion-in-blender-4-5-godot-4-5/135760).
- `Rokoko/rokoko-studio-live-blender` (the usual free GUI retargeter) states only **Blender 2.80 or higher** and makes **no claim about 4.5 or 5.x**; its install is a GUI zip-and-enable flow — [GitHub mirror](https://github.com/Atlas-3D-Artist/rokoko-studio-live-blender), [Rokoko support](https://support.rokoko.com/hc/en-us/articles/4410463492241-Install-the-Blender-plugin).

**Headless gotchas documented by a real pipeline**
From [`docs/PITFALLS.md`](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md) (67 numbered items; paraphrased in the fetch, not verbatim):
- Reading `bone.head` on a posed rig gives wrong values — **read the evaluated world matrix after a depsgraph update**.
- **Stale location keys** from failed passes persist; rebuild the action and key zero locations.
- World-space aiming must be done in **armature centimetres**, because the armature's scale is 0.01 — otherwise hands end up ~10 m high.
- **Screenshots come out black** when windows are occluded; render stills through a temporary camera instead.
- A long apply **freezes the UI for minutes** — this is normal, results are written to disk.
- The scene holds only the **last applied action**, so bind the intended action before rendering a preview.
- `mcp` SDK 2.x breaks the Blender MCP server → pin `mcp[cli]<2`.

### Inferences
- A safe headless retarget script shape is: import target FBX → import/construct source armature → add Copy Rotation (+ a Copy Location on the hips only) constraints per mapped bone pair → set the frame range → `bpy.ops.nla.bake(frame_start=…, frame_end=…, only_selected=True, visual_keying=True, clear_constraints=True, bake_types={'POSE'})` → rename the action → `bpy.ops.export_scene.fbx(...)`. This is directly supported by the documented semantics of `visual_keying`/`clear_constraints`, and matches what `mixamo-llm-mocap`'s constraint-free FK output implies, but I did not find a published canonical script to cite.
- Because the live SMPL-X add-on is only validated to **4.2.3** while `mixamo-llm-mocap` requires **5.1+**, a single Blender install cannot cleanly serve both. The practical answer is two Blender installations invoked by absolute path from your driver scripts.
- The root-motion add-on ecosystem (`enziop/mixamo_converter`, `Mixamo-Root`, `Import Mixamo - Root Motion`) has **not** followed Blender past 4.2/5.0. Root motion extraction in 2026 is better written as your own ~40-line bpy script than taken as an add-on dependency.
- `use_armature_deform_only=True` and `add_leaf_bones=False` are the two export flags most often needed for engine import hygiene (no helper/IK bones, no `_end` leaf bones polluting the skeleton), though I found no primary doc page stating that recommendation — treat as practitioner lore, not sourced fact.

### Gaps
- **I could not retrieve the Blender *manual* page for the FBX exporter.** `docs.blender.org/manual/en/latest/addons/import_export/scene_fbx.html` returns **404** (the page moved in 4.x+), and both `…/manual/en/latest/files/import_export/fbx.html` and `…/manual/en/4.2/addons/import_export/scene_fbx.html` are too long for the fetch tool to reach the body (195k–211k chars, only the nav sidebar returned). The API defaults above are therefore authoritative for *values*, but the manual's prose descriptions of "Apply Scalings" / "Use Space Transform" / "FBXNode Type" are **not** sourced here.
- `bpy.ops.export_scene.gltf` parameters were not retrieved (the operator section sits beyond the fetch window of the same oversized page).
- No primary source found for the **current** Blender version number in October 2026. Indirect evidence: the API docs publish a `5.3` tree — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html) — and the manual served from `…/manual/en/latest/` self-identified as the **"Blender 5.2 LTS Manual"**. So 5.x is current and at least 5.2/5.3 exist; I cannot cite an exact latest release.
- No open-source, maintained, headless armature **retargeting** add-on with a documented CLI/Python entry point was found. Rokoko's is GUI-and-Blender-2.8x-era; Auto-Rig Pro's Remap is commercial and I found no CLI documentation for it. This is a genuine hole — see Q7 gaps.

---

## Q3. SMPL/SMPL-X → game rig conversion: hierarchy, joint names, mapping tables, rotation pitfalls

### Takeaway
The SMPL-X joint order is fully pinned and citable (indices 0–54, `pelvis` … `right_thumb3`, with the
first 22 being the body). What does **not** exist in public, as far as I can find, is a maintained
SMPL/SMPL-X→Mixamo name table — the Mixamo side of the mapping is well covered by several projects
and you must write the SMPL side yourself. For Unreal the mapping is not name-based at all: it is a
**chain** mapping in the IK Retargeter, plus `Set Pelvis` on both rigs.

### Cited Findings

**The canonical SMPL-X joint order, indices 0–54, verbatim** — [smplx/joint_names.py](https://huggingface.co/spaces/Yuliang/ECON/raw/25a71fd0c00b36c8c284e4d1a4ab9df5c290bb59/lib/smplx/joint_names.py) (MPG file, mirrored; same file also at [PSHuman copy](https://huggingface.co/spaces/Stylique/PSHuman/raw/main/lib/smplx/joint_names.py)):
```
 0 pelvis            1 left_hip          2 right_hip         3 spine1
 4 left_knee         5 right_knee        6 spine2            7 left_ankle
 8 right_ankle       9 spine3           10 left_foot        11 right_foot
12 neck             13 left_collar      14 right_collar     15 head
16 left_shoulder    17 right_shoulder   18 left_elbow       19 right_elbow
20 left_wrist       21 right_wrist      22 jaw              23 left_eye_smplhf
24 right_eye_smplhf 25 left_index1      26 left_index2      27 left_index3
28 left_middle1     29 left_middle2     30 left_middle3     31 left_pinky1
32 left_pinky2      33 left_pinky3      34 left_ring1       35 left_ring2
36 left_ring3       37 left_thumb1      38 left_thumb2      39 left_thumb3
40 right_index1     41 right_index2     42 right_index3     43 right_middle1
44 right_middle2    45 right_middle3    46 right_pinky1     47 right_pinky2
48 right_pinky3     49 right_ring1      50 right_ring2      51 right_ring3
52 right_thumb1     53 right_thumb2     54 right_thumb3
```
- **The 22 vs 53 vs 55 confusion resolved**: indices 0–21 (`pelvis`…`right_wrist`) form the body-only set — the file does *not* label them as a separate list. One codebase describes the original SMPL-X set as **53 joints = 22 body + 30 hand + 1 jaw**, omitting the eyes; adding `left_eye_smplhf` and `right_eye_smplhf` gives **55** — [joint_names.py](https://huggingface.co/spaces/Yuliang/ECON/raw/25a71fd0c00b36c8c284e4d1a4ab9df5c290bb59/lib/smplx/joint_names.py), [SMPLer-X human_models.py](https://huggingface.co/spaces/cbensimon/SMPLer-X/blob/main/common/utils/human_models.py). Note the list continues past 54 with non-joint keypoints (nose, ears, toes, heels) and then face landmarks — do **not** feed those to a retargeter.
- Independent skeleton-definition references for the same hierarchy exist in NVIDIA's kimodo docs — [kimodo.skeleton.definitions](https://research.nvidia.com/labs/sil/projects/kimodo/docs/_modules/kimodo/skeleton/definitions.html) — and in `smog`'s `smpl_utils.py` — [smog](https://huggingface.co/vonexel/smog/blob/main/src/datasets/smpl_utils.py).

**Mixamo side of the mapping**
- Mixamo bone names carry a `mixamorig:` prefix, e.g. `mixamorig:Hips`, `mixamorig:Spine1`, `mixamorig:LeftHand`; the guidance is to **match by anatomical role, not by name string** — [search synthesis over joint_names sources](https://huggingface.co/spaces/Stylique/PSHuman/blob/main/lib/smplx/joint_names.py).
- **`uzairdeveloper223/mixabridge`** (MixaBridge, Godot 4.4+, MIT) auto-maps **80+ Mixamo bones** to `SkeletonProfileHumanoid` and accepts both `mixamorig:` and `mixamorig_` prefixes — the most complete published Mixamo-name table I found — [Godot Asset Library entry](https://godotengine.org/asset-library/asset/edit/22298).
- A reusable Mixamo-name lookup in JS maps e.g. `mixamorigLeftForeArm` → `leftLowerArm`; it is a good template for the Mixamo half, but it lives inside a HuggingFace Space (not a standalone repo) and that Space errors — [mixamoVRMRigMap.js](https://huggingface.co/spaces/AIbee999/CharacterGen_test/blob/604b72580a799f334d82bcc49c9075dce80694f4/render_script/three-js/src/mixamoVRMRigMap.js).
- `enziop/mixamo_converter` **renames bones to match the Unreal mannequin skeleton** as part of its conversion — so it doubles as a Mixamo→UE name table — [repo](https://github.com/enziop/mixamo_converter).
- `Animexport Pro` (commercial Blender add-on) ships a table that **strips the `mixamorig:` prefix and renames to plain `Hips`, `LeftUpLeg`** etc. — [docs](https://superhivemarket.com/products/animexport-pro/docs).
- Two existing SMPL→Mixamo retarget implementations exist as code but publish no table: `mixamo-llm-mocap`'s `pipeline/lift_to_mixamo.py` + `pipeline/retarget_smplx.py` — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md) — and `ComfyUI-Video2MotionCapture`'s `RetargetSMPLToMixamo` and `RetargetsMixamoToUEMannequin` nodes — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).

**Unreal UE5 Manny: chain mapping, not name mapping**
From [UE IK Rig / Animation Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine) (page self-identifies as **Unreal Engine 5.8 Documentation**, with links to 5.5 and 5.7 pages):
- On **both** IK Rigs: right-click the pelvis bone in the Hierarchy panel → **Set Pelvis**. "The pelvis is used so root motion transfers proportionally between characters."
- Chains are built with **Auto Create Retarget Chains** or added manually in the IK Retargeting panel; each needs a **Start Bone and an End Bone** (e.g. upper arm → hand). "Chains are defined by joint ranges rather than individual bones, so a target arm with more joints still maps correctly" — this is why SMPL-X's 3-segment spine (`spine1/2/3`) can drive Manny's longer spine.
- **Chain names are matched by fuzzy string comparison**, so `ArmLeft` and `left_arm` can pair. Auto-naming uses keyword lists ("arm", "leg", "spine") and appends Left/Right **based on the bones' average X position**. Duplicate chains get numeric suffixes (`Head_1`, `Head_2`).
- **Retarget poses** are the fix for A-pose source vs T-pose target: create with `+`, enable Edit Mode, adjust in viewport or Details; **a rotation of 0,0,0 returns a bone to its reference pose**; poses can be imported from an Animation Sequence (choose a frame, default 0) or a Pose Asset. **Auto Align** can match bone direction, local axes, global axes, or mesh-based direction; **Snap Character to Ground** fixes height.
- **Root motion is copied from the source at runtime**; `Ignore Root Lock in Preview` affects **only** the editor preview — export behaviour is controlled separately by **`ExportRootLockMode`**.
- Documented pitfalls, verbatim in substance: mismatched/ambiguous chain names cause wrong pairings — **check the Retarget Output Log for warnings and errors, especially after changing the target mesh**; changing a preview mesh relies on name matching and may fail across skeletons; **"Local and global axis alignment can produce odd results when rotation directions differ between skeletons"** — this is the bone-roll pitfall, stated by Epic.
- BEDLAM's production answer to the same problem: use the supplied **SMPL-X IK Rig** (credited to Joachim Tesch), map chains manually when names differ, and **keep IK disabled (FK only)** — [bedlam2_retargeting README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- A third-party plugin automates the Mixamo↔UE5 side and is explicit that it **adds a Root Bone to the Mixamo skeleton (and related skeletal meshes and animations) to enable root motion**, and that converting Mixamo animations to UE5 root-motion animations **requires both the "normal" and the "in-place" animation** — [UE5 Mixamo Animation Retargeting 2](https://www.fab.com/listings/5eaf2746-1891-435a-837c-67c9f7a847f4?lang=en).

**Rotation-order / axis / roll pitfalls, sourced**
- **Transfer joint *rotations*, not positions**, or you lose twist — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Blender FBX export's **Primary Y / Secondary X** bone axes are the default, and changing them is "the usual cause of 'my bones import rotated 90 degrees'" — [Blender→Unreal checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist).
- The armature's **0.01 scale** means world-space aim targets must be computed in armature centimetres, or limbs end up ~10 m from where you meant — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Left/right inversion is a real and repeated bug: "facing the camera, the viewer's left is the character's right — decide limbs from landmark data, not stills" — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Correct a distorted limb **by rotating the chain about the shoulder, not by translating joints** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Resample motion with a **C1 spline, not linear interpolation**, or you get stutter — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- **Joint centres are not keypoints** — compare only against matching definitions, or your validation will be systematically off — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Mixamo's `Hand` bone sits at the **wrist**, so arm reach must be measured as arm + forearm, not to "Hand" — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).

### Inferences
- The SMPL-X→Mixamo body mapping is essentially forced by the joint lists, and reads: `pelvis→mixamorig:Hips`; `left_hip/right_hip→LeftUpLeg/RightUpLeg`; `left_knee/right_knee→LeftLeg/RightLeg`; `left_ankle/right_ankle→LeftFoot/RightFoot`; `left_foot/right_foot→LeftToeBase/RightToeBase`; `spine1/spine2/spine3→Spine/Spine1/Spine2`; `neck→Neck`; `head→Head`; `left_collar/right_collar→LeftShoulder/RightShoulder`; `left_shoulder/right_shoulder→LeftArm/RightArm`; `left_elbow/right_elbow→LeftForeArm/RightForeArm`; `left_wrist/right_wrist→LeftHand/RightHand`; fingers `*_index1..3 → LeftHandIndex1..3` etc. **This is my reconstruction from the two cited name lists, not a published table** — flag it as such.
- The naming trap in that mapping: SMPL-X `*_collar` is the clavicle (Mixamo `*Shoulder`) while SMPL-X `*_shoulder` is the upper arm (Mixamo `*Arm`). Mapping by the word "shoulder" produces a one-bone-off shift through the whole arm — a very likely cause of twisted arms in hand-rolled scripts.
- SMPL-X has **three** spine joints and **no** dedicated chest/upper-chest, whereas UE5 Manny and many Mixamo variants have more. That is precisely why Epic's chain-range mapping (not 1:1 bone mapping) is the right abstraction for Unreal and why pure name mapping is the wrong one.
- SMPL-X carries `jaw` and two eye joints and **no finger tips beyond `*3`**; game rigs typically want neither the eye joints nor the jaw on the body skeleton. Expect to drop indices 22–24 in any game-rig mapping.

### Gaps
- **No published SMPL/SMPL-X→Mixamo joint-name table found.** Confirmed negative result across two searches. The implementations that do it (`lift_to_mixamo.py`, `RetargetSMPLToMixamo`) keep the table in source I did not read.
- No published SMPL-X→UE5 Manny **bone-name** table either; Epic's own tooling deliberately avoids one. BEDLAM supplies an IK Rig asset rather than a table — [bedlam2_retargeting](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md).
- SMPL-X's **axis convention** (which world axis is up, and the per-joint rest-pose axis alignment) is not stated in `joint_names.py` and I found no primary doc for it. The only concrete anchor is the SMPL-X Blender add-on's **up-axis choice in the import dialog**, which implies the convention differs from Blender's Z-up — [Meshcapade add-on](https://github.com/Meshcapade/SMPL_blender_addon). Do not assert a convention without checking.
- Mixamo's full bone list is only reachable second-hand (MixaBridge's "80+ bones", Animexport's rename table); I found no primary Adobe/Mixamo skeleton specification.

---

## Q4. Foot contact and root motion as explicit passes

### Takeaway
There is one genuinely open-source, downloadable foot-skate solver — **`InterDigitalInc/UnderPressure`**
(SCA 2022) — which detects contacts from estimated ground reaction forces and then removes skate with
optimisation-based IK. Everything newer (StableMotion 2025, EA's DRL refinement, Rokoko/UCPH ADAPT) is
published but with no confirmed code release. In Blender, root-motion extraction has **no maintained
free add-on**; the reliable route is a hand-written bpy script or the paid Foot-Work / Animexport Pro.

### Cited Findings

**Foot-skate solvers**
- **`InterDigitalInc/UnderPressure`** — official implementation of Mourot, Hoyet, Le Clerc & Hellier, *UnderPressure: Deep Learning for Foot Contact Detection, Ground Reaction Force Estimation and Footskate Cleanup*, SCA 2022 / **Computer Graphics Forum 41(8)**. Pipeline: "foot contact labels are first derived from estimated ground reaction forces. Then, footskate is removed by solving foot constraints through an optimisation-based inverse kinematics (IK) approach." **Not** physics-simulation-based. README setup is clone + conda env on **Python 3.9.7**. The repo ships the implementation, a pre-trained model, and the database — [paper PDF](https://diglib.eg.org/bitstream/handle/10.1111/cgf14635/v41i8pp195-206.pdf), [DeepAI listing](https://deepai.org/publication/underpressure-deep-learning-for-foot-contact-detection-ground-reaction-force-estimation-and-footskate-cleanup), [repo](https://github.com/InterDigitalInc/UnderPressure).
- The accompanying **UnderPressure dataset** (mocap paired with pressure-insole data) has DOI **10.57745/FYXKXY**, listed at **version 1.1, publication year 2025** — [B2FIND record](https://b2find.eudat.eu/dataset/ad5eddc0-c4ec-5e21-8900-0d3c368c1007).
- **Classic baseline algorithm**: Kovar, Schreiner & Gleicher, *Footskate Cleanup for Motion Capture Editing* — "a simple and efficient algorithm for removing footskate without physics simulation." Still the standard reference and the easiest thing to reimplement — [Wisconsin graphics gallery](https://research.cs.wisc.edu/graphics/Gallery/kovar.vol/Cleanup).
- **StableMotion (2025)** reports a **Foot Skating Distance of 3.70**, better than ground-truth AMASS at 3.85, on the BrokenAMASS benchmark. Code availability **not** confirmed — [alphaXiv overview](https://alphaxiv.org/overview/2505.03154v2).
- **EA, SIGGRAPH Asia 2025**: a physics-based motion refinement framework using deep reinforcement learning. Code release **not** stated — [CGF/EG proceedings PDF](https://diglib.eg.org/bitstream/handle/10.1111/cgf15172/v43i8_cgf15172.pdf) *(note: this DOI is the 2024 CGF volume; the EA 2025 item surfaced via the same search and I could not isolate a distinct primary URL — treat the EA attribution as weakly sourced).*
- **ADAPT** (Rokoko + University of Copenhagen, 2024) cleans IMU-based capture using "a combination of physics simulation and learned AI behavior, through reinforcement learning." No open-source release stated — [search result context](https://arxiv.org/html/2601.19036v1).
- **In-pipeline, non-ML foot locking that actually ships**: `mixamo-llm-mocap`'s `retarget_smplx.py` applies "floor, foot locks, and camera-true trajectory" as part of retargeting, and `eval_fidelity.py` measures **foot slide** and **jitter** as explicit metrics — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).

**The practical foot-locking algorithm, as a real pipeline states it** — all from [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md):
- **Pin the support ankle structurally in the lift stage, not per-frame in the apply stage** — per-frame landmark drift is what causes skating.
- **Treat a foot as planted when it is still in the image**, and lock only while it stays still.
- **Lock a foot only when the whole foot is still, not just its ball or heel**; "a static heel can still be raised — check both heel and ball height."
- **Cut a foot lock when its heading moves about 20°.**
- **Never snap the acting limb to the floor** — that is what causes popping frames.
- **Lift a sinking foot with its own leg, not by raising the body**; and **do not re-level the world to match planted feet**.
- **Align only foot yaw**, measure the foot **on the mesh, not the joint line**, and **flatten a foot before reading its heading**. Do **not** fit foot heading to 2D keypoints — "it is unreliable."
- Foot heading must follow the foot or hips, **never a world axis**, or toes turn outward.
- A toe aimed at its own head is a near-zero-length target → **aim a point ahead along the foot's heading**.
- **Keep toe-direction flattening for planted feet only**; kicking feet need their pitch.
- **Remove the measured steady tilt from each foot on static frames** (ground-plane fitting, per-foot).
- **Contact confidence can be confidently wrong — check it against foot speed and height.**
- **Re-pin flagged feet after 2D refinement**, because refinement can unplant them.
- **Do not pin the body to a foot that was never seen on the floor.**
- **Blend only the upper body to the rest pose — blending the legs makes feet slide.**
- With legs on **FK only**, plant feet via a **hip-height search plus Z-only flattening**; IK on the legs makes feet fly off the floor.

**Root motion extraction**
- **Foot-Work (Blender add-on)** is the closest match to a purpose-built tool: its feature list includes **converting between in-place and root-motion cycles**, plus ground snapping and stride adjustment. A forum poster notes it may work with in-place cycles if offset one frame at a time, untested. Rationale given: root-motion cycles reduce foot slide by moving the root only a set distance during each step interpolation — [Blender Artists thread](https://blenderartists.org/t/foot-work-addon/1561511).
- **Animexport Pro (Blender add-on, commercial)** documents the exact operator semantics you'd want: an extraction operator that **creates a new action named `{OriginalAction}_RootMotion` with object-level location keyframes and zeros out the root bone location channels for the extracted axes**, yielding an in-place action plus a separate root-motion action, with an **inject** step to reapply — [docs](https://superhivemarket.com/products/animexport-pro/docs).
- **Manual method, no add-on**: copy the pelvis's location channels onto the root bone in the Graph Editor, then delete them from the pelvis; or put a **Copy Location constraint on the root limited to the XY plane** so it stays on the ground — [Polycount discussion](https://polycount.com/discussion/comment/2506105), [second comment](https://polycount.com/discussion/comment/2505898).
- **`enziop/mixamo_converter`** adds a root bone and exposes `[Use Z]` / `[On Ground]` for vertical root motion and `[Use X]` / `[Use Y]` which "can be disabled to prevent movement of root on groundplane." Batch-converts a folder of FBX or Collada — [repo](https://github.com/enziop/mixamo_converter).
- **UE-side**: the Fab/Marketplace **Root Motion Extractor** is an Unreal plugin that addresses foot sliding inside UE, not in Blender — [listing](https://www.unrealengine.com/marketplace/en-US/product/root-motion-extractor).
- In UE5, retargeted **root motion is copied from the source at runtime**, `Ignore Root Lock in Preview` affects only the preview, and export is governed by **`ExportRootLockMode`** — [UE IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).
- Converting a Mixamo animation to UE5 root motion **requires both the "normal" and the "in-place" variants of the animation** for the computation — [UE5 Mixamo Animation Retargeting 2](https://www.fab.com/listings/5eaf2746-1891-435a-837c-67c9f7a847f4?lang=en).
- From monocular capture, the pipeline-level root-motion rules are: **drive root motion from the in-camera trajectory, using only its lateral component**; the world trajectory drifts, so **take height from the camera and correct horizontal drift per footfall**; **measure camera corrections at each footfall, not averaged over a stance** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- In Unity, a **Generic** rig has no Avatar and you nominate the **root node** yourself; "the Root node allows Unity to establish consistency between Animation clips for a generic model," i.e. it is what lets clips authored not-in-place blend — [Unity FBX Importer Rig docs](https://docs.unity3d.com/es/current/Manual/FBXImporter-Rig.html).

### Inferences
- The only end-to-end-usable open-source foot-skate solver you can `git clone` today is UnderPressure, and it is a 2022 Python/PyTorch research repo on Python 3.9.7 — i.e. it will need its own venv, separate from the CUDA-12.1/torch-2.3.0 GVHMR venv and from Blender's Python. Budget for three Python environments.
- The `mixamo-llm-mocap` pitfalls list is, in effect, a **free specification for a foot-locking pass**: contact = whole foot still in image; lock window ends at ~20° heading change; per-foot steady-tilt removal as ground-plane fitting; hip-height search for FK-only rigs. It is more actionable than any of the papers for someone writing their own pass.
- Root motion extraction is cheap to write yourself and the Animexport Pro docs give you the exact contract to implement: new `{Action}_RootMotion` action, object-level location keys, zeroed root-bone location channels on the extracted axes. That is ~30 lines of bpy.
- UE's requirement of *both* in-place and normal variants to synthesise root motion is a strong argument for extracting root motion **in Blender, before export**, where you have the pelvis curve directly.

### Gaps
- Could not verify a **2025–2026 open-source** foot-skate solver. StableMotion, the EA SIGGRAPH Asia 2025 work and ADAPT all lack confirmed code releases in the sources found.
- **Cascadeur**: the assignment asked about its features (AI physics / auto-posing, and its ground-contact tooling). I ran out of research budget before querying it and have **no sourced facts** on Cascadeur. Flag as unresearched.
- **UE5 Motion Trajectory / Motion Warping / Choosers** and the UE5 **IK "Op Stack"** (Retargeting Operation Stack) were only glancingly referenced by the IK Retargeting page ("Check the Retargeting Operation Stack page for those"). I did not fetch that page, so Speed Planting / Stride Warping / Blend to Source parameters are unsourced beyond their names — [IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).
- Foot-Work's and Animexport Pro's **current Blender version compatibility** is unverified; the search explicitly flagged this as unchecked.
- No sourced ground-plane-fitting algorithm (e.g. RANSAC on contact points) with an implementation; the only concrete guidance is PITFALLS' per-foot steady-tilt removal.

---

## Q5. Jitter and smoothing: concrete filters and parameters

### Takeaway
The **one-euro filter** is the practitioner default and is the only filter for which I found concrete,
sourced tuning procedure and parameter ranges — including inside Unreal's own IK Rig settings. The
consensus on not destroying motion extremes is explicit and repeated: **narrow windows (~5 frames),
then restore amplitude**; wide smoothing windows average away peaks. I found **no** source discussing
Savitzky-Golay for mocap, and none for Butterworth.

### Cited Findings

**One-euro filter: what it is and how to tune it**
- It is a **first-order low-pass filter with an adaptive cutoff frequency**: low cutoff at rest suppresses jitter, cutoff rises during fast motion to limit lag — [Derivative Filter CHOP docs](https://derivative.ca/UserGuide/Filter_CHOP), [one-euro-filter explainer](https://github.com/mksharaf/oneeurofilterexplained).
- **Tuning procedure, verbatim in substance**: set **Speed Coefficient (β) = 0** and **Cutoff Frequency to a reasonable middle-ground value such as 1 Hz**; then hold the body part steady or move it at very low speed while adjusting **Slope Cutoff Frequency**; **raise the speed coefficient only if lag during fast motion is a problem** — [Derivative Filter CHOP docs](https://docs.derivative.ca:443/Filter_CHOP).
- The authors' own rule of thumb, as restated by libossia: **raise `beta` when lag during fast motion is the issue; lower `min_cutoff` when jitter at rest is the issue** — [libossia one_euro_filter](https://ossia.io/libossia/html/structossia_1_1one__euro__filter.html).
- **Unreal Engine ships one-euro filter settings as a class** (`OneEuroFilterSettings`) in its IK Rig / Python API, and the docs describe the same trade-off: **a lower min cutoff gives more smoothing at rest but can feel "sticky," and a responsiveness value around 0.3–0.8 is listed as a typical starting range** — [UE OneEuroFilterSettings](https://dev.epicgames.com/documentation/en-us/unreal-engine/python-api/class/OneEuroFilterSettings).
- Other ready implementations: `OneEuroFilter` in Needle Engine — [docs](https://engine.needle.tools/docs/api/OneEuroFilter) — and a 1EuroFilter Arduino library — [listing](https://www.arduinolibraries.info/libraries/1euro-filter).
- **2026 research direction**: a Bournemouth University SCA 2026 poster on monocular-mocap jitter proposes **a variant of the one-euro filter combined with confidence-weighted filtering** — i.e. weighting the filter by per-keypoint detection confidence — [poster PDF](https://eprints.bournemouth.ac.uk/42215/1/VA_SCA2026_Poster.pdf), [record](https://eprints.bournemouth.ac.uk/42215/).

**The "don't kill the extremes" consensus, from a production pipeline**
All from [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md):
- **"Shrunken punches: wide smoothing windows average away peaks. Use narrow windows (about 5) and restore amplitude with `reach`."**
- **"Use a 5-frame landmark prefilter to avoid eating strikes."**
- **"Keep smoothing light; it costs punch amplitude."**
- Related: **regressors themselves flatten extremes** — refine on the plate's own 2D keypoints to get them back. And GVHMR specifically "flattens fast limbs" — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- Jitter is also caused by **resampling**: use a **C1 spline, not linear interpolation**.
- `eval_fidelity.py` measures **jitter** as a first-class metric alongside foot slide, i.e. the pipeline treats smoothing as something to verify numerically rather than eyeball — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).

### Inferences
- The two findings combine into a concrete recipe: a **5-frame narrow prefilter on landmarks** before lifting, then a **one-euro filter on the resulting joint rotations with β starting at 0 and min-cutoff ≈1 Hz**, raising β only if fast motion lags — then an explicit **amplitude-restoration pass** on the frames you care about, measured with a jitter metric. Each half is sourced; the combination is my synthesis.
- Confidence-weighted one-euro (Bournemouth SCA 2026) is the natural fit for monocular capture because ViTPose/MediaPipe already emit per-keypoint confidence, which the pipeline is already consuming. This is an inference about applicability, not a claim about the poster's results.
- The absence of Savitzky-Golay and Butterworth from the sources is itself informative: in the monocular-mocap community the adaptive filter has displaced fixed-kernel filters, precisely because fixed kernels are what flatten the extremes.

### Gaps
- **No source found discussing Savitzky-Golay for motion capture**, and therefore no window-length or polynomial-order parameter values. Explicit negative result from one search.
- **No source found for Butterworth filter parameters** in this context (cutoff frequency, order, zero-phase/filtfilt usage). Unresearched.
- The Bournemouth SCA 2026 poster's actual numbers and whether it compares against Savitzky-Golay were not readable from the excerpt; the full paper was not fetched.
- The exact default β and min-cutoff values Unreal ships in `OneEuroFilterSettings` were not retrieved (only the 0.3–0.8 "responsiveness" range).

---

## Q6. Format plumbing: BVH vs FBX vs GLB vs USD in 2026, and which converters are reliable

### Takeaway
For this pipeline **FBX remains the only format that carries everything in one file** — skeleton,
skinning, blendshapes and multiple animation takes — and Blender is the only converter you should
depend on. The **FBX Python SDK is effectively dead as a dependency**: the last official build is
2020.3.7 precompiled for **Python 3.10 only**, with Python 3.11 available solely as community builds
that have produced DLL-loading failures. **USD is actively bad here**: it binds one SkelAnimation per
skeleton and Blender's exporter writes only the active action.

### Cited Findings
- **FBX** carries skeletons, skinning, blend shapes and animation clips inside one file, and handles complex rigs, blend shapes, constraints and mocap data "more completely than any current glTF toolchain." It is proprietary (Autodesk) — [3DAI Studio comparison](https://www.3daistudio.com/convert/compare/glb-vs-fbx), [neural4d comparison](https://blog.neural4d.com/comparisons/gltf-vs-fbx/), [miris glossary](https://www.miris.com/glossary/fbx-filmbox).
- **glTF/GLB** supports skeletal animation and **morph targets natively** and is the web/real-time choice; sources conflict on animation depth — one says "animation support is limited," which the others contradict — [neural4d](https://blog.neural4d.com/comparisons/gltf-vs-fbx/); contradicted by [3DAI Studio](https://www.3daistudio.com/convert/compare/glb-vs-fbx).
- **USD is the weakest for multi-clip delivery**: "USD binds one SkelAnimation per skeleton, and Blender's exporter only writes the active action." The documented workaround is **one file per clip** beside the base asset, "whereas FBX and GLB carry every take natively in the single file" — [Apify gltf-to-usd-validated actor docs](https://apify.com/ernestx/gltf-to-usd-validated.md).
- **Blender carries every take in one FBX** via `bake_anim_use_all_actions=True` (default) and `bake_anim_use_nla_strips=True` (default) — [bpy.ops.export_scene](https://docs.blender.org/api/current/bpy.ops.export_scene.html).
- **FBX Python SDK availability**: an Autodesk staff post states that for **2020.3.7**, "the FBX python is now pre-compiled for Python 3.10 and comes in a wheel archive so it can be installed with a pip install command," and that bindings can be rebuilt with **SIP 6.6.x** per the README for other interpreters. The post is **undated** — [Autodesk forum thread](https://forums.autodesk.com/t5/fbx-forum/fbx-python-sdk-for-python-3-9/m-p/11531800), [thread root](https://forums.autodesk.com/t5/fbx-forum/fbx-python-sdk-for-python-3-9/td-p/11459327).
- **No official Python 3.11 build found.** A tech-artists.org thread reports community builds of **2020.3.9** for Python 3.7 / 3.9 / 3.11 ("My son compiled FBX SDK PYTHON 2020.3.9 for Python 3.7/3.9/3.11") — note that is 2020.3.9, not 2020.3.7 — [tech-artists.org](https://www.tech-artists.org/t/fbx-sdk-2020-3-9-for-python-3-7-3-9-3-11/18335). A third-party Python-3 binding repo also exists — [llmmcc/FBX-Python-SDK-for-Python3.x](https://github.com/llmmcc/FBX-Python-SDK-for-Python3.x).
- **Building it yourself**: a Japanese guide builds the 2020.3.9 bindings into a wheel with **`sip-wheel`** from Autodesk's "FBX Python Bindings" folder and installs with pip; its worked example is **Python 3.13 on Windows** — [Zenn article](https://zenn.dev/nadegata_memo/articles/47690559881ee5). Autodesk's own (ancient) "Installing Python FBX" page is still the reference for the layout — [Autodesk docs](https://download.autodesk.com/us/fbx/20112/FBX_SDK_HELP/files/WS73099cc142f48755-751de9951262947c01c-6dc7.htm).
- **Reported failure mode**: forum users report **"not a valid Win32 application" DLL errors** with some community-built FBX SDK binaries — test any build in your target environment before depending on it — [Autodesk forum](https://forums.autodesk.com/t5/fbx-forum/fbx-python-sdk-for-python-3-9/m-p/13078777).
- **BVH as the SMPL escape hatch**: `SlimeVRX/smpl2bvh` converts SMPL motion → BVH from `.npz` input containing `rotations` and `trans`; import the BVH into Blender and export FBX from there — [repo](https://github.com/SlimeVRX/smpl2bvh).
- **ComfyUI-MotionCapture** appears to emit BVH (sample filename `smpl_to_bvh_30fps.mp4`), though the README never states formats — [repo](https://github.com/PozzettiAndrea/ComfyUI-MotionCapture).
- **npz→FBX without Blender's GUI**: a ComfyUI node (`HY-Motion`, "wood fbx to data") converts `.npz` motion data to FBX with an fps setting and an output path — [RunComfy node page](https://www.runcomfy.com/comfyui-nodes/ComfyUI-HyMotion/hy-motion-wood-fbx-to-data).
- `ComfyUI-Video2MotionCapture`'s `SaveSMPLAsRiggedFBX` writes a rigged FBX "with automatic axis-orientation fixes," and `SaveSMPLAsMesh` writes an **OBJ per frame** (a mesh sequence, not skeletal) — [repo](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- **Blender's own FBX exporter source** is available if you need to read its behaviour rather than guess — [archived export_fbx.py](https://projects.blender.org/archive/blender-archive/src/commit/4c4ec56f49e41e75da1bf8d83ff36ff70f862f43/release/scripts/io/export_fbx.py).
- Format-conversion services exist for glTF/GLB/FBX↔USD/USDZ with rig verification, if you want a reference implementation of the conversion rules — [Apify actor](https://apify.com/ernestx/gltf-to-usd-validated), [artlabs USD conversion guide](https://simready.artlabs.ai/resources/how-to-convert-gltf-fbx-obj-to-openusd-for-simulation).

### Inferences
- **Use Blender as the converter, not a library.** It is the only tool in this space that is actively maintained, scriptable headlessly, reads BVH/FBX/glTF/OBJ, writes FBX/glTF/USD, and is already a hard dependency of every pipeline in Q1. Adding `fbx-sdk`, `pymeshio` or ASSIMP to this stack adds a build problem without adding capability.
- The FBX SDK's Python-3.10-only official wheel collides badly with this pipeline's other pins: UniRig wants **3.11**, GVHMR wants **3.10**, UnderPressure wants **3.9.7**, and Blender ships its own interpreter. Routing FBX through Blender avoids a fourth environment.
- **Recommended format chain**: capture → `.npz` (SMPL-X params) → Blender (bpy) → `.fbx` for Unreal/Unity delivery, with `.glb` only as a web/preview sidecar and **USD avoided entirely** for multi-clip animation libraries.
- **BVH is useful as a debugging/interchange format precisely because it is dumb**: rotations plus a root translation, nothing else. It carries root motion (root channels) but **no skinning, no blendshapes, no fingers beyond whatever joints you emit, and one animation per file** — which follows from its structure, though I found no single source stating this comparison, so treat it as inference.

### Gaps
- **`bvh-converter`, `pymeshio` and ASSIMP were not researched** — I ran out of budget. No sourced facts on their 2026 maintenance status, Python compatibility, or skeletal-animation fidelity. Flag as unresearched rather than absent.
- No source addressed **root motion** across the three formats. Explicit negative result from the format-comparison search.
- All the FBX-vs-glTF-vs-USD comparison sources are vendor/blog content, **none dated 2026**, and one pair directly contradicts the other on glTF animation depth. The USD multi-clip limitation is the one claim with a concrete mechanism behind it.
- Whether an **official FBX SDK release newer than 2020.3.7/2020.3.9** exists in 2026 is unconfirmed; the Autodesk forum posts are undated.
- `bpy.ops.export_scene.gltf` parameter list not retrieved (see Q2 gaps).

---

## Q7. What breaks and how to tell: failure symptoms → root cause → fix

### Takeaway
Six of the classic symptoms have clean, sourced root causes. The best single artifact on the web for
this is `mixamo-llm-mocap`'s **`docs/PITFALLS.md`** — 67 numbered symptom/fix pairs from someone who
actually shipped the pipeline. The 100×-scale bug in particular has a precise, documented mechanism
that most forum advice gets wrong.

### Cited Findings

**"Scale is 100× off" / "mesh is right size but skeleton is 0.01×"**
- Mechanism, stated precisely: with **Unit Scale 1.0 and Apply Scalings = All Local**, Blender's exporter **multiplies every top-level object transform by 100 and writes a `UnitScaleFactor` of 1.0**. The mesh arrives correct because its transform carries the 100 — **and so does the armature object, which Unreal turns into the root bone**, giving a 100×-too-big root bone. Blender's tracker has the mirror report: "the mesh will be the right scale, but the skeleton will be 0.01 the scale" — [blender-addons issue 47043](https://projects.blender.org/blender/blender-addons/issues/47043), [T99075](https://developer.blender.org/T99075), [T44424](https://developer.blender.org/T44424).
- **Fix A**: Scene Properties → Units → **Unit Scale = 0.01**, Length = Centimeters, and model at Unreal size. Caveat from the UE forum: this may not fix an *existing* asset — "it seems I have to start from scratch with a scale of 0.01" — [UE answers](https://answers.unrealengine.com/questions/617946/view.html), [Blender→UE checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist).
- **Fix B**: keep Unit Scale 1.0 and export with **Apply Scalings = `FBX_SCALE_ALL`** so the file header carries the conversion and Unreal's Convert Scene handles it — [Blender→UE checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist), enum confirmed in [bpy.ops.export_scene](https://docs.blender.org/api/current/bpy.ops.export_scene.html).
- Also relevant to Unity: "Wrong rotation / position / scale when exporting FBX from Blender" is a long-standing documented class of bug — [Unity discussions](https://discussions.unity.com/t/wrong-rotation-position-scale-when-exporting-fbx-from-blender/175140).

**"Limbs twisted 90°" / "bones import rotated 90 degrees"**
- Root cause: **non-default FBX bone axes.** Defaults are **Primary Y / Secondary X**, and changing them "is the usual cause of 'my bones import rotated 90 degrees'." Also keep **Forward `-Z`, Up `Y`** — [Blender→UE checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist); defaults confirmed in [bpy.ops.export_scene](https://docs.blender.org/api/current/bpy.ops.export_scene.html).
- Second root cause, from Epic: **"Local and global axis alignment can produce odd results when rotation directions differ between skeletons"** in the IK Retargeter's Auto Align — [UE IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).
- Third root cause: **transferring joint positions instead of rotations loses twist** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Fourth root cause: **left/right swap** — "facing the camera, the viewer's left is the character's right; decide limbs from landmark data, not stills" — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).

**"T-pose on import" / "animation didn't come through"**
- Root cause: wrong bake flags. `bake_anim` (default True), `bake_anim_use_all_actions` (default True), `bake_anim_use_nla_strips` (default True), `bake_anim_force_startend_keying` (default True) — if any is False you can ship a skeleton with no or truncated animation — [bpy.ops.export_scene](https://docs.blender.org/api/current/bpy.ops.export_scene.html).
- Root cause: **constraints were never baked**. `clear_constraints` without `visual_keying` gives a wrong bake — the docs say so explicitly: "To get a correct bake with this setting Visual Keying should be enabled" — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html).
- Root cause, in-Blender: **"the scene holds only the last applied action — bind the spec's action before rendering"**, and **stale location keys from failed passes persist; rebuild the action and key zero locations** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Root cause, retarget-side: **reference poses differ (A-pose source vs T-pose target)** → use UE **retarget poses**; "a rotation of 0,0,0 returns a bone to its reference pose"; or import a pose from an Animation Sequence frame — [UE IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).

**"Character animates in place" (no root motion)**
- Root cause: the rig has no root bone. `enziop/mixamo_converter` exists specifically because **"a Root Bone is added to the Mixamo skeleton (and to the related skeletal meshes and animations), which enables root motion"** — Mixamo rigs ship without one — [enziop/mixamo_converter](https://github.com/enziop/mixamo_converter), [UE5 Mixamo Animation Retargeting 2](https://www.fab.com/listings/5eaf2746-1891-435a-837c-67c9f7a847f4?lang=en).
- Root cause: translation lives on the hips, not the root. `mixamo-llm-mocap` emits motion where **"hips [are] the only translating bone"** — so without an extraction pass the character is in-place by construction — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).
- Fix: extract pelvis location to an object-level/root action — Animexport Pro's documented contract is **a new `{OriginalAction}_RootMotion` action with object-level location keyframes, with the root bone's location channels zeroed for the extracted axes** — [docs](https://superhivemarket.com/products/animexport-pro/docs); or the manual Graph-Editor copy/XY-limited Copy Location constraint — [Polycount](https://polycount.com/discussion/comment/2506105).
- Root cause, UE-side: **`ExportRootLockMode`** governs export behaviour, and `Ignore Root Lock in Preview` only changes the preview — people frequently "fix" the preview and ship the locked export — [UE IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).
- Root cause, Unity-side: on a **Generic** rig there is no Avatar and **you must nominate the root node**; Unity picks a default that may be wrong — [Unity FBX Importer Rig docs](https://docs.unity3d.com/es/current/Manual/FBXImporter-Rig.html).

**"Feet slide / skate" and "feet fly off the floor"**
- Root cause: **IK on the legs** of an FK-only rig → "feet fly far off the floor." Fix: FK only, plant feet with a **hip-height search plus Z-only flattening** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Root cause: **per-frame landmark drift** → pin the support ankle **structurally in the lift**, not per-frame in the apply — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Root cause: **blending the legs to a rest pose** → blend only the upper body — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Root cause: assets not at floor level. BEDLAM's hard rule: **"make sure all animations and skeletal meshes sit at floor level"** and run `adjust_floor_npz.py` — [bedlam2_retargeting README](https://raw.githubusercontent.com/PerceivingSystems/bedlam2_retargeting/main/README.md). UE offers **Snap Character to Ground** in retarget poses — [UE docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).
- Diagnostic: measure it. `eval_fidelity.py` reports **foot slide** and **jitter** numerically — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).

**"Punches/kicks look weak" (animation extremes destroyed)**
- Root cause: **wide smoothing windows average away peaks** → use windows of about **5** and restore amplitude with `reach`; **"use a 5-frame landmark prefilter to avoid eating strikes"** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Root cause: **the pose regressor itself flattens extremes** (GVHMR "flattens fast limbs") → refine on the plate's own 2D keypoints — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md), [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).

**"Stutter" / "wrong speed"**
- Root cause: **linear interpolation on resample** → use a **C1 spline** — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Relevant export knobs: **`bake_anim_step=1.0`** (sampling rate) and **`bake_anim_simplify_factor=1.0`** (curve decimation) — a non-1.0 step is a direct cause of wrong playback timing or lost frames — [bpy.ops.export_scene](https://docs.blender.org/api/current/bpy.ops.export_scene.html). Source motion in this pipeline is **30 fps** — [README](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/README.md).

**"Blendshapes/morphs are broken after FBX round-trip"**
- Root cause: **Blender up to and including LTS 3.6.1 clamps imported FBX blendshapes to 0 and 1.** Fix: the add-on's **"Fix Blendshape Ranges"** button; Blender fixed it upstream but had not shipped the fix at time of writing — [Meshcapade/SMPL_blender_addon](https://github.com/Meshcapade/SMPL_blender_addon).
- Related: SMPL pose correctives are **not** recalculated automatically — you must click the update button after pose changes, or the mesh will look wrong — [same](https://github.com/Meshcapade/SMPL_blender_addon).

**"Script fails in headless Blender"**
- `RuntimeError: Operator bpy.ops...` → "needs a valid display or a correct `bpy` install"; use context-safe selection — [ComfyUI-Video2MotionCapture](https://github.com/AKASubaz/ComfyUI-Video2MotionCapture).
- `bpy.ops.nla.bake` **TypeError on Blender 5.x** because arguments became keyword-only — [bpy.ops.nla 5.3](https://docs.blender.org/api/5.3/bpy.ops.nla.html).
- MCP server fails to import → **`mcp` SDK 2.x removed `mcp.server.fastmcp`**; pin `mcp[cli]<2` — [INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- Blender MCP server never starts → **Allow Online Access is off by default** — [INSTALL.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/INSTALL.md).
- **Black screenshots** from headless/occluded Blender → render stills through a temporary camera — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- Wrong bone positions read from a posed rig → **read the evaluated world matrix after a depsgraph update**, not `bone.head` — [PITFALLS.md](https://raw.githubusercontent.com/squall01337/mixamo-llm-mocap/main/docs/PITFALLS.md).
- `flash_attn` install failure during auto-rigging — UniRig itself warns "there is a high chance that you will encounter flash_attn installation error" — [UniRig README](https://raw.githubusercontent.com/VAST-AI-Research/UniRig/main/README.md).
- **Add-on silently misbehaves on too-new Blender**: "using a blender version thats too new with that particular plugin will cause the models and animations to be messed up," fixed by reverting to Blender 2.8.3 LTS — [Adobe community thread](https://community.adobe.com/t5/mixamo-discussions/animations-are-off-on-uploaded-mixamo-model-that-has-been-modified/td-p/13872592).
- **Rigged mesh won't auto-rig / upload**: there is a documented class of "problem with uploading already rigged characters" to Mixamo — relevant because an already-rigged input mesh is an explicitly supported case in the assignment — [Adobe community thread](https://community.adobe.com/t5/mixamo-discussions/problem-with-uploading-already-rigged-characters/td-p/12915518).
- **Wrong retarget pairings in UE**: check the **Retarget Output Log** for warnings and errors, especially after changing the target mesh — [UE IK Retargeting docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/ik-rig-animation-retargeting-in-unreal-engine).

### Inferences
- **Diagnostic order of operations** that falls out of the above: (1) does the skeleton arrive at the right scale and orientation (export flags), (2) does any animation arrive at all (bake flags + visual keying), (3) is the pose correct (retarget pose / axis alignment / L-R), (4) does the root translate (root bone + extraction), (5) do the feet stay put (contact pass), (6) do the extremes survive (narrow filters). Each stage has a distinct symptom, so you can bisect.
- "Mesh detaches from armature" (asked in the prompt) has **no sourced root cause** in what I found. The nearest sourced mechanisms are the armature-vs-mesh transform divergence in the 100× scale bug ([issue 47043](https://projects.blender.org/blender/blender-addons/issues/47043)) and `add_leaf_bones`/`use_armature_deform_only` changing which bones exist in the exported skeleton ([export params](https://docs.blender.org/api/current/bpy.ops.export_scene.html)). Present as a hypothesis, not a finding.
- The cluster of 2026 forum threads still asking "what is the most reliable Mixamo root-motion add-on now" ([Godot forum](https://forum.godotengine.org/t/blender-addon-for-mixamo-root-motion-in-blender-4-5-godot-4-5/135760)) is strong evidence that this specific step has **no good open-source solution** in 2026, as the assignment invited me to say plainly. Closest workarounds, in order: write ~30 lines of bpy to the Animexport Pro contract; or buy Foot-Work / Animexport Pro; or let UE's IK Retargeter copy root motion at runtime and set `ExportRootLockMode`.
- Likewise there is **no maintained free headless armature retargeter**. Closest workarounds: constraint-plus-`nla.bake` written yourself; `bedlam2_retargeting` if you accept a UE 5.3/5.4 dependency; `ComfyUI-Video2MotionCapture`'s `RetargetSMPLToMixamo`/`RetargetsMixamoToUEMannequin` nodes if you accept a WIP repo.

### Gaps
- "Mesh detaches from armature" — no sourced root cause (see inference above).
- Most of the scale/rotation sources are **forum posts, bug reports and third-party guides from 2016–2022**, not official docs, and one guide warns that **"UE 5.4+ made things worse by replacing the legacy FBX importer with the Interchange Framework"** — [Blender→UE checklist](https://app.cinevva.com/guides/blender-to-unreal-export-checklist). I could not verify current Interchange-era import behaviour; this is a live risk for anyone on UE 5.5+.
- Mixamo's own FBX export scale was not investigated as an independent factor, and could compound the Blender-side scale issue.
- Unity's **Apply Root Motion** requirements for Humanoid vs Generic specifically were not retrieved; the search returned only the FBX Importer Rig tab. The Animator-component docs were not fetched.
- `docs/PITFALLS.md` was summarised by the fetch tool, not quoted verbatim, and covers **67 numbered items** of which roughly 50 are reflected above. Anyone building the sheet should read the file directly: https://github.com/squall01337/mixamo-llm-mocap/blob/main/docs/PITFALLS.md
