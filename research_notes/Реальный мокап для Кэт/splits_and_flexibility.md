# Splits and extreme-flexibility female mocap (October 2026 state)

> Scope note: CMU and SFU were excluded by the assignment (settled negative for "split").
> Everything below is what else exists. Capture modality is flagged for every entry:
> **[MARKER]** = optical/marker mocap, **[IMU]** = inertial suit, **[VIDEO]** = video-estimated
> (multi-view triangulation or monocular regression), **[IMAGE-ONLY]** = no 3D at all.

---

## Q1. Ballet / contemporary dance mocap with 3D joint data — does any sequence contain a split, grand écart, penché, développé to vertical, or arabesque to vertical?

### Takeaway
I could **not verify a single named ballet/contemporary mocap sequence containing a split or a développé/penché to vertical**. DanceDB (AMASS) is real marker mocap with ballet content but publishes no per-sequence move-level index; AIST++ is video-estimated and its only ballet-adjacent genre is "Ballet Jazz". The one dataset where I *did* find a verified vertical-leg identifier is MOYO (yoga, see Q2), not a dance set.

### Cited Findings
- **DanceDB** (Dance Motion Capture Database, University of Cyprus) is in AMASS: "20 subjects, 153 sequences totalling roughly 3.5 hours of high quality dance mocap data", released **2021-05-07** — [AMASS news](https://amass.is.tue.mpg.de/news.html). **[MARKER]**
- A benchmark aggregator instead lists "AMASS DanceDB 30 sequences", conflicting with AMASS's own 153 — [SOTA2](https://www.sota2.com/research/dataset/amass-dancedb); treat AMASS's own figure as authoritative.
- DanceDB's public performance log labels entries by style + emotion, e.g. "Contemporary Dance, ballet, Ioanna Tacha", alongside Cypriot/Greek folk entries such as "Zorbas, Clio de la Vara" — [Dance Motion Capture Database](https://dancedb.eu/main/performances). The log is style/performer-level, **not move-level**, so no grand écart/penché identifiers exist in it.
- DanceDB's licence requires acknowledgement naming "the Dance Motion Capture Database of the University of Cyprus"; the AMASS redistribution is SMPL/SMPL-H/SMPL-X body parameters — [AMASS news](https://amass.is.tue.mpg.de/news.html).
- AMASS as a whole was re-released **2022-02-14** in "SMPL-X neutral and gender-specific" formats, re-fitted with MoSh++ from the original mocap markers — [AMASS news](https://amass.is.tue.mpg.de/news.html).
- **AIST++**: 10 genres — Old School (Break, Pop, Lock, Waack) and New School (Middle Hip-Hop, LA-style Hip-Hop, House, Krump, Street Jazz, **Ballet Jazz**) — [Google Research blog](https://research.google/blog/music-conditioned-3d-dance-generation-with-aist/). Sequence count is reported as 1408 in the original paper vs 992/991 in the SMPL-format motion release — [AIST++ download page](https://google.github.io/aistplusplus_dataset/download.html); [AI Choreographer paper](https://arxiv.org/abs/2101.08779).
- AIST++ annotations are "frame-by-frame SMPL-format `.pkl`" and "the 60 FPS frames were hard-coded during video conversion"; downloading the videos/images requires agreeing to the AIST Dance Video Database Terms of Use — [AIST++ download page](https://google.github.io/aistplusplus_dataset/download.html). **[VIDEO]** (multi-camera keypoint reconstruction, not marker mocap).
- AIST++ licence is reported inconsistently: CC BY 4.0 on a paper copy, CC0 on a Kaggle mirror, "Other" on hyper.ai — [hyper.ai AIST++](https://hyper.ai/en/datasets/20566); [Kaggle mirror](https://www.kaggle.com/datasets/zara2099/aist-dance-motion-capture-dataset). The underlying AIST video terms govern regardless.

### Inferences
- "Ballet Jazz" (`gJB`) and "Break" (`gBR`) are the only AIST++ genres where a floor split is choreographically plausible (break-dance floor work, jazz-ballet leaps). Since AIST++ is 60 fps SMPL with no text labels per move, locating one would require rendering and eyeballing the ~99 gJB/gBR sequences — doable offline but unverified here.
- DanceDB is the highest-quality *marker* dance source available non-commercially, but with no move-level index it must be triaged the same way: batch-render the 153 AMASS sequences and visually scan. Its contemporary/ballet subset (Ioanna Tacha) is the place to start.

### Gaps
- No frame rate is published for DanceDB on the AMASS news page. AMASS `.npz` files carry a `mocap_framerate` field per sequence, so the true rate must be read from the files after download. Unverified here.
- I could not reach a per-sequence listing of DanceDB's contemporary/ballet takes (only the style/performer log), so no sequence name can be cited as containing a grand écart.
- MoVi, Human3.6M, EMAGE/BEAT, PHANTOM/Motorica and the generic "Dance Motion Capture Database" were not individually verified within the tool budget; no claim is made about them either way.

---

## Q2. Gymnastics / yoga / fitness / contortion datasets with 3D joints — Fit3D exercise names, and any split or extreme hip abduction

### Takeaway
**MOYO is the answer, and it is a confirmed hit.** It is marker-based Vicon mocap of a single **female professional yoga instructor**, released as SMPL-X/SMPL-H through AMASS, and its released pose list contains `Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-a` and `-b` — i.e. the **standing split / vertical leg hold** the project needs, as real marker capture. It does **not** contain a floor split (no Hanumanasana, no Samakonasana). **Fit3D is a negative**: its named exercises contain no split and no high kick.

### Cited Findings — MOYO (the primary find) **[MARKER]**
- MOYO "contains 200 highly complex poses captured using a synchronized Mocap system, pressure mat, and a multi-view RGB video system with 8 static, calibrated cameras", ~1.75M 4K RGB frames paired with ground-truth SMPL-X fits, pressure and body centre of mass — [MOYO project page](https://moyo.is.tue.mpg.de/).
- Mocap hardware is **Vicon**: the toolkit ships "Raw marker data from Vicon" and "Center of mass data from Vicon plug-in gait", with a Nexus folder used by the CoM evaluation — [moyo_toolkit README](https://github.com/sha2nkt/moyo_toolkit).
- SMPL-X parameters were "recovered from the MoCap data using MoSh++"; AMASS later released "high-quality SMPL-H and SMPL-X body parameters of the MOYO dataset" — [3D Human Pose Estimation via Intuitive Physics (CVPR 2023 supplement)](https://openaccess.thecvf.com/content/CVPR2023/supplemental/Tripathi_3D_Human_Pose_CVPR_2023_supplemental.pdf); [AMASS news](https://amass.is.tue.mpg.de/news.html).
- AMASS release dates: **2024-01-16** — SMPL-H and SMPL-X parameters for "complex yoga motions performed by a professional yoga instructor… the most extreme poses in AMASS yet"; **2024-11-08** — motions from the **Nexus subset** that were missing from the previous release were added, plus "motions from a private test set", plus mp4 renders of all MOYO sequences — [AMASS news](https://amass.is.tue.mpg.de/news.html).
- **Verified split-relevant identifiers** (exact keys from `moyo/utils/constants.py`, value = frame count of that take):
  - `'Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-a': 224`
  - `'Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-b': 227`
  - `'Standing_big_toe_hold_pose_or_Utthita_Padangusthasana-c': 333` (plus `-a`, `-b` takes, confirmed in the download URL lists)
  - `'Bound_Angle_Pose_or_Baddha_Konasana_-d': 481`, `-e: 438`, `-f: 160` (plus `-b`, `-c`, `-g` in the URL lists) — straddle / maximal hip abduction, seated
  - `'Supta_Baddha_Konasana_-a': 563` — supine straddle
  - `'Low_Lunge_pose_or_Anjaneyasana_-b': 432`, `-c: 260`, `-d: 380` — deep front-back leg separation, the mechanical precursor to a front split
  - `'Firefly_Pose_or_Tittibhasana_-b': 649`, `-a: 788`, `-c: 832` (listed as Feathered Peacock/Pincha Mayurasana in the same block; Tittibhasana takes appear in the download URL lists) — wide straddle with arm support
  - also present: `Half_Moon_Pose_or_Ardha_Chandrasana_-b`, `Heron_Pose_or_Krounchasana_-b`, `Warrior_III_Pose_or_Virabhadrasana_III_-b`, `Warrior_II_Pose_or_Virabhadrasana_II_-a`, `viparita_virabhadrasana_or_reverse_warrior_pose-a/-b`, `Boat_Pose_or_Paripurna_Navasana_-b/-c/-d/-e/-f`, `Supported_Headstand_pose_or_Salamba_Sirsasana_-a/-b`, `Supta_Virasana_Vajrasana-a`
  — all quoted from [`moyo/utils/constants.py`](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/utils/constants.py) and the matching download URL manifests [`moyo/bash/assets/urls/images_train.txt`](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/bash/assets/urls/images_train.txt) / [`images_val.txt`](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/bash/assets/urls/images_val.txt), retrieved via GitHub code search at commit `2f1342e`.
- **Negative result, searched explicitly:** GitHub code search over `sha2nkt/moyo_toolkit` for `Splits`, and over `moyo/utils` for `Monkey OR Hanumanasana OR Samakonasana OR Upavistha OR Visvamitrasana`, returned **zero pose-name matches** (the only `Splits` hit is the Python string method `img_name.split('_')` in `moyo/scripts/ioi_vicon_frame_sync.py`). So the released MOYO pose set has **no floor split**.
- File naming convention, from the toolkit's own example asset `220923_yogi_body_hands_03596_Tree_Pose_or_Vrksasana_-a_YOGI_Cam_01_0190_markers.png`: `<YYMMDD>_yogi_<body|nexus_body>_hands_<subjectID 03596>_<Pose_Name>_-<take letter>_YOGI_Cam_<nn>_<frame>_<kind>` — [moyo_toolkit README](https://github.com/sha2nkt/moyo_toolkit).
- Subject ID is constant (`03596`) across all sequences, and the evaluation code skips a file named `female_stagei` — [moyo/eval/pressure_map_evaluation.py](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/eval/pressure_map_evaluation.py). One pose is damaged: `'Cow_Face_Pose_or_Gomukhasana_-c'` is skipped because "pressure mat was broken for this pose".
- Two capture blocks exist: `20220923_20220926_with_hands/` (SMPL-X fits with hand markers) and `20221004_with_com/` (no hand markers, includes centre-of-mass data) — [moyo_toolkit README](https://github.com/sha2nkt/moyo_toolkit).
- **Download route:** register at [moyo.is.tue.mpg.de](https://moyo.is.tue.mpg.de/), then `bash ./moyo/bash/download_moyo.sh -o ./data/ -u`; `-i` adds images, `-d` deletes zips after unzip, and `-a` fetches **AMASS format** with a choice of `SMPLH_FEMALE`, `SMPLH_NEUTRAL`, `SMPLX_FEMALE`, `SMPLX_NEUTRAL` — [moyo_toolkit README](https://github.com/sha2nkt/moyo_toolkit). AMASS-format data was released 2023-12-23. Toolkit needs Python 3.9.
- **Licence:** download requires registration and acceptance of the License Agreement; terms live at moyo.is.tue.mpg.de/license.html; commercial licensing is a separate channel (ps-licensing@tue.mpg.de), i.e. the free licence is research/non-commercial — [IPMAN project page](https://ipman.is.tue.mpg.de/); [moyo_toolkit README](https://github.com/sha2nkt/moyo_toolkit).
- The MOYO pose selection was drawn from Yoga-82: "the selected poses include all poses in the Yoga-82 dataset as well as their variations" — [CVPR 2023 supplement](https://openaccess.thecvf.com/content/CVPR2023/supplemental/Tripathi_3D_Human_Pose_CVPR_2023_supplemental.pdf).

### Cited Findings — Fit3D (negative for splits) **[MARKER]**
- Capture rig: "12 VICON motion cameras, synchronized with 4 calibrated RGB cameras"; **13 subjects** = one licensed fitness instructor + 12 trainees; ground truth is **GHUM** with SMPL-X pseudo-ground-truth derived by conversion — [Hitting the Gym with Fit3D (PMC13413046)](https://pmc.ncbi.nlm.nih.gov/articles/PMC13413046/).
- Dataset size: "611 multi-view sequences… and 2,964,236 ground truth 3D skeletons with GHUM and SMPL-X parameters" — [SMPLest-X](https://arxiv.org/html/2501.09782v1).
- Exercise count is inconsistent across sources: the AIFit paper says **37** ("simple and compound motions, covering all major muscle groups… warm-ups, barbells, dumbbells, push-ups, or yoga"); a later paper says **47** — [AIFit, CVPR 2021](https://mihaifieraru.github.io/publication/fieraru_2021_cvpr/Fieraru_2021_CVPR.pdf); [PMC13413046](https://pmc.ncbi.nlm.nih.gov/articles/PMC13413046/).
- **Exercise names I could actually verify** (the full 37/47 list is not published in the sources I reached): push-up, squat, dumbbell biceps raise, burpees, clean and press, **mule kick**, man maker, dumbbell biceps curls, dumbbell hammer curls, and numbered items "warm-up 1, 6, 8, 14, 18, 19" — [PMC13413046](https://pmc.ncbi.nlm.nih.gov/articles/PMC13413046/).
- **No split and no high kick are named.** "Mule kick" is described in the paper's Figure 4 as an inverted handstand-kick — [PMC13413046](https://pmc.ncbi.nlm.nih.gov/articles/PMC13413046/).
- Access: non-commercial research licence through the IMAR Fit3D resource at [fit3d.imar.ro](https://fit3d.imar.ro/); "restrictions apply and access requires IMAR permission", with registration/login on the site — [PMC13413046](https://pmc.ncbi.nlm.nih.gov/articles/PMC13413046/); [Fit3D home](https://fit3d.imar.ro/). The project page itself names no exercises and states no frame rate.
- The original Fit3D announcement: "3mil. images with corresponding ground truth 3d human shape and pose… one instructor and 12 trainees, each performing over 37 fitness exercises" — [Mihai Fieraru, 2021](https://twitter.com/mihaifieraru/status/1406972720043200517).

### Cited Findings — Yoga pose datasets (3D vs image-only)
- **Yoga-82 is image-only, no 3D**: "over 28.4K yoga pose images distributed among 82 classes", "downloaded from web using the Bing search engine", 64–1133 images per class (avg 347), with a 3-level hierarchy of 6 / 20 / 82 classes — [Yoga-82 paper](https://openaccess.thecvf.com/content_CVPRW_2020/papers/w70/Verma_Yoga-82_A_New_Dataset_for_Fine-Grained_Classification_of_Human_Poses_CVPRW_2020_paper.pdf). **[IMAGE-ONLY]**
- Yoga-82 **does** have a class literally named **"Split pose"** — the distributed `yoga_dataset_links/Split pose.txt` URL list is mirrored verbatim in many repos and its entries are Hanumanasana / monkey-king split photographs (e.g. `Split pose/2_197.jpg → .../Hanumanasana.jpg`) — [Yoga-82 links mirror](https://github.com/im-vvk/yogGURU/blob/master/data/Yoga-82/yoga_dataset_links/Split%20pose.txt). This is 2D web imagery only; useful as pose reference, not as motion.
- Naming cross-reference for searching other catalogues: Hanumanasana = "Monkey Pose (Front Splits)" / "Full Splits"; Samakonasana = "Side splits"; Supta Trivikramasana = "Supine front splits"; Eka Pada Koundinyasana II = "Flying Splits"; Ardha Hanumanasana = half split — [asanakit pose yaml](https://github.com/franzenzenhofer/asanakit/blob/main/poses/yoga/seated/hanumanasana.pose.yaml); [asana index](https://github.com/deanturpin/germs.dev/blob/main/docs/asana.md); [Wikipedia, Hanumanasana](https://en.wikipedia.org/wiki/Hanumanasana).

### Inferences
- There is a **direct contradiction worth acting on**: the CVPR'23 supplement claims MOYO covers "all poses in Yoga-82", Yoga-82 contains a class named "Split pose" (Hanumanasana), yet no split pose key exists in the released MOYO manifest. Either the floor split was dropped during capture, or it sits in the **"private test set" added to AMASS on 2024-11-08**, whose pose names are not in the public toolkit snapshot. Downloading the current AMASS MOYO package and listing its `.npz` filenames is the cheap way to settle this — the repo's `constants.py` predates that release.
- `Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-a/-b` is a near-exact match for the brief's "vertical standing leg hold (gymnastics-style, leg raised overhead) from which she strikes": a standing split is a 180° vertical leg extension on one supporting leg. At 224/227 frames each take, there is enough material for an entry, a hold and an exit — though whether the take actually captures the *transition in and out* (rather than only the held pose) is unverified.
- `Low_Lunge_pose_or_Anjaneyasana_-b/-c/-d` (432/260/380 frames) + `Bound_Angle_Pose_or_Baddha_Konasana_*` give real captured hip-abduction and front-back-split-direction extremes; combined with the standing split these are the raw parts from which a floor split can be *assembled from real capture* rather than hand-animated.
- Fit3D should be treated as out of scope for this task: marker-quality and SMPL-X, but no flexibility content.

### Gaps
- **MOYO frame rate is not stated** on the project page, the toolkit README, or the AMASS news page. Vicon lab capture is typically 60–120 Hz and AMASS npz carries `mocap_framerate`; must be read from the files. Unverified.
- MOYO subject gender is not stated in text anywhere I reached; the `female_stagei` MoSh file name and the `SMPLX_FEMALE` download option are strong but indirect evidence of a single female subject. Treat "female professional yoga instructor" as inferred, not quoted.
- The complete MOYO 200-pose list was not retrieved (the repo is not attachable to this session, so `constants.py` was only sampled via code search). The full list is in `moyo/utils/constants.py` and in the two `images_train.txt` / `images_val.txt` URL manifests — read them directly for the definitive index.
- The full Fit3D 37/47 exercise names could not be obtained: the CVPR open-access PDF returned HTTP 403 and the project page lists none.
- ASPset-510, SportsCap/SMART, MM-Fit, and biomechanics-lab releases of splits/extreme hip abduction were not investigated within budget — no claim either way. Note that ASPset-510 and SportsCap are video-derived by construction.

---

## Q3. Motion-X / Motion-X++ — text-searchable? marker or video-estimated?

### Takeaway
Motion-X **is** text-searchable, but only after you download it: sequence-level semantic labels ship as plain text files under `texts/semantic_labels/<subset>/`. Critically, **most of Motion-X is video-estimated**, not marker mocap — only the AMASS, GRAB and EgoBody subsets are real mocap, and those you must fetch from their own sites anyway. So Motion-X adds text search over motion you already own (AMASS) plus a large volume of pseudo-GT.

### Cited Findings
- Subset table (clips / frames) as published: AMASS 26K / 5.4M **[MARKER]**; EgoBody 1.0K / 0.4M **[MARKER]**; GRAB 1.3K / 0.4M **[MARKER]**; IDEA400 12.5K / 2.6M (explicitly "monocular", non-mocap) **[VIDEO]**; AIST++ 1.4K / 0.3M; HAA500 5.2K / 0.3M; HuMMan 0.7K / 0.1M; BAUM 1.4K / 0.2M; Online Videos 32.5K / 6.0M; total **81.1K clips / 15.6M frames** — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X).
- The repo places AMASS, GRAB and EgoBody in a mocap section and IDEA400 in a non-mocap/monocular section; the remaining subsets sit outside the mocap section but are not labelled — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X). (That the rest are video-derived is an inference from placement, not a quoted statement.)
- "All motions have been unified in **30 fps**" and are stored as 322-dimensional `smplx_322` arrays (root orient, body pose, hand pose, jaw pose, expression, face shape, translation, betas) — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X). **This matches the project's 30 fps retarget target exactly.**
- Text labels: sequence-level semantic labels live in `texts/semantic_labels/<subset>/` and are loaded with `np.loadtxt`; frame-level body/hand/face descriptions are in separate folders — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X). So grepping for "split", "high kick", "splits" across that folder after download is the concrete search route. No pre-download searchable index of the labels was found.
- Access: a **request form for non-commercial use**, then an email with download links; pose texts are on Google Drive and some zips moved to Baidu Disk; AMASS and GRAB require registering on their own sites separately. Licence documents: a Motion-X License PDF plus the IDEA400 licence and the AMASS/GRAB/EgoBody licence pages — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X).
- **Motion-X++** scale: "19.5M 3D whole-body pose annotations covering 120.5K motion sequences… 80.8K RGB videos, 45.3K audios, 19.5M frame-level whole-body pose descriptions, and 120.5K sequence-level semantic labels", with poses obtained by optimising SMPL-X parameters — [Motion-X++ paper](https://arxiv.org/html/2501.05098v1).
- Motion-X++ release status: "only IDEA400 has been released so far, with other subsets to follow"; it is hosted on HuggingFace at [YuhongZhang/Motion-Xplusplus](https://huggingface.co/datasets/YuhongZhang/Motion-Xplusplus) — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X).
- The **martial-arts evidence is a text label, not a subset**: the HF card's example sequence-level description reads "The person is performing a series of martial arts moves consistent with Kung Fu, incorporating an aerial kick". Action descriptions are generated by **GPT-4V**, and pose descriptions by **PoseScript** from body/hand joints — [HuggingFace Motion-Xplusplus card](https://huggingface.co/datasets/YuhongZhang/Motion-Xplusplus). A review notes the videos "include specialized motions like dancing or martial arts" — [Moonlight review](https://www.themoonlight.io/en/review/motion-x-a-large-scale-multimodal-3d-whole-body-human-motion-dataset).
- The Motion-X repo **names no martial-arts, kungfu or dance subset** and describes no subset's content; AIST++ is the only dance-suggesting name present — [Motion-X GitHub](https://github.com/IDEA-Research/Motion-X).

### Inferences
- Quality warning for this project: the "martial arts with aerial kick" material sits in the Online Videos / HAA500 / IDEA400 side of the corpus, i.e. **monocular or multi-view video-estimated SMPL-X**. For a Tekken-8-bar fighting game that is reference material at best — foot sliding, depth ambiguity and limb-length drift are expected. The marker-quality slice of Motion-X is just AMASS + GRAB + EgoBody, which the project substantially already has.
- The practical use of Motion-X here is as a **search index over AMASS**: grep `texts/semantic_labels/AMASS/` for split/kick/leg terms to locate marker sequences (including MOYO's standing split) without rendering 26K clips.
- Labels are GPT-4V-generated, so they are approximate: a true split may be described as "a wide-legged floor pose" and never contain the word "split". Grep should use a wide vocabulary (split, splits, straddle, legs apart, high kick, leg raised, vertical, écart).

### Gaps
- Whether the semantic labels are distributed as a single index/JSONL (vs one file per clip) is not stated in the repo README; unverified.
- Exact Motion-X licence terms (the linked PDF) were not read; only "for non-commercial purposes" is quoted.
- Whether AIST++/HAA500/HuMMan/BAUM are formally pseudo-GT is an inference from section placement; the repo does not use the term "pseudo-GT".

---

## Q4. Commercial-but-cheap or free game-asset animation libraries containing a split or extreme flexibility

### Takeaway
**I could not confirm a split animation in any of Mixamo, ActorCore, Rokoko's free library or Truebones.** Mixamo in particular returned nothing for split/flexibility; the nearest verified assets are parkour/acrobatics (ActorCore Parkour, paid) and kick clips in a non-Mixamo library (Meshy). All of these are **[MARKER]** or stunt-actor capture, so quality would be adequate — the content simply does not appear to exist under that name.

### Cited Findings
- **Mixamo:** searching for split/flexibility/high-kick clips surfaced nothing; a mocap vendor's comparison says Mixamo has only a small set of generic melee and kick animations, naming no clips — [Mixamo Alternatives, mocaponline](https://mocaponline.com/blogs/mocap-news/mixamo-alternatives-character-animations).
- A HuggingFace snapshot claims to be the complete Mixamo library — **2,317 motion clips and 114 rigged characters** — which makes an offline name-level search for "split"/"kick" feasible — [tanish434/Mixamo-Animations-Characters](https://huggingface.co/datasets/tanish434/Mixamo-Animations-Characters); mirrored at [Linzhan/Mixamo-Animations-Characters](https://huggingface.co/datasets/Linzhan/Mixamo-Animations-Characters). I did not verify the clip names in it.
- **Meshy's free animation library (631 animations, not Mixamo)** lists "High Kick", "Roundhouse Kick", "Spin Kick" and "Spartan Kick" — [Meshy animation library](https://www.meshy.ai/animation-library). No split listed.
- **ActorCore (Reallusion)** — Studio Mocap Parkour pack, **$129**: "well-known artistic-gymnastic maneuvers like rolling, climbing, vaulting, flipping", specifically 360 backflips, back rolls, aerials, cat vaults, palmspin jumps, "captured by professional stunt actors" — [ActorCore Parkour](https://actorcore.reallusion.com/3d-motion/pack/studio-mocap-parkour?info=1). A hand-to-hand combat pack has "over 60 polished, professionally choreographed fight animations" — [Reallusion Magazine](https://magazine.reallusion.com/2025/05/12/animate-epic-fight-scenes-with-actorcore-and-metahumans/). **No splits asset found.**
- ActorCore export: "After purchase, you can choose between **60, 30, or 24 fps** to download" — [ActorCore FAQ](https://actorcore.reallusion.com/learn-and-support/faq/content). (30 fps matches the project pipeline directly.)
- **Rokoko** free dance pack: **6 clips**, tech specs "**FBX file format, Mixamo skeleton, ready for retargeting, 30 FPS**" — [Rokoko 6 free dance animations](https://www.rokoko.com/resources/rokoko-mocap-6-free-dance-animations). Also free packs of 6 martial arts, 12 sports and 10 idle animations — [Rokoko free resources](https://www.rokoko.com/free-resources).
- Rokoko Motion Library: "**150 of the moves are available for free**", the rest at $3 or $6 each; a free Basic subscription "enables users to export data in **FBX, BVH or CSV** format"; the free moves "are licensed for use in commercial projects" — [CGChannel](https://www.cgchannel.com/2020/03/get-150-free-mocap-moves-from-rokokos-motion-library/); [Rokoko Studio Motion Library docs](https://support.rokoko.com/hc/en-us/articles/4410021327121-Getting-Started-Rokoko-Studio-Motion-Library). No splits or gymnastics clip confirmed.
- **Truebones** "Motherlode": "over 30,000 searchable motion captures" in BVH and FBX, covering genres including "Sports, Dances, Fights" — [Truebones Motherlode](https://truebones.gumroad.com/l/xfsub). Also a Just Dance pack of "OVER 300 FBX/BVH MOCAP Files" — [Just Dance pak](https://truebones.gumroad.com/l/xdrwU) — and a free 500-file pack — [FREE 500 Pak](https://truebones.gumroad.com/p/free-500-pak-only-from-truebones). Truebones's ballet offering is an **iClone-format** pack, not BVH. **No pack titled splits or gymnastics was found.**

### Inferences
- The absence of a "split" clip across four large commercial libraries is itself a signal: splits are a rare, performer-specific capture (a mocap actor who can do a full split is uncommon), which is consistent with the CMU/SFU negative and with MOYO being the one place a trained-flexibility performer was captured.
- The single highest-yield cheap check remaining is the Truebones Motherlode, since it is explicitly described as *searchable* and spans 30,000 clips including dance and fight genres — but it must be bought or its index obtained before the question can be answered.
- Rokoko's Mixamo-skeleton, 30 fps FBX output is the cleanest fit for the project's retarget pipeline of any commercial source here.

### Gaps
- I could not search Mixamo's live catalogue (it is behind an Adobe login/JS app), so "Mixamo has no split" is **unconfirmed negative**, not a verified absence. The HF 2,317-clip snapshot is the way to settle it offline.
- ActorCore's store search for "split" was not reachable; also unconfirmed.
- UE Marketplace / Fab free monthly animation packs and Quaternius / mocapdata.com / motioncapturedata.com were not checked within budget.

---

## Q5. Open taekwondo / capoeira / wushu / MMA datasets with 3D joints and a FEMALE performer

### Takeaway
**No open martial-arts dataset with 3D joints and a confirmed female performer was found.** The closest is TUAD-3D (taekwondo poomsae), which is IMU-suit capture with 23 joints and no published sex breakdown or confirmed public download. Capoeira, wushu and MMA returned nothing usable.

### Cited Findings
- **TUAD-3D** (Luo et al., *Sensors* 2023): "A 3D skeletal dataset comprising **16 unit actions** in Taekwondo poomsae was constructed using motion data collected by employing full-body motion-capture suits", with **40 Taekwondo experts** participating, and "the skeletons were processed to extract the positions of **23 joints**" — [Viewpoint-Agnostic Taekwondo Action Recognition (PMC10575175)](https://pmc.ncbi.nlm.nih.gov/articles/PMC10575175/), doi [10.3390/s23198049](https://doi.org/10.3390/s23198049). **[IMU]** Sex breakdown and public availability are not reported in the sources reached.
- **TUHAD** and the time-warped-motion-profile taekwondo work use "joint-motion data acquired via wearable **inertial measurement unit (IMU)** sensors", i.e. not 3D joint positions — [PMC11055144](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11055144/); [TUHAD](https://www.researchgate.net/publication/343957165_TUHAD_Taekwondo_Unit_Technique_Human_Action_Dataset_with_Key_Frame-Based_CNN_Action_Recognition). **[IMU]**
- **Human3.6M** is the generic fallback with a known gender split: "5 females and 6 males" with 3D human poses and corresponding images — [survey citation](https://arxiv.org/pdf/2203.01593). **[MARKER]** Not martial arts, and S9/S11 etc. contain no flexibility content I can cite.
- **SportsPose** is a dynamic 3D sports pose dataset, but its sports are not martial arts — [SportsPose](https://arxiv.org/pdf/2304.01865). **[VIDEO]**
- A validation dataset with synchronised video, marker mocap and force plates exists for markerless-analysis validation, but its movements are gait/clinical, not martial arts — [Nature Sci Data 2024](https://www.nature.com/articles/s41597-024-04077-3). **[MARKER]**

### Inferences
- The project's "strike sources are mostly male" problem is not solvable from open martial-arts datasets as of Oct 2026. The realistic routes are (a) MOYO's single female performer for flexibility/leg-extension, which *is* female marker capture, (b) DanceDB's female contemporary dancers for leg extension and whole-body carriage, and (c) emailing the TUAD-3D authors at Konkuk University for a data-availability answer and a sex breakdown.
- Rokoko's 6 free martial-arts clips are the only female-possible commercial martial-arts source found, but performer sex is not stated on the page.

### Gaps
- TUAD-3D download route, licence, format and performer sex: unanswered. The paper's data-availability statement would settle it; I could not read it within budget.
- Capoeira and wushu: no dataset found at all, open or otherwise. This is a genuine absence in the search results, not a budget gap.

---

## Q6. If no real capture of a split is downloadable anywhere — the plain answer and the nearest substitutes

### Takeaway
**A real captured split IS downloadable — but only the vertical/standing kind, and only from one dataset.** MOYO's `Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-a/-b` is genuine Vicon marker capture of a female performer in a 180° vertical leg extension, available as SMPL-X/SMPL-H via AMASS under a free non-commercial registration. A **floor split (front or side) could not be found in any downloadable real capture** — not in CMU/SFU (given), not in MOYO's released pose list, not in Fit3D, and not in any commercial library I could verify.

### Cited Findings — the ranked substitute list, with identifiers
All MOYO identifiers below are exact keys from [`moyo/utils/constants.py`](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/utils/constants.py) / the [download manifests](https://github.com/sha2nkt/moyo_toolkit/blob/main/moyo/bash/assets/urls/images_train.txt); dataset-level facts from [MOYO](https://moyo.is.tue.mpg.de/) and [AMASS news](https://amass.is.tue.mpg.de/news.html). All **[MARKER]**, Vicon, single female subject `03596`, SMPL-X/SMPL-H, non-commercial registration.

1. **The signature vertical leg hold — direct hit, use as-is:**
   `Standing_Split_pose_or_Urdhva_Prasarita_Eka_Padasana_-a` (224 frames), `-b` (227 frames).
2. **Leg raised and held to the front/side, supporting leg straight — the "strike from the hold" source:**
   `Standing_big_toe_hold_pose_or_Utthita_Padangusthasana-a / -b / -c` (`-c` = 333 frames).
3. **Maximal hip abduction / straddle (the side-split direction, seated):**
   `Bound_Angle_Pose_or_Baddha_Konasana_-b / -c / -d (481) / -e (438) / -f (160) / -g`; supine variant `Supta_Baddha_Konasana_-a` (563).
4. **Front-back leg separation (the front-split direction, kneeling/lunging):**
   `Low_Lunge_pose_or_Anjaneyasana_-b (432) / -c (260) / -d (380)`.
5. **Wide straddle with arm support (floor contact at extreme abduction):**
   `Firefly_Pose_or_Tittibhasana_-b` and the `Feathered_Peacock_Pose_or_Pincha_Mayurasana_-a (788) / -b (649) / -c (832)` block.
6. **One-leg balance with horizontal extension (arabesque analogues, for the hold-to-strike transition):**
   `Warrior_III_Pose_or_Virabhadrasana_III_-b`, `Half_Moon_Pose_or_Ardha_Chandrasana_-b`, `Heron_Pose_or_Krounchasana_-b`.
7. **Already on disk, per the brief:** CMU `49_09 side arabesque`, `49_18`–`49_20 balance on one leg` remain the best in-house vertical-leg holds and should be blended with MOYO item 1 rather than replaced by it.
8. **Pose-only visual reference for a true floor split (no 3D):** the Yoga-82 class **"Split pose"** URL list — [Split pose.txt](https://github.com/im-vvk/yogGURU/blob/master/data/Yoga-82/yoga_dataset_links/Split%20pose.txt) — Hanumanasana photographs, 2D web images, useful for silhouette/keying a hand-built floor split.

- Why the assembly route is viable: MOYO sequences are **long holds** (160–911 frames per take), so each contains a settled extreme pose rather than a single frame — enough for pose extraction, and plausibly enough for the entry/exit transition.
- MOYO also ships **pressure-mat and centre-of-mass ground truth** — [MOYO](https://moyo.is.tue.mpg.de/) — which is unusual and directly useful for making a drop-into-split land with believable weight transfer.
- mp4 renders of **all** MOYO sequences were added to AMASS on **2024-11-08** — [AMASS news](https://amass.is.tue.mpg.de/news.html) — so the whole 200-pose set can be triaged visually without downloading the 1.75M-frame image set.

### Inferences
- Recommended build for the signature move: take the **standing split** (MOYO item 1) for the vertical hold and the strike-from-hold; take the **drop** by blending `Low_Lunge_pose_or_Anjaneyasana` (front-back separation) into `Bound_Angle/Baddha_Konasana` (abduction) for the floor contact; take the **rise** by time-reversing the standing-split entry. Every frame then derives from real female marker capture, with only the interpolation hand-authored — which satisfies "assembled from real captured parts rather than generated".
- Pipeline fit: Motion-X's 30 fps unification matches the project's 30 fps target, but MOYO/AMASS native rate is unknown and likely 60–120 Hz, so a resample step is required; AMASS is SMPL-X/SMPL-H joint-angle data (no 138-bone UE rig), so a SMPL-X → UE-style retarget is needed, the same path the project already uses for AMASS-family data.
- Licence fit: MOYO (research/non-commercial, registration), Fit3D (non-commercial, IMAR permission), Motion-X (non-commercial, request form), DanceDB (acknowledgement) are all compatible with the project's stated non-commercial positioning; none is ND. Rokoko's 150 free moves are the only source verified as commercially licensed.

### Gaps
- **Unresolved and worth one follow-up:** whether a floor split (Hanumanasana / Samakonasana) exists in the MOYO "private test set" added to AMASS on 2024-11-08 but absent from the public toolkit's `constants.py`. Settle by listing the `.npz` names in the current AMASS MOYO download.
- MOYO native capture frame rate: not published; read `mocap_framerate` from the npz files.
- Whether the MOYO standing-split takes include the rise/descent transition or only the held pose: not verifiable from metadata; check the mp4 renders.
- Mixamo and ActorCore catalogue searches for "split" were blocked by their web apps, so those negatives are unconfirmed.
- Exact MOYO joint/skeleton topology beyond "SMPL-X (and SMPL converted from SMPL-X)" and "raw Vicon markers": the marker layout name is not stated.
