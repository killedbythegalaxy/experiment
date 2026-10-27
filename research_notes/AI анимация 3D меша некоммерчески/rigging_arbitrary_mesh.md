# Rigging an Arbitrary 3D Character Mesh to Full Animation Readiness (state: October 2026)

**Scope note for the report writer:** this file covers inspection → prep → auto-rig → skin QC → fingers → face → retargeting → non-human edge cases. Two classes of statement appear below and are labelled distinctly:

- **Cited Findings** — verified against a source fetched in this session, with URL.
- **Inferences** — includes a sub-block I have labelled **[unverified-in-session]** for Blender operator names, menu paths and `bpy` idioms that come from my own working knowledge of Blender and were *not* re-confirmed against the manual in this session (two attempts to fetch `docs.blender.org/manual/.../rigify/basics.html` returned only the nav sidebar, and `gltf-transform.dev/cli` did not enumerate `inspect` output). These are high-confidence but the reader should expect occasional drift in exact labels across Blender 4.2 → 5.2. Flagged throughout.

**Single biggest headline finding:** the recommended auto-rigger as of Oct 2026 is no longer UniRig. VAST-AI shipped **SkinTokens / TokenRig** (arXiv 2602.04805, MIT code), which the UniRig README itself now promotes as its successor, and which reports 98–133% better skinning accuracy than prior SOTA. It is a single-pass mesh → rigged GLB tool. ([UniRig README](https://github.com/VAST-AI-Research/UniRig); [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens); [arXiv HTML](https://arxiv.org/html/2602.04805v2))

---

## Q1. Inspection: what tools and scripts inspect an unknown mesh file non-interactively, and what must be checked before rigging

### Takeaway
There is no single tool that reports everything; the practical stack is `gltf-transform inspect` or `assimp info` for a fast format-level triage, then a headless `blender --background --python` dump as the authoritative pass (it is the only one that reliably reports armature + vertex groups + shape keys + object-level transforms + up-axis conversion in one place). `trimesh` is the right tool for the geometry-health questions (watertight, manifold, connected components, bounding box) that rigging actually gates on.

### Cited Findings

- **`gltf-transform` CLI** has an `inspect` command whose report "should identify performance issues" and whether a scene is "geometry-heavy, texture-heavy, or has too many draw calls"; it also ships `validate` (spec conformance against the glTF spec) and a global `-v/--verbose`. The underlying `@gltf-transform/functions` library exposes `inspect`, `getBounds` and `listTextureInfo` helpers. The CLI docs page does **not** enumerate the exact table columns `inspect` prints — [gltf-transform CLI docs](https://gltf-transform.dev/cli)
- **Formats a rigging pipeline must be ready for**: the auto-riggers in this space collectively accept `.obj`, `.fbx`, `.glb`, `.vrm` (UniRig — [UniRig README](https://github.com/VAST-AI-Research/UniRig)), `.glb` (SkinTokens examples — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)), and GLB/FBX/OBJ/STL with a 150 MB cap (Tripo API — [Tripo Auto Rig docs](https://developers.tripo3d.ai/en/docs/animations-rig)). Mixamo per third-party 2026 guides accepts FBX, OBJ and ZIP — [GOJO Mixamo guide 2026](https://www.gojo.blog/mixamo-guide/)
- **Scale is a real, observed failure mode, not a theoretical one**: in a hands-on 2026 comparison, "one character imported at 0.01x scale and needed a scale fix before retargeting" (AccuRig output into UE 5.7) — [Strayspark AI auto-rigging showdown 2026](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Joint-pivot placement is checkable and matters**: the same test found Meshy "hip pivots sat 5–15 cm from where a hand-rigger would put them", which caused foot-IK mismatch and feet sliding through the floor under UE Mover 2.0 — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Pose check gates tool choice**: Auto-Rig Pro's Smart detection "only works with humanoids" and "requires a T-Pose or A-Pose"; for non-humanoids the official docs direct you to skip Smart and go straight to Rig Definition — [Auto-Rig Pro Auto-Rig docs](https://lucky3d.fr/auto-rig-pro/doc/auto_rig.html)
- **Connectivity / single-mesh check gates Mixamo**: Adobe's guidance as relayed by a 2026 guide is that the rigger "maps joints from a symmetric shape" and needs "a connected mesh with no wings, tails, extra limbs" — [GOJO Mixamo guide](https://www.gojo.blog/mixamo-guide/)
- **Sub-mesh/parts check gates Voxel Heat Diffuse Skinning**: its developer advises "the algorithm will convert all the sub-meshes to one solid statue to obtain best result, you'd better select all the sub-meshes and one armature before you run voxel heat diffuse skinning" — [Mesh Online voxel page](https://www.mesh-online.net/voxel.html)
- **Pre-flight check exists as an API primitive**: Tripo's docs instruct "Run rig-check first to verify compatibility" before calling the rig endpoint — [Tripo Auto Rig docs](https://developers.tripo3d.ai/en/docs/animations-rig)
- **Existing-skin detection matters for format round-trips**: SkinTokens' README warns to "Remove the `glTF_not_exported` node when importing results into Blender" — i.e. the exported GLB carries a node that confuses Blender's importer — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)

### Inferences

- **The decision-relevant inspection checklist**, derived from which properties each downstream tool actually gates on (citations above), is: (1) format + whether it is a container with multiple objects; (2) triangle count; (3) number of connected components / separate objects; (4) presence of an `Armature`/skin and its bone count and names; (5) presence of vertex groups and whether their names match bone names; (6) presence of shape keys / morph targets; (7) pose (T / A / relaxed-A / arbitrary), measured from arm-bone or arm-vertex direction vectors; (8) bounding-box height in scene units (is the character ~1.7 or ~170 or ~0.017?); (9) up axis (Y for glTF/FBX-default, Z for Blender native) and forward axis; (10) unapplied object-level transforms (non-identity scale/rotation on the object); (11) left-right symmetry; (12) manifoldness / watertightness; (13) UVs present and non-overlapping; (14) material/texture count.
- **Why Blender headless must be the authority:** `assimp info` and `gltf-transform inspect` report the file as authored, but both FBX and glTF importers *apply an axis conversion*, so the up-axis question is only answerable in the coordinate frame you will actually rig in. Blender is also the only one of the listed tools that reports vertex groups and shape keys in a single dump alongside object matrices.
- **`trimesh` is the correct tool for geometry health**, because it exposes `is_watertight`, `is_winding_consistent`, `euler_number`, `split()` for connected components, `bounds`/`extents`, and `volume` — exactly the set that distinguishes "rigging-ready solid" from "shell of floating parts". It is not the right tool for armatures (it does not model skeletons/skinning in a useful way).
- **FBX SDK / `fbx-python` is only worth installing for one job**: reading FBX-native data that Blender's importer silently normalises or drops — specifically `FbxSkin`/`FbxCluster` link modes, original `FbxAxisSystem` and `FbxSystemUnit`, and bind-pose (`FbxPose`) data. For everything else it is slower to use than Blender. Its distribution has historically been awkward (Autodesk-built wheels for specific Python minors), so expect friction.
- **USD tooling** (`usdcat`, `usdchecker`, `usdview`, `usdtree`) is relevant only if the repo ships `.usd`/`.usdz`/`.usdc`. `usdchecker` gives a validation pass analogous to `gltf-transform validate`; `usdcat --flatten` plus `usdtree` give the scene-graph dump. AccuRig exports USD alongside FBX ([CG Channel on AccuRig](https://www.cgchannel.com/2022/12/reallusion-launches-free-auto-rigging-tool-accurig/)), so USD can appear on the output side of this pipeline too.

### Inferences — [unverified-in-session] concrete inspection recipes

These are the scripts/commands I would hand the reader. Operator and API names are from my working knowledge of Blender ~4.2–5.x and were not re-confirmed against the manual in this session.

**A. 10-second format triage (no Blender):**
```bash
# glTF/GLB
npm i -g @gltf-transform/cli
gltf-transform inspect model.glb
gltf-transform validate model.glb

# Anything assimp reads (fbx, obj, dae, 3ds, ply, stl, gltf...)
assimp info model.fbx          # node/mesh/material/animation counts, bone presence
assimp info --verbose model.fbx
assimp extract / assimp dump model.fbx out.txt   # full scene dump
```
`assimp info` is the fastest way to answer "does this file contain bones at all" for FBX, which `gltf-transform` cannot read.

**B. The authoritative Blender headless dump.** Save as `inspect_scene.py` *outside* the directory holding the downloaded mesh, and run it with the mesh path as an argument (do not run Python from inside an untrusted extracted archive):

```python
# inspect_scene.py  —  run: blender --background --factory-startup \
#   --python inspect_scene.py -- /abs/path/to/model.glb
import bpy, sys, os, math
from mathutils import Vector

argv = sys.argv[sys.argv.index("--") + 1:]
path = argv[0]
ext = os.path.splitext(path)[1].lower()

bpy.ops.wm.read_factory_settings(use_empty=True)

if ext in (".glb", ".gltf"):
    bpy.ops.import_scene.gltf(filepath=path)
elif ext == ".fbx":
    # automatic_bone_orientation helps when the source rig has odd rolls
    bpy.ops.import_scene.fbx(filepath=path, automatic_bone_orientation=True)
elif ext == ".obj":
    bpy.ops.wm.obj_import(filepath=path)          # 4.x+; was import_scene.obj
elif ext == ".dae":
    bpy.ops.wm.collada_import(filepath=path)
elif ext in (".usd", ".usda", ".usdc", ".usdz"):
    bpy.ops.wm.usd_import(filepath=path)
elif ext == ".vrm":
    raise SystemExit("install VRM add-on (saturday06/VRM-Addon-for-Blender) first")
elif ext == ".blend":
    bpy.ops.wm.open_mainfile(filepath=path)
else:
    raise SystemExit(f"unhandled ext {ext}")

print("\n=== SCENE UNIT / SCALE ===")
print("unit_system:", bpy.context.scene.unit_settings.system,
      "scale_length:", bpy.context.scene.unit_settings.scale_length)

for ob in bpy.data.objects:
    print(f"\n=== OBJECT {ob.name}  type={ob.type}  parent={ob.parent.name if ob.parent else None}")
    print("  loc:", tuple(round(v,4) for v in ob.location))
    print("  rot_euler(deg):", tuple(round(math.degrees(v),2) for v in ob.rotation_euler))
    print("  scale:", tuple(round(v,5) for v in ob.scale))
    print("  matrix_world identity?:", ob.matrix_world == ob.matrix_world.Identity(4))
    print("  dimensions:", tuple(round(v,4) for v in ob.dimensions))
    print("  modifiers:", [(m.name, m.type) for m in ob.modifiers])

    if ob.type == 'MESH':
        me = ob.data
        print("  verts/edges/polys/tris:", len(me.vertices), len(me.edges),
              len(me.polygons), sum(len(p.vertices)-2 for p in me.polygons))
        print("  uv_layers:", [l.name for l in me.uv_layers])
        print("  materials:", [m.name if m else None for m in me.materials])
        print("  color_attributes:", [a.name for a in me.color_attributes])
        print("  vertex_groups:", [g.name for g in ob.vertex_groups])
        print("  n_vertex_groups:", len(ob.vertex_groups))
        sk = me.shape_keys
        print("  shape_keys:", [k.name for k in sk.key_blocks] if sk else None)
        # loose geometry / non-manifold proxies
        loose_verts = sum(1 for v in me.vertices if not any(
            v.index in e.vertices for e in me.edges))
        print("  loose_verts(approx):", loose_verts)
        nonman_edges = sum(1 for e in me.edges
                           if sum(1 for p in me.polygons if e.key[0] in p.vertices
                                  and e.key[1] in p.vertices) != 2)
        print("  edges-not-shared-by-2-faces (slow approx):", nonman_edges)
        # armature binding
        for m in ob.modifiers:
            if m.type == 'ARMATURE':
                print("  ARMATURE MODIFIER ->", m.object.name if m.object else None,
                      "use_deform_preserve_volume:", m.use_deform_preserve_volume)

    if ob.type == 'ARMATURE':
        arm = ob.data
        print("  n_bones:", len(arm.bones))
        print("  bone names:", [b.name for b in arm.bones])
        roots = [b.name for b in arm.bones if b.parent is None]
        print("  root bones:", roots)
        for b in arm.bones:
            print(f"    {b.name:28s} head={tuple(round(v,3) for v in b.head_local)}"
                  f" tail={tuple(round(v,3) for v in b.tail_local)}"
                  f" roll_axis_x={tuple(round(v,3) for v in b.x_axis)}"
                  f" len={round(b.length,4)}  use_deform={b.use_deform}")

# ---- pose classification from mesh extents, works with or without a rig ----
meshes = [o for o in bpy.data.objects if o.type == 'MESH']
if meshes:
    pts = [o.matrix_world @ v.co for o in meshes for v in o.data.vertices]
    xs = [p.x for p in pts]; ys = [p.y for p in pts]; zs = [p.z for p in pts]
    bb = (max(xs)-min(xs), max(ys)-min(ys), max(zs)-min(zs))
    print("\n=== GLOBAL BBOX (X,Y,Z) ===", tuple(round(v,4) for v in bb))
    print("tallest axis index (expect 2=Z in Blender frame):", bb.index(max(bb)))
    print("width/height ratio X/Z:", round(bb[0]/bb[2], 3) if bb[2] else None)
    # heuristic: T-pose ~ X/Z >= 0.9 ; A-pose ~ 0.5-0.75 ; arms-down ~ <0.45
    # symmetry probe: mirror x and measure nearest-neighbour residual
```

Interpretation rules for that dump:
- `scale != (1,1,1)` or non-identity `matrix_world` on the mesh → **unapplied transforms**, must be applied before rigging (auto-weighting and most auto-riggers compute in world or local space inconsistently).
- `dimensions.z` around `1.6–1.9` → metres, good. `~170` → centimetres (FBX/Max origin). `~0.017` → a cm-authored file that got double-converted. Fix before rigging, because Mixamo/AccuRig/UE all assume roughly human-metre scale and the Strayspark test observed a real 0.01× import.
- tallest axis is Y rather than Z after import → the importer's axis conversion was wrong for this file; re-import with explicit axis settings.
- `vertex_groups` non-empty but no `ARMATURE` modifier → **orphaned weights** (a rig existed upstream and was stripped). These groups are gold: you can rebuild a skeleton that matches them, or at minimum they tell you the donor rig's bone naming convention.
- `vertex_groups` present whose names are `mixamorig:*`, `Bip001*`, `J_Bip_*` (VRM), `DEF-*`/`ORG-*` (Rigify), `root/pelvis/spine_01/clavicle_l/...` (UE5) → identifies the upstream standard immediately and tells you which retarget preset to use.
- `shape_keys is None` → no face animation capability; see Q6.
- many `root bones` in the armature → a broken/unparented imported hierarchy, needs re-parenting before retargeting.

**C. Geometry health with `trimesh`** (run isolated, `python -I`):
```python
# geom_health.py — python -I geom_health.py /abs/path/model.glb
import sys, trimesh
s = trimesh.load(sys.argv[1], force='scene')
print("geometries:", list(s.geometry.keys()))
m = trimesh.util.concatenate([g for g in s.geometry.values()
                              if isinstance(g, trimesh.Trimesh)])
print("faces:", len(m.faces), "verts:", len(m.vertices))
print("watertight:", m.is_watertight)
print("winding_consistent:", m.is_winding_consistent)
print("volume:", m.volume, "euler:", m.euler_number)
print("extents:", m.extents, "bounds:", m.bounds)
parts = m.split(only_watertight=False)
print("connected components:", len(parts),
      [round(float(p.area),3) for p in parts][:40])
print("duplicate faces:", len(m.faces) - len(trimesh.grouping.unique_rows(m.faces)[0]))
print("degenerate faces:", int((m.area_faces < 1e-10).sum()))
# symmetry probe
import numpy as np
mir = m.copy(); mir.vertices[:,0] *= -1
d = trimesh.proximity.closest_point(m, mir.vertices)[1]
print("mirror residual: mean", float(np.mean(d)), "p95", float(np.percentile(d,95)),
      "relative to height", float(np.percentile(d,95)/m.extents[2]))
```
A p95 mirror residual above roughly 1–2% of character height means the mesh is **not** usefully symmetric, which breaks Mixamo ("maps joints from a symmetric shape"), breaks Blender's X-Mirror weight painting, and breaks `symmetrize`.

### Gaps
- The exact column/field list printed by `gltf-transform inspect` is not documented on the CLI page I fetched; I could not confirm whether it reports skins/joint counts explicitly. The reader should just run it and read the tables.
- I found no current official Adobe page stating Mixamo's polygon ceiling (see Q3 gaps).
- I did not verify the current `bpy` operator name for OBJ import across 5.x (`bpy.ops.wm.obj_import` vs legacy `bpy.ops.import_scene.obj`); both existed during the 3.x→4.x transition.

---

## Q2. Mesh repair / prep when the mesh fails rigging requirements

### Takeaway
Three prep failures actually block rigging and must be fixed first: unapplied transforms/wrong scale, mesh split into many unmerged parts (breaks heat-diffusion weighting), and a non-T/non-A pose (blocks Mixamo, AccuRig and ARP Smart). Non-manifoldness is mostly *not* a blocker for skinning — it is a blocker for voxel-based weight solvers and for 3D-print-style repair tools. "AI mesh repair" in 2026 exists but is all 3D-print-oriented hole-closing, not rigging-oriented.

### Cited Findings

- **AI/automatic mesh repair in 2026 is print-oriented.** Meshy's free online STL repair claims to automatically fix "non-manifold edges and vertices, holes and gaps in the surface, flipped or inconsistent normals, self-intersections, and duplicate or isolated vertices" — [Meshy STL repair](https://www.meshy.ai/3d-tools/stl-repair); [Meshy auto-repair tutorial](https://www.meshy.ai/tutorials/auto-repair-3d-models)
- **Tripo** is credited (by a competitor-adjacent comparison) with built-in auto-repair that closes holes and fixes non-manifold geometry before export — [Tripo mesh repair guide](https://www.tripo3d.ai/content/en/use-case/the-best-ai-3d-model-repair)
- **3D AI Studio** ships a Mesh Repair utility that "automatically detects and corrects common geometry problems" — [3D AI Studio mesh repair docs](https://docs.3daistudio.com/utilities/mesh-repair)
- **Materialise Magics** "Fix Wizards" are described as automatically detecting and repairing "a vast array of mesh errors"; this is the professional/manufacturing-grade option — [3D AI Studio 2026 roundup](https://www.3daistudio.com/blog/best-ai-tools-for-3d-printing-2026)
- **No independent cross-tool benchmark of repair quality exists** in the sources surfaced; the "best of 2026" repair pages are all published by vendors selling the tools — [3D AI Studio roundup](https://www.3daistudio.com/blog/best-ai-tools-for-3d-printing-2026); [Meshy roundup](https://www.meshy.ai/blog/best-ai-tools-for-3d-printing)
- **Generative-3D output specifically needs repair before rigging:** one comparison estimates Rodin/Hyper3D meshes are "optimized for rendering, not slicing," adding "20–40 minutes of Blender repair to every model" — [Tripo repair guide](https://www.tripo3d.ai/content/en/use-case/the-best-ai-3d-model-repair) (one reviewer's estimate, treat as anecdote)
- **Pose-to-rest is a known-broken built-in with dedicated add-on fixes.** Blender's built-in apply-pose-as-rest "fails as the mesh doesn't update to the new pose" when the mesh has shape keys — [Blender Source Tools Steam thread](https://steamcommunity.com/groups/BlenderSourceTools/discussions/1/1473096694446678557)
- **Add-on 1: `Mysteryem/blender-apply-pose-as-rest-pose-plus`** — "applies a pose as the rest pose *and* applies the pose to the meshes rigged to the armature"; supports Blender 3.3+ — [repo](https://github.com/Mysteryem/blender-apply-pose-as-rest-pose-plus)
- **Add-on 2: `kxn4t/pose-to-rest-pose`** — applies current pose as rest pose "while preserving shape keys and drivers", specifically maintaining "shape keys, including values, slider ranges, mute states, relative keys, and vertex groups"; requires Blender 4.2.0+ — [repo](https://github.com/kxn4t/pose-to-rest-pose)
- **Correct manual ordering for pose-to-rest:** apply the Armature modifier on the mesh *before* applying the rest pose on the armature, "otherwise the mesh can snap back or deform oddly" — [egneva rest-pose guide](https://egneva.com/how-to-set-current-position-as-rest-pose-in-blender/)
- **The `Retarget` Blender extension includes a prep operator for exactly this**: "applying a new rest pose with mesh alignment", plus "applying scale or creating a scale offset while fixing the animation" and "merging head/tail bones" — [Retarget on extensions.blender.org](https://extensions.blender.org/add-ons/retarget/)
- **Multi-part meshes must be merged for voxel weighting** — see the Mesh Online quote in Q1 — [Mesh Online](https://www.mesh-online.net/voxel.html)

### Inferences

- **Decision rule for repair depth:** if the target is skinned deformation only, you need *watertight-enough per limb* and *no loose/duplicate geometry*; you do **not** need a globally manifold mesh. If you intend to run UniRig/SkinTokens voxel post-processing (`--use_postprocess`) or Voxel Heat Diffuse Skinning, you *do* need solid, non-self-intersecting, merged geometry, because both voxelise the mesh.
- **Order of operations matters and is the most common source of silent breakage:** repair → merge/join → apply transforms → fix orientation → fix scale → symmetrise → pose-to-rest → *then* rig. Doing pose-to-rest after binding, or applying transforms after binding, invalidates the bind.
- **For a non-T/non-A pose there are two distinct paths**, and which you pick depends on whether a rig already exists: (a) **rig exists** → pose the existing rig into T/A, then apply-as-rest with one of the two add-ons above; (b) **no rig exists** → you cannot "pose" a static mesh, so you must either build a throwaway armature, bind with automatic weights, pose to T, apply as rest, delete the throwaway rig, and then run the real auto-rigger on the now-T-posed mesh; or use a tool that tolerates arbitrary poses (AccuRig and Mixamo both *attempt* non-standard poses per Q3, SkinTokens/UniRig are pose-agnostic by design since they are learned on arbitrary static meshes).
- **Mirrored asymmetry** is only fixable automatically when the asymmetry is accidental (modelling drift). Deliberate asymmetry (one mechanical arm, an eyepatch) must not be symmetrised; instead disable X-Mirror and weight both sides independently, and expect Mixamo to perform worse.

### Inferences — [unverified-in-session] concrete Blender operators and paths

- **Find non-manifold:** Edit Mode → `Select ▸ Select All by Trait ▸ Non Manifold` (`bpy.ops.mesh.select_non_manifold()`), with the extend/boundary/multi-face/non-contiguous/vertex toggles in the redo panel. Also `Select All by Trait ▸ Loose Geometry`, `▸ Interior Faces`, `▸ Faces by Sides`.
- **Standard cleanup sequence (Edit Mode, all selected):**
  - `Mesh ▸ Merge ▸ By Distance` (`bpy.ops.mesh.remove_doubles(threshold=0.0001)`) — the single highest-value fix; unwelded duplicate vertices are why "the mesh tears when the arm bends".
  - `Mesh ▸ Normals ▸ Recalculate Outside` (`bpy.ops.mesh.normals_make_consistent(inside=False)`).
  - `Mesh ▸ Clean Up ▸ Delete Loose` (`bpy.ops.mesh.delete_loose()`), `▸ Degenerate Dissolve` (`bpy.ops.mesh.dissolve_degenerate()`), `▸ Fill Holes` (`bpy.ops.mesh.fill_holes(sides=4)`), `▸ Make Planar Faces`, `▸ Split Non-Planar Faces`.
  - `Face ▸ Triangulate Faces` / `Mesh ▸ Clean Up ▸ Limited Dissolve` as needed.
- **3D Print Toolbox add-on** (ships with Blender, enable in Preferences ▸ Add-ons; `mesh_3dprint`/"3D-Print Toolbox"): gives a one-click statistics + error report (non-manifold edges, bad contiguous edges, zero-area faces, intersecting faces, thin faces, sharp edges, overhangs) and `Make Manifold`. Operators are under `bpy.ops.mesh.print3d_*` (`print3d_check_all`, `print3d_clean_non_manifold`, `print3d_info_volume`, `print3d_info_area`). This is the fastest non-interactive repair pass available inside Blender with no paid add-on.
- **Nuclear option for hopeless topology:** add a **Remesh modifier** in **Voxel** mode (set Voxel Size to roughly 0.5–1% of character height, enable Smooth) or `Object ▸ Convert`/`Ctrl+R` voxel remesh in Sculpt mode, which produces a guaranteed-manifold, uniformly-triangulated, UV-destroying mesh. Use it to make a *proxy for weight computation*, then transfer weights back to the original with Data Transfer (Q4) — this is the key trick for rigging garbage topology without destroying the shipped asset. **Quad Remesher** (paid, Exoside) or Blender's built-in **QuadriFlow** (`Object ▸ Quad Remesh` / `bpy.ops.object.quadriflow_remesh()`) give quad output if you need deformation-friendly topology.
- **Separate / join parts:** `Mesh ▸ Separate ▸ By Loose Parts` (`bpy.ops.mesh.separate(type='LOOSE')`) to see what you have; `Object ▸ Join` (`Ctrl+J`, `bpy.ops.object.join()`) to merge back. For a rig, you generally want *one* mesh object (or a small handful) parented to one armature.
- **Apply transforms:** Object Mode → `Object ▸ Apply ▸ All Transforms` (`bpy.ops.object.transform_apply(location=True, rotation=True, scale=True)`). Then `Object ▸ Set Origin ▸ Origin to Geometry` or snap origin to world origin at the feet. For a scale fix on an *already-rigged* chain, select armature + mesh, scale, then `Object ▸ Apply ▸ All Transforms` on both, and in Edit Mode on the armature nothing further is needed — but if the armature is animated, use the `Retarget` extension's scale-offset operator instead so the animation is corrected too.
- **Fix orientation:** rotate the object (e.g. `R X 90` for a Y-up import that came in lying down), then Apply All Transforms. On export, set the exporter's Up/Forward explicitly rather than pre-rotating (glTF exporter has `+Y Up`; FBX exporter has Forward/Up dropdowns).
- **Symmetrise:** Edit Mode → `Mesh ▸ Symmetrize` (`bpy.ops.mesh.symmetrize(direction='POSITIVE_X')`); for weights, `Object ▸ Vertex Groups ▸ Mirror Vertex Group` or in Weight Paint enable the X-Mirror / Topology Mirror toggles; for armatures, Edit Mode on the armature → `Armature ▸ Symmetrize` (`bpy.ops.armature.symmetrize()`), which requires `.L`/`.R` suffixes.
- **Pose-to-rest, manual built-in route:** Pose Mode → pose into T → select mesh → in the Armature modifier dropdown choose `Apply` (or `Ctrl+A` with modifier focus; `bpy.ops.object.modifier_apply(modifier="Armature")`) → re-add a fresh Armature modifier → select armature, Pose Mode → `Pose ▸ Apply ▸ Apply Pose as Rest Pose` (`bpy.ops.pose.armature_apply(selected=False)`). If the mesh has shape keys, the built-in modifier-apply fails and you must use the `kxn4t/pose-to-rest-pose` add-on.

### Gaps
- No source found that benchmarks any AI repair tool on *rigging-readiness* (as opposed to print-readiness); all repair claims are vendor self-reported and the roundups are vendor-published.
- I did not verify whether the 3D Print Toolbox is still bundled and under the same operator namespace in Blender 5.2 (it has been bundled since 2.6x; low risk, but unconfirmed here).

---

## Q3. Auto-rigging tools in detail (install, run, outputs, headless)

### Takeaway
The 2026 landscape has four tiers: (1) **learned one-shot riggers** — SkinTokens (new, best, local, MIT) and UniRig (its predecessor, skinning now released); (2) **hosted API riggers** — Tripo (best-documented API, 8 skeleton types, 6 bone-naming conventions), Meshy, Rodin; (3) **marker-based desktop riggers** — Auto-Rig Pro ($50, the only one with real creature/extra-limb support), AccuRig (free, cleanest weights of the hosted set), Mixamo (free, humanoid-only, dated); (4) **manual-with-generator** — Rigify (free, in-box, best final control, no automation of bone placement). RigNet is superseded and should only be mentioned as history.

### Cited Findings — SkinTokens / TokenRig (the current recommendation)

- **Identity and status:** `VAST-AI-Research/SkinTokens`, "SkinTokens: A Learned Compact Representation for Unified Autoregressive Rigging", code MIT-licensed; the UniRig README promotes it as UniRig's successor — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens); [UniRig README](https://github.com/VAST-AI-Research/UniRig)
- **Method:** skinning weights are encoded as discrete tokens by an **FSQ-CVAE**; a model called **TokenRig** predicts skeleton parameters *and* skin tokens in one autoregressive sequence; the autoregressive backbone is **Qwen3-0.6B**; a GRPO reinforcement-learning stage uses geometric and semantic rewards — [arXiv abs 2602.04805](https://arxiv.org/abs/2602.04805); [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Hardware:** "NVIDIA GPU with at least 14 GB of memory for inference" — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)
- **Install (verbatim shape):** Python 3.11+, CUDA Toolkit 12.1+, `uv` recommended → `uv venv --python 3.11` → install torch/torchvision/torchaudio **2.7.0** from the `cu128` index → `uv pip install -r requirements.txt` → `uv pip install flash-attn --no-build-isolation` → `python download.py --model` which fetches checkpoints into `experiments/` and the Qwen3-0.6B config into `models/` — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)
- **Checkpoints (HF `VAST-AI/SkinTokens`):** `articulation_xl_quantization_256_token_4` = the TokenRig rigging model refined with GRPO (**the README's recommended checkpoint**); `skin_vae_2_10_32768` = the FSQ-CVAE skin-weight tokenizer — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens); [HF model](https://huggingface.co/VAST-AI/SkinTokens)
- **Run (headless-capable via `demo.py`):** `--input` accepts a file **or a directory** (so batch works), `--output` sets destination. Flags: `--use_skeleton` (skin only, against an existing skeleton), `--use_transfer` (keep original texture and scale), `--use_postprocess` (voxel-based skin post-processing). Sampling defaults `--top_k 5 --top_p 0.95 --temperature 1.0 --repetition_penalty 2.0 --num_beams 10`. Running `python demo.py` with no input starts a local Gradio UI on `http://127.0.0.1:1024`. Examples are `.glb` (e.g. `giraffe.glb`), outputs are `.glb` — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)
- **Blender import caveat:** "Remove the `glTF_not_exported` node when importing results into Blender" — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)
- **Skeleton representation / naming:** joint coordinates are quantised into integer tokens (dx, dy, dz) and serialised per bone; **each chain starts with a type token, e.g. `mixamo`**; bone order comes from "predefined templates (e.g., for bipeds) or chain partitioning strategies for more general structures." The paper does **not** fix a bone count — dataset average joint counts are ~20 (VRoid Hub), ~34 (Articulation 2.0), ~96 (ModelsResource) — [arXiv HTML 2602.04805v2](https://arxiv.org/html/2602.04805v2)
- **Fingers:** TokenRig is described as "the only method that preserves finger-level weight differentiation" in the paper's comparison. Finger/facial **bone generation** is not explicitly described — [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Training data:** Articulation 2.0 (70%), VRoid Hub (20%), ModelsResource (10%); GRPO stage on a smaller curated set of AI-generated meshes; **Truebones Zoo held out as an out-of-distribution benchmark** (i.e. animals are an explicit evaluation target) — [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Numbers vs UniRig and RigNet** — [arXiv HTML](https://arxiv.org/html/2602.04805v2):
  - Skeleton Chamfer (J2J/J2B/B2B), ModelsResource: UniRig 3.390 / 2.592 / 1.890 → TokenRig (4 skin tokens) **2.857 / 2.025 / 1.568**
  - Skeleton Chamfer, Articulation 2.0: UniRig 3.115 / 2.211 / 1.926 → TokenRig **2.515 / 1.694 / 1.469**
  - Skinning L1, ModelsResource: RigNet 0.0573, UniRig 0.0381 → TokenRig (6 skin tokens) **0.0166**
  - Skinning L1, Articulation 2.0: UniRig 0.0297 → TokenRig **0.0153**
  - Headline: 98–133% improvement in skinning accuracy, 17–22% in bone prediction — [arXiv abs](https://arxiv.org/abs/2602.04805)
- **Stated limitations:** failures concentrate on "extremely thin or topologically ambiguous geometry that is rare in training" (examples given: a looped handle-like structure, a slender claw); discrete tokens leave a measurable reconstruction gap vs a continuous CVAE (a deliberate trade-off for a unified autoregressive model and fast sampling without iterative denoising); **the system does not accept user-specified topology templates or interactive guidance** (listed as future work); physics-based rewards are future work. Baselines "miss anatomical features like ears or horns on non-humanoid characters" — [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Access without a 14 GB GPU:** an official HF Space accepts OBJ/FBX/GLB uploads — [SkinTokens Space](https://huggingface.co/spaces/VAST-AI/SkinTokens)
- **Third-party ports (not official):** `localai-org/skin-tokens.cpp` — C++/GGML conversion running on CPU or Vulkan; `chris-straka/skintokens` — macOS/Apple-Silicon CLI with `rig`, `skin`, `joints`, `doctor` subcommands; `waterhan0493-tech/skintokens-ue` — C++/GGML conversion for UE — [search-surfaced repos](https://github.com/localai-org/skin-tokens.cpp)

### Cited Findings — UniRig (predecessor; skinning model status resolved)

- **Released:** skeleton **and skinning** code, plus a checkpoint trained on **Articulation-XL2.0** on Hugging Face, plus the Rig-XL and VRoid datasets and training code. The current README marks Automated Skinning Prediction as available — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **Not released:** the paper's full UniRig checkpoints (skeleton + skinning) trained on **Rig-XL/VRoid** remain "Planned Future Releases"; **bone attribute prediction** (e.g. stiffness for physics) is "Coming Soon" — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **This is a change from earlier 2025 state.** An earlier HF model-card revision listed only the skeleton checkpoint with skinning "Coming Soon" — [HF model card commit 1e055b0](https://huggingface.co/VAST-AI/UniRig/commit/1e055b0cb26b995084a8408a4486fca422f5bd19). A HF Space README states the Articulation-XL2.0 checkpoint covers "Skeleton & Skinning Prediction" — [Mesh_Rigger Space README](https://huggingface.co/spaces/jkorstad/Mesh_Rigger/blob/main/UniRig/README.md). **So: the skinning model IS released, but it is the Articulation-XL2.0 variant, not the paper's best Rig-XL/VRoid variant.**
- **Install:** Python 3.11, PyTorch tested 2.3.1+; conda env; install torch, `requirements.txt`, `spconv`, `torch_scatter` and `torch_cluster` from the PyG wheel index, and **numpy 1.26.4** pinned. `flash_attn` install "may fail" (README defers to upstream). The skeleton checkpoint downloads automatically. Optional Blender add-on for `.vrm` import/export — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **Run:** `generate_skeleton.sh --input/--output` or `--input_dir/--output_dir`, plus `--seed` for variations; accepts `.obj`, `.fbx`, `.glb`, `.vrm`. Then `generate_skin.sh` takes a **skeleton FBX**. Then `merge.sh` combines the skeleton-or-skin result with the original model. **Critical gotcha from the README: merging a skeleton-only file produces no skinning — you must merge the skin output.** The README recommends refining the skeleton before skinning because "missing tail or wing bones degrade results" — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **Hardware:** generation needs a CUDA GPU with **≥8 GB VRAM**; skin *training* needs **≥60 GB on a single GPU even at batch size 2** — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **Known skinning-quality complaint:** an open issue titled "skinning weight predict wrong" — [UniRig issue #20](https://github.com/VAST-AI-Research/UniRig/issues/20)
- **Scope claim:** the framework aims to handle humans, animals and objects; the abstract mentions anime characters and organic and inorganic structures, but the README does not detail coverage, and gives no bone-naming standard, no statement on fingers/face — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)

### Cited Findings — Tripo (best-documented hosted API)

All from [Tripo Auto Rig docs](https://developers.tripo3d.ai/en/docs/animations-rig):
- **Endpoint:** `POST https://openapi.tripo3d.ai/v3/animations/rig`, Bearer API key, **asynchronous** — poll the Task Query API or use a webhook.
- **Pre-flight:** "Run rig-check first to verify compatibility."
- **Input:** exactly one of a `task_id` from a prior Tripo generation, a `file_token` from File Upload, or a public URL. Formats **GLB, FBX, OBJ, STL**; **max 150 MB**.
- **`model` versions:** `v1.0-20240301` (default; **biped only**, 90+ animation presets); `v2.5-20260210` (**non-humanoid creatures**; bipeds work but not recommended); `v3.0-20260909` (**recommended for humanoids**, must be requested explicitly; also supports `rig_type: others` and the actorcore/unity/unreal/vrm conventions).
- **`rig_type`:** `biped` (default), `quadruped`, `hexapod`, `octopod`, `avian`, `serpentine`, `aquatic`, `others`.
- **`spec`** (bone naming, **biped only**): `tripo` (default), `mixamo`, `actorcore`, `unity`, `unreal`, `vrm`.
- **`out_format`:** `glb` (default) or `fbx`.
- **Response:** `task_id` on create; on completion `model_url` + `credits_consumed` (example shows 25.00 credits).
- Docs do **not** state finger/facial bone coverage, any T-pose requirement, or a polygon limit.
- **Hands-on 2026 result:** Tripo produced a "standard biped layout with recognizable names", "close to Manny/Quinn, needed a minor foot-bone offset", and was "best of the group on a stylized robot; bones placed sensibly with no manual fixes"; but it has **no facial joints and no pre-set IK chains**, its **jaw weights are unsuitable for close-up dialogue**, and shoulder/chest weights were over-smoothed so "broad shoulder pads slumped during arm raises" — [Strayspark showdown 2026](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)

### Cited Findings — Meshy, Rodin, Cascadeur

- **Meshy:** accepts generated or uploaded meshes and "handles wider topology on uploads", but produces **non-standard spine and clavicle bone names that UE's retargeter does not auto-detect**, requiring manual remapping (~8 minutes in the test); hip/pelvis weighting was inconsistent and hip pivots were 5–15 cm off, causing foot-IK mismatch and feet sliding through the ground under Mover 2.0 — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo). Sources disagree on scope: one says biped-humanoid only, another says humanoids **and** quadrupeds — [Meshy auto-rig roundup](https://www.meshy.ai/blog/best-ai-auto-rigging-tool) vs [ludo.ai comparison](https://ludo.ai/compare/best-ai-auto-rigging-tools). I found **no official Meshy API bone-naming documentation**.
- **Rodin (Hyper3D):** sources conflict — one says basic rigging in beta, others say no rigging module; no documented bone-naming options found — [ludo.ai](https://ludo.ai/compare/best-ai-auto-rigging-tools)
- **Cascadeur:** **requires an existing rig; it does not generate one from scratch**. Bone names described as retarget-ready; its auto-rig rated "OK but not great" — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)

### Cited Findings — Auto-Rig Pro ($50)

- **Smart is humanoid-only and pose-gated:** the official Smart docs say the feature "only works with humanoids" and requires a **T-Pose or A-Pose**; for non-humanoids you skip Smart and go to **Rig Definition** — [ARP Auto-Rig docs](https://lucky3d.fr/auto-rig-pro/doc/auto_rig.html)
- **Quadrupeds use Multi-Ped:** switch rig type from the default **Biped** to **Multi-Ped** for dogs/cats/horses; controller shapes then get free orientation since the animal stands on its "hands". Caveat in the docs: "This setting only affects the visual shape though, the actual bones may be oriented in any directions." — [ARP docs](https://lucky3d.fr/auto-rig-pro/doc/auto_rig.html)
- **Tail options:** a **Count** setting for the number of tail bones, and a **Master Controller at Root** option that places a controller at the tail root rotating all tail bones at once. ARP **3.62** changelog: tail limbs can be added multiple times and duplicated — [ARP docs](https://lucky3d.fr/auto-rig-pro/doc/auto_rig.html); [ARP updates log](https://lucky3d.fr/auto-rig-pro/doc/updates_log.html)
- **What it does better:** reviewers consistently cite Smart marker placement, a facial rig, **creature limbs**, and mocap retargeting via **Remap**, which maps bone names *and orientations* between any source and target skeleton — [Cinevva ARP alternatives 2026](https://app.cinevva.com/guides/auto-rig-pro-alternatives); [cgdive Rokoko review](https://addons.cgdive.com/tools/rokoko-addon)
- **Rokoko's own developers reportedly recommend ARP as the more robust alternative** for retargeting — [cgdive](https://addons.cgdive.com/tools/rokoko-addon)
- Vendor comparison claiming Smart supports quadrupeds conflicts with the developer's own docs; prefer the docs — [Meshy comparison](https://www.meshy.ai/blog/best-ai-auto-rigging-tool) (conflicting)
- **Batch/headless precedent exists:** `Shimingyi/ARP-Batch-Retargeting` is a Blender/ARP batch pipeline for BVH retargeting between skeletons, including `SMPL_to_Mixamo` sample configs — [repo](https://github.com/Shimingyi/ARP-Batch-Retargeting)

### Cited Findings — AccuRig (free)

- **Pose:** Reallusion recommends T-pose or A-pose "including the variant with bent wrists often found in 3D scans", although "AccuRig will attempt to rig characters in custom poses" — [CG Channel AccuRig 1.1](https://www.cgchannel.com/2022/12/reallusion-launches-free-auto-rigging-tool-accurig/). Guides add it performs well with T-pose, A-pose or relaxed A-pose, and that "for more complex characters or specific requirements, additional manual weight painting may be necessary" — [cgdive AccuRig](https://cgdive.com/accurig-easy-rigging-for-humanoid-characters/)
- **Fingers: yes, and flexible.** Hand rigs are generated separately with the option for up to five fingers; "AccuRIG can accommodate hands with zero to five fingers". Finger markers typically need manual tuning — "it's essential to manually fine-tune the finger markers to ensure they are accurately placed" — [cgdive](https://cgdive.com/accurig-easy-rigging-for-humanoid-characters/); [ActorCore auto-rig](https://actorcore.reallusion.com/auto-rig)
- **Export:** FBX and USD; requires a free ActorCore account "even if you are only exporting locally" — [CG Channel](https://www.cgchannel.com/2022/12/reallusion-launches-free-auto-rigging-tool-accurig/)
- **Weight quality is the best of the hosted/desktop auto-riggers tested**: "weight painting was described as tight, the cleanest of the group" — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Non-human: not supported**; skeleton is closer to UE Manny than Meshy's but not as close as Tripo's; one AccuRig character imported at **0.01× scale** — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Posed-export caveat (anecdotal, old):** a user reported exporting to FBX from AccuRig and opening in Rhino reverted the character to the default A-pose; the suggested workaround was "import your posed model into Blender, then re-export with the pose baked" — [McNeel forum](https://discourse.mcneel.com/t/how-to-import-posed-character/192168) (single user, a few years old)

### Cited Findings — Mixamo (free)

- **Humanoid bipeds only**; Adobe's guidance as relayed is that the rigger "maps joints from a symmetric shape" and needs "a connected mesh with no wings, tails, extra limbs" — [GOJO Mixamo guide 2026](https://www.gojo.blog/mixamo-guide/)
- **Pose:** "uploading in the T-Pose helps, although quick tests suggest the Autorigger does a reasonable job even with non-standard poses"; a forum post says it "requires a model that it can detect is in t-pose stance" — [nicwhitevideo guide](https://nicwhitevideo.com/step-by-step-guide-to-auto-rigging-with-mixamo/); [three.js forum](https://discourse.threejs.org/t/auto-rigging-still-something-only-mixamo-can-do/43709)
- **Polycount: no current official figure found and sources conflict.** The 10,000-triangle figure is from the **2012** free-tier launch (larger models then cost $89.99) — [CG Channel 2012](https://www.cgchannel.com/2012/06/mixamo-makes-auto-rigger-free-for-low-poly-meshes/); [CinemaBlend 2012](https://www.cinemablend.com/games/Mixamo-Auto-Rigger-Now-Free-Character-Models-Under-10k-Polygons-43474.html). 2026 third-party guides variously say 10K–30K is a comfortable range, "strict polygon limits … get under 100,000 triangles", and warn about a **60 MB** upload limit (an older source says 30 MB) — [Meshy Mixamo tutorial](https://www.meshy.ai/tutorials/how-to-use-mixamo-with-meshy); [neural4d prep guide](https://www.neural4d.com/how-tos/how-to-prepare-3d-models-for-auto-rigging-and-animation)
- **Formats:** FBX, OBJ, ZIP — [GOJO](https://www.gojo.blog/mixamo-guide/)
- **Output:** Mixamo-specific bone names; **UE has built-in Mixamo-to-Manny retargeting**. The test's complaint was the dated mocap library, not the rig — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)

### Cited Findings — Rigify (free, in-box) and other Blender add-ons

- **Rigify is "automatic rigging from building-block components"**, i.e. you place the metarig bones by hand and Blender computes the weights — "less automatic than the name suggests" — [Blender Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html); [Cinevva Blender rigging guide 2026](https://app.cinevva.com/guides/how-to-rig-a-character-in-blender)
- **Metarig choice matters for fingers/face:** the manual lists **Basic Human** among the predefined metarigs and notes it "doesn't include face and fingers". Guidance: "for a game character pick Basic Human unless you need finger bones, in which case pick Human and delete the face later" — [Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html); [Cinevva](https://app.cinevva.com/guides/how-to-rig-a-character-in-blender)
- **Face rig system changed:** the current manual documents an **Upgrade Face Rig** button that appears **above Generate Rig** when a metarig uses the legacy face rig, migrating it to the new modular face system — [Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html). Historically the default human metarig used rig type `faces.super_face`, which the Blender team was "deprecating … in favor of more modular sub-rigs" — [oddlyshapeddog Rigify eye guide](https://medium.com/@oddlyshapeddog/how-to-rig-eyes-with-eyelids-in-blender-3-x-4-0-using-rigifys-skin-eye-rig-f8198943a83b) (3.x/4.0-era, may be stale)
- **Rigify documentation for the new face sub-rigs is thin:** "the documentation for these rigs is very sparse right now" — [oddlyshapeddog](https://medium.com/@oddlyshapeddog/how-to-rig-eyes-with-eyelids-in-blender-3-x-4-0-using-rigifys-skin-eye-rig-f8198943a83b)
- **Blender 5.x added no new skinning algorithm.** The 5.0 notes' weight-related changes are: weight values < 0.0001 are rounded to 0 when painting; the **Zero Weights** overlay now defaults to **Active** instead of **None**; loop selection in Weight Paint mode with Vertex Selection (attributed to 5.0 by one mirror and to 5.1 by another guide). "There is no new skinning algorithm in 5.x: Automatic Weights is still bone heat." — [Blender 5.0 release page](https://www.blender.org/download/releases/5-0/); [Releasebot mirror](https://releasebot.io/updates/blender); [Cinevva Blender 5 for game artists](https://app.cinevva.com/guides/blender-5-for-game-artists)
- Blender 5.0 released **18 Nov 2025**; 5.0.1 on **16 Dec 2025** with 132 bug fixes; the current manual is labelled **5.2 LTS** — [Wikipedia Blender](https://en.wikipedia.org/wiki/Blender_(software)); [Blender manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html)
- **SKAVA Auto Rigger Pro** (paid add-on) claims humanoid, quadruped and simple skeletal chains, real-time previews, automatic weight painting, and meshes up to 1 million vertices — [Cinevva ARP alternatives](https://app.cinevva.com/guides/auto-rig-pro-alternatives) (vendor-sourced claim, unverified)

### Inferences

- **Recommended default path for an arbitrary mesh in Oct 2026:** run **SkinTokens** first. It is the only tool that is simultaneously free, local, format-flexible, pose-agnostic, non-human-capable, and state-of-the-art on skinning; its skinning L1 is ~2.3× better than UniRig's on both reported benchmarks. Use the HF Space if you lack 14 GB VRAM. Then import to Blender and treat the result as a *starting skeleton + starting weights*, not a finished rig.
- **Use UniRig only in two cases:** you need `.vrm` input/output specifically (UniRig ships a VRM Blender add-on; SkinTokens' examples are GLB), or you only have 8 GB VRAM (UniRig's 8 GB floor vs SkinTokens' 14 GB).
- **The "type token, e.g. `mixamo`" detail in the SkinTokens paper strongly implies** its biped templates can emit Mixamo-convention chains, which would make retargeting trivial — but the README/paper do not document an explicit naming flag, so treat bone names as *unknown until inspected*. This is exactly why the Q1 inspection step must run on the auto-rigger's own output, not just on the input mesh.
- **Tripo is the right choice when you need guaranteed naming**, because `spec` is an explicit API parameter with six conventions including `mixamo`, `unreal` and `vrm` — no other tool surveyed exposes bone naming as a parameter. Its `rig_type` enumeration (hexapod/octopod/avian/serpentine/aquatic) is also the only documented non-biped taxonomy in the hosted set.
- **Nobody auto-rigs a usable face.** Tripo explicitly has no facial joints; AccuRig/Mixamo are body-only; ARP has a facial rig but only via its own system; Rigify has a face metarig but you place it manually. Treat face as a wholly separate project (Q6).
- **"Headlessly" summary:** fully scriptable → SkinTokens (`demo.py --input dir`), UniRig (`.sh` scripts with `--input_dir`), Tripo (REST + webhook), Rigify/ARP (via `blender --background --python` driving `bpy.ops.pose.rigify_generate()` / ARP's operators). Not headless → Mixamo (web UI only), AccuRig (desktop GUI, account-gated export), Cascadeur (GUI).
- **RigNet should be reported as historical.** It appears in SkinTokens' baseline table with the worst skinning L1 of the four compared methods (0.0573 vs TokenRig 0.0166), and it was not surfaced as actively used in any 2026 practitioner source. Its practical problems (old PyTorch, PointNet++/`torch-geometric` build pain, 5 k-vertex-class input assumptions) make it a poor choice when SkinTokens exists.

### Gaps
- **SkinTokens bone naming is not documented.** Neither the README section fetched nor the paper states the emitted bone-name strings for the biped template. **The reader must inspect the output GLB.** This is the single most important unknown for the retargeting step.
- **Whether SkinTokens emits finger bones is not stated**, only that it preserves finger-level *weight* differentiation. Likely it emits whatever the matched template has, which for VRoid/Mixamo-style data would include fingers — but unconfirmed.
- **Meshy and Rodin official API docs for rigging were not located**; all their capability claims here are third-party.
- **Mixamo's current polygon/file-size limits are unverified**; no official Adobe page was reachable (`help.mixamo.com` did not resolve from this session).
- **UniRig/SkinTokens licensing of *weights*** (as opposed to MIT code) was not checked — irrelevant for this non-commercial project but worth flagging as unverified.
- **Anything World** returned no usable results in this session's searches; I cannot state its 2026 status, API, or whether it still operates. Treat as unknown.
- I did not verify Rigify's exact 5.2 metarig list beyond "Basic Human" and "Human".

---

## Q4. Skinning quality: evaluating and fixing bad skin weights

### Takeaway
Auto-rig weights in 2026 fail in characteristic, predictable places — shoulders/chest (over-smoothing → armor and shoulder pads slump), hips/pelvis (inconsistent weights plus mis-placed pivots → foot sliding), jaw (noisy), and forearm twist (candy-wrapper). Candy-wrapper specifically **cannot be fixed by weights at all**; it requires dual-quaternion skinning (Blender's Preserve Volume), twist-bone chains, or corrective shape keys — and Preserve Volume does not survive export to engines that only do linear blend skinning.

### Cited Findings

- **Observed artifact catalogue from a 2026 hands-on test** — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo):
  - Tripo: "shoulder and chest weights were over-smoothed, so broad shoulder pads slumped during arm raises"; jaw weights noisy
  - Meshy: "hip and pelvis weighting was inconsistent"; hip pivots 5–15 cm off → foot-IK mismatch, feet sliding through the ground
  - AccuRig: weights "tight, the cleanest of the group"
  - Cascadeur auto-rig: "OK but not great"
- **Preserve Volume = dual quaternion skinning.** Blender's Armature modifier has a **Preserve Volume** checkbox that "switches to dual quaternion skinning, which avoids the collapse" — [Jessy Leite candy-wrap post (2026)](https://jessyleite.dev/posts/blender-candy-wrap-twist-deformation/)
- **Weights alone cannot fix candy-wrapper, with the mechanism stated:** on a Rigify forearm "the upper one does not twist at all, and the lower one twists 100% like the hand. This will always result in a candy-wrapper effect when the forearm is twisted 180 deg", and "every weight combination still averages two opposed rotations" — [Blender Artists: Rigify arm twist](https://blenderartists.org/t/rigify-arm-twist/555512); [Jessy Leite](https://jessyleite.dev/posts/blender-candy-wrap-twist-deformation/)
- **Twist bones only redistribute the problem, they don't remove it:** one practitioner who added extra forearm bones found "it only moves the shrinking from one point to another" — [Polycount: fix arm twist for a UE4 rig](https://polycount.com/discussion/210412/trying-to-fix-arm-twist-for-a-ue4-rig)
- **Corrective blend shapes are the reliable fix:** "corrective blend shapes can push the mesh back into shape at problem angles" — [Jessy Leite](https://jessyleite.dev/posts/blender-candy-wrap-twist-deformation/)
- **Preserve Volume does not export:** "Unreal Engine 4 doesn't support this feature, only Linear Blend Skinning", so the fix must be baked into the rig (twist bones) or into shapes — [Polycount](https://polycount.com/discussion/210412/trying-to-fix-arm-twist-for-a-ue4-rig)
- **Voxel Heat Diffuse Skinning** (Mesh Online, paid, sold via Superhive/Blender Market): "a bone-weighting algorithm similar to Blender's Automatic Weights. However, it solves many of the problems with the default feature", combining Blender's heat-map approach with voxels. Free version = **surface** heat diffuse skinning; paid version adds the **voxel** algorithm and extra features — [Blender Artists release thread](https://blenderartists.org/t/addon-voxel-heat-diffuse-skinning/696066); [cgpress](https://cgpress.org/archives/voxel-heat-diffuse-skinning-add-on-for-blender.html)
- **Voxel Heat usage requirement:** "the algorithm will convert all the sub-meshes to one solid statue to obtain best result, you'd better select all the sub-meshes and one armature before you run voxel heat diffuse skinning" — [Mesh Online](https://www.mesh-online.net/voxel.html)
- **Voxel Heat has an adaptive mode:** "an adaptive voxel mode works well in most cases, it balances the speed and the quality" — [Superhive FAQ](https://superhivemarket.com/products/voxel-heat-diffuse-skinning/faq)
- **Voxel Heat version/compat is murky:** a download listing gives v3.5.3 supporting "Blender v2.8 – v4.2.0 Alpha", while the developer's own page still says "the latest version is 3.4.2, updated on April 2, 2022" supporting v2.78–v3.3. Prices quoted range $30–$50 — [desirefx listing](https://desirefx.me/blender-add-ons/voxel-heat-diffuse-skinning-blender-addon-v3-5-3/); [Mesh Online](https://www.mesh-online.net/voxel.html)
- **Blender 5.x contributes nothing new algorithmically** (see Q3): weight-paint threshold rounding, Zero Weights overlay default, loop select in weight paint; Automatic Weights is still bone heat — [Blender 5.0 release](https://www.blender.org/download/releases/5-0/); [Cinevva Blender 5](https://app.cinevva.com/guides/blender-5-for-game-artists)
- **AI skinning in 2026 = SkinTokens.** Its skinning L1 on ModelsResource is 0.0166 vs UniRig 0.0381 and RigNet 0.0573, and it is "the only method that preserves finger-level weight differentiation" in its comparison — [arXiv HTML 2602.04805v2](https://arxiv.org/html/2602.04805v2)
- **SkinTokens can re-skin an existing skeleton:** `--use_skeleton` runs "skin only, using an existing skeleton", and `--use_postprocess` applies "voxel-based skin postprocessing" — [SkinTokens repo](https://github.com/VAST-AI-Research/SkinTokens)

### Inferences

- **`--use_skeleton` is the single most valuable fix in this whole document for the "good skeleton, bad weights" case.** If you have a hand-placed or retarget-correct skeleton but awful weights (common after Mixamo on a stylised body, or after Rigify auto-weights on bad topology), SkinTokens can regenerate *only* the weights against your skeleton, at state-of-the-art quality, locally and for free. This replaces the traditional answer (Voxel Heat Diffuse Skinning, paid).
- **Diagnostic protocol for weights** (no source frames it this way; this is the practical synthesis): build a QC action that drives, per limb, (a) 90° and 135° flexion of elbow and knee, (b) 180° forearm roll, (c) arm abduction to full overhead, (d) hip flexion to 90° and full split, (e) spine twist ±45° and full side bend, (f) jaw open + head turn ±80°. Scrub it and look for the five named artifacts. Then quantify: add a **Mesh Statistics / volume check** by comparing `trimesh` volume of the evaluated (modifier-applied) mesh at rest vs at extreme pose — volume loss above ~5% at shoulders/hips indicates weight averaging collapse.
- **Artifact → cause → fix mapping:**
  - *Candy-wrapper twist (forearm, wrist, neck, thigh)* → linear blend skinning averaging opposed rotations. Fix: Preserve Volume (viewport/Blender-only), **or** split the twist across a twist-bone chain whose roll is driven at fractional ratios (0.33/0.66/1.0) from the hand/foot, **or** corrective shape keys driven by the roll angle. For engine export, twist bones + shape keys only.
  - *Volume loss / collapse at shoulder and hip* → too-smooth weights spreading influence across a joint plus a mis-placed pivot. Fix first the **pivot** (move the bone head to the true rotation centre — Meshy's 5–15 cm offsets are a pivot problem, not a weight problem), then tighten weights, then Preserve Volume, then corrective shapes.
  - *Elbow/knee collapse or pinching* → insufficient geometry (needs 3+ edge loops across the joint) or weights bleeding past the joint. Fix: add loops, re-solve weights, Corrective Smooth modifier.
  - *Breast/armor/shoulder-pad deforming wrong* → rigid or semi-rigid sub-geometry weighted as if it were skin. Fix: assign those vertices **100% to a single bone** (so they stay rigid), optionally add a dedicated child bone, and exclude them from any smoothing pass. This is exactly the Tripo "shoulder pads slumped" failure.
  - *Feet sliding / floating* → root/pelvis pivot and scale, not weights (per the Strayspark Meshy case and the UE retargeting guidance in Q7).
- **"Delta Mush" in Blender is the Corrective Smooth modifier in its bind mode** — it smooths the *delta* between deformed and rest geometry rather than the surface, so it removes skinning noise without flattening the sculpt. It is the cheapest global improvement to mediocre auto-weights and should be the second thing tried after Preserve Volume. (Mechanism statement is my own; not re-verified against the manual this session.)
- **Expect to re-bind rather than repair when the auto-rigger over-smoothed globally.** Repainting a whole body by hand is days of work; re-solving with a different algorithm (SkinTokens `--use_skeleton`, Voxel Heat, or Blender's heat-map auto weights on a *voxel-remeshed proxy* then Data-Transferred back) is minutes.

### Inferences — [unverified-in-session] concrete Blender tools and paths

- **Bind / re-bind:** select mesh then armature, `Ctrl+P` → `Object ▸ Parent ▸ With Automatic Weights` (`bpy.ops.object.parent_set(type='ARMATURE_AUTO')`); `With Envelope Weights` (`ARMATURE_ENVELOPE`) for a crude fallback on broken topology.
- **Preserve Volume:** Armature modifier panel → **Preserve Volume** checkbox (`mod.use_deform_preserve_volume = True`).
- **Delta-Mush equivalent:** add a **Corrective Smooth** modifier *after* the Armature modifier; set **Smooth Type** to `Length Weight`, raise **Factor**/**Repeat**, and press **Bind** so it stores rest deltas. Optionally restrict with a vertex group.
- **Weight transfer / copy (the Data Transfer workflow):** select source mesh then target, `Object ▸ Link/Transfer Data ▸ Transfer Mesh Data` or add a **Data Transfer** modifier; enable **Vertex Data ▸ Vertex Group(s)**, set mapping to `Nearest Face Interpolated` (or `Nearest Corner and Best Matching Normal` for layered clothing), then `Object ▸ Apply ▸ Data Transfer` / `Datalayout Transfer` to create missing groups first (`bpy.ops.object.datalayout_transfer`, `bpy.ops.object.data_transfer`). This is the mechanism for: donor-rig weight transfer, voxel-proxy → original transfer, and clothing/armour inheriting body weights.
- **Weight painting essentials:** `Weight Paint` mode; brushes Draw/Blur/Smear/Average; `Weights ▸ Normalize All`, `▸ Normalize`, `▸ Clean` (removes sub-threshold weights), `▸ Limit Total` (cap influences at 4 for game engines — critical, most engines only read 4), `▸ Smooth`, `▸ Mirror`, `▸ Transfer Weights`. Enable **X-Mirror** and the **Zero Weights** overlay (now defaulting to Active in 5.0) to find unweighted vertices.
- **Corrective shape keys driven by pose:** create the fix shape in the posed state (sculpt it), then on the shape key's Value add a **Driver** whose variable is a `Transform Channel` (Rotation, local space) of the twisting bone, with a scripted or generator mapping. Add-ons that automate the "sculpt in pose, extract as corrective key" loop exist (e.g. Corrective Shape Keys / ShapeKey-based "pose correctives" add-ons); the manual route is Sculpt-in-pose → `Shape Key Specials ▸ New Shape From Mix` with the armature applied as a basis.
- **Normalise for export:** before FBX/glTF export, run `Weights ▸ Limit Total` (4) then `Weights ▸ Normalize All` with Lock Active off, else engine deformation differs from Blender.

### Gaps
- Voxel Heat Diffuse Skinning's **current Blender 5.x compatibility is unresolved** — the developer page and reseller listings disagree and neither mentions 5.x. If the reader is on Blender 5.2, assume it may not load, and prefer SkinTokens `--use_skeleton`.
- No source quantifies SkinTokens' skinning quality on *stylised/armoured* characters specifically; its benchmarks are ModelsResource/Articulation-2.0/Truebones.
- No 2026 AI tool for *repairing existing* weights (as opposed to regenerating them) was found.

---

## Q5. Fingers: adding them when the auto-rig gives none

### Takeaway
Of the tools surveyed, only AccuRig documents explicit finger rigging (0–5 digits, with markers you must hand-tune); Tripo's test report notes no finger findings and explicitly no facial joints; Rigify's **Basic Human** metarig has no fingers while **Human** does. The practical answer is almost always: generate the body with the auto-rigger, then graft a Rigify (or donor) hand chain and transfer weights.

### Cited Findings

- **AccuRig is the one auto-rigger with documented finger control:** hand rigs are generated separately with the option for up to five fingers, and "AccuRIG can accommodate hands with zero to five fingers"; "it's essential to manually fine-tune the finger markers to ensure they are accurately placed" — [cgdive AccuRig](https://cgdive.com/accurig-easy-rigging-for-humanoid-characters/); [ActorCore](https://actorcore.reallusion.com/auto-rig)
- **Rigify metarig choice determines fingers:** "Basic Human (doesn't include face and fingers)" is listed in the manual; practitioner guidance is to "pick Human and delete the face later" if you need finger bones — [Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html); [Cinevva](https://app.cinevva.com/guides/how-to-rig-a-character-in-blender)
- **SkinTokens preserves finger-level weight differentiation** — the only method in its comparison to do so — [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Finger bone tables exist in retargeting code for the SMPL-X 55-joint layout:** `Shinhyoung/UnrealMotionRetargetingSMPL` includes a Mixamo → SMPL 24-joint mapping and "also covers finger bones for a SMPL-X 55-joint blob" (notes in Korean) — [repo](https://github.com/Shinhyoung/UnrealMotionRetargetingSMPL)
- **Data Transfer is the documented weight-copy mechanism** (see Q4) and the `Retarget` extension offers "bone renaming for rig conversion" and "Extract Metarig (converts a humanoid rig to Rigify)" — [Retarget extension](https://extensions.blender.org/add-ons/retarget/)

### Inferences

- **Three viable paths, in order of preference:**
  1. **Rigify hand graft (best control, free).** Add a `Human` metarig, delete everything except one arm's hand/finger chain, scale and position it to the mesh's hand, mirror with `Armature ▸ Symmetrize`, then either (a) generate a full Rigify rig for the character and discard the auto-rig, or (b) keep the auto-rig's body and parent the Rigify finger `DEF-` bones under the auto-rig's hand bone. Then weight only the hand region.
  2. **Donor-rig weight transfer.** Take any correctly-weighted hand of similar topology (a Mixamo-rigged free character, a Rigify-generated hand), align it to the target hand, and use **Data Transfer ▸ Vertex Group(s)** with `Nearest Face Interpolated` to pull the finger weights across, then rename the groups to match the new bones. Fast, and the only sane approach for 15 finger bones × 2 hands × hundreds of vertices.
  3. **SkinTokens re-skin.** Add the finger bones by hand (positions only, no weights), export the skeleton, and run SkinTokens with `--use_skeleton` to solve all weights including fingers — the paper's finger-differentiation claim makes this the most promising automated route.
- **Minimum viable finger skeleton** is 15 bones per hand: thumb 01/02/03, index 01/02/03, middle, ring, pinky — matching both Mixamo (`mixamorig:LeftHandThumb1..3`, `...Index1..3`, etc.) and UE5 (`thumb_01_l..thumb_03_l`, `index_01_l`, `middle_01_l`, `ring_01_l`, `pinky_01_l`) conventions. Using one of those two naming schemes from the start removes all finger retargeting work later. SMPL-X's 55-joint layout also uses 15 per hand (3 per digit), so the three standards map 1:1 at the finger level — the mapping tables in Q7 therefore work for fingers without special-casing.
- **Weighting fingers is where polycount bites.** If each finger segment has fewer than 2 edge loops, no weighting will produce a believable bend; you must add loops or accept rigid segments. Rigid segments (100% to one bone, no blending) actually look acceptable for stylised characters and are far faster than painting.
- **Pose libraries for grips:** Blender's **Asset Browser** pose-library workflow (create pose assets from selected bones, mark as asset, apply with blending from the Pose Library in the Asset Browser sidebar) is the current mechanism and is the right place to store fist/relaxed/point/grip-cylinder/grip-pistol poses. These are per-rig, so build them once after the finger bones are final and name them so they can be reapplied after any rig regeneration.

### Inferences — [unverified-in-session] operators
- `Armature ▸ Symmetrize` (`bpy.ops.armature.symmetrize()`) requires `.L`/`.R` suffixes — if the auto-rig used `_l`/`_r` or `Left`/`Right`, rename to `.L`/`.R` for the symmetrize/X-mirror workflows, then rename back for export.
- Pose assets: Pose Mode, select finger bones, Asset Browser → `Create Pose Asset`; apply via the Pose Library panel with a blend slider. In older versions this lived in the legacy Pose Library (`bpy.ops.poselib.*`), now superseded by Asset Browser pose assets.

### Gaps
- **No source states whether SkinTokens or UniRig emit finger *bones*** for a bare biped mesh — only that SkinTokens handles finger *weights* well. Must be tested.
- Tripo's docs do not state finger coverage, and the hands-on test "does not report finger support for any tool" — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo). So finger quality across the hosted riggers is genuinely undocumented in 2026.

---

## Q6. Face: adding facial animation to a mesh with no blendshapes

### Takeaway
2026 has a genuine research answer and a genuine practical answer. Research: **OmniFaceRig** (arXiv 2606.08043) is fully automatic, works across diverse 3D character topologies, produces up to **155 FACS blendshapes**, and — uniquely — generates **inner-mouth geometry** (teeth, gums, tongue), which is the thing every transfer-based pipeline lacks. Practical: **Faceit** (paid Blender add-on) for the 52-shape ARKit set, **KeenTools FaceBuilder** if you want the shapes generated from a head fit, and **Mesh to MetaHuman** if your target is Unreal — Epic's own docs say it accepts **any topology** but require a tracked neutral pose.

### Cited Findings

- **OmniFaceRig** (arXiv 2606.08043): a fully automatic pipeline that turns a **static, surface-only** character mesh into a **FACS rig with up to 155 blendshapes**, and additionally generates **inner-mouth geometry (teeth, gums, tongue)** — its stated departure from pipelines that transfer only external facial shapes. Title: "OmniFaceRig: Fully Automatic Inner-Mouth-Aware Face Rigging Across Diverse 3D Character Topologies" — [arXiv 2606.08043](https://arxiv.org/pdf/2606.08043); [EmergentMind summary](https://www.emergentmind.com/topics/omnifacerig)
- **TopoRig**: "TopoRig: Topology-Agnostic Facial Rigging via Multi-Source Supervision" — [arXiv 2609.15746](https://arxiv.org/pdf/2609.15746)
- **RigAnyFace** and similar learning-based systems are "focused on outer-surface deformation and mostly trained on human faces", which is the gap OmniFaceRig targets — [EmergentMind](https://www.emergentmind.com/topics/omnifacerig)
- **Faceit** (Blender, paid, ~$78+): semi-automatic and non-destructive; you place landmarks to fit the rig; "optimized for the 52 ARKit shapekeys" but also supports nVidia Audio2Face and others; presets loadable from **ARKit, Audio2Face, tongue, or phoneme** sets, plus custom presets; it can **bake deformation from the modifier stack, pose and sculpting into final shape keys**; mocap retargeting works "for shape keys with non-identical names"; it can "animate any 3D character equipped with a set of shape keys resembling the 52 captured micro expressions" — [Faceit docs](https://faceit-doc.readthedocs.io/en/latest/); [Faceit mocap docs](https://faceit-doc.readthedocs.io/en/latest/mocap_general/); [Superhive listing](https://superhivemarket.com/products/faceit)
- **Faceit workflow order** (from a CGDive course and a walkthrough): fit landmarks → generate the 52 ARKit expressions → refine shape keys → build a facial control rig → bake → import mocap; final control can be via **Rigify, Auto-Rig Pro, or custom rigs** — [CGDive Faceit tutorial](https://www.classcentral.com/course/youtube-faceit-tutorial-1-facial-mocap-shapekeys-addon-blender-208778); [YouTube walkthrough](https://www.youtube.com/watch?v=FSCjDZKQiY8)
- **Faceit version/compat:** a download listing refers to **v2.3.71 (Blender 5)** — [vfxmed listing](https://www.vfxmed.com/2026/06/latest-evrsion-of-faceit-blender-addon-free-download/) (reseller/piracy-adjacent source; treat version number as weakly sourced, check the official docs)
- **KeenTools FaceBuilder** (Blender) "can generate all 52 ARKit shapes for a head", exportable to Unity or Unreal as FBX — [Super Render Farm on FaceBuilder 2024.1](https://superrendersfarm.com/article/keentools-facebuilder-2024-1-blender-update)
- **MetaHuman Facial Rig Transfer (MHFRT)** — a paid Blender add-on offering auto facial rig + auto retopology by transferring the MetaHuman facial rig — [Superhive MHFRT](https://superhivemarket.com/products/mhfrt); **ArkitAutoRiger** is another paid Blender add-on in this space — [Superhive ArkitAutoRiger](https://superhivemarket.com/products/arkitautoriger)
- **Open-source deformation-transfer attempt, with honest failure notes:** a QtMeshEditor issue (July–Sept 2026) describes chaining **non-rigid ICP + deformation transfer** to produce 52 ARKit deltas on humanoid meshes; its own commit history notes the shapes "were initially too weak and needed fixes" — [QtMeshEditor issue #889](https://github.com/fernandotonon/QtMeshEditor/issues/889)
- **Smartphone-driven personalisation:** an ACM paper uses ARKit captures plus deformation transfer to build personalised avatar blendshapes automatically — [ACM 10.1145/3562939.3565622](https://dl.acm.org/doi/fullHtml/10.1145/3562939.3565622)
- **Mesh to MetaHuman — topology:** Epic's documentation says the mesh "can use any topology"; the backend builds a MetaHuman-compatible **Template Mesh** from your mesh, so retopologising is unnecessary — [Epic: From Mesh](https://dev.epicgames.com/documentation/en-us/metahuman/from-mesh)
- **Mesh to MetaHuman — required neutral pose workflow:** you must create and track a **Neutral Pose** before the solve. Steps per Epic: set the **field of view to 20 degrees or less**, face the head forward so it's fully visible, **promote the frame**, then **Track Active Frame** and run the **Identity Solve** — [Epic: From Mesh](https://dev.epicgames.com/documentation/en-us/metahuman/from-mesh)
- **Mesh to MetaHuman — practitioner input requirements (third-party, not Epic):** neutral expression with closed mouth, head upright and facing forward with the neck straight; mesh should be a **single continuous head surface without separate eyeball or teeth geometry**; watertight and centred; remove hair, clothing and shoulders so the mesh is mainly head plus a small part of the neck — [yelzkizi Mesh to MetaHuman](https://yelzkizi.org/mesh-to-metahuman/); [yelzkizi part 2](https://yelzkizi.org/mesh-to-metahuman-2/). Flagged explicitly: "Treat these as cautious practice rather than official Epic requirements, since the Epic page doesn't list them."
- **MetaHuman docs version:** the Epic page reflects **Unreal Engine 5.6 and later**; a separate **MetaHuman Creator From Template Tool** exists in-engine — [Epic: From Mesh](https://dev.epicgames.com/documentation/en-us/metahuman/from-mesh); [Epic: MetaHuman Creator From Template](https://dev.epicgames.com/documentation/metahuman/metahuman-creator-from-template-tool-in-unreal-engine)
- **Rigify's face system is modular now, with an Upgrade Face Rig migration path, and sparse documentation** (see Q3 citations) — [Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html); [oddlyshapeddog](https://medium.com/@oddlyshapeddog/how-to-rig-eyes-with-eyelids-in-blender-3-x-4-0-using-rigifys-skin-eye-rig-f8198943a83b)
- **Retargeting MetaHuman facial animation into Blender:** Poly Hammer's Character Control Rig retargeting engine advertises MetaHuman animation retargeting in Blender with templates that detect the source skeleton and need no manual mapping, supporting Rokoko, Mixamo, MetaHuman, Rigify and Auto-Rig Pro — [Poly Hammer blog](https://www.polyhammer.com/blog/character-control-rig-retargeting-engine)

### Inferences

- **Decision tree for face:**
  - Target is **Unreal and the character is roughly humanoid-headed** → **Mesh to MetaHuman**. It is the only path that gives you a production facial rig (full MetaHuman facial animation, LiveLink, Audio-to-face) for free effort, and Epic's "any topology" claim means you don't retopologise. Cost: the result *is* a MetaHuman, so you inherit MetaHuman's look/proportions in the head; and your original head mesh is used as a *target to fit*, not preserved exactly.
  - Target is **Blender/engine-agnostic, humanoid head, you want the 52 ARKit set** → **Faceit**. It is the mature, documented, shape-key-producing option, it bakes to real shape keys (so the result is engine-portable), and its mocap retargeting tolerates non-matching shape-key names.
  - **Stylised / non-human head** → **OmniFaceRig** is the only thing that claims to work "across diverse 3D character topologies" *and* to add inner-mouth geometry, which is the usual killer for creature faces. Verify code availability before committing (see Gaps).
  - **No budget, no research code** → joint-based face rig: build a bone-driven face in Rigify's modular face system (jaw, eyelids via `skin_eye`, lip chains, brow chains) and weight it. Joint-based faces are also the right answer when the character is a mechanical/creature head where blendshapes make no sense, and they export cleanly to every engine as plain skinning.
- **Blendshape transfer from a donor head, the free manual route**, is a real and well-trodden Blender workflow: align a donor head that already has the 52 ARKit keys, add a **Surface Deform** modifier on the donor bound to the target (or wrap the target to the donor), drive each donor key to 1.0 in turn, and apply the deformed result as a new shape key on the target. In practice this is what the paid add-ons automate, and the QtMeshEditor issue shows the characteristic failure — transferred deltas come out **too weak**, because non-rigid ICP under-fits the lips and eyelids. Expect to scale the resulting deltas up and hand-fix the eye and mouth corners.
- **Order matters relative to the body rig:** generate shape keys **before** applying any pose-to-rest or armature changes, or use the `kxn4t/pose-to-rest-pose` add-on, since the built-in rest-pose apply breaks on meshes with shape keys (Q2).
- **The ARKit 52 set is the right standard to target** regardless of path, because it is what iPhone/LiveLink capture emits, what Faceit is optimised for, what FaceBuilder generates, and what Audio2Face maps to. Adopting it makes face mocap a solved input problem.

### Gaps
- **OmniFaceRig code/weights availability is unverified.** I have the arXiv paper and a summary page but did not confirm a public repo, released checkpoints, install instructions, or VRAM. This is the most important open question in the face section — the capability described is exactly what the reader wants, but it may be paper-only.
- **TopoRig likewise:** paper located, no implementation details verified.
- **Faceit's confirmed Blender 5.x support** — the only version claim (v2.3.71 "Blender 5") comes from a reseller/download-aggregator page. Check `faceit-doc.readthedocs.io` directly.
- **Mesh to MetaHuman limitations for stylised/non-human heads are not documented by Epic** on the page fetched, and third-party sources only give cautious practice. Whether a heavily stylised or non-human head solves acceptably is unknown; the Identity Solve fits a human template, so strong failure is plausible but unconfirmed.
- No source benchmarks the ARKit-transfer tools against each other: "the ARKit transfer results are not benchmarked against each other in these sources" — [search synthesis](https://www.emergentmind.com/topics/omnifacerig)

---

## Q7. Retargeting onto the finished rig: mapping tables, pitfalls, tools

### Takeaway
The dominant failure is **rest-pose mismatch (A vs T)**, and the dominant fix is to make the source and target *retarget poses* visually match before batching — not to fiddle with individual chains. Secondary failures are bone-name mismatch (Meshy's non-standard spine/clavicle names defeat UE's auto-detection), bone roll/axis mismatch (ARP Remap is the tool that maps orientations as well as names), and root/scale problems (which cause floating or sinking feet, not limb errors).

### Cited Findings — the core pitfall and fix

- **Root cause of shoulder/arm error:** "the source skeleton is in an A-pose and the target in a T-pose (or the reverse), so every frame inherits that offset" — [Strayspark UE retargeting guide](https://www.strayspark.studio/blog/unreal-engine-animation-retargeting-ik-rig-complete-guide)
- **Fix:** open the retargeter and **edit the retarget pose until both skeletons visually match before batching**; alternatively use the Source/Target **Post Retarget Pose** settings to align rest poses first — [Strayspark](https://www.strayspark.studio/blog/unreal-engine-animation-retargeting-ik-rig-complete-guide); [cosindra UE5 IK Retargeter guide](https://cosindra.ai/guides/ue5-ik-retargeter)
- **The guides contradict each other on which side to change**, and this should be reported as unresolved: one says set the source to T-pose to match the animation data and the target to your character's A-pose and let the IK Retargeter handle the offset automatically; another says the source should match the target's pose; a third says apply T-pose to the MetaHuman in the IK Retargeter. "Test whichever setup gives correct shoulders on your rig, since the guides don't agree." — [Strayspark](https://www.strayspark.studio/blog/unreal-engine-animation-retargeting-ik-rig-complete-guide); [yelzkizi UE retargeting](https://yelzkizi.org/retarget-any-animation-in-unreal-engine/); [yelzkizi Mixamo→MetaHuman](https://yelzkizi.org/retarget-mixamo-animations-to-metahuman/)
- **Don't let IK paper over proportions:** "IK is not a substitute for compatible proportions or a good retarget pose. Excessive pinning can push the distortion into the hips and shoulders." — [Strayspark](https://www.strayspark.studio/blog/unreal-engine-animation-retargeting-ik-rig-complete-guide)
- **Floating/sinking feet are a root-and-scale problem, not a pose problem** — point to the retarget root and scale settings; bone debugging in the retargeter helps verify mappings — [Strayspark](https://www.strayspark.studio/blog/unreal-engine-animation-retargeting-ik-rig-complete-guide)
- **A-pose ↔ T-pose conversion without re-rigging is an established UE workflow** via the Retarget Manager — [mocaponline A-pose/T-pose conversion](https://mocaponline.com/blogs/mocap-news/a-pose-or-t-pose-conversion-ue4-retarget-manager); [mocaponline T-pose fix UE5/Unity](https://mocaponline.com/blogs/mocap-news/tpose-animation-retargeting-fix)
- **Mixamo → UE5 Manny is built in:** "UE has built-in Mixamo-to-Manny retargeting" — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Non-standard names break auto-detection:** Meshy's "non-standard spine and clavicle names … UE's retargeter does not auto-detect them", requiring manual remapping (~8 min) — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **Tripo needed only "a minor foot-bone offset"** to work against Manny/Quinn — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)

### Cited Findings — tools

- **Auto-Rig Pro Remap** "maps bone names *and orientations* between any source and target skeleton" — this orientation handling is the differentiator for bone-roll/axis mismatch — [Cinevva ARP alternatives](https://app.cinevva.com/guides/auto-rig-pro-alternatives)
- **Rokoko Blender plugin status is a problem in 2026:** a review says the add-on "has not yet been updated to fully work with the latest Blender version", and for Rigify targets "automatic mapping will likely be wrong and incomplete, so you will need to manually adjust the bone pairs". Rokoko's own developers reportedly recommend ARP instead — [cgdive Rokoko addon](https://addons.cgdive.com/tools/rokoko-addon)
- **`Retarget` (free, extensions.blender.org, by KBS-DEV)** — a fork of **Expy Kit** and **Animaide**, **Blender 5.0+ only**, latest **v5.2.0 published 19 Dec 2025**, GPL v3+. Features relevant here — [Retarget extension](https://extensions.blender.org/add-ons/retarget/):
  - **Presets for Mixamo, Unreal, VRoid, MMD, Daz, Auto Rig Pro** and others; you can add your own preset in the N-panel
  - **Bind to Active Armature / Unbind**, apply/disable/remove constraints, select constrained controls, **align bones**, mirror support, **Bind to an active metarig (any rig to Rigify)**; v5.2.0 adds `Pole_bone_settings` for Rigify pole bones
  - **Extract Metarig** (converts a humanoid rig to Rigify), **bone renaming for rig conversion**, **Rigify game-friendly output**
  - **Apply a new rest pose with mesh alignment**; **apply scale or create a scale offset while fixing the animation**; **merge head/tail bones**
  - **Bake Constrained Actions** (with bake-similar-actions-together), **Add Root Bone**, **Transfer Root Motion**, Action Range to Scene, FBX import
  - Migration note: Expy Kit presets need the preset file's second line changed from `expykit_retarget` to `retarget_retarget`
  - Tools live in the **right-click context menu in Object and Pose modes**; presets in the N-panel
- **`Keemap` (free, open source)** — "allows mapping motions of one rig to another. Works with ANY rig and allows user to map bones from one rig to another and save mapping files out to hard drive"; tested on Blender 3.3.1, so current-version compatibility is uncertain — [Keemap repo](https://github.com/nkeeline/Keemap-Blender-Rig-ReTargeting-Addon)
- **Poly Hammer Character Control Rig** — templates detect the source skeleton with no manual mapping; supports Rokoko, Mixamo, MetaHuman, and now Rigify and Auto-Rig Pro — [Poly Hammer](https://www.polyhammer.com/blog/character-control-rig-retargeting-engine)
- **Rokoko bone-list files for specific characters are sold separately** (e.g. a CC3 character bone list for Blender), indicating mapping files are the real unit of work — [Gumroad Rokoko bone list](https://adnanlatif.gumroad.com/l/ltqqr?layout=profile)

### Cited Findings — SMPL-X and open-source retargeting repos

- **No single dedicated SMPL-X → Mixamo bone-map repo was found.** Closest matches — [search synthesis](https://github.com/squall01337/mixamo-llm-mocap):
  - **`squall01337/mixamo-llm-mocap`** — closest match; transfers SMPL-X joint rotations onto a Mixamo rig via a `retarget_smplx.py` step; tagged `smpl-x` and `mixamo`; drives Blender via MCP — [repo](https://github.com/squall01337/mixamo-llm-mocap); [write-up](https://themenonlab.blog/blog/mixamo-llm-mocap-video-to-3d-animation)
  - **`MaxLiu22/Motion2MixamoRig`** — takes a Mixamo character plus the SMPL-X model and drives the Mixamo rig from video; SMPL-X body model must be downloaded separately — [repo](https://github.com/MaxLiu22/Motion2MixamoRig)
  - **`Shinhyoung/UnrealMotionRetargetingSMPL`** — contains a **Mixamo bone name → SMPL 24-joint mapping** (notes in Korean) and **finger bones for a SMPL-X 55-joint layout**; it maps Mixamo→SMPL rather than the reverse, but the table is adaptable — [repo](https://github.com/Shinhyoung/UnrealMotionRetargetingSMPL)
  - **`Shimingyi/ARP-Batch-Retargeting`** — Blender + Auto-Rig Pro batch BVH retargeting between skeletons, with **`SMPL_to_Mixamo` sample configs** — [repo](https://github.com/Shimingyi/ARP-Batch-Retargeting)
  - **`ccrpRepo/robot_retargeter`** — "Motion retargeting pipeline from SMPL-X / source robots to target humanoid robots" — [repo](https://github.com/ccrpRepo/robot_retargeter)
  - **`Arthur151/blender-animation-retargeting`** — a Blender animation-retargeting script surfaced in the same search — [repo](https://github.com/Arthur151/blender-animation-retargeting)
- **`RaidTheory/Godot-Mixamo-Animation-Retargeter`** covers Mixamo→Godot only — [repo](https://github.com/RaidTheory/Godot-Mixamo-Animation-Retargeter)
- **A bone-table PR exists but is low-provenance:** "Retargeting: exact bone tables for SMPL, VRM, Daz and UE5" in `sambena/autorig-workbench` PR #76 — [PR](https://github.com/sambena/autorig-workbench/pull/76). **I could not establish this repo's provenance or whether the PR was merged; treat as a lead, not a source.**
- **Pose-aware Attention Network for Flexible Motion Retargeting by Body Part** — a learned retargeting approach (for context, not a tool) — [arXiv 2306.08006](https://arxiv.org/pdf/2306.08006)

### Inferences

- **Mapping tables — the structural facts to report.** The three standards the reader named differ in three ways that matter, and each has a distinct remedy:

  | | SMPL-X | Mixamo | UE5 Manny |
  |---|---|---|---|
  | Naming | `pelvis`, `spine1..3`, `left_collar`, `left_shoulder`, `left_elbow`, `left_wrist`, `left_hip`, `left_knee`, `left_ankle`, `left_foot`, `jaw`, `left_eye_smplhf`, + 15 per hand | `mixamorig:Hips`, `Spine`, `Spine1`, `Spine2`, `LeftShoulder`, `LeftArm`, `LeftForeArm`, `LeftHand`, `LeftUpLeg`, `LeftLeg`, `LeftFoot`, `LeftToeBase`, + `LeftHandThumb1..3` etc. | `root`, `pelvis`, `spine_01..05`, `clavicle_l`, `upperarm_l`, `lowerarm_l`, `hand_l`, `thigh_l`, `calf_l`, `foot_l`, `ball_l`, + `thumb_01_l` etc. |
  | Rest pose | arms slightly down/forward, **not** a clean T or A (SMPL's canonical pose) | **T-pose** | **A-pose** |
  | Root | **no root bone** — `pelvis` is the root | **no root bone** by default (Hips is root); root motion lives in Hips translation | **has a dedicated `root` bone** at the floor, separate from `pelvis` |
  | Spine segments | 3 | 3 (Spine, Spine1, Spine2) | 5 (spine_01..05) |
  | Toes | `left_foot` only (toe-less) | `LeftToeBase` (+ optional `LeftToe_End`) | `ball_l` |

  Consequences: **SMPL-X → anything** needs a rest-pose correction (SMPL canonical → T or A) *and* synthesis of a root bone *and* a decision about the 3-spine → 5-spine remap (typical choice: `spine1→spine_01`, `spine2→spine_03`, `spine3→spine_05`, leaving 02/04 to interpolate, or constrain 02/04 to blend). **Mixamo → UE5** needs root-motion extraction from Hips into a new `root` bone (this is precisely what the `Retarget` extension's **Add Root Bone** + **Transfer Root Motion** operators are for) plus the T→A pose correction. **UE5 Manny → custom** is the easiest if your rig uses UE naming, because UE's own IK Retargeter handles it natively.
- **Bone roll / axis mismatch is the pitfall that produces "the arm rotates around the wrong axis" and is invisible in a name-mapping table.** Two tools in this survey address it explicitly: ARP **Remap** (maps orientations as well as names) and the `Retarget` extension's **align bones** operator. In raw Blender, the fix is per-bone `Armature ▸ Bone Roll ▸ Recalculate Roll` or copying rolls from the source, and on FBX import, enabling **Automatic Bone Orientation** — which is why the inspection script in Q1 sets that flag and prints each bone's `x_axis`.
- **Recommended free 2026 retargeting stack for Blender:** the **`Retarget` extension** (Blender 5.0+) is now the best free option and supersedes Rokoko for this purpose, because (a) Rokoko's add-on is reportedly behind on Blender versions and maps Rigify poorly, (b) `Retarget` ships Mixamo/Unreal/VRoid/MMD/Daz/ARP presets, (c) it has the three prep operators you otherwise hand-roll (rest-pose apply with mesh alignment, scale-offset with animation fix, add-root + transfer-root-motion), and (d) it has `any rig → Rigify` binding and Extract Metarig. If you are on Blender 4.x it will not install (5.0+ only), in which case Expy Kit (its upstream) or ARP Remap are the alternatives.
- **Unity avatar mapping** is a different kind of thing and should be framed as such: Unity's Avatar/Humanoid configuration is a *mapping to Unity's own 54-slot humanoid template* done in the Avatar Configuration inspector (Mapping tab, with Auto-Map, and a Muscles & Settings tab for per-joint limits), plus **Avatar Mask** for partial-body and **Optimize Game Objects**. Unity's auto-map works well for Mixamo/UE-style names and poorly for novel names, and Unity enforces a required-bone subset (hips, spine, head, upper/lower arms, hands, upper/lower legs, feet) while fingers and chest/neck/shoulders are optional. Unity also wants a T-pose and offers **Enforce T-Pose** in the Pose dropdown. (This paragraph is from my working knowledge; not re-verified this session.)
- **Procedure that avoids most pain**, independent of tool: (1) name your final rig to one existing standard (UE5 or Mixamo) — do not invent names; (2) build it in **A-pose if targeting UE5, T-pose if targeting Mixamo/Unity/SMPL**; (3) add an explicit floor-level `root` bone even if your engine doesn't need one; (4) keep the skeleton's bone count minimal and avoid helper/twist bones in the *export* hierarchy where possible, or mark them clearly; (5) verify with a single walk cycle and a single arms-overhead clip before batching hundreds of animations.

### Gaps
- **Which side's retarget pose to edit in UE5 is genuinely contested across the three guides found**; no authoritative Epic statement was retrieved. Report as "test both".
- **No canonical, maintained SMPL-X ↔ Mixamo ↔ UE5 mapping table repo was found.** The tables above are assembled from my knowledge of the three standards plus the partial tables in `Shinhyoung/UnrealMotionRetargetingSMPL`; the exact SMPL-X joint-name strings and the UE5 spine count should be re-verified against the SMPL-X release and the UE5 skeleton before anyone types them into code.
- `sambena/autorig-workbench` PR #76 (claimed "exact bone tables for SMPL, VRM, Daz and UE5") is unverified provenance.
- Rokoko add-on's exact current Blender version support was not confirmed from Rokoko's own site.

---

## Q8. Non-human and stylised proportions: what fails and the workarounds

### Takeaway
The failures are tool-specific and well documented: **Mixamo and AccuRig reject or mangle anything non-humanoid** (Mixamo needs a connected, symmetric mesh with no wings, tails or extra limbs; AccuRig is "not supported" for non-human). **ARP Smart is humanoid-only** and you must drop to Rig Definition + Multi-Ped + explicit tail/limb modules. **Tripo exposes an explicit non-biped taxonomy** (quadruped/hexapod/octopod/avian/serpentine/aquatic/others) with a dedicated creature model. **SkinTokens is the only tool with animals as an explicit held-out benchmark**, and its paper names the actual failure mode: extremely thin or topologically ambiguous geometry, and baselines missing "anatomical features like ears or horns".

### Cited Findings

- **Mixamo:** humanoid bipeds only; needs "a connected mesh with no wings, tails, extra limbs" and maps joints from a symmetric shape — [GOJO Mixamo guide](https://www.gojo.blog/mixamo-guide/). Listed flatly as "Humanoid only" — [Cinevva ARP alternatives](https://app.cinevva.com/guides/auto-rig-pro-alternatives)
- **AccuRig:** "Not supported" for non-human characters — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **ARP:** Smart "only works with humanoids"; non-humanoids go to **Rig Definition**; quadrupeds use **Multi-Ped**, which gives controllers free orientation because the animal stands on its "hands" — but "This setting only affects the visual shape though, the actual bones may be oriented in any directions." Tail has a **Count** parameter and a **Master Controller at Root** option; since 3.62 tail limbs "can be added multiple times and duplicated" — [ARP docs](https://lucky3d.fr/auto-rig-pro/doc/auto_rig.html); [ARP updates log](https://lucky3d.fr/auto-rig-pro/doc/updates_log.html)
- **ARP's modularity is the cited reason to pay for it:** reviewers note that "for non-standard characters or creatures, the system allows adding modular wings, tails, and extra limbs"; one comparison lists it as "Biped, quadruped, custom at $50 for the Full version" — [Cinevva ARP alternatives](https://app.cinevva.com/guides/auto-rig-pro-alternatives)
- **Tripo exposes non-biped structure as an API parameter:** `rig_type` ∈ {biped, quadruped, hexapod, octopod, avian, serpentine, aquatic, others}, with `v2.5-20260210` the model for "non-humanoid creatures" and `v3.0-20260909` supporting `rig_type: others`. **Crucially, `spec` (bone naming) "only applies when rig_type is biped"** — so non-biped output has no naming guarantee — [Tripo Auto Rig docs](https://developers.tripo3d.ai/en/docs/animations-rig)
- **Tripo was the best performer on a stylised non-human in the hands-on test:** "best of the group on a stylized robot; bones placed sensibly with no manual fixes" — [Strayspark showdown](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)
- **SkinTokens/TokenRig treats non-humans as first-class:** **Truebones Zoo** is used as a held-out out-of-distribution benchmark; bone order comes from "predefined templates (e.g., for bipeds) **or chain partitioning strategies for more general structures**"; and the paper's critique of baselines is that they "miss anatomical features like ears or horns on non-humanoid characters". Its own stated failures are "extremely thin or topologically ambiguous geometry that is rare in training", with a **looped handle-like structure** and a **slender claw** as the published examples — [arXiv HTML 2602.04805v2](https://arxiv.org/html/2602.04805v2)
- **UniRig degrades specifically on missing creature bones:** the README recommends refining the skeleton before skinning because "missing tail or wing bones degrade results" — [UniRig repo](https://github.com/VAST-AI-Research/UniRig)
- **SkinTokens cannot be told the topology you want:** "the system generates rigs from learned priors and does not yet accept user-specified topology templates or interactive guidance" — [arXiv HTML](https://arxiv.org/html/2602.04805v2)
- **Rigify ships non-human metarigs** — predefined metarigs beyond Human/Basic Human exist in the Add Armature menu — [Rigify manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html)
- **Floating parts break the "one solid statue" assumption** of voxel weighting — [Mesh Online](https://www.mesh-online.net/voxel.html)
- **SKAVA Auto Rigger Pro** claims humanoid + quadruped + "simple skeletal chains" with automatic weight painting and up to 1M vertices — [Cinevva](https://app.cinevva.com/guides/auto-rig-pro-alternatives) (vendor claim)
- **Meshy's quadruped support is contested** (one source yes, one no) — [Meshy roundup](https://www.meshy.ai/blog/best-ai-auto-rigging-tool) vs [ludo.ai](https://ludo.ai/compare/best-ai-auto-rigging-tools)

### Inferences

- **Failure-mode table, mapping each stylisation to its concrete breakage and workaround:**

  | Stylisation | What actually fails | Workaround |
  |---|---|---|
  | **Huge hands / tiny legs** (chibi, cartoon) | Learned riggers place joints by regressing to training-set proportions, so pivots land inside the wrong volume; retargeting then produces ground-penetration and limb over/under-reach. Mixamo's symmetric-humanoid joint mapping mis-sizes limbs. | Accept the auto-skeleton's *topology*, hand-move the *pivots* (Edit Mode, move bone heads/tails to true rotation centres), then re-solve weights only — SkinTokens `--use_skeleton`. On retarget, this is a proportion problem: use IK-based retargeting with foot/hand pinning, and expect to scale limb-length ratios. |
  | **Tail** | Mixamo rejects it outright. UniRig degrades at the skinning stage if the tail bones are missing from the skeleton. | ARP: Rig Definition + Tail limb with explicit **Count** and **Master Controller at Root**; Tripo: `rig_type: serpentine`/`quadruped`; SkinTokens/UniRig: inspect the predicted skeleton and **add the tail chain by hand before running the skin stage**. |
  | **Wings** | Mixamo rejects ("no wings"). UniRig README names wings as a degradation case. | ARP modular wing limbs; Tripo `avian`; or hand-build the chain and re-solve weights. |
  | **Four arms / extra limbs** | Mixamo rejects. Every learned biped template has exactly two arms, so a second pair is either ignored or absorbed into the torso weights. | ARP Rig Definition with duplicated arm limbs (its modular limb system is the only surveyed tool that supports this), or Rigify with duplicated `limbs.super_limb` arm chains in the metarig. Then weight the extra pair by hand or by Data Transfer from the first pair. |
  | **Floating / disconnected parts** (hovering orbs, detached armour, cloth strips) | Heat-diffusion and voxel weighting both need connectivity/solidity — floating parts get no weights or get averaged garbage; Voxel Heat explicitly fuses sub-meshes into "one solid statue". | Do not auto-weight them. Separate them, assign each 100% to a single dedicated bone (rigid), parent that bone sensibly, and rejoin. For many small parts, use `Separate ▸ By Loose Parts`, weight each to a bone, rejoin. |
  | **Non-biped structure** (spider, snake, fish, mechanical) | ARP Smart refuses; AccuRig/Mixamo refuse; Tripo's `spec` naming parameter stops applying, so you get Tripo-native names with no retarget preset anywhere. | Tripo's matching `rig_type` for the skeleton, then **rename bones yourself** to a convention you control; or SkinTokens (its chain-partitioning handles general structures); or hand-build in Rigify from `basic.raw_copy`/`basic.super_copy` chains. There is no retargeting path — animate by hand or procedurally. |
  | **Very thin geometry** (claws, antennae, fingers on a thin glove, chains) | SkinTokens' *published* failure case: "extremely thin or topologically ambiguous geometry". Voxel methods collapse thin features into or out of existence depending on voxel size. | Reduce voxel size (Voxel Heat adaptive mode / Remesh), or exclude the thin parts from the auto-solve and weight them rigidly. |
  | **Horns, ears, non-human head features** | Baselines "miss anatomical features like ears or horns"; no auto face rig covers them. | Add bones by hand and weight rigidly or with a simple falloff; these are secondary-motion candidates (jiggle/physics), not deformation. |

- **The single most important structural insight for non-human work:** every tool in this survey separates *skeleton prediction* from *weight solving*, and both SkinTokens (`--use_skeleton`) and UniRig (`generate_skin.sh` taking a skeleton FBX) let you substitute your own skeleton. That makes the correct non-human workflow: **hand-author or hand-correct the skeleton (where human judgement is irreplaceable), then let the AI solve the weights (where it beats you).** This also sidesteps SkinTokens' stated inability to accept topology templates — you supply the topology by supplying the skeleton.
- **Retargeting is effectively unavailable for non-bipeds** and the report should say so plainly: Tripo's naming conventions are biped-only, no retargeting add-on surveyed ships a quadruped/hexapod preset, and UE's IK Retargeter and Unity's Humanoid avatar are both built around a humanoid template. Non-biped animation means keyframing, procedural/IK rigs, or a source animation library authored on the *same* skeleton (e.g. Truebones Zoo for animals).
- **Stylised bipeds are the sweet spot for 2026 tooling** — Tripo handled a stylised robot with no manual fixes, and SkinTokens trains 20% on VRoid (anime) data. A chibi/anime/stylised humanoid is a well-served case; a four-armed spider-centaur is not.

### Gaps
- No source quantifies how far from human proportions a learned rigger tolerates before pivots become unusable; the Meshy 5–15 cm hip-pivot error is the only numeric datapoint and it was measured on a normal humanoid.
- Whether Tripo's non-biped `rig_type`s produce usable *weights* (as opposed to plausible skeletons) is not reported by any source I found; the hands-on test only exercised a stylised biped robot.
- **Anything World** — requested in the assignment, and specifically relevant because its pitch was non-human/animal rigging — returned nothing usable in this session's searches. I cannot confirm whether it still operates, what its 2026 API looks like, or its capabilities. Explicit gap.
- No 2026 source covers tail/wing *weight* quality from any auto-rigger.

---

## Cross-cutting: the decision tree, assembled

This is an **inference** — a synthesis of the cited findings above, not a sourced artifact. Presented for the report writer to restructure.

```
1. INSPECT  (Q1)
   blender --background --python inspect_scene.py -- model.*
   + python -I geom_health.py model.*
   + gltf-transform inspect / assimp info

2. BRANCH ON EXISTING RIG
   ├─ Armature present, bone names recognised (mixamorig:/UE5/VRM/Rigify),
   │  weights look OK in the QC action (Q4)
   │     → SKIP RIGGING. Go to 6 (fingers), 7 (face), 8 (retarget).
   ├─ Armature present, weights bad
   │     → keep skeleton, re-solve weights: SkinTokens --use_skeleton
   │       (fallback: Voxel Heat Diffuse Skinning; fallback: voxel-remesh
   │        proxy + heat weights + Data Transfer back)
   ├─ Vertex groups present, no armature ("orphaned weights")
   │     → read group names to identify the donor standard; rebuild a
   │       matching skeleton (Rigify metarig named to match, or Tripo with
   │       spec=that standard), then re-solve or reuse the groups
   └─ No armature
         → go to 3

3. PREP  (Q2)   [always, even if a rig exists, before re-binding]
   repair (3D Print Toolbox / Merge by Distance / Recalc Normals)
   → join parts  → Apply All Transforms  → fix up-axis  → fix scale to metres
   → symmetrise if accidental asymmetry  → pose-to-rest to T or A if needed
     (Pose ▸ Apply Pose as Rest Pose; use kxn4t/pose-to-rest-pose if shape keys)

4. CHOOSE RIGGER  (Q3)
   ├─ Non-human / extra limbs / tail / wings
   │     ├─ have $50 + want a controllable animator-facing rig
   │     │     → Auto-Rig Pro, Rig Definition + Multi-Ped + modular limbs
   │     ├─ want it automatic
   │     │     → SkinTokens (free, local, 14GB VRAM)  or
   │     │       Tripo API with matching rig_type (no bone-name guarantee)
   │     └─ want full control, free
   │           → Rigify from a creature metarig / raw_copy chains
   ├─ Stylised but humanoid biped
   │     → SkinTokens first.  If weights disappoint → Tripo (spec=unreal|mixamo)
   │       → if you need the cleanest weights of the hosted set, AccuRig
   ├─ Realistic humanoid, Unreal target
   │     → Tripo spec=unreal, or AccuRig, or ARP;  face via Mesh to MetaHuman
   ├─ Realistic humanoid, want free + Mixamo animation library
   │     → Mixamo (watch: humanoid-only, symmetric, connected, T-pose,
   │       unverified polycount ceiling, 60MB)
   └─ Want maximum long-term control / IK / animator-friendly
         → Rigify (Human metarig if you need fingers), weights via
           auto-weights then Corrective Smooth, or SkinTokens --use_skeleton

5. QC WEIGHTS  (Q4)
   run the QC action; check candy-wrapper, shoulder/hip volume,
   elbow/knee collapse, rigid-part slump
   fixes in order: pivot placement → Preserve Volume → Corrective Smooth(Bind)
   → tighten weights / rigid-assign armour → twist bones → corrective shape keys
   finally: Weights ▸ Limit Total(4) + Normalize All before export

6. FINGERS  (Q5)   if absent
   Rigify Human hand chain graft  OR  Data Transfer from a donor hand
   OR add bones by hand + SkinTokens --use_skeleton
   name them mixamorig:*Thumb1..3 / thumb_01_l style from the start
   build grip pose assets in the Asset Browser

7. FACE  (Q6)   if no shape keys
   Unreal target + humanoid head → Mesh to MetaHuman (any topology, needs
     tracked neutral pose, FOV ≤ 20°, closed mouth, head-only mesh)
   Engine-agnostic + want ARKit 52 → Faceit
   Stylised / non-human head → OmniFaceRig (verify code release!) or a
     joint-based Rigify modular face
   Free manual → donor head + Surface Deform wrap, transfer each of 52 keys,
     expect weak deltas, hand-fix lips and eyelids

8. RETARGET  (Q7)
   name the final rig to ONE standard (UE5 or Mixamo). add a floor root bone.
   Blender 5.0+ → the free `Retarget` extension (Mixamo/Unreal/VRoid/MMD/Daz
     presets, rest-pose apply with mesh alignment, scale offset, Add Root Bone,
     Transfer Root Motion, any-rig→Rigify)
   paid/robust → ARP Remap (maps orientations as well as names)
   Unreal → IK Rig + IK Retargeter; make the two retarget poses visually match
   pitfalls: A-vs-T rest pose (the big one), bone roll/axis, root+scale
     (= floating feet), non-standard names defeating auto-detect
```

## Cross-cutting: timeline facts worth stating in the report

- Blender 5.0 released 18 Nov 2025; 5.0.1 on 16 Dec 2025; manual now labelled 5.2 LTS — [Wikipedia](https://en.wikipedia.org/wiki/Blender_(software)); [Blender manual](https://docs.blender.org/manual/en/latest/addons/rigify/basics.html)
- `Retarget` extension v5.2.0 published 19 Dec 2025, Blender 5.0+ only — [extensions.blender.org](https://extensions.blender.org/add-ons/retarget/)
- SkinTokens paper = arXiv 2602.04805 (Feb 2026 identifier), v2 HTML available — [arXiv](https://arxiv.org/abs/2602.04805)
- OmniFaceRig = arXiv 2606.08043; TopoRig = arXiv 2609.15746 (June and Sept 2026 identifiers) — [OmniFaceRig](https://arxiv.org/pdf/2606.08043); [TopoRig](https://arxiv.org/pdf/2609.15746)
- Tripo rig models: `v2.5-20260210` (creatures), `v3.0-20260909` (humanoid, recommended, opt-in) — [Tripo docs](https://developers.tripo3d.ai/en/docs/animations-rig)
- Epic's Mesh to MetaHuman docs reflect UE 5.6+ — [Epic](https://dev.epicgames.com/documentation/en-us/metahuman/from-mesh)
- The Strayspark hands-on test used Blender 5.1 → UE 5.7 on a ~30K-tri single-material humanoid — [Strayspark](https://www.strayspark.studio/blog/ai-auto-rigging-showdown-2026-tripo-meshy-cascadeur-mixamo)

## Cross-cutting: source-quality warnings for the report writer

- **Primary/authoritative here:** the UniRig and SkinTokens GitHub READMEs, the arXiv papers, the Tripo API docs, the Auto-Rig Pro docs (lucky3d.fr), the Epic MetaHuman docs, the Blender manual and extensions.blender.org, the Faceit readthedocs.
- **Useful but single-source and self-reported:** the Strayspark 2026 hands-on showdown — it is the only hands-on comparative test found with specific artifacts and numbers, which makes it valuable, but it is one blog, one mesh, one pipeline, and it is unreplicated. Every Meshy/Tripo/AccuRig artifact claim in this document traces to it.
- **Vendor-published "best of 2026" roundups** (Meshy, Tripo, 3D AI Studio, ludo.ai, Cinevva, Guideflow, assethub, tooltrim) were the bulk of search results and routinely contradict primary docs — e.g. Meshy's roundup claims ARP Smart supports quadrupeds, which the ARP docs deny. Prefer primary docs on every conflict.
- **Reseller/aggregator pages** (desirefx, vfxmed, unityunreal, gfxplugin) were the only sources for some version numbers (Voxel Heat 3.5.3, Faceit 2.3.71). Treat those version numbers as weak.
- **Mixamo's own documentation was unreachable** in this session (`help.mixamo.com` did not resolve); all Mixamo limits here are third-party or from 2012.
