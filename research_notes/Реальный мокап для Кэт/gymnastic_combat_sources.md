# Real mocap sources for gymnastic / acrobatic female combat moves (state as of 2026-10-27)

Scope note: every identifier below was read off a primary source or a documented mirror. Where a move group
(split, vertical leg hold, true gymnastic tumbling) could **not** be verified in any public dataset, it is
flagged explicitly rather than assumed.

Important practical finding up front: **`mocap.cs.cmu.edu` returned HTTP 503 on every request during this
research session (2026-10-27)**, for `search.php`, `motcat.php` and `subjects.php` alike, and
`web.archive.org` was not reachable from this environment. All CMU trial identifiers below therefore come
from the **cgspeed BVH conversion motions list**, which is a verbatim mirror of the CMU per-subject index —
[cgspeed BVH Conversion Release motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list).
Treat the CMU site as temporarily down, not gone; re-check it before concluding a trial does not exist.

## CMU Motion Capture Database: martial arts, acrobatics, gymnastics, rolls, splits, kicks

### Takeaway
CMU is the single richest free source for this request and contains one **female fighting subject (144,
"punching female")** plus five acrobatic subjects (85, 87, 88, 89, 90) and a Shotokan karate subject (135)
with named kata and named kicks. CMU contains **no split at all** (keyword-verified) and **no verified
vertical leg hold** — the closest is the modern-dance subject 49's "side arabesque" trials.

### Cited Findings

Subject-level headings, verbatim — [cgspeed motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list):
- `Subject #144 (punching female)` — the only CMU subject whose heading combines a female performer with striking.
- `Subject #135 (Martial Arts Walks)`, `Subject #106 (Female General Subject)`, `Subject #85 (jumps; flips; breakdance)`, `Subject #90 (cartwheels; acrobatics; dances)`, `Subject #49 (modern dance, gymnastics)`, `Subject #5 (modern dance)`, `Subject #86 (sports and various activities)`, `Subject #111 (Pregnant Woman)`, `Subject #113 (Post pregnant woman)`, `Subject #114 (Pregnant Woman)`, `Subject #93 (Charleston Dance)`, `Subject #94 (indian dance)`, `Subject #103 (Charelston Dancing)`, `Subject #118 (Jumping)`, `Subject #141 / #143 (General Subject Capture)`.
- Subject-level summary cross-check (same descriptions, independent mirror): "Martial arts walks: 135", "Modern dance, gymnastics: 49", "Acrobatics: 87, 88, 89", "Cartwheels; acrobatics; dances: 90", "Jumps; flips; breakdance: 85", "Kicks and walking on slopes: 74", "Action adventure obstacles, running, jumping, ducking, rolling: 127", "...rolling, stopping: 128" — [Shriinivas/cmubvh README](https://raw.githubusercontent.com/Shriinivas/cmubvh/master/README.md).

**Subject 144 — "punching female" (34 trials), the headline find.** Verbatim trial list — [cgspeed motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list):
- `144_01 Cartwheels`, `144_02 Cartwheels001`
- `144_05 Front_Kicking`, `144_06 Front_Kicking001`, `144_09 Left_Front_Kicking`, `144_10 Left_Front_Kicking001`
- `144_07 Left_Blocks`, `144_08 Left_Blocks001`, `144_26 Right_Blocks`, `144_27 Right_Blocks001`
- `144_13 Left_Punch_Sequence001`, `144_14 Left_Punch_Sequence002`, `144_20 Punch_Sequence`, `144_21 Punch_Sequence001`
- `144_15 Left_Spin_reach001`, `144_16 Left_Spin_reach002`, `144_28 Right_Spin_reach`, `144_29 Right_Spin_reach001`
- `144_11 Left_Lunges`, `144_12 Left_Lunges001`, `144_17 Lunges`, `144_18 Lunges001`
- `144_30 Sun Salutation`, `144_31 Sun Salutation001`, `144_32 Sun Salutation002`
- `144_03/04 Figure8s`, `144_19 motorcycle pose` (T-pose equivalent / calibration), `144_22–25 Reach_Left/Right`, `144_33/34 Walking`

**Subject 135 — "Martial Arts Walks", actually Shotokan karate kata and named kicks** (11 trials), verbatim:
`135_01 Bassai`, `135_02 Empi`, `135_03 Empi`, `135_04 Front Kick`, `135_05 Gedanbarai`, `135_06 Heiansyodan`,
`135_07 Mawashigeri`, `135_08 Motorcycle`, `135_09 Oiduki`, `135_10 Syutouuke`, `135_11 Yokogeri` —
[cgspeed motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list).
(`Mawashigeri` = roundhouse kick, `Yokogeri` = side kick, `Gedanbarai` = low block, `Oiduki` = lunge punch,
`Syutouuke` = knife-hand block, `Heiansyodan`/`Bassai`/`Empi` = kata.)

**Acrobatics / tumbling subjects**, verbatim trial lists — same source:
- Subject 90 (36 trials): `90_01 bkwd summersult`, `90_02/03/04 cartwheel`, `90_05/06/07 jump kick`, `90_08 side flip`, `90_09 Flip forward onto hands and back again`, `90_11 hand spring`, `90_12/13 bck flp twst fall`, `90_14/15 front hand flip`, `90_16 fall on face`, `90_17 BannanaPeelSlip`, `90_18 RugPullFall`, `90_19 monkey backflip`, `90_20/21 monkey sequence`, `90_28 breakdance`, `90_29 sequesnce`, `90_30/31 russian dance`, `90_32 moonwalk`, **`90_33 arm up wide leg roll`, `90_34/35/36 wide leg roll`**, `90_22/23 straight walk`, `90_24 ball mount`, `90_25/26 fwd ball walk`, `90_27 bwk ball walk`, `90_10` (no description, listed as `90_10.amc`).
- Subject 88 (11 trials): `88_01 backflip`, `88_02 backflips, jump onto platform, handstands, vertical pushups`, `88_03 motorcycle pose`, **`88_04 stretches, cartwheels, flips, spin kicks, spins, and fall`**, `88_05 cartwheel into backflip`, **`88_06 jump and spin kick`**, `88_07 cartwheel`, `88_08 crouch and flip backward on hands`, `88_09 stretch and cartwheel`, `88_10 stretch and spin`, `88_11 stretches and jumps`.
- Subject 87 (5 trials): **`87_01 Jump with kick and spin`**, `87_02 T-Pose`, `87_03 Backflip`, `87_04 backflip`, `87_05 cartwheels`.
- Subject 89 (6 trials): `89_01 balance object on forehead`, `89_02 motorcycle pose`, `89_03 flip and stand on one hand`, `89_04 spins, flips, stand on one hand`, `89_05 spin upside down, handstand, flips`, `89_06 balance beam`.
- Subject 85 (15 trials, breakdance): `85_01/02 JumpTwist`, `85_03 UpRightSequence`, `85_04 FancyFootWork`, **`85_05 HandStandKicks`**, **`85_06 KickFlip`**, `85_07 KickFlipStumble`, `85_08 Helicopter`, `85_09 motorcycle pose`, `85_10 EndofBreakDance`, `85_11 UpRightSequence`, `85_12 LongSequenceGood`, `85_13 BadStartSequnce`, `85_14 BreakSequencewithFlips`, `85_15 90TwistsFall`.
- Subject 49 (22 trials, modern dance + gymnastics): `49_06/07 cartwheel`, `49_08 cartwheels`, `49_04/05 run, leap`, **`49_09 dance - arms held high, side arabesque`**, `49_10/11/13/16 dance - fold in from side arabesque, curl inwards`, `49_12/14/17 dance - lean forward, bring back leg forward, arching arms`, `49_15 static dance pose - folded in, head lowered, leg bent`, **`49_18/19/20 balance on one leg, outstretched arms`**, `49_21 acrobatics - spin/twirl, hang on ropes`, `49_22 dance - lean back on bent leg, balance, bend elbow by ear`, `49_01 walk`, `49_02/03 jump up and down, hop on one foot`.

**Rolls specifically** (keyword scan of the whole index) — same source:
`90_33 arm up wide leg roll`, `90_34/35/36 wide leg roll`, `111_21 Roll over`, `127_23 Run Dive Over Roll Run`,
`127_24 Run Dive Over Roll Run`, `128_09 Run Roll Underneath`, `128_10/11 Run Dive Over Roll Run`.

**Kicks specifically** (keyword scan of the whole index) — same source:
`74_03/04/05/06 kick`, `75_16 jump kick`, `85_05 HandStandKicks`, `85_06 KickFlip`, `85_07 KickFlipStumble`,
`86_01 jumps kicks and punches`, `86_03`, `86_06 (knee kicking)`, `86_08`, `87_01`, `88_04 (spin kicks)`,
`88_06`, `90_05/06/07 jump kick`, `106_14 punch and kick (problem with feet data)`, `111_19 Punch Kick`,
`113_13 Punch and kick`, `135_04 Front Kick`, `141_14 Punch and Kick`, `143_24 Kicking`,
`144_05/06 Front_Kicking`, `144_09/10 Left_Front_Kicking`.

**Negative results, keyword-verified across the entire CMU index** — same source: the words
**karate, kung fu, capoeira, somersault (spelled correctly), split, gymnast, tumble appear in no trial
description**. "martial" appears only in subject 135's heading; "gymnastics" only in subject 49's heading;
"rolling" only in subject 127/128's headings.

Other CMU facts:
- Subject 143 (General Subject Capture) has `143_23 Punching`, `143_24 Kicking`, `143_30 Stretch And Yawn`, `143_31 Hopscotch`, `143_34 Chicken Dance`, `143_35 Macarena Dance` — [cgspeed motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list). Note this is a *different* subject from the project's existing `137_38`.
- CMU native format is ASF/AMC, not BVH: "the original CMU motion capture database isn't in BVH format - it's in ASF/AMC format" — [cgspeed ReadmeFirst, as quoted in search results](https://github.com/una-dinosauria/cmu-mocap/blob/master/READMEFIRST.txt).
- Licence: CMU's own usage terms, quoted in the cgspeed readme, state the data is "free for use in research and commercial projects worldwide"; for the BVH conversion, "CMU places no restrictions on the use of the original dataset, and [Bruce Hahne] placed no additional restrictions on this BVH conversion" — [cgspeed / search result summary](https://github.com/una-dinosauria/cmu-mocap/blob/master/READMEFIRST.txt). This is the most permissive licence of anything in this report.
- Mirrors that work while the CMU site is down: [Shriinivas/cmubvh](https://github.com/Shriinivas/cmubvh) (categorised zipped BVH, ~1.8 GB per a [Reallusion forum thread](https://forum.reallusion.com/Topic529542.aspx)); [una-dinosauria/cmu-mocap](https://github.com/una-dinosauria/cmu-mocap); [Kaggle CMU Mocap](https://www.kaggle.com/datasets/kmader/cmu-mocap); [HuggingFace ckinpdx/CMUMDB](https://huggingface.co/datasets/ckinpdx/CMUMDB) (states "BVH Conversions: cgspeed.com", licence cc-by-4.0); [HuggingFace gbionics/cmu-fbx](https://huggingface.co/datasets/gbionics/cmu-fbx); [outworldz BVH repack](https://www.outworldz.com/Secondlife/Posts/CMU/).
- An **FBX conversion of the whole CMU database** is archived with a DOI at 4TU.ResearchData, "compiled from the individual CMU index files by B. Hahne" — [data.4tu.nl dataset 0448aab2-3332-449f-a8e2-d208cb58c7df](https://data.4tu.nl/datasets/0448aab2-3332-449f-a8e2-d208cb58c7df) (the page itself returned 503 during this session, so licence and skeleton details are unverified).

### Inferences
- **Buy-list for this project from CMU**: for the "feminine performance read" requirement, subject **144** is the only verified female striking capture in CMU and should be the base layer (front kicks `144_05/06/09/10`, cartwheels `144_01/02`, punch sequences `144_13/14/20/21`, spin-reach `144_15/16/28/29`). Supplement with **135_07 (roundhouse)** and **135_11 (side kick)** for correct karate technique, **88_04/88_06 and 87_01 (spin kicks)** for spinning/jumping kicks, **90_05/06/07 (jump kick)**, and **127_23/24 + 128_09/10/11** for dive-rolls.
- Subject 49 reads strongly as a trained female modern dancer (side arabesque, one-leg balances, cartwheels), which would make `49_09` and `49_18–20` the best CMU candidates for a high leg extension — but CMU does not publish performer gender for subject 49, so this is an inference, not a fact.
- `90_33–36 "wide leg roll"` is the only CMU trial family whose description implies a legs-apart floor roll; it may be the nearest thing CMU has to a straddle/split-adjacent ground move. Needs visual inspection of the clip to confirm.

### Gaps
- **No CMU split exists.** Verified by keyword scan; the shopping list must get the шпагат from elsewhere.
- **No CMU vertical leg hold / développé / needle scale verified.** `49_09` (side arabesque) is a lateral/rear extension, not an overhead vertical hold.
- Gender is unpublished for subjects 49, 85, 87, 88, 89, 90 and 135, so the "female performer" requirement can only be met confidently by 144 (and 106/111/113/114, which are not acrobatic).
- Frame rate and skeleton could not be verified this session because `mocap.cs.cmu.edu` is 503. CMU's documented capture rate (120 Hz, 41-marker Vicon) is from prior knowledge and **must be re-verified**; the cgspeed BVH release is also commonly described as 120 fps with an optional downsampled variant — unverified here. Any 30 fps pipeline will need resampling either way.
- A commercial vendor claims the cgspeed BVH set "had no hand data and was improperly re-targeted to all formats" — [Truebones listing](https://truebones.gumroad.com/l/CPaSkd). This is a competitor's claim about a free product and was not independently verified, but it is a reason to prefer converting ASF/AMC yourself if finger/wrist fidelity matters.
- CMU subject 90 trial 29+ labels: a 2026 humanoid-control paper refers to a subject-90 "cartwheel and back handsprings" sequence under trial 29, whereas the cgspeed index lists `90_29` only as "sequesnce" — [OmniXtreme arXiv 2602.23843](https://arxiv.org/pdf/2602.23843). The index label is uninformative; the paper's description suggests `90_29` is worth inspecting.

## SFU Motion Capture Database: Kendo and Wushu confirmed

### Takeaway
Confirmed: SFU contains **Kendo (subject 0015) and Wushu kicks (subject 0017)**, plus cartwheels, parkour
rolls and vaults — but **every acrobatic and martial take is performed by a male subject**. The two female
subjects (0008, 0018) do yoga, cha-cha and Chinese dance, including a "bridge". BVH and FBX are offered
directly, which is the shortest path of any source here.

### Cited Findings
All from [mocap.cs.sfu.ca](https://mocap.cs.sfu.ca/) (8 subjects, ~70 takes):
- Martial arts takes: `0015_BasicKendo001` (basic Kendo movements), `0015_Kirikaeshi001` (Kendo Kirikaeshi), `0015_KendoKata001` (Kendo kata) — subject 0015 is **M, 18–25, 171 cm, 70 kg**.
- **`0017_WushuKicks001` (Wushu kicking motions)** — subject 0017 is **M, 18–25, 165 cm, 63 kg**. Same subject also has `0017_ParkourRoll001` (parkour roll from a kneeling start), `0017_JumpAndRoll001` (climb on, jump down, forward roll, 2x), `0017_MonkeyVault001`, `0017_SpeedVault001/002`, `0017_JumpingOnBench001`, `0017_RunningOnBench001/002`.
- Acrobatic takes on other subjects: `0007_Cartwheel001` (cartwheels; subject 0007 M, 18–25, 175 cm, 60 kg), `0012_JumpAndRoll001` (jump down from a platform then forward roll; M, 18–25, 174 cm, 74 kg), `0012_SpeedVault001/002`.
- Female subjects: **0008 (F, 25–30)** — `0008_Skipping001`, `0008_Walking001` (swaggering walk with poses), `0008_Walking002`, `0008_ChaCha001`, `0008_Yoga001`; **0018 (F, 18–25, 161 cm, 55 kg)** — `0018_Walking001`, `0018_Catwalk001`, `0018_TipToe001`, `0018_DanceTurns001` (on-the-spot dance spins), `0018_DanceTurns002` (Chinese dance turns in a vertical circle), `0018_Moonwalk001`, **`0018_Bridge001` (bridge dance)**, `0018_TraditionalChineseDance001`, `0018_XinJiang001/002/003` (Xinjiang dance).
- Formats: most takes offer **.bvh, .fbx, .c3d, .hdf and .txt**, plus a per-subject **.vsk** calibration file; .txt is an ASCII version of the c3d. The .bvh "contains the final skeleton and animation" — [mocap.cs.sfu.ca](https://mocap.cs.sfu.ca/).
- Download: relative links of the form `nusmocap/0015_BasicKendo001.bvh`, i.e. `http://mocap.cs.sfu.ca/nusmocap/<filename>` (this path was inferred from the page's relative links, not confirmed by a successful download). Subject 0019's files sit in a `nusmocapnew` folder.
- Known data-quality issue on the site: several links are mismatched — the Kendo and HopOverObstacle `.bvh` links point to `.c3d` files, and some `.txt`/`.bvh`/`.hdf` links point to `.html` — [mocap.cs.sfu.ca](https://mocap.cs.sfu.ca/).
- Licence: **free for research purposes only; cannot be used in commercial products or resold**; acknowledgement of `mocap.cs.sfu.ca` plus the NUS AcRF and SFU President's Research Start-up Grant requested in publications — [mocap.cs.sfu.ca](https://mocap.cs.sfu.ca/). No registration step.
- SFU is also redistributed inside AMASS as SMPL parameters: "The SFU motion capture data... obtained from http://mocap.cs.sfu.ca" is one of AMASS's 15 unified optical marker-based sources — [AMASS paper](https://arxiv.org/pdf/1904.03278).

### Inferences
- `0017_WushuKicks001` is the single best verified "wushu high kick" clip that is free and already in BVH/FBX. It is male-performed, so it would be a technique reference to be re-performed in style rather than a drop-in feminine clip.
- `0012_JumpAndRoll001` and `0017_JumpAndRoll001` are the cleanest verified **forward rolls** found anywhere in this research, and `0017_ParkourRoll001` starts from a kneeling position, which is useful for a short, tight roll.
- `0018_Bridge001` (female, "bridge dance") is a plausible source for a backbend/walkover-adjacent shape, but the description does not say walkover.

### Gaps
- **No backward roll** anywhere in SFU (all rolls are described as forward/parkour).
- **No split and no vertical leg hold** in SFU.
- **Frame rate and skeleton are not stated anywhere on the SFU page**; they must be read from the .bvh headers / .vsk files after download — [mocap.cs.sfu.ca](https://mocap.cs.sfu.ca/).
- The exact download base path is inferred, and some site links are known-broken, so expect to fetch c3d and convert for some takes.

## Sports / fitness / dance datasets: do they really contain splits, vertical leg holds, rolls?

### Takeaway
None of Fit3D, AIST++, Motion-X or the gymnastics-video datasets could be **verified** to contain a split or
a vertical leg hold. AMASS's **DanceDB** (contemporary dance, University of Cyprus) and **Fit3D** are the two
most plausible homes for those shapes, but in both cases the per-clip content list could not be read during
this session.

### Cited Findings
- **Fit3D**: official site is [fit3d.imar.ro](https://fit3d.imar.ro/), listing "611 multi-view sequences; minimum 5 annotated repetitions per sequence; 2,964,236 highly accurate ground truth 3d skeletons, GHUM & SMPLX human pose and shape parameters". Exercise count is inconsistent between sources: the paper says "over 37 repeated exercises", the dataset's own page lists "47 Exercises" in categories including warmups, barbell, dumbbell and equipment-free. Split is TRAIN 8 subjects (all trainees) / TEST 3 subjects (1 trainer, 2 trainees); ground-truth body shape "is only available for the training subset". Ground truth comes from fitting GHUM to markers, multi-view images and body scans. Visualisation code for GHUM/SMPLX is at [fit3d.imar.ro/code](https://fit3d.imar.ro/code). A "Download" link exists on the homepage; **registration requirements and licence text could not be retrieved**.
- **AIST++**: annotations use "COCO-format for 2D & 3D keypoints, and SMPL-format for human motion annotations"; downloads at `google.github.io/aistplusplus_dataset/download.html`; the SMPL model itself must be obtained separately from the SMPL project site; the API repo is archived/read-only — [google/aistplusplus_api](https://github.com/google/aistplusplus_api). One AIST++ demo sequence is described as CC BY 4.0 in a third-party repo — [SCANimate](https://github.com/shunsukesaito/SCANimate) — but that covers a single demo file, not the dataset.
- **Motion-X**: 15.6M SMPL-X whole-body pose annotations over 81.1K sequences (NeurIPS 2023 version; a later abstract says 13.7M over 96K) — [Motion-X paper](https://arxiv.org/pdf/2307.00818). Its **kungfu content comes from online videos**: the NeurIPS slides list "Online videos [6.0M]: kungfu, music, performance" — [NeurIPS slides](https://neurips.cc/media/neurips-2023/Slides/73564.pdf). The repo states the IDEA400 subset is released and other subsets will follow in the same directory — [IDEA-Research/Motion-X](https://github.com/IDEA-Research/Motion-X/blob/main). Motion-X requires filling out a form to request authorisation for **non-commercial** use.
- **DanceDB (AMASS subset)**: AMASS's news page reports "body parameters for dance motions by professional dancers under the DanceDB dataset, with 20 subjects and 153 sequences totaling roughly 3.5 hours" — [amass.is.tue.mpg.de/news.html](https://amass.is.tue.mpg.de/news.html). The DanceDB homepage says it stores "high-quality AutoDesk FBX, C3D motion capture data and videos for every performance" — [dancedb.cs.ucy.ac.cy](https://dancedb.cs.ucy.ac.cy); the related VIDAPE project page says "you can download FBX, BVH, C3D data of contemporary dance performances" — [vidape.cs.ucy.ac.cy/Data.html](https://vidape.cs.ucy.ac.cy/Data.html). Content includes contemporary dance sessions with emotional labels ("afraid", "angry", "excited") by named dancers from 2015, plus Cypriot folk dances. Licence: "Software Copyright License for Non-Commercial Scientific Research Purposes"; data is proprietary to the University of Cyprus — [dancedb.cs.ucy.ac.cy](https://dancedb.cs.ucy.ac.cy). A University of Cyprus FAQ describes the BVH files as having "a virtual character ... incorporated to the actor", "compatible with Autodesk MotionBuilder, 3Ds Max, Maya, Unity3D etc." — [dancemuseum.cs.ucy.ac.cy/faq.html](https://dancemuseum.cs.ucy.ac.cy/faq.html).
- **AMASS access**: "before you proceed with downloads you have to register"; the registration form states the data "is made available only for noncommercial research purposes" — [amass.is.tue.mpg.de/register.php](https://amass.is.tue.mpg.de/register.php). AMASS unifies 15 optical marker-based mocap datasets — [AMASS paper](https://arxiv.org/pdf/1904.03278). ACCAD inside AMASS is listed as 82 subjects / 252 motions. Community setup guides: download SMPL+H, DMPL and SMPL-X body models separately into a `body_models` folder, then the AMASS motions; use the "SMPL-H G" variant for SMPL+H work — [UniMoCap](https://github.com/LinghaoChan/UniMoCap).
- **SMART / SportsCap**: "The action labels (not include joints) of gymnastics sports in SMART dataset are from FineGym", i.e. the gymnastics content is label-level, not mocap-derived — [SportsCap paper](https://arxiv.org/pdf/2104.11452). Public release of SMART was not confirmed.
- **HCM (cited by HybridCap)** is described as a dance dataset containing "a large amount of rare challenging motions, such as rolling, tumbling, fitness, boxing, yoga" — [HybridCap paper](https://arxiv.org/pdf/2203.09287). Availability and licence unverified.
- **Chang-E** (Chinese classical Dunhuang dance): captured dance artists, fitted to SMPL, and several clips list "a single female performer" or "two female performers"; **access requires a licence agreement signable only by employees of research institutions** (students need a supervisor's signature) — [cislab.hkust-gz.edu.cn/projects/chang-e](https://cislab.hkust-gz.edu.cn/projects/chang-e/).
- **InfiniteDance** claims to be the first large-scale dance dataset to feature ballet, listing Ballet at 8.31 hours — [arXiv 2603.13375](https://arxiv.org/pdf/2603.13375). No download link surfaced.

### Inferences
- The owner's "real captured motion, not hand-keyed" requirement **rules out Motion-X's kungfu subset**: it is SMPL-X fitted to online video, i.e. pseudo-ground-truth monocular estimation, not marker capture. Same caution applies to any FineGym-derived gymnastics "pose" data.
- AIST++ is genuine multi-view capture of professional street dancers and is the most likely free source of **high leg extensions and floor work with a feminine performance read** among dance datasets, but no specific split/leg-hold clip could be named from its documentation, so it needs to be browsed clip-by-clip.
- Fit3D's equipment-free/warm-up categories are the most likely place in a fitness dataset for a seated/standing hamstring stretch, but the dataset is built around gym exercises with repetition counts, so a full split is unlikely.

### Gaps
- **Could not verify a split in any of Fit3D, AIST++, Motion-X, DanceDB, MoVi, ASPset-510, Human3.6M, HumanAct12 or UESTC.** Per-clip content listings for these were not retrievable within this session's budget; MoVi, ASPset-510, Human3.6M, HumanAct12 and UESTC were not examined at all and should be treated as unresearched, not as negative results.
- **NTU RGB+D 120**: not examined in this session. It is widely documented as Kinect-derived 25-joint skeletons rather than marker mocap, which makes it a poor retargeting source, but no source was retrieved here to support that — treat as unverified.
- Fit3D licence/registration text, AIST++ licence text and the per-performance DanceDB download table were all unreachable (the DanceDB performances page returned 503).

## Ballet / contemporary dance mocap for the vertical leg hold

### Takeaway
**No freely downloadable ballet mocap with 3D joints was found.** The only non-commercial research routes are
DanceDB (contemporary, non-commercial research licence, University of Cyprus) and possibly AIST++; ballet
specifically exists mainly in paid marketplace packs and in a dataset (InfiniteDance) with no published
download.

### Cited Findings
- A targeted search for downloadable ballet mocap with 3D joints returned no dedicated free dataset; the hits were commercial packs — Reallusion's "20 Contemporary Ballet Dances" described as "20 high quality MoCap contemporary/ballet dance motions" — [reallusion.com](https://www.reallusion.com/ContentStore/Pack/20-contemporary-ballet-dances) — and an Unreal Marketplace barre pack of "35 motion capture files" with its own skeletal hierarchy — [docs.unrealengine.com akob ballet motion capture 2 barre](https://docs.unrealengine.com/marketplace/ko/product/akob-ballet-motion-capture-2-barre).
- A Toyo University KAKENHI project (2001–2003) built a "3D-motion Database for Dance" from ballet demonstrations — [kaken.nii.ac.jp KAKENHI-PROJECT-13680491](https://kaken.nii.ac.jp/grant/KAKENHI-PROJECT-13680491/). No evidence the data is downloadable.
- DanceDB is the one contemporary-dance mocap with a stated non-commercial research licence and FBX/C3D (and per VIDAPE, BVH) downloads — [dancedb.cs.ucy.ac.cy](https://dancedb.cs.ucy.ac.cy), [vidape.cs.ucy.ac.cy/Data.html](https://vidape.cs.ucy.ac.cy/Data.html).

### Inferences
- The vertical-leg-hold shape (développé / penché / needle scale) is most likely to be found by hand-browsing DanceDB's contemporary performances or AIST++ clips, since no dataset indexes moves at that granularity.

### Gaps
- **The "raise one leg vertically overhead and hold, then strike" move remains unsourced from any verified public dataset.** This is the single biggest hole in the shopping list.
- No ballet dataset with published per-clip move names and a free licence was found.

## Gymnastics-specific mocap

### Takeaway
**No public 3D marker-mocap dataset dedicated to gymnastics or tumbling was found**, and a 2026 paper
states directly that such motions are hard to capture with marker systems at all. FineGym is video-only.

### Cited Findings
- A 2026 trampoline-gymnastics pose paper notes that existing data "remains limited in its coverage of highly dynamic acrobatic movements involving frequent body inversion and rapid rotations", and that such motions "are difficult to capture reliably with traditional marker-based motion capture systems" — [arXiv 2604.01322](https://arxiv.org/pdf/2604.01322).
- FineGym is a gymnastics **video** dataset with fine-grained action annotations, not 3D mocap (referenced via [SportsCap](https://arxiv.org/pdf/2104.11452)).
- Northumbria University published markerless 3D analysis of **gymnastic tumbling** from a single mobile camera — [researchportal.northumbria.ac.uk](https://researchportal.northumbria.ac.uk/en/publications/predictive-tracking-for-3d-markerfree-motion-analysis-of-gymnasti/), [nrl.northumbria.ac.uk/id/eprint/16908](https://nrl.northumbria.ac.uk/id/eprint/16908). These are method papers; no data release was found.
- A biomechanics data release does exist for a generic movement screen — "Three-Dimensional Motion Capture Data of a Movement Screen from 183 Athletes" — [PMC10126034](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10126034/), but it is a screening battery, not tumbling.
- ACCAD has a single named acrobatic clip, "Handspring 1", available in mov/bvh/amc/c3d — [accad.osu.edu motion lab data](https://accad.osu.edu/research/motion-lab/mocap-system-and-data).

### Inferences
- For tumbling, CMU subjects 85/87/88/89/90 plus SFU's parkour rolls are effectively the whole free corpus; there is no better gymnastics-specific release to wait for.

### Gaps
- **No forward roll, backward roll, walkover or split with published 3D joint angles from a biomechanics lab was located.** Searches surfaced only method papers and unrelated screening data.

## Taekwondo / karate / wushu, and female performers

### Takeaway
**No public taekwondo mocap dataset exists** that could be verified — the axe-kick literature uses private
captures. The only verified public karate data is CMU subject 135 (gender unpublished); the only verified
public wushu take is SFU `0017_WushuKicks001` (male); the only verified public **female** striking capture is
**CMU subject 144**.

### Cited Findings
- Axe-kick biomechanics studies exist but no data release was found: a 2012 Archives of Budo study filmed "12 subjects (4 professionals & 8 advanced fighters)" with four camcorders — [archbudo.com](https://archbudo.com/view/abstract/id/10562); Wąsik & Shan (2014) recorded "six International Taekwon-do Federation (ITF) practitioners" performing the axe kick, open-access via [University of Lethbridge](https://opus.uleth.ca/items/50951dac-80a6-48a0-b8b7-c364eb3c9f69/full); a German sport-science record indexes "Biomechanical characteristics of the Axe kick in Tae Kwon-Do" — [lida.sport-iat.de](https://lida.sport-iat.de/ta/Record/4026729?lng=en).
- **ACCAD**: martial arts data exists **only for Male 2** — "Martial Arts Extended; Martial Arts Kicks (21 files, C3D, calibration); Martial Arts Punches (15, C3D); Martial Arts Stances (15, C3D); Martial Arts Walks/Turns (22, C3D)". **Female 1 is the only female performer and has no martial arts, kicks or acrobatics** — her sets are Full set (81 BVH files), General (18 C3D), Gestures (6 C3D), Running (27 C3D), Walking (30 C3D) — [accad.osu.edu](https://accad.osu.edu/research/motion-lab/mocap-system-and-data). The Male 2 martial arts kicks PDF list includes an axe kick — [ACCAD_mocap_Data_Male2_MartialArtsKicks.pdf](https://www.accad.ohio-state.edu/sites/accad.osu.edu/files/ACCAD_mocap_Data_Male2_MartialArtsKicks.pdf).
- ACCAD licence: "The Open Motion Project is licensed under a Creative Commons Attribution 3.0 Unported License"; formats are C3D (Vicon IQ reconstruction), BVH, AMC/ASF, TVD, TXT (comma/space-delimited xyz from Vicon Blade), MOV samples; **no frame rate is stated on the page**; FBX is described as the main export format but no FBX downloads are offered — [accad.osu.edu](https://accad.osu.edu/research/motion-lab/mocap-system-and-data).
- Bandai Namco (already on disk): "over 3,000 moves"; dataset-1 "consists of 175 individual moves spanning 17 motion types, including fighting and dancing"; dataset-2 has "7 core styles: normal, active and exhausted; youthful and elderly; **masculine and feminine**"; licensed for research and personal use; movements performed by three professional actors — [3dart.it](https://3dart.it/en/bvh-free-mocap-moves-from-bandai-namco), [CG Channel](https://www.cgchannel.com/?p=136588).
- Rokoko's free motion library: "150 of the moves are available for free" and "the free moves are licensed for use in commercial projects" — [3dart.it](https://3dart.it/en/download-150-mocap-moves-library). Truebones offers free BVH/FBX demo packs including an "UNREAL ENGINE FREE MOCAP LIBRARY" — [truebones.gumroad.com](https://truebones.gumroad.com/l/eZRYS).

### Inferences
- **The male-performer problem is only partly fixable from free data.** The practical fix is: take technique from CMU 135 / SFU 0017 / ACCAD Male2 as *reference*, and take the performance read (timing, hip/shoulder carriage, arm lines) from CMU 144 and Bandai Namco's explicit **"feminine" style** variants in dataset-2, which the project already has on disk. Bandai's feminine style on fighting motions is the cheapest available answer to the "feminine performance read" requirement and needs no new download.
- The axe-kick / vertical-kick-from-hold request has **no verified free mocap source**. The realistic options are: (a) buy a taekwondo mocap pack commercially, (b) combine CMU 49's one-leg balance/extension with a kick clip, or (c) commission/record it.

### Gaps
- No public taekwondo dataset, female or otherwise, was verified.
- CMU 135's performer gender is unpublished.
- Chang-E has female performers and SMPL data but its licence is **not available to a personal non-commercial user** (research-institution employees only), so it should be treated as out of reach — [cislab.hkust-gz.edu.cn](https://cislab.hkust-gz.edu.cn/projects/chang-e/).

## Conversion paths and converter repos

### Takeaway
Every format in this report has a documented open-source path to BVH; FBX is reached via Blender or the FBX
SDK rather than a dedicated converter. The shortest paths are: SFU (already BVH/FBX), CMU (pre-converted BVH
from cgspeed mirrors, or `amc2bvh` from ASF/AMC), AMASS/AIST++ (SMPL npz → BVH via `smpl2bvh`).

### Cited Findings
- **ASF/AMC → BVH**: [thcopeland/amc2bvh](https://github.com/thcopeland/amc2bvh) "converts a pair of files, one in the Acclaim Skeleton Format (ASF) and the other in the Acclaim Motion Capture (AMC) format, to a single file in the Biovision Hierarchy (BVH) format"; no non-standard libraries, `make` to build, usage `amc2bvh FILE.asf FILE.amc -o FILE.bvh`. It is an open-source replacement for the original closed `amc2bvh` whose site went offline. A 2023 x3d-public mailing-list post reports building it under Cygwin and successfully converting CMU `56_03.amc` — [web3d.org x3d-public 2023-September/019503](https://web3d.org/pipermail/x3d-public_web3d.org/2023-September/019503.html).
- cgspeed's own readme advises using the pre-converted BVH "in preference of converting them with amc2bvh" — [cgspeed ReadmeFirst](https://github.com/una-dinosauria/cmu-mocap/blob/master/READMEFIRST.txt).
- **SMPL / AMASS npz → BVH**: [kosukefukazawa/smpl2bvh](https://github.com/kosukefukazawa/smpl2bvh) — MIT-licensed script (SMPL model itself needs its own licence); input is an `.npz` holding `rotations` and `trans`; requires SMPL models under `./data/smpl/smpl/` and `pip install smplx[all]`; typical call `python smpl2bvh.py --gender MALE --poses <input> --fps 60 --output <path> --mirror`. Its README points to a separate repo of the same author for AMASS→BVH specifically (that repo was not located). A mirror exists at [SlimeVRX/smpl2bvh](https://github.com/SlimeVRX/smpl2bvh), and the same script is embedded in the MoMask demo space — [HuggingFace MoMask visualization/smpl2bvh.py](https://huggingface.co/spaces/MeYourHint/MoMask/blame/090c3d7bb37eb220879335c69c53da25e239f84c/visualization/smpl2bvh.py).
- **AMASS NPZ (SMPL-X) ↔ BVH**: NVIDIA's Kimodo provides a `kimodo_convert` command that converts between AMASS NPZ (SMPL-X) and SOMA BVH; it cannot produce FBX and its BVH export targets the SOMA skeleton, not SMPL — [NVIDIA Kimodo motion-conversion docs](https://research.nvidia.com/labs/sil/projects/kimodo/docs/user_guide/motion_convert.html).
- **AMASS → 3D-joint formats** (for retarget-by-joint pipelines): [goldbricklemon/AMASS-to-3DHPE](https://github.com/goldbricklemon/AMASS-to-3DHPE); [LinghaoChan/UniMoCap](https://github.com/LinghaoChan/UniMoCap) unifies AMASS-based datasets into a common representation.
- **SMPL in Unity / body-model tooling**: [BioMotionLab/SUP](https://github.com/BioMotionLab/SUP).
- **CMU → FBX, already done**: an FBX conversion of the whole CMU database is archived at [data.4tu.nl](https://data.4tu.nl/datasets/0448aab2-3332-449f-a8e2-d208cb58c7df) (page 503 during this session) and a copy is on HuggingFace as [gbionics/cmu-fbx](https://huggingface.co/datasets/gbionics/cmu-fbx).
- **No open-source SMPL→FBX converter surfaced in these searches**; the documented route is BVH→FBX through Blender or the Autodesk FBX SDK.

### Inferences
- Recommended per-source path for a 138-bone UE-style rig at 30 fps:
  - **SFU**: download `.fbx` (or `.bvh`), read the real frame rate from the BVH header, resample to 30 fps, retarget. Fewest steps of any source.
  - **CMU**: prefer a cgspeed-derived BVH mirror (`Shriinivas/cmubvh`, `ckinpdx/CMUMDB`) or the 4TU/HuggingFace FBX conversion; fall back to `thcopeland/amc2bvh` on the native ASF/AMC if hand/wrist fidelity or the conversion-quality complaint matters.
  - **AMASS subsets (DanceDB, SFU, ACCAD, CMU as SMPL)**: register, download the SMPL-H G variant plus the body models, convert with `smpl2bvh`, then BVH→FBX in Blender. Note AMASS is a *resampled, fitted* representation — for SFU and CMU you are better off taking the original BVH/C3D directly.
  - **AIST++/Fit3D (SMPL/SMPL-X)**: same `smpl2bvh` route; Fit3D's native GHUM parameters have no documented BVH converter and would need the SMPL-X variant instead.
  - **ACCAD C3D (e.g. Male2 Martial Arts Kicks, incl. the axe kick)**: C3D is marker data, not skeletal — it needs solving (Blender C3D importer / Vicon-style solve) before retargeting, unlike the ACCAD BVH "Full set" files.

### Gaps
- No verified open-source SMPL→FBX direct converter; BVH is always the intermediate.
- The author's dedicated AMASS→BVH repo referenced by `smpl2bvh`'s README could not be located.
- Frame rates: **not stated by SFU, not stated by ACCAD, and CMU's page was unreachable**, so the 30 fps resampling step must be driven by each file's own header rather than by documented dataset rates.
