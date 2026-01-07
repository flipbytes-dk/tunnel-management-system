# SLAM Infrastructure Specification
## Tunnel Management System - AR Positioning Infrastructure

**Document Version:** 1.0
**Date:** December 2024
**Author:** Technical Consultant
**Status:** Draft - Pending VaaaN Approval

**Related Documents:**
- Project Plan: Task N (SLAM Integration & Positioning Infrastructure)
- Procurement Plan: PROC2.4a-d (SLAM & Positioning Infrastructure)
- Agreement Section 2.2: "SLAM algorithms needed for accurate positioning"

---

## 1. Executive Summary

### 1.1 Problem Statement

Tunnel environments present a worst-case scenario for visual SLAM (Simultaneous Localization and Mapping):

| Challenge | Impact on HoloLens |
|-----------|-------------------|
| Uniform wall surfaces | No distinctive features for tracking |
| Repetitive geometry | Loop closure failures |
| Long corridors | Cumulative drift over distance |
| Variable lighting | Inconsistent feature detection |
| Reflective surfaces | False feature matches |

**Result:** HoloLens 2 built-in tracking alone will experience positional drift of 1-5 meters over 100m of tunnel traversal, making AR overlays unusable for equipment identification.

### 1.2 Solution Overview

A **hybrid positioning infrastructure** combining:

1. **UWB (Ultra-Wideband) Beacons** - Primary positioning (radio-based, not affected by visual conditions)
2. **Visual Markers (ArUco)** - Secondary positioning + anchor points for HoloLens spatial anchors
3. **Pre-built 3D Map** - Reference map from BIM/LiDAR for map-based localization
4. **Sensor Fusion Algorithm** - Kalman filter combining all sources

**Target Accuracy:** < 30cm in 95% of tunnel coverage area

---

## 2. System Architecture

### 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           AR DEVICE (HoloLens 2)                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌───────────────┐  │
│  │ UWB Tag     │  │ Camera      │  │ IMU         │  │ Spatial       │  │
│  │ Receiver    │  │ (ArUco Det) │  │ (Accel/Gyro)│  │ Mapping       │  │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └───────┬───────┘  │
│         │                │                │                  │          │
│         └────────────────┴────────────────┴──────────────────┘          │
│                                   │                                      │
│                    ┌──────────────▼──────────────┐                      │
│                    │    SENSOR FUSION ENGINE     │                      │
│                    │    (Extended Kalman Filter) │                      │
│                    └──────────────┬──────────────┘                      │
│                                   │                                      │
│                    ┌──────────────▼──────────────┐                      │
│                    │   FUSED POSITION OUTPUT     │                      │
│                    │   (X, Y, Z, Orientation)    │                      │
│                    └──────────────┬──────────────┘                      │
│                                   │                                      │
│                    ┌──────────────▼──────────────┐                      │
│                    │   AR OVERLAY RENDERING      │                      │
│                    │   (Equipment labels, data)  │                      │
│                    └─────────────────────────────┘                      │
└─────────────────────────────────────────────────────────────────────────┘
                                    ▲
                                    │ UWB Radio
                                    │
┌───────────────────────────────────┴─────────────────────────────────────┐
│                         TUNNEL INFRASTRUCTURE                            │
│                                                                          │
│  [UWB]         [ArUco]        [UWB]         [ArUco]        [UWB]        │
│    ●─────────────█─────────────●─────────────█─────────────●            │
│    │             │             │             │             │   Ceiling  │
│    │   ┌─────┐   │   ┌─────┐   │   ┌─────┐   │   ┌─────┐   │            │
│    │   │#001 │   │   │#002 │   │   │#003 │   │   │#004 │   │   Wall     │
│    │   └─────┘   │   └─────┘   │   └─────┘   │   └─────┘   │            │
│ ───┴─────────────┴─────────────┴─────────────┴─────────────┴─── Floor   │
│   0m           25m           50m           75m          100m            │
│                                                                          │
│  Legend: ● = UWB Anchor    █ = ArUco Marker    #xxx = Equipment         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Data Flow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           POSITIONING DATA FLOW                          │
└─────────────────────────────────────────────────────────────────────────┘

UWB Anchors ──► UWB Tag ──► Position (X,Y,Z) ──┐
     (10Hz)                    ±20cm           │
                                               │
ArUco Markers ──► Camera ──► Marker ID + Pose ─┼──► Kalman ──► Fused
     (30Hz)                    ±5cm (close)    │    Filter    Position
                                               │              ±15cm
IMU ──────────────────────► Acceleration ──────┤
     (200Hz)                  Rotation         │
                                               │
HoloLens Spatial ─────────► Relative Pose ─────┘
     (60Hz)                   (drift-prone)
```

---

## 3. UWB Infrastructure Specification

### 3.1 UWB Technology Overview

**Ultra-Wideband (UWB)** uses time-of-flight radio signals to measure distance with centimeter-level accuracy, unaffected by visual conditions.

| Parameter | Specification |
|-----------|---------------|
| Frequency | 6.5 GHz (Channel 5) or 8 GHz (Channel 9) |
| Bandwidth | 500 MHz |
| Range | Up to 100m (line-of-sight) |
| Accuracy | 10-30 cm typical |
| Update Rate | 10-100 Hz |
| Power | 3.3V, ~100mA per anchor |

### 3.2 Recommended UWB System

**Primary Recommendation:** Decawave/Qorvo DWM3000 Module

| Component | Model | Qty (per 1km tunnel) | Unit Cost (INR) |
|-----------|-------|---------------------|-----------------|
| UWB Anchor | DWM3001C | 25 | 15,000 |
| UWB Tag | DWM3001CDK | 4 | 25,000 |
| Gateway | Raspberry Pi 4 + DWM3001C | 1 | 15,000 |
| PoE Injector | 802.3af | 25 | 1,500 |

**Alternative Systems:**
- Pozyx Enterprise (easier setup, higher cost)
- Sewio RTLS (enterprise features, complex)

### 3.3 UWB Anchor Placement Strategy

#### 3.3.1 Placement Rules

| Rule | Specification | Rationale |
|------|---------------|-----------|
| Spacing | 40m maximum | Ensure 3+ anchors visible at any point |
| Height | 3-4m (ceiling or high wall) | Avoid obstruction, improve geometry |
| Geometry | Staggered left/right walls | Better triangulation |
| Line-of-sight | Clear path to walkway | No obstructions |

#### 3.3.2 Placement Diagram (Cross-Section)

```
                    TUNNEL CROSS-SECTION (Looking along tunnel)

                              ┌───────────────────┐
                             /                     \
                            /    [UWB Anchor]       \
                           /          ●              \
                          │      (ceiling mount)      │
                          │                           │
          [ArUco]  █──────│                           │──────█  [ArUco]
          (wall)          │                           │          (wall)
                          │                           │
                          │     ┌─────────────┐       │
                          │     │  Walkway    │       │
                          │     │   Area      │       │
                          └─────┴─────────────┴───────┘

          ◄───────────── 10-12m tunnel width ─────────────►
```

#### 3.3.3 Placement Diagram (Plan View)

```
                    TUNNEL PLAN VIEW (1km example)

    0m        100m       200m       300m       400m       500m
    │          │          │          │          │          │
    ●──────────●──────────●──────────●──────────●──────────●  Left Wall
    │          │          │          │          │          │
    █    █    █    █    █    █    █    █    █    █    █    █  ArUco (25m)
    │          │          │          │          │          │
    ══════════════════════════════════════════════════════════ Carriageway
    │          │          │          │          │          │
    █    █    █    █    █    █    █    █    █    █    █    █  ArUco (25m)
    │          │          │          │          │          │
    ●──────────●──────────●──────────●──────────●──────────●  Right Wall
    │          │          │          │          │          │

    Legend: ● = UWB Anchor (40m spacing, staggered)
            █ = ArUco Marker (25m spacing)

    Total for 1km: ~25 UWB Anchors, ~40 ArUco Markers
```

### 3.4 UWB Network Topology

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         UWB NETWORK TOPOLOGY                             │
└─────────────────────────────────────────────────────────────────────────┘

                    ┌─────────────────────────────┐
                    │      UWB Gateway            │
                    │   (Raspberry Pi + DWM3001)  │
                    │      IP: 192.168.x.x        │
                    └──────────────┬──────────────┘
                                   │ Ethernet (to TMS network)
                                   │
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
   ┌────▼────┐               ┌─────▼─────┐              ┌─────▼─────┐
   │ Anchor 1│◄─── UWB ────► │ Anchor 2  │◄─── UWB ───►│ Anchor 3  │
   │ ID: 0x01│    Mesh       │ ID: 0x02  │    Mesh     │ ID: 0x03  │
   └─────────┘               └───────────┘              └───────────┘
        ▲                          ▲                          ▲
        │                          │                          │
        └──────────── UWB Ranging ─┴───── UWB Ranging ────────┘
                                   │
                            ┌──────▼──────┐
                            │  UWB Tag    │
                            │ (on HoloLens)│
                            │  ID: 0xF1   │
                            └─────────────┘
```

### 3.5 UWB Installation Requirements

| Requirement | Specification |
|-------------|---------------|
| Power | PoE (802.3af) or 5V DC |
| Cabling | Cat6 Ethernet to each anchor |
| Mounting | Stainless steel bracket, M8 bolts |
| Enclosure | IP65 minimum (dust/water) |
| Temperature | -10°C to +50°C operating |
| Commissioning | Survey each anchor position to ±5cm |

---

## 4. Visual Marker Specification

### 4.1 Marker Type Selection

**Selected:** ArUco markers (OpenCV library)

| Criterion | ArUco | AprilTag | Vuforia |
|-----------|-------|----------|---------|
| Cost | Free | Free | License |
| HoloLens Support | Native (MRTK) | Custom | Native |
| Detection Speed | Fast | Medium | Fast |
| Robustness | Good | Excellent | Good |
| Documentation | Excellent | Good | Good |
| **Selection** | **Yes** | Backup | No |

### 4.2 Marker Specifications

| Parameter | Specification |
|-----------|---------------|
| Dictionary | DICT_6X6_250 |
| Size | 200mm x 200mm (A3 paper) |
| Border | 25mm white border |
| Material | PVC with retroreflective backing |
| Mounting | Adhesive + mechanical bracket |
| Height | 1.5m from floor (eye level) |

### 4.3 Marker ID Assignment

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      MARKER ID ASSIGNMENT SCHEME                         │
└─────────────────────────────────────────────────────────────────────────┘

ID Range        Purpose                      Example
─────────────────────────────────────────────────────────────────────────
0-99           Chainage markers             ID 25 = Chainage 250m
100-149        Emergency exits              ID 101 = Exit 1
150-199        Cross passages               ID 151 = Cross Passage 1
200-219        Jet fans                     ID 201 = Jet Fan 1
220-239        Fire hydrants                ID 221 = Hydrant 1
240-249        Electrical panels            ID 241 = Panel 1
250-299        Reserved (future)            —
```

### 4.4 Marker Placement Strategy

| Location Type | Spacing | Height | Orientation |
|---------------|---------|--------|-------------|
| Wall (general) | 25m | 1.5m | Perpendicular to wall |
| Equipment | At each item | 1.5m or equipment height | Facing walkway |
| Emergency exits | At door | 1.8m | Above door frame |
| Cross passages | Both sides | 1.5m | Facing both directions |
| Chainage points | Every 10m | 1.5m | Alternating walls |

### 4.5 Marker Design Template

```
┌─────────────────────────────────────────┐
│                                         │
│    ┌─────────────────────────────┐      │
│    │                             │      │
│    │    ██████    ██████         │      │
│    │    ██  ██        ██         │      │  200mm
│    │    ██████    ██████         │      │
│    │        ██    ██  ██         │      │
│    │    ██████    ██████         │      │
│    │                             │      │
│    │       ArUco ID: 025         │      │
│    └─────────────────────────────┘      │
│                                         │
│    CHAINAGE: 250m                       │
│    TUNNEL: NORTH BORE                   │
│                                         │
└─────────────────────────────────────────┘
        ◄────── 200mm ──────►

Note: Human-readable text below marker for
      visual identification without AR device
```

### 4.6 Retroreflective Enhancement

For low-light tunnel conditions, markers should use retroreflective material:

| Layer | Material | Purpose |
|-------|----------|---------|
| Base | 3M Diamond Grade reflective | Visibility in low light |
| Print | UV-stable black ink | ArUco pattern |
| Laminate | Clear anti-glare | Protection |
| Mounting | Aluminum bracket | Durability |

---

## 5. Pre-built Map Specification

### 5.1 Map Sources

| Source | Priority | Accuracy | Format |
|--------|----------|----------|--------|
| **LiDAR Scan (VaaaN owned)** | **1** | **±1-2cm** | **LAS, E57** |
| BIM Model (VaaaN provided) | 2 | ±5cm | IFC, RVT |
| Navisworks Model | 3 | ±5cm | NWC, NWD |

**Note:** With VaaaN owning LiDAR equipment (PROC2.4c), the site team can perform high-accuracy tunnel surveys on-demand. LiDAR data becomes the primary source for both SLAM reference maps and virtual walkthrough visualization.

### 5.1.1 LiDAR vs 360° Camera Decision

If the selected LiDAR scanner includes an HDR camera (e.g., Leica RTC360, Leica BLK2GO):
- **Colored point cloud** can serve as "street view" walkthrough base
- **PROC2.3 (360° cameras) becomes optional** - potential savings of INR 4,00,000
- **Single capture process** for both SLAM and visualization

### 5.2 Map Processing Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      MAP PROCESSING PIPELINE                             │
└─────────────────────────────────────────────────────────────────────────┘

BIM/LiDAR ──► Extract ──► Simplify ──► Add ──► Export ──► Deploy
  Input       Geometry    Mesh       Anchors   Format    to Device

  │            │           │          │         │          │
  ▼            ▼           ▼          ▼         ▼          ▼

IFC/LAS    Walls,       <500K      UWB IDs,   GLB/       HoloLens
           Floor,       triangles  ArUco IDs  OBJ        Spatial
           Equipment                          + JSON     Anchors
```

### 5.3 Map Coordinate System

| Axis | Direction | Origin |
|------|-----------|--------|
| X | Along tunnel (chainage direction) | Tunnel portal (Ch. 0+000) |
| Y | Across tunnel (left-right) | Tunnel centerline |
| Z | Vertical (up-down) | Road surface level |

**Coordinate System:** Local Cartesian (meters), aligned with tunnel chainage

### 5.4 Map-to-World Alignment

The pre-built map must be aligned to the real-world tunnel using:

1. **Survey Control Points** - Minimum 3 known coordinates (from tunnel survey)
2. **UWB Anchor Positions** - Each anchor position surveyed and stored
3. **ArUco Marker Positions** - Each marker position stored in map database

---

## 6. Sensor Fusion Algorithm

### 6.1 Extended Kalman Filter Design

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    EXTENDED KALMAN FILTER STATE                          │
└─────────────────────────────────────────────────────────────────────────┘

State Vector (13 elements):
┌                                       ┐
│  x       Position X (along tunnel)    │
│  y       Position Y (across tunnel)   │
│  z       Position Z (vertical)        │
│  vx      Velocity X                   │
│  vy      Velocity Y                   │
│  vz      Velocity Z                   │
│  qw      Orientation quaternion W     │
│  qx      Orientation quaternion X     │
│  qy      Orientation quaternion Y     │
│  qz      Orientation quaternion Z     │
│  bax     Accelerometer bias X         │
│  bay     Accelerometer bias Y         │
│  baz     Accelerometer bias Z         │
└                                       ┘
```

### 6.2 Measurement Models

| Sensor | Measurement | Noise (σ) | Update Rate |
|--------|-------------|-----------|-------------|
| UWB | Position (X, Y, Z) | 0.20m | 10 Hz |
| ArUco (close <2m) | Position + Orientation | 0.05m, 2° | 30 Hz |
| ArUco (far >2m) | Position + Orientation | 0.15m, 5° | 30 Hz |
| IMU Accel | Acceleration | 0.1 m/s² | 200 Hz |
| IMU Gyro | Angular velocity | 0.01 rad/s | 200 Hz |
| HoloLens Spatial | Relative pose | Drift-dependent | 60 Hz |

### 6.3 Fusion Strategy

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         FUSION PRIORITY                                  │
└─────────────────────────────────────────────────────────────────────────┘

Priority 1: UWB + IMU
    └── Primary positioning, always available
    └── Accuracy: ±20cm

Priority 2: ArUco Detection (when visible)
    └── Refines position when marker in view
    └── Accuracy: ±5cm (close range)
    └── Also sets HoloLens spatial anchor

Priority 3: HoloLens Spatial Tracking
    └── Fills gaps between UWB updates
    └── Provides smooth motion
    └── Reset when drift detected

Priority 4: Map-based Localization
    └── Sanity check against pre-built map
    └── Detects impossible positions
```

### 6.4 Drift Detection & Correction

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    DRIFT DETECTION ALGORITHM                             │
└─────────────────────────────────────────────────────────────────────────┘

Every 1 second:

    uwb_position = GetUWBPosition()
    hololens_position = GetHoloLensPosition()

    drift = Distance(uwb_position, hololens_position)

    IF drift > 0.5m THEN
        // Significant drift detected
        ResetHoloLensSpatialAnchor(uwb_position)
        LogDriftEvent(drift, timestamp, location)
    END IF

    IF drift > 2.0m THEN
        // Critical drift - UWB only mode
        SetPositionSource(UWB_ONLY)
        AlertOperator("AR positioning degraded")
    END IF
```

---

## 7. Software Integration

### 7.1 UE5/HoloLens Integration Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    SOFTWARE ARCHITECTURE                                 │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│                         UNREAL ENGINE 5                                  │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │                    TMS AR Application                            │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│         │                    │                    │                     │
│  ┌──────▼──────┐     ┌───────▼───────┐    ┌──────▼──────┐             │
│  │ AR Overlay  │     │ Equipment     │    │ Navigation  │             │
│  │ Manager     │     │ Data Manager  │    │ System      │             │
│  └──────┬──────┘     └───────────────┘    └─────────────┘             │
│         │                                                              │
│  ┌──────▼──────────────────────────────────────────────────────────┐  │
│  │                  POSITIONING SUBSYSTEM                           │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │  │
│  │  │ UWB Module  │  │ArUco Module │  │ Sensor Fusion (EKF)     │  │  │
│  │  │ (DWM3000)   │  │ (OpenCV)    │  │ (Custom C++)            │  │  │
│  │  └──────┬──────┘  └──────┬──────┘  └────────────┬────────────┘  │  │
│  └─────────┼────────────────┼──────────────────────┼───────────────┘  │
│            │                │                      │                   │
└────────────┼────────────────┼──────────────────────┼───────────────────┘
             │                │                      │
      ┌──────▼──────┐  ┌──────▼──────┐        ┌──────▼──────┐
      │ UWB Tag     │  │ HoloLens    │        │ IMU         │
      │ (Bluetooth) │  │ Camera      │        │ (Built-in)  │
      └─────────────┘  └─────────────┘        └─────────────┘
```

### 7.2 API Specification

#### 7.2.1 Position Output

```cpp
// Position data structure
struct FTunnelPosition
{
    // Position in tunnel coordinates (meters)
    float X;        // Along tunnel (chainage)
    float Y;        // Across tunnel
    float Z;        // Vertical

    // Orientation (quaternion)
    FQuat Orientation;

    // Metadata
    float Accuracy;         // Estimated accuracy (meters)
    int32 PrimarySource;    // 0=UWB, 1=ArUco, 2=HoloLens
    float Timestamp;        // Unix timestamp

    // Nearest reference
    int32 NearestMarkerID;
    float DistanceToMarker;
    float Chainage;         // Interpolated chainage
};
```

#### 7.2.2 Event Callbacks

```cpp
// Positioning events
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnPositionUpdated, FTunnelPosition, Position);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FOnMarkerDetected, int32, MarkerID, FTransform, MarkerPose);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnDriftDetected, float, DriftAmount);
DECLARE_DYNAMIC_MULTICAST_DELEGATE(FOnPositioningDegraded);
```

---

## 8. Installation Procedure

### 8.1 Phase 1: Survey & Planning (Week 22-24)

| Step | Activity | Owner | Deliverable |
|------|----------|-------|-------------|
| 1.1 | Obtain tunnel survey data | VaaaN | Survey coordinates |
| 1.2 | Plan anchor positions | Consultant | Anchor placement map |
| 1.3 | Plan marker positions | Consultant | Marker placement map |
| 1.4 | Coordinate access schedule | VaaaN | Installation windows |
| 1.5 | Procure hardware | VaaaN | PROC2.4a-d items |

### 8.2 Phase 2: Infrastructure Installation (Week 26-28)

| Step | Activity | Owner | Deliverable |
|------|----------|-------|-------------|
| 2.1 | Install UWB anchor mounts | VaaaN Facilities | Mounts in place |
| 2.2 | Run PoE cabling | VaaaN Facilities | Cabling complete |
| 2.3 | Install UWB anchors | Dev 1 | Anchors powered |
| 2.4 | Survey anchor positions | Consultant | Anchor coordinates |
| 2.5 | Install ArUco markers | Dev 1 | Markers in place |
| 2.6 | Survey marker positions | Dev 2 | Marker coordinates |
| 2.7 | Configure UWB network | Dev 1 | Network operational |

### 8.3 Phase 3: Calibration & Validation (Week 28-30)

| Step | Activity | Owner | Deliverable |
|------|----------|-------|-------------|
| 3.1 | Static accuracy test | All | Accuracy report |
| 3.2 | Dynamic tracking test | All | Tracking report |
| 3.3 | Drift analysis | Consultant | Drift characterization |
| 3.4 | Calibration refinement | Consultant | Final parameters |
| 3.5 | Acceptance test | All | Sign-off |

---

## 9. Validation & Acceptance Criteria

### 9.1 Accuracy Requirements

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| Static position accuracy | < 20cm | Compare to surveyed points |
| Dynamic position accuracy | < 30cm | Walking test with reference |
| Orientation accuracy | < 5° | Marker alignment test |
| Update latency | < 100ms | Timestamp analysis |
| Coverage | > 95% | Walking full tunnel |

### 9.2 Test Procedure

#### 9.2.1 Static Accuracy Test

```
1. Mark 20 test points at known survey coordinates
2. At each point:
   a. Stand stationary for 30 seconds
   b. Record average reported position
   c. Calculate error vs. known position
3. Acceptance: 95% of points < 20cm error
```

#### 9.2.2 Dynamic Accuracy Test

```
1. Walk tunnel at normal pace (5 km/h)
2. Pass each ArUco marker
3. At each marker, system reports position
4. Compare reported chainage to marker chainage
5. Acceptance: 95% of readings < 30cm error
```

#### 9.2.3 Coverage Test

```
1. Walk entire tunnel length
2. Log position output every 1 second
3. Flag any "position unavailable" events
4. Acceptance: < 5% of readings unavailable
```

### 9.3 Acceptance Sign-off

| Criterion | Target | Actual | Status |
|-----------|--------|--------|--------|
| Static accuracy | < 20cm | ___ cm | [ ] Pass [ ] Fail |
| Dynamic accuracy | < 30cm | ___ cm | [ ] Pass [ ] Fail |
| Orientation accuracy | < 5° | ___ ° | [ ] Pass [ ] Fail |
| Coverage | > 95% | ___ % | [ ] Pass [ ] Fail |
| Update latency | < 100ms | ___ ms | [ ] Pass [ ] Fail |

**Sign-off:**

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Consultant | | | |
| VaaaN Technical Lead | | | |
| VaaaN Project Manager | | | |

---

## 10. Maintenance & Operations

### 10.1 Routine Maintenance

| Activity | Frequency | Owner |
|----------|-----------|-------|
| Visual marker inspection | Monthly | VaaaN Operations |
| UWB anchor status check | Weekly (automated) | System |
| Accuracy spot check | Quarterly | VaaaN Operations |
| Marker replacement (damaged) | As needed | VaaaN Operations |

### 10.2 Troubleshooting Guide

| Symptom | Possible Cause | Resolution |
|---------|----------------|------------|
| Position jumps | UWB anchor offline | Check anchor status, power |
| Consistent offset | Anchor position incorrect | Re-survey anchor |
| Drift in one area | Insufficient anchors | Add anchor or markers |
| No position | UWB tag battery | Replace/charge tag battery |
| ArUco not detected | Marker damaged/dirty | Clean or replace marker |

### 10.3 Spare Parts Inventory

| Item | Recommended Qty | Reorder Level |
|------|-----------------|---------------|
| UWB Anchor | 3 | 1 |
| UWB Tag | 2 | 1 |
| ArUco Marker (printed) | 20 | 10 |
| PoE Injector | 2 | 1 |
| Mounting brackets | 5 | 2 |

---

## 11. Appendices

### Appendix A: Bill of Materials (1km Tunnel)

#### A.1 UWB Infrastructure (PROC2.4a)
| Item | Qty | Unit Cost (INR) | Total (INR) |
|------|-----|-----------------|-------------|
| UWB Anchor (DWM3001C) | 25 | 15,000 | 3,75,000 |
| UWB Tag (DWM3001CDK) | 4 | 25,000 | 1,00,000 |
| UWB Gateway | 1 | 15,000 | 15,000 |
| PoE Injector | 25 | 1,500 | 37,500 |
| Cat6 Cable (per 100m) | 10 | 5,000 | 50,000 |
| Anchor Mounting Bracket | 25 | 1,000 | 25,000 |
| **UWB Subtotal** | | | **5,02,500** |

#### A.2 Visual Markers (PROC2.4b)
| Item | Qty | Unit Cost (INR) | Total (INR) |
|------|-----|-----------------|-------------|
| ArUco Marker (retroreflective) | 50 | 2,000 | 1,00,000 |
| Marker Mounting Frame | 50 | 500 | 25,000 |
| **Marker Subtotal** | | | **1,25,000** |

#### A.3 LiDAR Mapping System (PROC2.4c) - PURCHASE
| Item | Qty | Unit Cost (INR) | Total (INR) |
|------|-----|-----------------|-------------|
| LiDAR Scanner (Leica BLK2GO) | 1 | 25,00,000 | 25,00,000 |
| Survey Spheres (6") | 10 | 15,000 | 1,50,000 |
| Checkerboard Targets | 20 | 5,000 | 1,00,000 |
| Registration Software (ReCap Pro) | 1 | 50,000/yr | 50,000 |
| **LiDAR Subtotal** | | | **28,00,000** |

#### A.4 Calibration Equipment (PROC2.4d)
| Item | Qty | Unit Cost (INR) | Total (INR) |
|------|-----|-----------------|-------------|
| Laser Distance Meter | 1 | 15,000 | 15,000 |
| Total Station (rental for calibration) | 2 days | 12,500 | 25,000 |
| Reference Point Markers | 20 | 500 | 10,000 |
| **Calibration Subtotal** | | | **50,000** |

#### A.5 Total SLAM Infrastructure
| Category | Total (INR) |
|----------|-------------|
| UWB Infrastructure | 5,02,500 |
| Visual Markers | 1,25,000 |
| LiDAR Mapping System | 28,00,000 |
| Calibration Equipment | 50,000 |
| **GRAND TOTAL** | **34,77,500** |

**Note:** LiDAR system is a capital asset owned by VaaaN, reusable for future tunnel projects.

### Appendix B: Coordinate System Definition

```
TUNNEL COORDINATE SYSTEM

Origin: Tunnel South Portal, Road Centerline, Road Surface

        Z (Up)
        │
        │    Y (Right)
        │   /
        │  /
        │ /
        └─────────── X (Along tunnel, increasing chainage)

X: Chainage direction (0 at portal, increasing into tunnel)
Y: Perpendicular to tunnel axis (+ = right when facing increasing X)
Z: Vertical (+ = up)

Units: Meters
Datum: Local (aligned to tunnel survey)
```

### Appendix C: ArUco Marker Dictionary

Using OpenCV ArUco DICT_6X6_250:
- 250 unique markers available
- 6x6 internal grid
- Robust to partial occlusion
- Detection range: 0.5m to 5m (200mm marker)

### Appendix D: References

1. Decawave DWM3000 Datasheet
2. OpenCV ArUco Documentation
3. Microsoft HoloLens 2 Spatial Anchors Guide
4. "UWB Positioning in GPS-Denied Environments" - IEEE
5. "Visual-Inertial SLAM for AR Applications" - ACM SIGGRAPH

---

**Document Control:**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Dec 2024 | Consultant | Initial draft |

---

*End of Document*
