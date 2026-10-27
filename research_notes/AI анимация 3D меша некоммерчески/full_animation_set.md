# Complete Animation Set for a Humanoid Character — Authoring Guide, Checklist, Polish Layer (as of October 2026)

Scope note on sourcing: I was able to verify the *architecture*, *naming conventions*, *pack-level counts*, *tool versions* and *research papers* from primary or near-primary sources. I was **not** able to retrieve clip-by-clip lists for ALS or Kubold packs, nor any documented per-character animation count from a shipped AAA title (see Gaps under Q1 and Q2). Where I give a concrete clip list or duration, I mark it clearly as an **inference / practitioner convention** rather than a cited fact, so the report-writer does not present it as sourced.

---

## Q1: The complete animation list — what a fully-animated humanoid needs, by category

### Takeaway
No single authoritative "complete list" exists in public documentation; the de facto references are Epic's Game Animation Sample Project (500+ mocap clips covering locomotion, ledge scaling and vaulting), the ALS/ALS-Refactored state-machine architecture (gait × stance × overlay-mode × locomotion-state matrix), and commercial packs whose category breakdowns (MoCap Online Mobility Pro: 223+ clips / 527 files) are the closest thing to a published minimum locomotion set. The structure is better documented than the clip list: coverage is defined by the *state matrix*, not by a flat list.

### Cited Findings

**Epic's Game Animation Sample Project (GASP) — the current reference locomotion set**
- GASP ships "over 500 free animations" compatible with all UE Mannequins — [CGW press item](https://www.cgw.com/Press-Center/News/2024/Epic-Games-releases-Game-Animation-Sample-Projec.aspx); Epic's own blog announcement is titled "Get over 500 free animations with the Game Animation Sample Project" — [Unreal Engine blog](https://www.unrealengine.com/blog/game-animation-sample) (page returns HTTP 403 to automated fetch; title/count confirmed via search result metadata and the CGW item).
- Epic's documentation describes the content as "a suite of motion captured animations" covering **locomotion, scaling ledges, and vaulting**, and describes the release as "centered on Motion Matching for third-person locomotion on a capsule-driven movement model," also covering "montages and entry frames for gameplay actions" — [Game Animation Sample Project docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- GASP folder layout: Animation Sequences at `Content/Characters/UEFN_Mannequin/Animations`; motion-matching data at `Content/Characters/UEFN_Mannequin/Animations/MotionMatchingData/`, with `CHT_PoseSearchDatabases` holding the Chooser Table and `Databases` holding the Pose Search Databases — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- GASP databases are split by gameplay context: the docs reference **walk databases** (used when the character walks) and a **pivot database** (checked by the `IsStarting` logic); Choosers filter which databases are eligible based on context such as walk versus run. The docs note that **arcs are planned for future databases** and that leaning logic would need adjusting once added — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).

**ALS / ALS-Refactored — the state-machine reference**
- The Fab "ALS animation pack" listing offers *only the animation sequences* from the ALS sample project, retargeted for UE5 Mannequin skeletons, "split into subfolders by animation type," and notes Epic Games originally created the animations — [Fab listing](https://www.fab.com/listings/d366bdfa-a665-424e-be88-0c9629582489).
- ALS-Refactored (the C++ rewrite of ALS V4) splits content into three categories: `ALS` (main content), `ALSCamera` (camera-related), `ALSExtras` (other optional content). It uses **multiple linked animation blueprints instead of one monolithic blueprint**, and **animation layer interfaces switch overlay states at runtime** — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored specifics relevant to authoring: **Control Rig handles IK and other bone manipulation**; virtual bone count is reduced and IK bones can be disabled in favour of virtual bones only; a skeleton asset action "Setup Als Skeleton" configures curves, slots and virtual bones; `Slot` overlay curves control blending of overlay poses with montages in `Layering` slots; new nodes `Blend Curves` and `Blend Poses by Gameplay Tag`; **gameplay tags replace C++ enums such as `EAlsOverlayMode`**; mantling uses a root motion source; foot locking and foot/pelvis offsets are reworked for stairs and slopes — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored ships `B_Als_AnimationModifier_FootSyncMarkers`, an **animation modifier for automatic foot sync markers** — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- The GASP-ALS project combines ALS overlays with Unreal's motion matching sample, and states that **all overlays from ALS are included** — [gokhocse/GASP-ALS](https://github.com/gokhocse/GASP-ALS).

**MoCap Online "Mobility" — a published category breakdown of a locomotion-only set**
- Mobility Pro: "223+ Mobility Pro Animations - All Formats Included"; formats are FBX, BIP, and versions for Unreal Engine, Unity, Blender and iClone — [itch.io Mobility Pro](https://mocaponline.itch.io/mobility-pro-animations).
- Categories named on the Mobility Pro listing: **Idle** (relaxed and combat stances, fidgets); **Walk** (forward, backward, strafes); **Run and jog** (including backward and strafe variants); **Crouch and crouch-walk**; **Jump and hop** (including split jumps); **Turn and pivot** (described as in-place); **Aim offsets** (look up, down, around); **Death** (falling backward, forward, sideways); **Transitions** (between states); plus conversations — [itch.io Mobility Pro](https://mocaponline.itch.io/mobility-pro-animations).
- Superhive's listing for the same pack breaks it down as **18 aim offsets and 21 split jumps, for 527 total files**; Mobility Basic is **122 animations (10 aim offsets, 7 split jumps, 195 files)**; Mobility Starter is **35 animations (3 aim offsets, 2 split jumps, 59 files)** — [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations). A Fab listing for Starter cites "235+" for Pro, so the Pro figure has drifted between versions — [Fab listing](https://www.fab.com/listings/2c29cfb9-07c4-4e02-a466-5e7ef863633d).
- The Pro listings describe **eight-direction movement with diagonal strafes**, and **turn animations with transitions** — [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations).

**Mixamo — catalogue structure**
- Mixamo's library is described as "over 2,000 animations," grouped as "basic locomotion (walking, running, and jumping), combat (such as boxing and taekwondo), dance, idle poses, adventure, thriller-style motions, and more"; walks and runs come in styles such as casual, military, zombie — [School of Motion](https://schoolofmotion.com/blog/why-mixamo-is-still-one-of-the-best-animation-resources).
- A community bulk-conversion thread counted **463 in-place and 2,491 root-motion files** across the catalogue, and names Mixamo's weapon/discipline packs: **Melee Axe, Great Sword, Longbow, Pistol, Rifle, Sword and Shield, MMA, Takedown** — [Adobe community thread](https://community.adobe.com/questions-696/all-mixamo-animations-converted-to-daz-studio-bvh-and-fbx-589323).
- A competitor's comparison characterises Mixamo as having "a small set of generic melee and kick animations" — [MoCap Online blog](https://mocaponline.com/blogs/mocap-news/mixamo-alternatives-character-animations).

### Inferences (practitioner-convention checklist — NOT sourced clip-for-clip)
The cited sources establish the *axes*; the list below is the standard decomposition those axes imply. Treat durations/frame counts as conventions at 30 fps, not citations.

**A. Locomotion (the non-negotiable core; GASP + Mobility Pro categories imply ~120–220 clips)**
- Idle: 1 per stance (relaxed / alert / combat / crouched) — loop, in-place, 60–120 f. Mobility Pro explicitly ships "relaxed and combat stances."
- Idle breaks / fidgets: 3–6 one-shots, 90–240 f, must start and end on the exact idle pose so they can be blended in at zero cost.
- Walk / jog / run / sprint × 8 directions: Mobility Pro's "eight-direction movement with diagonal strafes" is the reference. Loops, foot-phase matched, 24–40 f (run) to 40–60 f (walk) per cycle. In-place for blendspace use; root-motion duplicates only if the engine drives movement from animation.
- Start / stop transitions: per speed, per direction, per starting foot (plant-left / plant-right). One-shot, root-motion preferred, 15–30 f. GASP's `IsStarting` logic and dedicated **pivot database** confirm starts/pivots are first-class data, not blended-on-the-fly.
- Turn-in-place at 45/90/180, left and right: one-shot, in-place but with yaw delta baked, 20–45 f. Mobility Pro lists "Turn and pivot (in-place)."
- Crouch set: idle, walk 8-dir, turn-in-place, crouch-to-stand and stand-to-crouch transitions.
- Jump chain: takeoff (one-shot, ~10–15 f), apex/in-air loop, land-soft (~12 f), land-hard/roll-out (~25–40 f), plus a separate fall loop for long drops. Mobility Pro's "split jumps" (21 of them) are exactly this decomposition — one jump split into separately-triggerable phases.
- Traversal: GASP covers **scaling ledges and vaulting** as shipped content; ALS-Refactored implements mantling via a **root motion source**. Author at multiple heights (low vault, mid mantle, high climb) since these are height-parameterised.

**B. Overlay / stance layer (ALS's "overlay modes")**
ALS's architecture makes this a separate, cheap axis: one additive or per-bone-blended **upper-body pose or loop set** per weapon/prop/attitude (unarmed, rifle, pistol, bow, two-handed, injured, carrying, torch, handcuffed…). ALS-Refactored selects these with `Blend Poses by Gameplay Tag` and layers them through `Layering` slots. This is why an overlay costs ~1–5 clips rather than a full locomotion set re-capture.

**C. Combat**
- Stance/guard idle per weapon; light/medium/heavy attack; 2–4-hit combo strings with distinct windup/contact/recovery windows; special/charged; grab/throw (attacker + matching victim clip, frame-locked).
- Hit reactions: direction (front/back/left/right) × height (head/torso/legs) × magnitude (flinch/stagger/launch) — a 4×3×2 grid is 24 clips; most projects ship a 4×2 subset plus additive flinches.
- Blocks (idle, impact-absorb), parry, dodge/roll in 4–8 directions, knockdown (front/back), getup (from front/back/side), death variants. Mobility Pro ships **death falling backward, forward and sideways** as the minimum death set.

**D. Interaction**
Pick up (high/mid/low), put down, open door (push/pull/slide), push/pull sustained loops, lever/button press, sit-down, sit idle, stand-up, lean (wall/rail), use-item, reload per weapon.

**E. Emotive / dialogue**
Mobility Pro lists **conversations** as a category; Mixamo groups **idle poses, dance, thriller-style motions**. Author: talk-gesture loops (2–4 energy levels), listen idle, nod/shake, point, shrug, victory, defeat, taunt, plus 2–4 personality idles that define the character.

### Gaps
- **No clip-level enumeration of ALS or GASP exists in public docs.** Epic's GASP page gives folder paths, database categories and node names, but explicitly no clip names, counts per category, database count, or pose counts. The Fab ALS pack listing confirms subfolders-by-type but does not enumerate them. To get the real list you must open the project in-editor or browse the `Animations` folder in the repo.
- **ALS-Refactored's README does not name its gait/stance/overlay/locomotion state values.** It mentions overlay modes only as a former enum (`EAlsOverlayMode`) now replaced by gameplay tags, and names only `mantling` as a movement state. The canonical ALS state names (Walking/Running/Sprinting gaits; Standing/Crouching stances; Grounded/InAir/Mantling/Ragdolling states) are widely repeated by practitioners but I could not verify them from the repo README in this pass.
- **Kubold pack clip lists: not found.** Searches surfaced only kubold.com's blog and store landing pages, plus unrelated "Kobold" iClone creature packs. The individual Animset Pro product pages (Longsword, Rifle, Pistol, Movement Animset Pro) were not retrievable; their published per-pack clip lists remain unverified.
- **Explosive LLC pack contents: not found** in this pass.
- No GDC talk specifically enumerating a complete state-machine clip list was retrievable; most GDC Vault recordings are members-only.

---

## Q2: How many clips and how many seconds for "full" coverage? (tiered)

### Takeaway
No source gives a per-character animation count for a shipped game. The only hard, citable numbers are pack and sample-project totals: **35 clips** (minimum shippable locomotion sample), **122 clips** (basic locomotion), **223+ clips / 527 files** (full locomotion-only professional pack), **500+ clips** (Epic's motion-matching locomotion + traversal reference), **~2,000+ clips** (Mixamo's whole multi-genre catalogue). These bracket the tiers well, but durations are not published.

### Cited Findings
- **35 animations / 59 files** — MoCap Online Mobility Starter, described as "a sample drawn from the Pro pack" — [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations); [itch.io Mobility Starter](https://mocaponline.itch.io/mobility-starter).
- **122 animations / 195 files** (10 aim offsets, 7 split jumps) — Mobility Basic — [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations).
- **223+ animations / 527 files** (18 aim offsets, 21 split jumps) — Mobility Pro, covering idle through death and transitions — [itch.io Mobility Pro](https://mocaponline.itch.io/mobility-pro-animations); [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations).
- **"Over 500" animations** for locomotion + ledge scaling + vaulting in Epic's GASP — [CGW](https://www.cgw.com/Press-Center/News/2024/Epic-Games-releases-Game-Animation-Sample-Projec.aspx), [Unreal Engine blog title](https://www.unrealengine.com/blog/game-animation-sample).
- Epic's docs note GASP's Motion Matching Database LOD setting exists so you can observe how data scales "when using Pose Search Databases with more dense or sparse animation data," and describe scenarios selecting "a dense dataset with hundreds of animations versus a sparser set with less than a hundred" — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- **Over 2,000 animations** in Mixamo's catalogue — [School of Motion](https://schoolofmotion.com/blog/why-mixamo-is-still-one-of-the-best-animation-resources); a community conversion counted **463 in-place + 2,491 root-motion files** — [Adobe community](https://community.adobe.com/questions-696/all-mixamo-animations-converted-to-daz-studio-bvh-and-fbx-589323).
- For motion-matching data volume specifically: Ubisoft's Kristjan Zadziuk's first test used **"10-15 minutes worth of untouched data,"** all in one file, which was enough to reproduce weight shifts and quick direction changes — [DualShockers](https://www.dualshockers.com/ubisoft-introduces-motion-matching-the-first-step-to-next-gen-animation/). Simon Clavet's GDC 2016 talk describes importing **"5 or 10 minutes of a person running around"** directly into the engine — same source / [GDC Vault: Motion Matching and The Road to Next-Gen Animation](https://gdcvault.com/play/1022985/Motion-Matching-and-The-Road).

### Inferences (tiering — counts anchored to the cited pack sizes; durations are my arithmetic, not sourced)
Assume 30 fps and typical clip lengths (locomotion loops 0.8–2.0 s; one-shot transitions 0.4–1.2 s; reactions 0.8–2.0 s; idles and fidgets 2–8 s).

| Tier | Clips | Rough total runtime | What it buys you |
|---|---|---|---|
| **Minimal viable humanoid** | **30–40** | ~1.5–2.5 min | Mobility Starter scale (35 clips, cited). Idle, walk/run fwd+back+2 strafes, 4 turn-in-place, jump 3-phase, fall, crouch idle+walk, 1 hit react, 1 death, 1 pick-up, 1 talk loop. Reads as "animated," not "alive." |
| **Solid / indie-complete** | **120–250** | ~6–14 min | Mobility Basic→Pro scale (122→223, cited). Full 8-dir at 3 speeds, start/stop per foot, 45/90/180 turns, complete crouch set, split jumps, 2–3 overlay stances, 8–12 hit reactions, 3 deaths, 10–15 interactions, 6–10 emotive, 4–6 idle fidgets. This is the realistic solo-project target. |
| **AAA-complete (single character)** | **500–1,500+** | ~25–75 min+ | GASP's 500+ is *locomotion and traversal only* (cited) — so a character with combat, interactions, dialogue and cinematics on top lands several times higher. Epic's own docs talk about "hundreds of animations" per dense motion-matching database. |
| **Motion-matching alternative** | continuous capture, not clips | **5–15 min of continuous capture** to start (cited: Clavet 5–10 min; Zadziuk 10–15 min) | Replaces much of the discrete locomotion graph, but needs planned coverage (see Q4). |

### Gaps
- **No published per-character animation count from a shipped AAA game was found.** Searches surfaced Naughty Dog's GDC 2011 NPC-animation talk (which discusses scaling NPC *count*, not per-character clip count), a Skylanders cinematics talk about a "cast of 300 plus characters," and Daniel Holden's GDC 2018 data-driven animation talk — none gives a per-character total. The widely-repeated figures for e.g. The Last of Us or Uncharted could not be sourced.
- **No source gives total seconds/minutes for any pack or sample project.** All published figures are clip or file counts. The runtime column above is my estimate from typical clip lengths and should be labelled as such.
- GASP's actual database count, pose counts, and dense-vs-sparse split are not in Epic's docs.

---

## Q3: Naming conventions and library organization

### Takeaway
Epic publishes an official recommended convention — `[AssetTypePrefix]_[AssetName]_[Descriptor]_[OptionalVariantLetterOrNumber]` with `AS_` for Animation Sequence, `AM_` for Montage, `BS_` for Blend Space, `ABP_` for Animation Blueprint — but it deliberately does not cover every animation type (no Aim Offset, Control Rig or Pose Asset prefix), telling you to extrapolate. Organization that a state machine can consume means folder-by-state-category plus a separate data layer (Chooser Tables / Pose Search Databases in UE5) that selects clips by gameplay context rather than by name.

### Cited Findings
- Epic's official pattern: **`[AssetTypePrefix]_[AssetName]_[Descriptor]_[OptionalVariantLetterOrNumber]`**, described as reflecting how Epic names assets in sample projects such as the In-Camera VFX Production Test — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).
- Animation-relevant prefixes on that page: **`AS_`** Animation Sequence, **`AM_`** Montages, **`BS_`** Blend Space, **`ABP_`** Animation Blueprint, **`SKEL_`** Skeleton, **`Rig_`** Rig, **`PHYS_`** Physics Asset, **`SK_`** Skeletal Mesh, **`LS_`** Level Sequence, **`EDIT_`** Sequencer Edits — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).
- The page **does not list prefixes for Aim Offset, Control Rig, or Pose Asset**, and advises: "If you are using an Asset type not listed, use the existing list as a guideline for your naming convention for that Asset" — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).
- GASP in practice uses `ABP_` (Animation Blueprint), `CBP_` (Character Blueprint), `CHT_` (Chooser Table), and `RTG_UEFN_to_{name}` for IK Retargeter map entries — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- Epic's GASP docs state that GASP **does not describe a naming scheme for animation clips or databases** — clip naming is left to the project.
- The 4.27-era version of the same doc is still live and is the one many older tutorials cite — [UE 4.27 AssetNaming](https://docs.unrealengine.com/4.27/ProductionPipelines/AssetNaming); Tom Looman maintains a widely-used expanded community version — [Unreal Engine Naming Convention Guide](https://tomlooman.com/unreal-engine-naming-convention-guide/).
- An independent validator package reproduces Epic's prefix table including `AS_`, `AM_`, `BS_`, `Rig_`, `SKEL_` under a "Skeletal Mesh Animations" heading — [bootloader-uassets-validator (PyPI)](https://pypi.org/project/bootloader-uassets-validator) (secondary source; the prefixes are confirmed on Epic's own page above).
- Organizationally, GASP separates **Animation Sequences** (`.../Animations`) from **motion-matching selection data** (`.../Animations/MotionMatchingData/`, with `CHT_PoseSearchDatabases` for the Chooser Table and `Databases` for Pose Search Databases). Databases **carry tags, which the Animation Blueprint reads to make additional selections** — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- ALS-Refactored organizes by *module* rather than by clip type (`ALS` / `ALSCamera` / `ALSExtras`) and uses **gameplay tags** as the selection key instead of enums, consumed by a `Blend Poses by Gameplay Tag` node — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).

### Inferences
- A clip name that a state machine can consume mechanically should encode, in fixed order: `AS_<Character>_<Stance>_<Gait>_<Action>_<Direction>_<Variant>` — e.g. `AS_Hero_Crouch_Walk_Loop_Fwd`, `AS_Hero_Stand_Run_Start_Bwd_LeftFoot`, `AS_Hero_Combat_Idle_Fidget_01`. This follows Epic's `prefix_name_descriptor_variant` pattern literally while making directions and foot-phase greppable.
- Because Epic gives no Aim Offset prefix, the community convention `AO_` is the sensible extrapolation the doc's own instruction invites; `CR_` for Control Rig and `POSE_`/`PA_` for Pose Asset likewise.
- The architectural lesson from both ALS-Refactored (gameplay tags) and GASP (Chooser Tables + database tags) is that **selection should live in data, not in names or in a hand-wired graph**. For a solo project this means: name clips consistently, then register them once into a tag/table layer, so adding a clip is a data edit rather than a graph edit.
- Practical folder layout implied by both references: `/Animations/Locomotion/{Stand,Crouch}/{Idle,Walk,Jog,Run,Sprint,Turn,StartStop,Pivot}`, `/Animations/Traversal/{Jump,Fall,Mantle,Vault,Climb}`, `/Animations/Overlays/<WeaponOrAttitude>/`, `/Animations/Combat/{Attack,Hit,Block,Dodge,Death}`, `/Animations/Interact/`, `/Animations/Emote/`, `/Animations/Additive/`, plus `/MotionMatchingData/` or `/StateMachine/` for the selection layer.

### Gaps
- Unity has no equivalent first-party naming standard that I could source; Unity project conventions in the wild are community-driven.

---

## Q4: Blending and state-machine architecture; motion matching as the alternative

### Takeaway
The hard technical requirements are confirmed by ALS-Refactored and GASP: locomotion loops need **foot sync markers** (ALS ships an automatic animation modifier for this), the upper body is handled by **layered/additive overlays blended through named slots and curves** rather than duplicated full-body clips, and IK is a **Control Rig pass** on top. Motion matching replaces the graph with a pose/trajectory search driven by current and predicted velocity and facing plus a pose history, and the cited starting data volume is 5–15 minutes of continuous capture, with *coverage* mattering more than raw minutes.

### Cited Findings

**Blending requirements**
- ALS-Refactored ships `B_Als_AnimationModifier_FootSyncMarkers`, "an animation modifier for automatic foot sync markers" — i.e. matched foot phase across locomotion loops is a required, tooled-for property, not an optional nicety — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored uses **`Slot` overlay animation curves to control blending of overlay poses with montages in `Layering` animation slots**, and provides a `Blend Curves` node operating on virtual-bone curves (`VB curves`) — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored uses **Control Rig for IK and other bone manipulation**, with reworked **foot locking and foot/pelvis offsets for stairs and slopes** — the procedural-IK pass for uneven ground is part of the architecture, not the clip data — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored reduces virtual bone count and allows disabling IK bones in favour of virtual bones only — relevant to skeleton setup before authoring — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- ALS-Refactored deliberately uses **multiple linked animation blueprints instead of one monolithic blueprint**, with animation layer interfaces switching overlay states at runtime — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- Epic's naming doc confirms **Blend Space (`BS_`)** as a first-class asset type for 1D/2D blending — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).

**Motion matching**
- GASP's `Generate Trajectory` function "builds the trajectory that Motion Matching queries, including **current and predicted velocities and facing**" — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- The `Pose History` node "stores previous poses and samples a list of bones," and also "sets how often poses are collected and how many are kept" — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- Databases use a **Pose Search Schema**; Choosers filter eligible databases by gameplay context; databases carry tags read by the Animation Blueprint — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- To use a custom character with GASP you need a **Skeletal Mesh, an IK Rig, and an IK Retargeter using `UEFN_Mannequin` as the source** — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- Data volume: Zadziuk's first Ubisoft test used **10–15 minutes of untouched capture in a single file**, enough to reproduce weight shifts and quick direction changes — [DualShockers](https://www.dualshockers.com/ubisoft-introduces-motion-matching-the-first-step-to-next-gen-animation/); Clavet's GDC 2016 talk describes **5 or 10 minutes** imported directly — same source, [GDC Vault](https://gdcvault.com/play/1022985/Motion-Matching-and-The-Road).
- **Coverage beats volume:** a motion-matching authoring guide states that one of the primary factors for quality is *coverage*, and that past a certain point there are diminishing returns — [Motion Matching for Unity: Guide for Animation Authoring (PDF)](https://motion-matching-for-unity.readthedocs.io/en/latest/Motion%20Matching%20-%20Guide%20for%20Animation%20Authoring.pdf); see also the [Quick Start Guide](https://motion-matching-for-unity.readthedocs.io/en/latest/quick-start/).
- Ubisoft's capture planning used **"Dance Cards"**: cards indicating all the actions needed for the capture, "repeated for walk, jog and run" — [DualShockers](https://www.dualshockers.com/ubisoft-introduces-motion-matching-the-first-step-to-next-gen-animation/).
- Epic's docs confirm motion matching still needs **montages and entry frames for gameplay actions** — i.e. motion matching covers locomotion, not the whole set — [GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine).
- Daniel Holden's GDC 2018 session argues data-driven systems "vastly reduce the complexity and manpower involved in building an animation system for character control" — [Game Developer / GDC listing](https://www.gamedeveloper.com/art/2014-gdc-animation-bootcamp-speakers) (talk referenced in search results; primary slides not retrieved).

### Inferences
- The authoring contract for a locomotion loop, derived from the above: (1) same number of foot contacts and the **same foot-phase offset** across all clips that will blend in one blendspace — one standard is "frame 0 = left foot contact" for every cycle; (2) **root at origin, facing +X, zero net translation** for in-place clips, or a clean monotonic root track for root-motion clips; (3) identical cycle length within a speed tier where possible, or sync-marker-driven rescaling where not; (4) consistent pelvis height so the blend does not bob.
- 1D blending for speed (`idle→walk→jog→run→sprint`); 2D for direction × speed (the classic 8-direction + magnitude blendspace); aim offsets as a separate 2D (yaw × pitch) additive. Mobility Pro's 18 aim offsets imply a 3×3 to 5×4 sampled grid.
- Additive layering is the correct answer for breathing, aim, lean, flinch and injury — authored as *difference* clips against a reference pose, so they stack onto any locomotion state without multiplying the clip count. ALS's `Layering` slot + `Slot` curve design is the model.
- For a solo project, the honest trade-off: motion matching needs **continuous, planned capture** (a "dance card" of starts, stops, turns, speed changes repeated at walk/jog/run) which is hard to fake from Mixamo-style discrete clips; a hand-built state machine works fine with discrete clips but costs the start/stop/pivot transitions explicitly. Mixing is viable — GASP-ALS exists precisely to run ALS overlays on top of motion-matched locomotion ([gokhocse/GASP-ALS](https://github.com/gokhocse/GASP-ALS)).

### Gaps
- GASP's Pose Search Schema feature list (which bone positions/velocities and trajectory samples are weighted, and at what time offsets) is not in Epic's docs page; nor are the `Pose History` sampling rate/count values.
- No source quantifies a minimum motion-matching database size in poses or megabytes.
- I did not verify Unity's Kinematica status (deprecated) or current Unity motion-matching options in this pass.

---

## Q5: Secondary and procedural motion — what automates it in 2026

### Takeaway
Cascadeur is the only tool I could verify that *physically corrects and generates* secondary motion from AI/physics rather than simulating it at runtime; version **2026.2** is current, adding Additive Animation Layers (alpha) and easing, with AutoPhysics' Collision Penetration Cleaning now fulcrum-aware and on by default. In Blender the live option is the **Wiggle Bones** extension (a refactor of Wiggle 2, listed for Blender 4.5 LTS+ and targeting 5.0+), since original Wiggle 2 has documented bake failures on 4.5.

### Cited Findings

**Cascadeur (current: 2026.2)**
- **Cascadeur 2026.2** adds **Additive Animation Layers (Alpha)** — motion split into layers, each storing its own animation data, with enabled layers added together; described as the "first iteration" with more planned — plus **Easing** controls (changing frame spacing along an interpolated path to make motion faster/slower within an interval without changing the trajectory's shape), and Chinese/Japanese/Korean UI — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- 2026.2 **AutoPhysics**: "Collision Penetration Cleaning now takes fulcrums into account and is included by default in AutoPhysics passes involving the ground or kinematic colliders" — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- 2026.2 **AutoPosing**: hands and feet adjust to surrounding scene geometry, which helps with characters in contact with the environment; quadruped AutoPosing improved — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- 2026.2 **AI tools**: **Inbetweening** and **Motion Generation (formerly Root Motion)** retrained on larger datasets for better output — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- 2026.2 also adds **BVH animation import**, multiple materials on one mesh, new camera exposure controls, faster Rigging Tools, and Python support for custom PySide/QML UI with IntelliSense stubs — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- **Cascadeur 2026.1** introduced Collision Penetration Cleaning, **Constraints in AutoPhysics**, major Quadruped AutoPosing improvements, a fully integrated **Filament rendering engine**, a new **Unreal Engine Live Link**, and an AI-powered Root Motion Tool — [Cascadeur blog, via search result text](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages) (2026.1 summary surfaced alongside the 2026.2 post; I did not fetch the 2026.1 post itself).
- **AutoPhysics' original definition:** introduced in Cascadeur **2021.1** as a tool that "analyses an existing animation and generates a physically accurate version of it" — [CGChannel: Nekki ships Cascadeur 2021.1](https://www.cgchannel.com/?p=125620).
- Cascadeur **2022.2 EA** is documented as improving "AutoPosing, AutoPhysics, secondary motion and quick rigging" — i.e. secondary motion is an explicit Cascadeur feature area — [Cascadeur blog](https://cascadeur.com/blog/general/cascadeur-20222ea-improves-autoposing-autophysics-secondary-motion-and-quick-rigging).
- Independent review of Cascadeur 2024.2 describes it as "automated tools that supercharge your character animation" — [Creative Bloq review](https://www.creativebloq.com/3d/cascadeur-2024-2-review); further 2024.2 coverage at [80.lv](https://80.lv/articles/nekki-teases-cascadeur-2024-2-features/) and [GameFromScratch](https://gamefromscratch.com/cascadeur-2024-2-released/).

**Blender spring bones / jiggle**
- **Wiggle Bones** (Blender Extensions) is "a refactor of Wiggle 2" that "aims to ensure compatibility with Blender 5.0 and newer and resolve some of the long-standing issues"; listed compatibility **Blender 4.5 LTS and newer**; its 1.0.0 release **restores preroll functionality when baking wiggle into keyframes** — [extensions.blender.org/add-ons/wiggle-bones](https://extensions.blender.org/add-ons/wiggle-bones/), [version history](https://extensions.blender.org/add-ons/wiggle-bones/versions/).
- **Original Wiggle 2** (shteeve3d) has a documented Blender 4.5 problem: a user reports it "creates keyframes that do nothing after baking," with physics re-enabling when the playhead moves; their workaround is to bake then disable the add-on; it reportedly worked in 4.3 — [Blender Artists thread](https://blenderartists.org/t/wiggle-bones-2/1602337); repo at [shteeve3d/blender-wiggle-2](https://github.com/shteeve3d/blender-wiggle-2).
- **Wiggle 2: RTX Edition** is a community fork updated for Blender 5.0 and 5.1, listed compatibility Blender 4.2 LTS and newer; one reviewer reported that changing stiffness resets, possibly a bug — [extensions.blender.org/add-ons/wiggle-2](https://extensions.blender.org/add-ons/wiggle-2/), [versions](https://extensions.blender.org/add-ons/wiggle-2/versions/).
- A separate paid Blender jiggle tool, **Wobbly Wiggler**, exists — [Gumroad](https://bartoszstyperek.gumroad.com/l/wobbly_wiggler).

**Engine-side procedural**
- ALS-Refactored uses **Control Rig** for IK and bone manipulation, with foot locking and foot/pelvis offsets specifically reworked for stairs and slopes — the canonical reference implementation of procedural foot IK on uneven ground in UE5 — [Sixze/ALS-Refactored](https://github.com/Sixze/ALS-Refactored).
- Aim/look-at as a data layer: MoCap Online's Mobility packs ship **aim offsets (look up, down, around)** as a distinct asset category — 18 in Pro, 10 in Basic, 3 in Starter — [Superhive Mobility](https://superhivemarket.com/products/mobility-mocap-animations).
- Epic's naming doc lists **`PHYS_` Physics Asset** as a standard asset type, the hook for ragdoll/physical-animation blending in UE — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).

### Inferences
- Division of labour that the sources support: **breathing and weight shift** are best authored as additive clips (cheap, art-directable, stack on any state); **jiggle, cloth, hair** belong to simulation (Wiggle Bones in Blender for baked offline results; Chaos Cloth / physical animation in UE for runtime); **foot IK on uneven ground and look-at/aim** belong to the runtime rig (Control Rig / aim offsets), never to baked clips; **physical plausibility cleanup** belongs to Cascadeur AutoPhysics as an offline pass.
- Cascadeur 2026.2's Additive Animation Layers are directly useful for authoring the additive breathing/lean/flinch layer described in Q4, since each layer stores its own data and layers sum.
- On "how well does AutoPhysics work": the only defensible statement from these sources is what it *does* (analyse an existing animation and output a physically accurate version, with ground/collider penetration cleanup now fulcrum-aware and default-on). I found no independent quantitative evaluation.

### Gaps
- **UE5 Chaos Cloth, Chaos Flesh, and UE's Control Rig feature set: not verified in this pass.** No primary UE docs page on Chaos Cloth was retrieved; the report should not assert specific Chaos Cloth capabilities from these notes.
- **Unity Animation Rigging package: not verified in this pass** — no source retrieved.
- No source found for Cascadeur's current pricing/licensing tiers (irrelevant here, non-commercial) or for an AI tool that generates muscle jiggle specifically.
- Cascadeur 2026.1's release post was not fetched directly; its feature list above comes from a summary surfaced in search results alongside the 2026.2 post and should be treated as one step removed.

---

## Q6: Hands and fingers — authoring when mocap gives none

### Takeaway
The verified modern path is video-based hand reconstruction: **HaMeR** (transformer, trained on 2.7M images) and **WiLoR** (CVPR 2025, adds hand localization, handedness and a pose-refinement step, detector trained on >2M in-the-wild hand images). I found no 2026 benchmark or successor to confirm what currently leads, and I could not verify the hand-pose datasets (InterHand2.6M, HanCo, Re:InterHand) or any procedural grip solver in this pass.

### Cited Findings
- **HaMeR** (Reconstructing Hands in 3D with Transformers) is a transformer-based system reconstructing a 3D hand mesh from a single RGB image, using "a composite supervision regime with parameter, 2D reprojection, and adversarial losses," **trained on 2.7M images**; its claimed advantage is robustness on hard in-the-wild images, where it "outperforms the state-of-the-art by significant margins" — [EmergentMind: HaMeR](https://www.emergentmind.com/topics/hamer); [review](https://liner.com/review/reconstructing-hands-in-3d-with-transformers).
- **WiLoR** (CVPR 2025) is a full pipeline that "first localizes hands and identifies handedness, then lifts them to 3D with a transformer module," adds a **refinement step that corrects misaligned poses**, and reports "state-of-the-art performance under different benchmark datasets"; its detector was trained on "over than 2M in-the-wild hand images" — [WiLoR, CVPR 2025 open-access PDF](https://openaccess.thecvf.com/content/CVPR2025/papers/Potamias_WiLoR_End-to-end_3D_Hand_Localization_and_Reconstruction_in-the-wild_CVPR_2025_paper.pdf); [arXiv 2409.12259](https://arxiv.org/abs/2409.12259v2); [ML Anthology entry](https://mlanthology.org/cvpr/2025/potamias2025cvpr-wilor).
- Both results are self-reported in their own papers; I found no independent 2026 leaderboard confirming either still leads — [search outcome noted above].
- Earlier baseline for context: end-to-end hand mesh recovery from a monocular RGB image — [arXiv 1902.09305](https://arxiv.org/pdf/1902.09305).
- Relevant to the pose-library approach: Epic's naming doc has **no prefix for Pose Asset**, confirming pose assets are a real UE type but leaving naming to the project — [Epic naming conventions doc](https://dev.epicgames.com/documentation/en-us/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects).
- Cascadeur 2026.2's AutoPosing now makes **hands and feet adjust to surrounding scene geometry** "which helps with characters in contact with the environment" — the closest thing to an automated grip/contact solver I verified — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).

### Inferences
- A workable solo pipeline given the above: (1) author a **finger pose library** as Pose Assets / shape-key-free bone poses — relaxed-open, loose-fist, tight-fist, pointing, pinch, flat-palm, grip-cylinder (weapon), grip-wide (ledge), grip-thin (rope), spread — roughly 10–14 poses; (2) drive them with a single 0→1 curve per hand so any clip can be fingered by curve editing rather than per-joint keyframing; (3) layer the hand poses additively over the arm animation so they are independent of the body clip; (4) use WiLoR/HaMeR on reference video only where a bespoke hand performance matters (dialogue gestures, a signature move), since monocular reconstruction output needs manual cleanup to be rig-ready.
- Per-weapon grip is better solved as a **socket + attachment with a baked hand pose** than as per-clip finger keys — this is why overlay-mode architectures (ALS) keep hand poses with the overlay rather than with the locomotion clip.

### Gaps
- **Hand pose datasets not verified: InterHand2.6M, HanCo, Re:InterHand.** None appeared in the retrieved results; the report should not cite sizes or contents for these from these notes.
- **No 2026 successor to HaMeR/WiLoR identified**, and no current benchmark to rank them.
- **No procedural grip solver verified** (no source retrieved for e.g. UE's Hand IK / finger IK solvers, Unity grip solvers, or Blender grip addons).
- No source found on authoring finger animation from mocap gloves vs. video vs. by hand, or on time cost per clip.

---

## Q7: Facial animation runnable locally in 2026

### Takeaway
The decisive 2025 event is NVIDIA **open-sourcing Audio2Face** (September 2025): models, SDK, Maya and UE5 plugins, training framework and sample data, with output converted to **ARKit blendshape weights** — making locally-runnable, ARKit-standard, audio-driven facial animation freely available. **UniTalker** (ECCV 2024, open source) is the main academic alternative but outputs vertex displacements, not blendshapes. The hard practical problem remains getting 52 ARKit shapes onto a mesh that has none; NVIDIA's own staff confirm their template generates only **46**.

### Cited Findings

**NVIDIA Audio2Face (open-sourced September 2025)**
- The open-source release includes "the Audio2Face model for lip sync generation, the Audio2Emotion model for emotional state estimation from speech, the Audio2Face software development kit (SDK) and plugin for Autodesk Maya, the plugin for Unreal Engine 5, the training framework for the Audio2Face model, and sample training data for the Audio2Face model" — [Open Source For U](https://www.opensourceforu.com/2025/09/nvidia-moves-audio2face-technology-to-open-source/); corroborated by [Gigazine](https://gigazine.net/gsc_news/en/20250926-nvidia-audio2face-open-sources) and [GadgetBond](https://gadgetbond.com/nvidia-audio2face-ai-voice-animation-open-source/).
- Repositories: `NVIDIA/Audio2Face-3D-SDK` and `NVIDIA/Audio2Face-3D-training-framework` — [Open Source For U](https://www.opensourceforu.com/2025/09/nvidia-moves-audio2face-technology-to-open-source/).
- Model variants: **v2.3 regression-based networks for individual actors**, and a **v3.0 diffusion-based network supporting multiple identities with a single network** — [Audio2Face-3D paper, arXiv 2508.16401](https://arxiv.org/pdf/2508.16401).
- Output format: after post-processing the system "uses blendshape decomposition to generate **ARKit blendshape weight values**, which can be used by various face models," and can drive MetaHuman or custom blendshape rigs — [arXiv 2508.16401](https://arxiv.org/pdf/2508.16401). NVIDIA's NGC container description likewise says Audio2Face converts speech into facial animation in the form of ARKit blendshapes — [NGC catalog](https://catalog.ngc.nvidia.com/orgs/nvidia/ace/containers/audio2face/-).
- Developers can **fine-tune the models for custom characters**; the release is aimed at games and apps — [Open Source For U](https://www.opensourceforu.com/2025/09/nvidia-moves-audio2face-technology-to-open-source/).
- Audio2Face product/docs index remains at [docs.omniverse.nvidia.com/audio2face](https://docs.omniverse.nvidia.com/audio2face/latest/genindex.html).

**UniTalker and academic alternatives**
- **UniTalker** (ECCV 2024) is a unified multi-head model designed "to leverage datasets with varied annotations," letting it train on more data than earlier models; its training set **A2F-Bench combines five publicly available datasets and three newly curated datasets**; it reports **lip vertex error reductions of 9.2% on BIWI and 13.7% on VOCASET**; code at `X-niper/UniTalker` — [arXiv 2408.00762](https://arxiv.org/pdf/2408.00762); [ECCV 2024 paper](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/05747.pdf); [ML Anthology](https://mlanthology.org/eccv/2024/fan2024eccv-unitalker).
- UniTalker's described output is **vertex-based, not blendshape-based** — relevant because a blendshape rig would need a conversion step — [arXiv 2408.00762](https://arxiv.org/abs/2408.00762v1).

**Getting ARKit blendshapes onto a mesh that has none**
- NVIDIA staff on their own forum: "generating 52 ARKit shapes on a custom mesh is not supported at the moment," and **only 46 can be generated from their template** — [NVIDIA developer forum](https://forums.developer.nvidia.com/t/how-to-export-arkit-blendshapes-weights/199713).
- A GitHub tool **transfers 52 ARKit blendshapes and 15 Oculus visemes from a reference GLB avatar onto a target GLB avatar that lacks them**, working from a reference model that has the shapes plus a target without them, exposing **distance and falloff settings for vertex matching** — [digitalp/arkit-blendshape-tool](https://github.com/digitalp/arkit-blendshape-tool).
- Blender add-ons: **ARKit Blendshape Tools / Helper** (free) "adds the 52 ARKit blendshapes to a mesh" and links secondary meshes via driver setups — [extensions.blender.org/add-ons/arkit-blendshape-tools](https://extensions.blender.org/add-ons/arkit-blendshape-tools/), [versions](https://extensions.blender.org/add-ons/arkit-blendshape-tools/versions/); **ARKit Magic** (paid) links meshes with drivers — select secondary objects, pick a Master Mesh, and it "handles the technical setup" — [Superhive ARKit Magic](https://superhivemarket.com/products/arkit-magic), [docs](https://superhivemarket.com/products/arkit-magic/docs).
- ARKit shape **names must match the host software's convention** (e.g. Warudo expects `eyeBlinkLeft`, `eyeBlinkRight`) — [Warudo 3D primer](https://docs.warudo.app/docs/tutorials/3d-primer).

**MetaHuman Animator (UE 5.7 / 5.8)**
- UE **5.7** MetaHuman Animator changes: Live Link real-time animation can be driven through a **Blueprint interface**; stereo-pair checkerboard **calibration** gained better automatic frame selection and repeatable settings across takes; a new **experimental MetaHuman Animator Calibration Diagnostics plugin** checks calibration against performance footage; **Live Link Face** can produce real-time facial animation from an **external camera on supported Android devices or iPads** (not iOS yet) — [MetaHuman 5.7 release notes](https://dev.epicgames.com/documentation/metahuman/metahuman-5-7-release-notes).
- UE **5.8** adds an experimental **Markerless Motion Capture plugin** capturing **body, or face and body, from a single camera**; Windows-only; requires UE 5.8 or later — [MetaHuman animation from mono video capture](https://dev.epicgames.com/documentation/metahuman/metahuman-animation-from-mono-video-capture-in-unreal-engine).
- UE 5.7 ships MetaHuman Creator as a built-in plugin — [3DArt](https://www.3dart.it/en/new-unreal-engine-5-7-with-built-in-metahuman-creator-plugin/).

### Inferences
- For a mesh with no blendshapes, the pragmatic order is: (1) get a donor head that *has* the 52 ARKit shapes (a MetaHuman, a Ready Player Me-style GLB, or a free ARKit-ready head); (2) transfer by vertex-matching/wrap using the GLB transfer tool or Blender's Surface Deform/Shrinkwrap + Join-as-Shapes workflow; (3) sanity-fix the ~6 shapes NVIDIA's own template can't produce (tongue and some extreme jaw/cheek shapes are the usual casualties, given 46 of 52); (4) drive with open-source Audio2Face for speech plus Audio2Emotion for expression, then hand-key the eyes, brows and blinks, which audio drivers systematically under-animate.
- ARKit's 52-blendshape set is the de facto interchange standard: Audio2Face emits it, MetaHuman/Live Link Face consume it, and the Blender and GLB tooling targets it. Authoring to anything else in 2026 is a self-inflicted problem.
- Blinks are the single highest-value hand pass: no audio-driven system can infer them, and their absence is the most noticeable "dead" tell.

### Gaps
- **FaceFormer, EMOTE, and Meta's audio2photoreal were not retrieved** in this pass. The report should not cite their architectures or results from these notes.
- I did not verify Audio2Face's license terms, local hardware requirements, or real-time performance.
- No source found quantifying how well Audio2Face handles non-English speech or singing.
- No source retrieved on Blender-native audio-driven facial addons (e.g. Rhubarb-based lipsync).

---

## Q8: Physics-based cleanup and exaggeration

### Takeaway
Cascadeur's AutoPhysics is the production tool: since 2021.1 it "analyses an existing animation and generates a physically accurate version of it," and in 2026.1–2026.2 gained Constraints and fulcrum-aware Collision Penetration Cleaning that is on by default for ground/collider passes. On the research side, **PhysDiff** applies physics projection *during* diffusion denoising rather than as a post-step, a per-sequence **DRL physics refinement** framework (SIGGRAPH Asia 2025 Technical Communications) targets exactly the artefacts mocap leaves, and **StableMotion** shows a purely kinematic diffusion cleanup reducing motion pops 68% and frozen frames 81%.

### Cited Findings

**Cascadeur**
- AutoPhysics' definition, from its 2021.1 introduction: a tool that "analyses an existing animation and generates a physically accurate version of it" — [CGChannel](https://www.cgchannel.com/?p=125620).
- **2026.1** introduced Collision Penetration Cleaning, **Constraints in AutoPhysics**, major Quadruped AutoPosing improvements, the Filament renderer, a new Unreal Engine Live Link, and an AI-powered Root Motion Tool — [summary alongside Cascadeur 2026.2 post](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- **2026.2**: Collision Penetration Cleaning "now takes fulcrums into account and is included by default in AutoPhysics passes involving the ground or kinematic colliders"; AI **Inbetweening** and **Motion Generation** (renamed from Root Motion) retrained on larger datasets; **Additive Animation Layers (Alpha)**; **Easing**; **BVH import** — [Cascadeur 2026.2 blog](https://cascadeur.com/blog/view/cascadeur-2026-2-animation-layers-easing-new-languages).
- Cascadeur's collision-mesh generation workflow is documented for third-party use — [Hollywood Camera Work: generating collision meshes in Cascadeur](https://backup.hollywoodcamerawork.com/generating-collision-meshes-in-cascadeur.html).
- 2022.2 EA is explicitly described as improving "AutoPosing, AutoPhysics, secondary motion and quick rigging" — [Cascadeur blog](https://cascadeur.com/blog/general/cascadeur-20222ea-improves-autoposing-autophysics-secondary-motion-and-quick-rigging); further release history at [CGChannel 2022.3](https://www.cgchannel.com/?p=142761).

**Research (physics-based correction, 2022–2026)**
- **PhysDiff** (Yuan et al.) "leverages a physics-based motion projection module that projects an input motion to a physically-plausible space," applied **during denoising** rather than only at the end, because "the final denoised kinematic motion from diffusion may be too physically-implausible to be corrected by physics"; projection is done by **motion imitation in a simulator**, removing "floating, foot sliding, and ground penetration" — [arXiv 2212.02500](https://arxiv.org/pdf/2212.02500); [ar5iv HTML](https://ar5iv.labs.arxiv.org/html/2212.02500).
- **Physics-based mocap refinement via DRL** (arXiv 2601.19036, SIGGRAPH Asia 2025 Technical Communications) builds a physics-based motion refinement framework using deep reinforcement learning operating **per-sequence**, targeting "floating, mesh interpenetration, phantom contact, and foot sliding" — [arXiv 2601.19036](https://arxiv.org/html/2601.19036v1), [abstract](https://arxiv.org/abs/2601.19036v1).
- **StableMotion** is a diffusion-based cleanup method trained on **unpaired corrupted data**, kinematic rather than physics-based, reporting reductions of "motion pops and frozen frames by 68% and 81%, respectively" — [alphaXiv overview of 2505.03154](https://alphaxiv.org/overview/2505.03154v2).
- **InterMimic** (human–object interaction) "enables a single policy to robustly learn from hours of imperfect MoCap data" — evidence that physics-imitation policies tolerate dirty input — [alphaXiv author page](https://www.alphaxiv.org/@hung-yu-ling) (secondary; primary paper not fetched).

### Inferences
- The practical cleanup stack for a solo project, in order: (1) retarget and fix skeleton/root issues first — no physics pass fixes a bad root track; (2) run Cascadeur **AutoPhysics** with the ground collider for foot-slide, floating and penetration (2026.2 makes this pass default-on and fulcrum-aware); (3) use **AutoPosing** to seat hands and feet onto actual scene geometry for contact clips (vaults, ledge grabs, sitting); (4) use **Easing** and the new **Animation Layers** for the exaggeration/snap pass rather than re-keying; (5) use AI **Inbetweening** to fill sparse keys and **Motion Generation** for root paths.
- PhysDiff's central finding — that physics must be in the loop, not bolted on after — is the right mental model for why Cascadeur's "analyse then regenerate physically accurate" approach beats a single corrective post-pass.
- The DRL per-sequence refinement paper is the closest open-source analogue to AutoPhysics for someone who wants a scriptable batch cleanup, but per-sequence training is slow compared to a desktop tool.

### Gaps
- **MaskedMimic and PHC were not found in this pass.** Searches returned nothing on either, so I cannot state what they do for cleanup-rather-than-generation. The report should not characterise them from these notes.
- No independent quantitative evaluation of Cascadeur AutoPhysics quality (error reduction, foot-slide metrics) was found — only feature descriptions and a qualitative review.
- No source found for an open-source tool that reproduces AutoPhysics' interactive workflow.
- Cascadeur's exact 2026.2 release date is not given on its own release post.

---

## Cross-cutting: suggested build order (inference, derived from the cited architecture)

This is the "what do I make, in what order" answer. It is my synthesis of the ALS/GASP architecture and the pack tiering above, not a cited source.

1. **Rig and skeleton prerequisites first.** For UE: Skeletal Mesh + IK Rig + IK Retargeter with `UEFN_Mannequin` as source ([GASP docs](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine)); run the skeleton setup action that adds curves, slots and virtual bones ([ALS-Refactored](https://github.com/Sixze/ALS-Refactored)). Decide now on foot-phase convention and root convention — retrofitting these across 200 clips is the single most expensive mistake.
2. **Idle + the core four locomotion loops** (walk fwd, run fwd, walk bwd, run bwd), foot-phase matched, with auto foot sync markers.
3. **Fill the 8-direction grid at each speed tier**, build the 2D blendspace, verify blending before adding anything else.
4. **Transitions**: start/stop per foot, turn-in-place 45/90/180, pivots. These are what separate "sliding" from "grounded"; GASP gives pivots their own database.
5. **Air set**: takeoff / in-air loop / land-soft / land-hard / fall loop. Split-jump structure (Mobility Pro ships 21 split jumps).
6. **Crouch set** as a second full stance pass.
7. **Procedural pass, before adding more clips**: Control Rig foot IK on slopes/stairs, aim offsets, look-at. This removes the need for dozens of slope and look-direction variants.
8. **Overlay layer**: one upper-body pose/loop set per weapon or attitude, blended via layering slots + gameplay tags. Cheapest coverage multiplier in the whole project.
9. **Additive layer**: breathing, weight shift, lean, flinch — authored as difference clips; Cascadeur 2026.2 Animation Layers helps here.
10. **Traversal**: vault, mantle, climb at 2–3 heights (GASP ships ledge scaling and vaulting as core content).
11. **Combat**: stance, attacks, combos, then hit reactions by direction and height, blocks, dodges, knockdown/getup, deaths.
12. **Interaction and emotive**: pick up, open, push/pull, sit/stand, lean, talk gestures, victory/defeat/taunt, personality idles.
13. **Hands**: finger pose library driven by a single curve per hand, layered additively; video reconstruction (WiLoR/HaMeR) only for hero gestures.
14. **Face**: transfer 52 ARKit blendshapes from a donor mesh, then drive with open-source Audio2Face + Audio2Emotion; hand-key blinks, brows and eye darts.
15. **Secondary simulation**: Wiggle Bones (Blender, baked) or runtime cloth/physical animation for hair, cloth, straps, soft tissue.
16. **Physics cleanup pass over everything**: Cascadeur AutoPhysics with ground colliders, AutoPosing for contact clips, then the easing/exaggeration pass.
