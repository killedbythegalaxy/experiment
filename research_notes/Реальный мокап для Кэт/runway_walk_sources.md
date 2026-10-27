# Real motion-capture sources for a female runway / catwalk walk (state as of 2026-10-27)

Scope note: every trial ID below was read out of a primary index file, not recalled. The CMU trial
IDs come from the verbatim CMU index text (`cmu-mocap-index-text.txt`, a mirror of the CMU
"View All: Motions" listing); the 100STYLE style names come from the official project page. Where a
fact could not be confirmed it is in **Gaps**, not in the findings.

---

## Q1. CMU Motion Capture Database — which subjects/trials are catwalk / model / "sexy" / feminine / heel-like walks, and where is the canonical download?

### Takeaway
CMU has far more than trial 137_38: there are **four separate style-walk protocols** (subjects 137,
142, 91 and 105) each containing an explicitly labelled "sexy" walk, plus subject 132's
`Walk Crossover` (the crossing step line) and subject 136's six `Walk on Toes` trials (the
plantarflexed ankle that heels force). The licence is the most permissive of any source here —
"may be copied, modified, or redistributed without permission" — and the master ASF/AMC zip is live
and about 1.08 GB; CMU itself ships no BVH, so BVH comes from third-party conversions.

### Cited Findings

**Subject #137 (Stylized Motions) — the richest single block. 42 trials; each style is captured as a
4-take set (Coffee Mug / Pick Up / Wait / Walk), which is ideal because you get a matched idle and
prop-interaction in the same style as the walk:**
- `137_35` Sexy Lady Coffee Mug, `137_36` Sexy Lady Pick Up, `137_37` Sexy Lady Wait, `137_38` Sexy Lady Walk — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `137_21` Graceful Lady Coffee Mug, `137_22` Graceful Lady Pick Up, `137_23` Graceful Lady Wait, `137_24` Graceful Lady Walk — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `137_29` Normal Walk (plus `137_26`/`137_27`/`137_28` Normal Coffee Mug / Pick Up / Wait) — the neutral baseline from the same actor, same session — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `137_04` Cat Walk — **not a fashion catwalk**: the index groups it with `137_01` Cat Coffee Mug, `137_02` Cat Pick Up, `137_03` Cat Wait, i.e. an animal-cat impression alongside Chicken (`137_05`–`137_08`) and Dinosaur (`137_09`–`137_12`) — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `137_34` Range of Motion (calibration/ROM take, useful for retarget rig fitting) — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- Other styles in 137: Drunk `137_13`–`137_16`, Gangly Teen `137_17`–`137_20`, Old Man `137_30`–`137_33`, Strong Man `137_39`–`137_42`, Motorcycle `137_25` — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subject #142 (Stylized Walks) — 22 single-take emotional/stylised walks, a different actor:**
- `142_18` Sexy; `142_10` Lavish; `142_04` Cool; `142_06` Elated; `142_08` Happy; `142_09` Joy; `142_13` Relaxed; `142_19` Shy; `142_01` Childish; `142_02`/`142_03` Clumsy; `142_05` Depressed; `142_07` Elderlyman; `142_11` Marching; `142_12` Painfulleftknee; `142_14` Rushed; `142_15` Sad; `142_16`/`142_17` Scared; `142_20`/`142_21` Singing in the rain jump; `142_22` Sneaky — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subjects #91 ("walks and turns") and #105 ("motion") — two actors run through an identical
40-trial style-walk protocol, trial-for-trial. This gives you the same style from two bodies:**
- `91_30` SexyWalk and `105_30` SexyWalk — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `91_11` / `105_11` LavishWalk; `91_12` / `105_12` TooCoolWalk; `91_23` / `105_23` CoolWalk; `91_33` / `105_33` MachoWalk; `91_20` / `105_20` ShyWalk; `91_28` / `105_28` GhettoWalk — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- Neutral baselines in the same protocol: `91_29`, `91_31`, `91_34` NormalWalk; `105_29`, `105_31`, `105_34` NormalWalk; `91_02` / `105_02` WalkStraight; `91_01` / `105_01` Walk digital figure eight (curved-path walking, useful for a turn at the end of the runway) — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subject #132 (Varying Weird Walks) — contains the two mechanical ingredients of a runway walk:**
- `132_16` Walk Crossover — the narrow/crossing step line, the single most runway-specific gait mechanic in CMU — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `132_42`, `132_43`, `132_44` Walk Swinging Shoulders — upper-body counter-rotation, i.e. arms swinging across the body — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- Also in 132: `132_29`–`132_34` Bouncy Walk; `132_14` Walk Duck Footed; `132_39` Pigeon Toed Walking; `132_53` Walk With Legs Apart; `132_35`/`132_36` Walk Leaning To The Right; `132_40`/`132_41` Walk With Stiff Arms; `132_45`–`132_50` Walk Slow; `132_51` Tpose; `132_52` Range of Motion — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subject #136 (Weird Walks) — the closest thing in CMU to a plantarflexed, heel-wearing ankle:**
- `136_27`, `136_28` Walk on Toes; `136_29`, `136_30` Walk Backwards on Toes — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `136_05`–`136_08` Walk on Toes Bent Forward (fwd and backward variants); `136_13`–`136_16` Walk on Toes Crouched — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- `136_31`, `136_32` Quail, quick little steps (short-stride, high-cadence — the stride signature heels produce) — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subject #141 — crossing-leg lateral steps for runway turns:**
- `141_32` Walk Sideways, Cross Legs; `141_33` Walk Sideways, Foot to Foot; `141_29`/`141_30` Random Walk; `141_31` Walk Backwards — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)

**Subjects that look promising from their titles but are NOT useful for this character (checked and ruled out):**
- Subject #120 "Various Style Walks" is a cartoon set: `120_01` Alien Turn, `120_02` Gorilla, `120_03`–`120_07` Mickey cast spell/conducting/dance, `120_08`–`120_14` Mickey sneaky/sneaking walk, `120_17`/`120_18` Mickey Walk, `120_19` Walk slow, `120_20` Walk, `120_21` Robot, `120_22` Zombie — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- Subject #17 "different walking styles" is all masculine/negative affect: `17_01`/`17_02` walk with anger, frustration; `17_03`/`17_04` walk stealthily; `17_05` walk/hobble; `17_06`/`17_07` whistle, walk jauntily; `17_08`/`17_09` muscular, heavyset person's walk — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- Subject #133 is "Baby Styled Walk"; Subject #135 is "Martial Arts Walks"; Subject #131 is "Michael Jackson Styled Motions"; Subject #138 is "Marching, Walking and Talking"; Subject #139 is "Action Walks, sneaking, wounded, looking around" — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt)
- A full-text search of the whole CMU index for `heel`, `femin`, `runway`, `strut`, `sashay`, `prissy`, `prance`, `swagger`, `pageant`, `model walk` returned **zero** matches (only `Walk on Toes` / `Lavish` / `tiptoe`-adjacent hits listed above). There is no literal catwalk/model-walk/heels trial in CMU.

**Canonical downloads, formats, sizes, licence:**
- Licence, verbatim from the CMU FAQ: "The motion capture data may be copied, modified, or redistributed without permission." No registration, no account, no non-commercial restriction — [CMU Graphics Lab Mocap FAQ](http://mocap.cs.cmu.edu/faqs.php)
- Capture hardware and rate: "a Vicon motion capture system consisting of 12 infrared MX-40 cameras, each of which is capable of recording at 120 Hz", capture volume ~3 m x 8 m, subject wears 41 markers — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- Whole-database ASF/AMC zip: `http://mocap.cs.cmu.edu/allasfamc.zip` — offered directly on the FAQ page ("Here is a zipfile of all asf's and amc's"). Verified reachable on 2026-10-27 (HTTP 206 on a range request; `Last-Modified: Fri, 06 Apr 2007`; Apache ETag implies ~1,083,475,145 bytes ≈ 1.08 GB) — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- C3D marker data: "Here are all the c3d's - they sum to over 4GB so the files were split up by subject number" — `allc3d_0.zip`, `allc3d_1a.zip`, `allc3d_1b.zip`, `allc3d_234.zip`, `allc3d_56789.zip`. Reference video also available as `allavi.zip` and mpg zips — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- ASF/AMC units: data is in inches with the ASF header scale 0.45; multiply bone lengths and root position by `(1.0/0.45)*2.54/100.0 = 0.056444` to get metres — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- AMC holds joint angles only, no markers; C3D holds 41+ markers plus Vicon BodyBuilder model-output markers — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- CMU ships **no BVH**: "Asf/amc is easy to parse and work with… That said, several external sites have converted our data into other formats; see the Resources page." — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php)
- cgspeed "MotionBuilder-friendly BVH conversion release" (the standard CMU BVH mirror): recommended 2010 re-release, 17 zips by subject range. Relevant ranges for the style subjects: **131–135 = 52 MB**, **136–140 = 61 MB**, **141–144 = 64 MB**, **102–111 = 75 MB** (covers subject 105), **86–94 = 88 MB** (covers subject 91) — [cgspeed BVH conversion release](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database)
- cgspeed conversion properties: joints renamed to MotionBuilder naming conventions; the 2010 re-release did not change bone structure or names; **a T-pose is inserted as frame 1, facing +Z**; licence stated as "The files are free to use worldwide for any purpose", as-is, no affiliation with CMU or Autodesk — [cgspeed BVH conversion release](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database)
- cgspeed hosting is MediaFire (Google Sites 10 MB limit); the page itself records that "MediaFire deleted all files in 2011 without notice" before restoration. A probe of one of those MediaFire URLs on 2026-10-27 returned **HTTP 403** to scripted access — [cgspeed BVH conversion release](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database)
- A per-motion cgspeed BVH motions list (so you can confirm which BVH filename corresponds to `91_30` etc.) is published separately — [cgspeed BVH conversion release — motions list](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database/bvh-conversion-release-motions-list)
- Second-life/OpenSim-oriented BVH repack of CMU, still online — [Outworldz CMU BVH](https://www.outworldz.com/Secondlife/Posts/CMU/)
- The KIT Whole-Body Human Motion Database re-hosts CMU content with per-motion detail pages and its own converters — [KIT motion database](https://motion-database.humanoids.kit.edu/list/motions/)
- CMU trial index as a plain-text file, greppable offline, which is how all IDs above were obtained — [una-dinosauria/cmu-mocap index](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt) (raw: `https://raw.githubusercontent.com/una-dinosauria/cmu-mocap/master/cmu-mocap-index-text.txt`, 2887 lines)

### Inferences
- The highest-value CMU shopping list for this character, in priority order: `137_38` (already have) → `137_24` Graceful Lady Walk → `91_30` + `105_30` SexyWalk (two bodies, same protocol) → `142_18` Sexy → `132_16` Walk Crossover (steal the step line, not the style) → `132_42–44` Walk Swinging Shoulders (steal the arm cross) → `136_27/28` Walk on Toes (steal the ankle). The runway signature is more likely to be *assembled* from these than found in any one take.
- Because 137 captures each style as Walk + Wait + Pick Up + Coffee Mug, `137_37` Sexy Lady Wait and `137_23` Graceful Lady Wait are directly usable as the character's idle, in the same style as the walk — that is unusual and worth taking.
- Subjects 91 and 105 having identical trial numbering means a style-transfer or averaging approach across the pair is cheap; it also means `91_29`/`105_29` NormalWalk are perfect "neutral minus style" difference references.
- 120 Hz capture against a 30 fps target means decimation by exactly 4 — no resampling artefacts, which is the cleanest ratio of any source in this report.

### Gaps
- The frame rate of the **cgspeed BVH files** is not stated anywhere on the cgspeed release page, so I cannot confirm whether they are 120 fps or down-sampled. Check a file header before planning the retarget. (The source ASF/AMC are 120 Hz per the CMU FAQ.)
- Whether the cgspeed MediaFire links still serve files to a normal browser could not be confirmed — scripted access got 403, which is consistent with bot-blocking rather than deletion, but it is not proof the files are there.
- CMU's own index gives no actor sex, height or footwear per subject, so there is no way to tell from the index whether the 137 / 142 / 91 / 105 performers were women, or what shoes they wore.

---

## Q2. 100STYLE (Mason / Starke / Komura) — the actual style names, is there a catwalk, and how to download

### Takeaway
100STYLE has **no** catwalk / model / runway / sexy / feminine style, but it has the four mechanical
components of a runway walk as separate, isolated, long-duration styles: `CrossOver` (crossing step
line), `WiggleHips` (pelvic sway), `Strutting`, and `SwingShoulders` — plus `Proud`, `GracefulArms`,
`Tiptoe` and `FairySteps`. It is CC BY 4.0, 60 fps, a clean 28-bone skeleton, and the raw BVH zip is
only 1.5 GB. **Caution: the style called `OnHeels` is heel-walking with the toes lifted, i.e. the
opposite of high-heel footwear gait; the heel-footwear analogue is `Tiptoe` / `OnToesBentForward`.**

### Cited Findings
- Full verified list of the 100 style names: Aeroplane, Akimbo, Angry, ArmsAboveHead, ArmsBehindBack, ArmsBySide, ArmsFolded, Balance, BeatChest, BentForward, BentKnees, BigSteps, BouncyLeft, BouncyRight, Cat, Chicken, **CrossOver**, Crouched, CrowdAvoidance, Depressed, Dinosaur, DragLeftLeg, DragRightLeg, Drunk, DuckFoot, Elated, **FairySteps**, Flapping, FlickLegs, Followed, **GracefulArms**, HandsBetweenLegs, HandsInPockets, Heavyset, HighKnees, InTheDark, KarateChop, Kick, LawnMower, LeanBack, LeanLeft, LeanRight, LeftHop, LegsApart, LimpLeft, LimpRight, LookUp, Lunge, March, Monk, Morris, Neutral, Old, **OnHeels**, OnPhoneLeft, OnPhoneRight, **OnToesBentForward**, OnToesCrouched, PendulumHands, Penguin, PigeonToed, **Proud**, Punch, Quail, RaisedLeftArm, RaisedRightArm, RightHop, Roadrunner, Robot, Rocket, Rushed, ShieldedLeft, ShieldedRight, Skip, **SlideFeet**, SpinAntiClock, SpinClock, Star, StartStop, Stiff, **Strutting**, Superman, Swat, Sweep, Swimming, SwingArmsRound, **SwingShoulders**, Teapot, **Tiptoe**, **TogetherStep**, TwoFootJump, WalkingStickLeft, WalkingStickRight, Waving, WhirlArms, WideLegs, **WiggleHips**, WildArms, WildLegs, Zombie — [100STYLE project page](https://www.ianxmason.com/100style/)
- Scale: "over 4 million frames of motion capture data for 100 different styles of locomotion" — [100STYLE project page](https://www.ianxmason.com/100style/)
- Frame rate: BVH files are captured at **60 fps** — [100STYLE project page](https://www.ianxmason.com/100style/)
- Skeleton: **28 bones**. Root `Hips`; spine `Chest → Chest2 → Chest3 → Chest4 → Neck → Head`; arms off `Chest4` as `RightCollar`/`LeftCollar → Shoulder → Elbow → Wrist`; legs off `Hips` as `RightHip`/`LeftHip → Knee → Ankle → Toe`. No fingers — [100STYLE project page](https://www.ianxmason.com/100style/)
- File organisation: `<StyleName>_<MovementType>.bvh`, where MovementType is `BR` backwards running, `BW` backwards walking, `FR` forwards running, `FW` forwards walking, `ID` idling, `SR` sidestep running, `SW` sidestep walking, `TR1`/`TR2`/`TR3` transitions — so the target file is e.g. `CrossOver_FW.bvh`, `WiggleHips_FW.bvh`, `Strutting_FW.bvh`, `Proud_FW.bvh`, `Tiptoe_FW.bvh` — [100STYLE project page](https://www.ianxmason.com/100style/)
- The BVH are unedited and each stylised take begins and ends with a T-pose; `Frame_Cuts.csv` holds the trim indices and `Dataset_List.csv` the style list for automatic filtering/mirroring — [100STYLE project page](https://www.ianxmason.com/100style/)
- Licence: **CC BY 4.0**. Academic use cites Mason, Starke & Komura (2022); creative/commercial use is asked to credit "The 100STYLE Dataset - Ian Mason". No registration — [100STYLE project page](https://www.ianxmason.com/100style/)
- Download, Zenodo record 8127870, total 16.2 GB: **`100STYLE.zip` = 1.5 GB** (the original BVH — this is the one to take) and `100Style-Labelled-Data.zip` = 14.8 GB (processed features: joint positions, rotations, velocities, local-phase labels, with `InputLabels.txt` / `OutputLabels.txt`) — [Zenodo 8127870](https://zenodo.org/records/8127870). The `100STYLE.zip` URL responded to a range request on 2026-10-27 (HTTP 206).
- Google Drive mirrors given on the project page: BVH-only / whole-dataset zip `https://drive.google.com/uc?id=1-Wr0MkBEIk0SkByJaDXt0wUEZevYFBAk&export=download`; `Dataset_List.csv` `https://drive.google.com/uc?id=19b1zCGOI1ouVhx__GFU5OF0Q2_DN5mnS&export=download`; `Frame_Cuts.csv` `https://drive.google.com/uc?id=1d0VM8k4UjA4dDmaviuZMjAf-WQNLUwnZ&export=download` — [100STYLE project page](https://www.ianxmason.com/100style/). Note the project page has moved from `ianxmason.github.io/100style/` (301) to `www.ianxmason.com/100style/`.
- A **SMPL-retargeted** 100STYLE is distributed via the SMooDi project — [neu-vi/SMooDi](https://github.com/neu-vi/SMooDi)
- Daniel Holden published a further retargeted 100STYLE matching his other datasets, with the explicit caveat that "the quality is lower due to the inertial capture, and it's missing finger motion" — [@anorangeduck](https://x.com/anorangeduck/status/1882863800388591856)
- Underlying paper: Mason, Starke, Komura, "Real-Time Style Modelling of Human Locomotion via Feature-Wise Transformations and Local Motion Phases" — [arXiv:2201.04439](https://arxiv.org/abs/2201.04439)

### Inferences
- `CrossOver_FW.bvh` + `WiggleHips_FW.bvh` + `SwingShoulders_FW.bvh` are, between them, an almost exact decomposition of the stated runway signature (narrow crossing step line, pelvic rotation/sway, arms swinging across the body). Layering or blending these three onto a `Neutral_FW` base is a plausible way to synthesise the walk without any runway capture existing.
- Because each style also exists as `ID` (idle), `SW` (sidestep) and `TR1-3` (transitions), 100STYLE is the only source here that can supply a *complete locomotion set* in one consistent style — relevant for a fighting-game character that needs idle, strafe and turn, not just a forward walk.
- 60 fps → 30 fps is a clean 2:1 decimation.
- The 100STYLE skeleton has a `Toe` joint under `Ankle`, which matters for a heeled bind pose: a toe joint lets you re-pose the foot as a rigid plantarflexed block rather than faking it at the ankle.
- The dataset being inertial (per Holden) means foot-contact sliding is likely; expect to need IK foot locking after retarget, more so than with CMU's optical 120 Hz data.

### Gaps
- Zenodo's record page does not state the style count or fps (those come from the project page), and neither source states an uncompressed size for the BVH set.
- `Dataset_List.csv` could not be downloaded directly (the old GitHub Pages path 404s; the live copies are Google Drive links that need a browser), so the style list above is from the project page's own rendering rather than from the CSV itself. The two should agree but that was not verified byte-for-byte.
- The number of takes/files per style, and the duration per style, are not stated on either page.

---

## Q3. AMASS subsets with expressive / performance walking

### Takeaway
AMASS is the weakest fit of the realistic options: it is gated behind registration and a licence
that third-party summaries describe as academic/research-only, per-subset download sizes are not
published outside the login, and none of its subsets is documented as containing runway-like
walking. Its value is as SMPL-parameterised data (if your pipeline wants SMPL) rather than as a
source of the specific style.

### Cited Findings
- `amass.is.tue.mpg.de/download.php` serves only a sign-in form plus a "Not Registered? Register" link to anonymous requests — registration is mandatory before any subset can be downloaded — [AMASS download page](https://amass.is.tue.mpg.de/download.php)
- DanceDB's SMPL `.npz` and rendered `.mp4` files "fall under the AMASS dataset terms"; you must be registered, logged in, and must agree to the AMASS licence — [Dance Motion Capture Database](https://dancedb.eu/main/performances)
- Eyes Japan (ACCAD-adjacent in AMASS's table): 37 markers, 12 subjects, **795 motions, 385.42 minutes** in one version of the AMASS paper, but **750 motions, 363.64 minutes** in another version of the same paper — the two figures conflict — [AMASS paper (ar5iv)](https://ar5iv.labs.arxiv.org/html/1904.03278); [AMASS ICCV 2019 PDF](https://openaccess.thecvf.com/content_ICCV_2019/papers/Mahmood_AMASS_Archive_of_Motion_Capture_As_Surface_Shapes_ICCV_2019_paper.pdf)
- MoVi: 90 subjects each performing the same predefined set of 20 actions plus one self-chosen movement; its MoSh++ `.npz` files are distributed as part of AMASS, with access instructions and licence on `biomotionlab.ca/movi` — [MoVi paper (PLOS ONE)](https://journals.plos.org/plosone/article?id=10.1371%2Fjournal.pone.0253157); [MoVi arXiv](https://arxiv.org/pdf/2003.01888)
- A third-party summary states AMASS "requires registration at the MPI website and is restricted to academic/research use" — this is a secondary source, so the binding text is AMASS's own `license.html` — [Robotics Center dataset page](https://www.roboticscenter.ai/datasets/amass-motion-capture)
- A Kaggle mirror of "AMASS SMPL+H G" lists folders including `DanceDB` and `EyesJapanDataset` with a total of 22.01 GB at version 6, but gives no per-folder breakdown — [Kaggle AMASS SMPL+H G](https://www.kaggle.com/datasets/mrriandmstique/amass-smpl-h-g/data)
- AMASS footer is "© 2020 Max-Planck-Gesellschaft" — [AMASS download page](https://amass.is.tue.mpg.de/download.php)
- A 2022 survey of mocap for artistic performance concludes that "few of these databases have sufficient coverage of artistic performances", i.e. expressive performance walking is under-represented across the public corpora generally — [Pathway to Future Symbiotic Creativity](https://arxiv.org/pdf/2209.02388)

### Inferences
- For a personal **game** character, even a non-commercial one, an academic-research-only licence is a worse fit than CMU's unrestricted terms or 100STYLE's CC BY 4.0. AMASS should be the last resort, and the licence text read first.
- DanceDB is the subset most likely to contain expressive feminine carriage (contemporary/ballet performers hold an upright, elongated posture close to runway carriage), but no public per-motion index was found to name takes, so it cannot be turned into a shopping list without logging in and browsing.
- AMASS stores SMPL-H body parameters, not a skeleton hierarchy, so it needs a different conversion path (SMPL → FBX/BVH) than the BVH sources — an extra pipeline step for a 138-bone UE rig.

### Gaps
- Per-subset download sizes in GB for DanceDB, Eyes Japan, MoVi, BMLmovi, SOMA, Transitions and TotalCapture are **not published outside the login**. They could not be obtained.
- SOMA, Transitions, TotalCapture and BMLmovi could not be characterised for stylised/runway content at all from public pages; BMLmovi appears to be the AMASS packaging of MoVi, but that identity was not confirmed from a primary source.
- AMASS per-file frame rates (the `mocap_framerate` field varies by source lab) could not be confirmed from a citable page.

---

## Q4. Dedicated fashion / runway / high-heel motion data

### Takeaway
**No public academic or hobbyist mocap dataset of fashion-show walking, modelling, or walking in
high heels was found.** The only runway-labelled mocap is commercial (StockMoves' fashion section)
or a single free FBX animation on Mixamo. The nearest free research data with the right label is the
Xia/BFA style-transfer set, which contains a `strutting` style in BVH on the CMU skeleton.

### Cited Findings
- StockMoves (Motek Entertainment) sells a mocap library that "contains also a FASHION related section" — commercial, sold per download — [StockMoves fashion mocap compilation](https://www.facebook.com/motekEnt/videos/stockmoves-fashion-mocap-data-compilation/541861856454849/)
- The Xia et al. style-transfer data, distributed as `mocap_xia.zip`, has **8 styles** and the style labels include `strutting`; a paper describing the same Mason et al. (2018) recordings lists the eight as 'angry', 'childlike', 'depressed', 'neutral', 'old', 'proud', 'sexy' and 'strutting', captured by a single actor in a mocap suit as planar walking and running — [Multi-Task Dynamical Systems](https://arxiv.org/pdf/2210.04023)
- The style-transfer bundle ships both the Xia set and the BFA set as `.bvh` files "retargeted to the standard skeleton of CMU mocap dataset", pre-processed into `xia.npz` / `bfa.npz` — [deep-motion-editing](https://github.com/DeepMotionEditing/deep-motion-editing)
- Download: the project page's Data button points to `https://drive.google.com/drive/folders/1C-_iZJj-PSUWZwh25yAsQe1tLpPm9EZ5` (the repo also lists a Baidu mirror) — [deepmotionediting style_transfer project page](https://deepmotionediting.github.io/style_transfer)
- Mixamo has a **`Catwalk Walk Forward`** animation, confirmed by third parties: a Sketchfab upload credits Adobe Mixamo for the character Amy using the "Catwalk walk Forward" animation, and a game project's PR describes converting "Catwalk Walk Forward" from Mixamo onto its avatar skeleton specifically because its CC0 alternative had no female animation — [Sketchfab: Amy Catwalk Walk Forward](https://sketchfab.com/3d-models/624230011-amy-catwalk-walk-forward-e0dddf1c6331452690c390a984e27dcd); [zaeon-os PR #433](https://github.com/web3-engineer/zaeon-os/pull/433)
- A Steam Workshop Mixamo package listing paired `Catwalk Walk Forward` with `Female Walk`, indicating a catwalk-named family in the library, though that item has been removed — [Steam Workshop Mixamo Animation Package](https://steamcommunity.com/sharedfiles/filedetails/?id=3103817811)
- Searches for a runway/fashion mocap dataset returned only commercial libraries, general-purpose walking datasets (CMU, MoVi) and the strutting style above; nothing dataset-shaped and runway-specific — [search result set above]

### Inferences
- `Catwalk Walk Forward` on Mixamo is the only asset found anywhere whose own label means "fashion runway walk", and it is free FBX with skinning/retargeting built in. For a UE-style rig this is also the lowest-friction single asset in this whole report.
- The Xia `strutting` and `sexy` clips are short, single-actor, planar and low-joint-count, so they are better used as *style references* (for a style-transfer or a hand-authored pass) than as final animation.

### Gaps
- No complete list of Mixamo's catwalk variants could be obtained (Mixamo's library is behind an Adobe-account web app, not a crawlable index), so beyond `Catwalk Walk Forward` the existence of turn/idle/stop catwalk variants is **unverified**. Mixamo's exact licence text for personal use was also not retrieved this round.
- Whether `sexy` is actually a directory/label inside the distributed `mocap_xia.zip` is **unconfirmed**: one paper lists it among the eight styles, but a search of the DeepMotionEditing material surfaced only `angry`, `childlike`, `strutting`, `old`, `neutral`, `proud`, `depressed`. Treat the 'sexy' label as probable but check the archive.
- No clip-level file names or counts for the Xia/BFA BVH could be read without downloading the Drive folder.
- StockMoves' fashion section could not be enumerated (no public take list) and is commercial anyway.

---

## Q5. Free BVH libraries worth mining

### Takeaway
Of the hobbyist libraries, none was confirmed to contain a model/catwalk walk. Truebones' free packs
are large but monster/animal-oriented and their licence text could not be confirmed; Quaternius UAL
is genuinely CC0 but ships FBX/GLB/Blend rather than BVH and is explicitly reported to lack female
animation; SFU's BVH database is free but non-commercial. The CMU BVH conversions (Q1) remain the
best free BVH route.

### Cited Findings
- Truebones offers a free "MONSTERBONES 2,500 PAK" "in both .FBX and .BVH formats", a free CMU-database conversion to FBX/BVH, a free Zoo pack of "Over 75 Animals and Animations", and a free 500 Pak — all via Gumroad, some with checkout code `truebonesfree` — [Truebones MonsterBones 2500](https://truebones.gumroad.com/p/new-free-monsterbones-2-500-pak-only-from-truebones); [Truebones free masterlist](https://truebones.gumroad.com/l/gnwcyc); [Truebones Zoo](https://truebones.gumroad.com/p/free-truebones-zoo-over-75-animals-and-animations); [Truebones 500 Pak](https://truebones.gumroad.com/p/free-500-pak-only-from-truebones)
- The only licence statement found for Truebones free content is a commenter in a 2010 Unity forum thread saying the free files cannot be used commercially — a secondhand claim from an old post, not current licence text — [Unity Discussions: Free .BVH from TrueBones](https://discussions.unity.com/t/free-bvh-motion-capture-files-from-truebones-com/378089)
- Quaternius Universal Animation Library: **CC0**, free for personal, educational and commercial use; shipped in **FBX, GLB and Blend** (BVH is not offered); the free "Standard" version includes 45 of the animations while the full 120+ set is the paid "Source" version. UAL 2 is a 130+ animation kit with root motion on locomotion, also CC0 with a free Standard version — [Quaternius UAL](https://quaternius.com/packs/universalanimationlibrary.html); [UAL on OpenGameArt](https://opengameart.org/content/universal-animation-library); [UAL 2 on itch.io](https://quaternius.itch.io/universal-animation-library-2); [Godot Asset Store](https://store.godotengine.org/asset/quaternius/universal-animation-library/)
- A developer working on a female-walk animation stated Quaternius (CC0) "doesn't have female animation", which is why they used Mixamo's catwalk clip instead — [zaeon-os PR #433](https://github.com/web3-engineer/zaeon-os/pull/433)
- SFU Motion Capture Database publishes BVH and is restricted from use in commercial products — [SFU MOCAP](https://mocap.cs.sfu.ca/)
- The Bandai Namco Research Motion Dataset (already on disk) is **CC BY-NC-ND**, which is the most restrictive of the sets in play: no derivatives, so retargeting/editing it is arguably outside the licence — [CG Channel: Download 3,000 free mocap moves from Bandai Namco](https://www.cgchannel.com/2022/05/download-3000-free-mocap-moves-from-bandai-namco-research/)
- `motioncapturedata.com` is live (HTTP 200 on 2026-10-27) — [motioncapturedata.com](https://www.motioncapturedata.com/)
- cgspeed's CMU BVH conversion licence, verbatim: "The files are free to use worldwide for any purpose." — [cgspeed BVH conversion release](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database)

### Inferences
- CC BY-NC-ND on the Bandai Namco set is worth flagging to the owner: the ND clause sits awkwardly with a retarget-and-edit pipeline, whereas CMU (no restrictions) and 100STYLE (CC BY) do not have that problem. If licence cleanliness matters, the CMU and 100STYLE material should carry the signature walk rather than Bandai Namco.
- Truebones' free packs are described entirely in terms of monsters, animals and generic gestures; nothing suggests a female runway walk, so the expected yield for this specific need is low relative to the download size.

### Gaps
- `motioncapturedata.com`'s catalogue, licence page and whether any take is a model/catwalk walk could not be determined — the site is up but its content was not enumerated.
- Truebones' current licence terms for the free packs are **unknown**; the Gumroad masterlist page returned only its title to automated fetching. The product pages must be read in a browser.
- Mixamo's licence terms for personal use were not retrieved (see Q4).
- ACCAD's own mocap library (as distinct from its AMASS packaging) was not examined this round.

---

## Q6. Is there any published mocap captured ON high heels?

### Takeaway
**No.** High-heel gait is well studied in biomechanics, but none of the heel-specific studies found
has a public data deposit, and no animation dataset was captured in heels. The best available
substitutes are toe-walking trials — which reproduce the plantarflexed ankle and the shortened,
quicker stride — from one open CC BY dataset (Lencioni et al.) and from CMU subject 136.

### Cited Findings
- Simonsen et al. (2012) compared 14 women walking barefoot versus in **9 cm heels** and found high-heeled walking made subjects "flex the knee joint significantly more in the first half of the stance phase". No data deposit was found for it — [Walking on High Heels Changes Muscle Activity and the Dynamics of Human Walking Significantly (ResearchGate record)](https://www.researchgate.net/publication/221723818_Walking_on_High_Heels_Changes_Muscle_Activity_and_the_Dynamics_of_Human_Walking_Significantly)
- Cronin et al., "Long-term use of high-heeled shoes alters the neuromechanics of human walking" (J Appl Physiol 2012), recorded **51 markers at 100 Hz** and reports that ankle range of motion was smaller in heels; no public repository is mentioned — [Journal of Applied Physiology](https://journals.physiology.org/doi/full/10.1152/japplphysiol.01402.2011)
- "Kinetics of High-Heeled Gait" exists as a published study but no open data was located — [ResearchGate record](https://www.researchgate.net/publication/10944908_Kinetics_of_High-Heeled_Gait)
- **Open and directly downloadable, with toe/heel-walking conditions:** Lencioni et al., "Human kinematic, kinetic and EMG data during different walking and stair ascending and descending tasks" (Scientific Data, 2019). Tasks are level walking at different velocities, **toe-walking and heel-walking**, and stair ascent/descent. It provides joint angles in all three anatomical planes computed by a supplied MATLAB function `CalcAngle.m`, plus kinetics and EMG. Licence **CC BY 4.0**; Figshare collection DOI `10.6084/m9.figshare.c.4494755` — [Nature Scientific Data paper](https://www.nature.com/articles/s41597-019-0323-z); [Figshare collection](https://springernature.figshare.com/collections/Human_kinematic_kinetic_and_EMG_data_during_level_walking_toe_heel-walking_stairs_ascending_descending/4494755); [PMC copy](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6897988/)
- Other open gait datasets found, none with heels: a full-body Vicon ramp-walking dataset of 13 healthy young adults with 3D markers, force plates and anthropometrics on Figshare — [Scientific Data](https://www.nature.com/articles/s41597-025-06535-y); K2MUSE, a lower-limb multimodal walking dataset spanning task and acquisition variability — [arXiv:2504.14602](https://arxiv.org/pdf/2504.14602); the Toronto Older Adults Gait Archive (2D video + 3D inertial, lower body, 14 older adults) — [PMC](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9272879/); FootGait3D, foot-ankle point clouds from 46 subjects via five depth cameras, on Hugging Face — [arXiv:2507.11037](https://arxiv.org/html/2507.11037v1); a high-resolution plantar-pressure dataset "across varying footwear and walking speeds" (pressure, not mocap) — [arXiv:2502.17244](https://arxiv.org/pdf/2502.17244); a calibrated multi-view gait dataset, 166 sequences from 32 people with Vicon at 100 Hz, whose paper notes that among the gait-recognition databases it reviewed "only one publicly available dataset contains moCap data" — [Springer](https://link.springer.com/article/10.1007/s11042-019-07945-y)
- Toe-walking analogues in the animation corpora, already catalogued above: CMU `136_27`/`136_28` Walk on Toes, `136_29`/`136_30` Walk Backwards on Toes, `136_05`–`136_08` Walk on Toes Bent Forward, `136_13`–`136_16` Walk on Toes Crouched, `136_31`/`136_32` Quail quick little steps — [CMU index text](https://github.com/una-dinosauria/cmu-mocap/blob/master/cmu-mocap-index-text.txt) — and 100STYLE `Tiptoe_FW.bvh`, `OnToesBentForward_FW.bvh`, `OnToesCrouched_FW.bvh`, `FairySteps_FW.bvh` — [100STYLE project page](https://www.ianxmason.com/100style/)

### Inferences
- **Terminology trap worth passing on:** in both biomechanics and 100STYLE, "heel-walking" / `OnHeels` means walking on the heels with the forefoot lifted (dorsiflexed) — the *opposite* of what high-heeled footwear does. High-heeled footwear forces **plantarflexion**, so the correct donor motion is **toe-walking** (`Tiptoe`, `OnToesBentForward`, CMU `136_27`). Picking `OnHeels_FW.bvh` for a character in platform heels would produce exactly the wrong ankle.
- Given the 257 mm heel in the bind pose, the practical route is: take the style/carriage from a flat-shod stylised walk (CMU `137_38`/`137_24`, 100STYLE `Strutting`/`WiggleHips`/`CrossOver`), then apply the heel adaptations by hand or procedurally — shorten stride, raise cadence, hold the ankle plantarflexed, increase anterior pelvic tilt, reduce knee extension at mid-stance — using the Lencioni toe-walking trials and the Simonsen/Cronin findings (more stance-phase knee flexion, reduced ankle ROM) as quantitative targets.
- The "heel-strike-first" part of the brief is in tension with real heeled gait: in high heels heel contact is brief and the foot is already plantarflexed, so the visual read of a heel-first runway strike is more a styling choice than a physical one. Worth flagging to the owner before the retarget rather than after.

### Gaps
- No public 3D mocap dataset captured in high heels was found, in any format, academic or hobbyist. If one exists it is not discoverable through the searches run here; the realistic alternatives are to request data from the Simonsen or Cronin groups directly, or to synthesise the heel adaptation.
- The Lencioni dataset's subject count, marker set, sampling rate and file format were not read from the paper (only the task list, licence and DOI were confirmed), so the per-file specifics are unverified.
- Footwear is not reported in the search results for the ramp-walking, multi-view gait, Toronto or FootGait3D datasets, so none can be assumed to include heels.

---

## Cross-cutting practical notes for the 30 fps / 138-bone UE retarget

### Cited Findings
- CMU: Vicon at **120 Hz**, 41 markers, ASF/AMC in inches (scale factor 0.056444 to metres), joint-angle data only — [CMU FAQ](http://mocap.cs.cmu.edu/faqs.php); cgspeed BVH add a T-pose as frame 1 facing **+Z** and use MotionBuilder-style joint names — [cgspeed](https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/the-motionbuilder-friendly-bvh-conversion-release-of-cmus-motion-capture-database)
- 100STYLE: **60 fps**, **28 bones**, `Hips` root, four-segment chest chain, `Ankle → Toe` in each leg, no fingers, T-pose at both ends of every take with `Frame_Cuts.csv` giving trim indices — [100STYLE project page](https://www.ianxmason.com/100style/)
- Xia/BFA style BVH are already "retargeted to the standard skeleton of CMU mocap dataset" — [deep-motion-editing](https://github.com/DeepMotionEditing/deep-motion-editing)
- Bandai Namco set (on disk) is CC BY-**NC-ND** — [CG Channel](https://www.cgchannel.com/2022/05/download-3000-free-mocap-moves-from-bandai-namco-research/)

### Inferences
- Decimation ratios to 30 fps: CMU 120 → 30 is exact 4:1; 100STYLE 60 → 30 is exact 2:1. Both avoid interpolation artefacts. AMASS subsets vary by source lab and will not all be integer ratios.
- 100STYLE's 28-bone skeleton and CMU's ~31-joint skeleton both map cleanly onto the spine/limb core of a 138-bone UE rig; neither supplies fingers (100STYLE explicitly), so hand poses for a runway walk must be authored.
- Licence ranking for a personal non-commercial game, cleanest first: CMU (no restrictions at all) → cgspeed CMU BVH ("free to use worldwide for any purpose") → 100STYLE (CC BY 4.0, just credit) → Quaternius (CC0 but FBX and no female clips) → SFU (non-commercial) → Bandai Namco (CC BY-NC-ND, the ND clause conflicting with editing) → AMASS (registration plus academic-use terms) → Truebones (unknown).
