# ☁️ VR Cloud Teleport

A VR experience built in **Unreal Engine 5.5** where the player stands on a cloud in the sky and can teleport between floating cloud platforms.

## 🎮 Overview

The player spawns on a cloud high in the sky, surrounded by other cloud platforms and a volumetric cloud atmosphere. Using VR controllers (or keyboard input for desktop testing), the player can teleport to different clouds, experiencing a serene sky-walking environment.

### Key Features

- **Sky Environment** — Full atmospheric sky setup with SkyAtmosphere, Volumetric Clouds, ExponentialHeightFog, and dynamic lighting
- **Cloud Platforms** — Multiple `BPCloud` actors placed at high altitude, each serving as a teleportable surface
- **VR Teleportation** — Arc-based teleport system using the VR template's motion controllers with tag-based validation (only clouds tagged `Teleport` are valid targets)
- **Random Teleport (Desktop Testing)** — Press `T` to randomly teleport to one of the designated cloud platforms
- **Configurable Cloud Targets** — Instance-editable `Clouds` array (type: `BPCloud`) on the VRPawn allows level designers to hand-pick which clouds are teleport destinations
- **Tethered VR Mode** — Designed for tethered desktop VR (Oculus Link, SteamVR, etc.)

## 🏗️ Project Structure

```
Content/
├── Blueprints/
│   ├── VRPawn           — VR pawn with teleport trace, camera, motion controllers
│   ├── BPCloud          — Cloud platform actor (tagged "Teleport")
│   ├── BPPortal         — Portal actor for teleportation
│   └── GM_CloudWorld    — Game Mode with VRPawn as default pawn
├── Levels/
│   └── CloudWorld       — Main sky level
└── ...
```

## 📋 Blueprint Architecture

### VRPawn

- **Components:** VROrigin, Camera (HMD-locked), Left/Right MotionControllers with aim and grip, TeleportTraceNiagaraSystem
- **TeleportTrace Function:** Predictive projectile path → Break Hit Result → Actor Has Tag ("Teleport") → Validate → Set teleport location
- **Random Teleport (T Key):** Gets `Clouds` array → Random Array Item → Get Actor Location → Set Actor Location (with Z offset, Teleport flag enabled)
- **Desktop Testing:** HMD detection at Begin Play to unlock camera from HMD tracking when no headset is connected

### BPCloud

- Static mesh with collision for the platform surface
- Tagged with `Teleport` for teleport validation
- Placed at high Z altitude (~50,000 units) in the level

### Variables (VRPawn)

| Variable | Type | Description |
|---|---|---|
| `Clouds` | BPCloud Array (Instance Editable) | Manually assigned cloud targets for teleportation |
| `bValidTeleportLocation` | Boolean | Whether current trace hit is a valid teleport target |
| `bTeleportTraceActive` | Boolean | Whether the teleport trace is currently active |
| `ProjectedTeleportLocation` | Vector | The validated teleport destination |
| `TeleportTracePathPositions` | Vector Array | Path points for the Niagara arc visual |

## 🚀 How to Run

### VR Mode (Tethered Headset)
1. Connect your VR headset (Quest via Link, Valve Index, Vive, etc.)
2. Open the project in Unreal Engine 5.5
3. Open the `CloudWorld` level
4. Click **Play → VR Preview**
5. Use the motion controller thumbstick to aim the teleport arc at a cloud and release to teleport

### Desktop Testing (No Headset)
1. Open the `CloudWorld` level
2. Click **Play**
3. Press **T** to randomly teleport between cloud platforms
4. Verify teleportation by observing coordinate changes (Print String debug output)

## 🛠️ Tech Stack

- **Engine:** Unreal Engine 5.5
- **Language:** Blueprints (visual scripting)
- **VR Runtime:** OpenXR
- **Platforms:** Desktop VR (tethered)

## 📝 Notes

- The project is designed to run in tethered VR mode and does not need to be exported/packaged
- Desktop testing uses the `T` key for random teleportation as a substitute for VR controller input
- All teleport logic uses the `Teleport` flag on `Set Actor Location` to prevent physics collision issues during movement
- Cloud platforms are manually assigned via the instance-editable `Clouds` array on the VRPawn, giving level designers full control over teleport destinations
