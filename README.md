# Game Animation Sample with Overlay Layering | Unreal Engine 5

## Introduction

This project integrates the ALS Overlay Layering System into the new Unreal Engine Motion Matching Game Animation Sample.
Advanced Locomotion System (ALS) provides a nice Overlay System that allows us to alter the entire locomotion animation just by applying simple overlay poses.

## Features

- Game Animation Sample
- Overlay layering system built with separate Anim Graphs and Linked Layers
- All overlays from ALS
- Basic weapon attach system from ALS
- Basic overlay switcher widget from ALS
- Removed Echo and Twinblast characters and Manny/Quinn 4k textures to lower project size

## Overview

An overview of the system is available on my YouTube channel [Polygon Hive](https://www.youtube.com/watch?v=RDWNfIqvWBk&list=PLs9e0eJQMI2aaulgKJzC8feN1UEwDkEnq)

## Using the plugins in another project (UE 5.8)

This repo ships two content-only plugins:

| Plugin | What it is | Depends on |
|---|---|---|
| `PlayerTemplate` | Player systems: inventory/hotbar/equip, guns, sword, bow, throwables, FP/TP cameras, flight, telekinesis, teleport, cover, interaction, doors/keys, checkpoints, enemy AI | nothing (works on its own) |
| `GASPALS` | Motion-matching locomotion (Game Animation Sample + ALS overlays) and `BP_GASPCharacter`, which runs PlayerTemplate's systems on GASP movement | `PlayerTemplate` |

**PlayerTemplate's master copy lives in its own project, `D:\PlayerTemplate` (repo `nooburger81/PlayerTemplate`).** The copy in this repo's `Plugins/PlayerTemplate` is a consumer snapshot. Make PlayerTemplate changes in the master project, then copy the folder here. Fixes made here won't flow back on their own.

Dependencies only go one way: GASPALS → PlayerTemplate. Never reference `/GASPALS/` or `/Game/` content from inside PlayerTemplate. If PlayerTemplate needs something that GASPALS supplies, add an empty variable for it and have the GASPALS side fill it in, the way `SourceRetargeter` works (see below).

### PlayerTemplate only

1. Copy `Plugins/PlayerTemplate` into your project's `Plugins` folder.
2. Merge `Plugins/PlayerTemplate/Config/DefaultEngine.ini` into your project's `Config/DefaultEngine.ini`. It adds the Grass/Metal/Wood physical surfaces used for footsteps and a 1 cm near clip plane for first person. **If your project already defines physical surfaces, Grass/Metal/Wood must still be SurfaceType1/2/3.**
3. Set the game mode to `/PlayerTemplate/Core/GMB_TemplateTest`, either in Project Settings > Maps & Modes or in a level's World Settings.
4. Optional: open `/PlayerTemplate/Maps/GameGym` as a test level.

### PlayerTemplate + GASPALS

1. Copy both `Plugins/PlayerTemplate` and `Plugins/GASPALS`.
2. Merge both plugins' `Config/DefaultEngine.ini` files into your project's `Config/DefaultEngine.ini`. GASPALS adds the renderer settings it needs, its debug CVars, and the **`Traversable` trace channel, which must be `ECC_GameTraceChannel1`**. If your project already uses GameTraceChannel1 for something else, the traversal traces will break.
3. Copy `Plugins/GASPALS/Config/Tags/GameplayTags_GASPALS.ini` into your project's `Config/Tags/` folder. Unreal does **not** load tags from the plugin folder. Without this file, Foley footstep/jump/land sounds silently break with "Invalid GameplayTag Foley.Event.*" warnings.
4. Set the game mode to `/GASPALS/Player/GM_GASPPlayer`. Its pawn is `/GASPALS/Player/BP_GASPCharacter`.
5. If a level spawns the wrong character, check that level's World Settings for a GameMode override (the stock GASP levels use `GM_Sandbox`).

### Packaging

Plugin maps aren't cooked unless something references them. Add every map you ship (for example `GameGym`) to Project Settings > Packaging > *List of maps to include*.

### How the two plugins hook together

- `CBP_SandboxCharacter` (GASPALS) is a child of `BP_MasterCharacter` (PlayerTemplate). Per-frame player logic for the GASP character lives in `BP_GASPCharacter`'s Tick, because `CBP_SandboxCharacter` doesn't call the parent Tick.
- `ABP_MasterCharacter` retargets the hidden UEFN driver mesh onto the visible Soldier mesh only when the character supplies an IK Retargeter. `BP_MasterCharacter.SourceRetargeter` is empty by default, and `BP_GASPCharacter` sets it to `RTG_UEFN_to_UE5_Mannequin`. `ABP_MasterCharacter.DetectSourceMeshPose` copies it into the Retarget Pose From Mesh node at init.
- The bow's mesh, skeleton and AnimBP live in `PlayerTemplate/Core/Equipment/Weapons/Assets/Bow`. GASP's `DA_Overlay_Bow` points at them there.

### Known leftovers (harmless)

- Some imported animations and the Soldier skeleton still name `/Game/...SKM_Manny_Simple` as their editor preview mesh. The SkyTown candle/daisy/rope materials carry stale texture-streaming names under `/Game/...`. Neither is loaded or cooked.
- The unused `PostProcess/Holograms` materials use textures from editor-only engine plugins (SpeedTreeImporter, MeshModelingToolsetExp).

## Contributing

Contributions are welcome! I hope that, with the help of the community, we can turn this into a next-gen fully featured locomotion system.

Please follow these steps to contribute:

1. Fork the repository. Click the Fork button on the project’s GitHub page.
2. Clone your fork (`git clone https://github.com/your-username/repo-name.git`).
3. Create a new branch (`git checkout -b feature/your-feature`)
4. Make your changes & commit (`git add . -> git commit -m 'Add some feature'`).
5. Push to your fork (`git push origin feature/your-feature`).
6. Open a pull request. Go to the original repo and click New Pull Request.

Please ensure your code follows the project's coding and naming standards.

## License

[UE-Only Content - Licensed for Use Only with Unreal Engine-based Products](https://www.unrealengine.com/en-US/eula/content)

## Discord

For questions, suggestions, or feedback, you can join our Discord server:

- [Polygon Hive Discord](https://discord.gg/8KK5WnDp3D)

## Support My Work

- [Buy Me A Coffee](https://buymeacoffee.com/PolygonHive)

---

Thanks for checking out the project! I hope it helps you create amazing games.
