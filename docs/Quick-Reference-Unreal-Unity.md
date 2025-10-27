# Quick Reference: Unreal/Unity Development for Tunnel Management System

## 📋 Document Overview

This is a quick reference extracted from the comprehensive specification. For full details, see `MMI-UI-Unreal-Unity-Specifications.md`.

---

## 🎮 Platform Recommendation

### **Primary System: Unreal Engine 5**
**Use for**: Main control room displays, high-fidelity 3D visualization, VR training
- ✅ Nanite (automatic LOD)
- ✅ Lumen (real-time GI for lighting simulation)
- ✅ Niagara (advanced particles for smoke, fire, airflow)
- ✅ Blueprint + C++ (rapid prototyping + performance)
- ✅ Native VR support

### **Secondary System: Unity**
**Use for**: AR field operator tools, mobile/tablet interfaces, cross-platform
- ✅ AR Foundation (excellent AR support)
- ✅ Universal Render Pipeline (optimized multi-platform)
- ✅ XR Interaction Toolkit
- ✅ C# (accessible, rapid development)
- ✅ WebGL export (remote browser access)

### **Hybrid Architecture**
Both systems communicate with same backend via REST API/WebSocket

---

## 🎯 Core Requirements Summary

### 12 Minimum UI Screens (PMCS_FR1)
1. **Overview Screen** - Full 3D tunnel + status dashboard
2. **Schematic Map** - Technical drawing style with all systems
3. **Lighting** - Zone overlays + individual luminaire control
4. **Electrical Distribution** - Single-line diagram with power flow
5. **Ventilation** - Airflow particles + fan models + air quality
6. **Service Building Security** - 3D floor plan + access control
7. **Environmental** - Sensor positions + heat maps
8. **Pumping** - Sump visualization + water levels
9. **Fire Safety** - Detection zones + suppression systems
10. **Network Communications** - Topology graph with status
11. **Parameter Input** - Context-sensitive control panels
12. **Lighting Control Popup** - Zone/luminaire adjustment

### 3D Visualization Layers
```
Layer 0: Base Tunnel 3D Model
Layer 1: Structural Elements
Layer 2: Road Surface & Markings
Layer 3: Equipment Models
Layer 4: Lighting Zone Overlay (Yellow/Orange/Blue)
Layer 5: Fire Safety Zone Overlay (Red/Green)
Layer 6: Traffic Zone Overlay (Cyan/Blue/Purple)
Layer 7: Sensor Data (icons, values)
Layer 8: Incident Markers (pulsing)
Layer 9: UI Widgets (2D overlays)
Layer 10: Alert Popups
```

---

## 🔧 Technical Stack

### Unreal Engine 5
```cpp
// Data Integration
- VaRest Plugin (REST API)
- MQTT++ Plugin (MQTT pub/sub)
- WebSocket (real-time data)
- RapidJSON (fast parsing)

// Rendering
- Lumen (global illumination)
- Nanite (virtual geometry)
- Niagara (particle systems)

// VR/AR
- Native VR templates
- UXTools (for HoloLens 2)
- Advanced Sessions (multiplayer)
```

### Unity
```csharp
// Data Integration
- Best HTTP (REST API)
- M2MQTT (MQTT client)
- UniRx (reactive streams)

// AR
- AR Foundation
- ARCore XR Plugin (Android)
- ARKit XR Plugin (iOS)
- MRTK (HoloLens 2)

// VR
- XR Interaction Toolkit
```

---

## 📊 Performance Targets

| Platform | FPS | Resolution | Triangle Budget |
|----------|-----|------------|-----------------|
| Desktop (Video Wall) | 60 | 3840x2160 | 10-15M |
| VR (Quest 2) | 90 | 1832x1920/eye | 3-5M |
| VR (Index/PSVR2) | 120 | 2016x2240/eye | 3-5M |
| AR (HoloLens 2) | 60 | 1280x720/eye | 1-2M |
| AR (Tablet) | 60 | Device native | 2-3M |

---

## 🎨 Color-Coded Zones

### Lighting Zones
```cpp
THRESHOLD_ZONE:  #FFC107 (Yellow)  - Semi-transparent
TRANSITION_ZONE: #FF9800 (Orange)  - Semi-transparent
INTERIOR_ZONE:   #2196F3 (Blue)    - Semi-transparent
```

### Fire Safety Zones
```cpp
ALARM_ZONE:     #F44336 (Red)      - Pulsing
ACTIVATED_ZONE: #B71C1C (Dark Red) - Solid
NORMAL_ZONE:    #4CAF50 (Green)    - Transparent
```

### Traffic Zones
```cpp
LANE_1:              #00BCD4 (Cyan)
LANE_2:              #03A9F4 (Light Blue)
LANE_3:              #2196F3 (Blue)
MONITORING_SECTION:  #9C27B0 (Purple)
```

---

## 🎮 VR Interaction Patterns

### Hand Controllers
**Left Controller:**
- **Trigger**: Point/grab UI panels
- **Grip**: Navigation mode
- **Thumbstick**: Movement (WASD)
- **Button A/X**: Context menu
- **Button B/Y**: Back/cancel

**Right Controller:**
- **Trigger**: Select/confirm
- **Grip**: Tool mode
- **Thumbstick**: Zoom/rotate
- **Button A/X**: Acknowledge alarm
- **Button B/Y**: Open alarm list

### Gestures
- **Pinch** (Thumb + Index): Select small objects
- **Point** (Index extended): Highlight object
- **Palm Up**: Summon menu
- **Grab & Pull**: Move through space

### Voice Commands
```
"Show lighting"      → Switch to lighting screen
"Zoom to incident"   → Camera to incident location
"Acknowledge alarm"  → Acknowledge active alarm
"Close tunnel"       → Initiate closure procedure
"Camera one"         → Switch to CCTV camera 1
```

---

## 📱 AR Interaction Patterns

### HoloLens 2
- **Air Tap**: Select
- **Bloom**: Open menu
- **Voice**: Commands
- **Direct Touch**: Physical button press

### Tablet AR
- **Point at Equipment**: See status overlay
- **Tap**: Select/interact
- **Pinch**: Zoom
- **QR Code Scan**: Jump to equipment

---

## 🌐 Data Integration

### MQTT Topic Structure
```
tunnel/
├── sensors/
│   ├── environmental/{sensor_id}  → {"value": 12.5, "unit": "ppm"}
│   ├── traffic/{detector_id}      → {"event": "breakdown", "lane": 1}
│   └── fire/{detector_id}         → {"status": "alarm", "confidence": 0.95}
├── equipment/
│   ├── lighting/{id}              → {"status": "on", "brightness": 80}
│   ├── ventilation/{id}           → {"status": "running", "rpm": 1450}
│   └── cameras/{id}               → {"status": "online", "ptz": {...}}
├── incidents/
│   ├── detected                   → {"type": "breakdown", "location": 1200}
│   ├── confirmed                  → {"incident_id": "INC-001", ...}
│   └── resolved                   → {"incident_id": "INC-001", ...}
└── alarms/
    ├── urgent                     → {"system": "fire", "message": "..."}
    ├── alert                      → {"system": "lighting", "message": "..."}
    └── record                     → {"event": "normal_operation"}
```

### Update Frequencies
| Data Type | Frequency | Priority | Latency Target |
|-----------|-----------|----------|----------------|
| Environmental sensors | 1-5s | High | <100ms |
| Traffic detection | Real-time | Critical | <50ms |
| Lighting status | 10s | Medium | <200ms |
| Ventilation | 5s | High | <100ms |
| Alarms | Real-time | Critical | <50ms |

### REST API Endpoints
```
GET  /api/v1/tunnel/{id}/status
GET  /api/v1/tunnel/{id}/sensors
GET  /api/v1/tunnel/{id}/incidents
POST /api/v1/tunnel/{id}/incidents/{id}/confirm
POST /api/v1/tunnel/{id}/equipment/{id}/control
GET  /api/v1/tunnel/{id}/cameras/{id}/stream
POST /api/v1/tunnel/{id}/cameras/{id}/ptz
POST /api/v1/tunnel/{id}/closure
```

---

## 🎬 Incident Response UI Pattern

### Standard Flow
```
1. AUTOMATED DETECTION
   └─→ Sensor triggers alert

2. AUTO PTZ ZOOM
   └─→ Camera focuses on incident

3. OPERATOR ALERT (Pop-up)
   ├─→ Video feed
   ├─→ Incident details
   └─→ [CONFIRM] or [REJECT]

4. CONTROL INTERFACE (if confirmed)
   ├─→ Signages (VMS, Lane signs, Speed limit, Blinkers)
   ├─→ PA System (Message selection)
   ├─→ Environmental (Ventilation, Lighting)
   └─→ Timeout: Auto-execute if no input

5. EXECUTION & LOGGING
   ├─→ Commands to actuators
   ├─→ Actions logged to database
   └─→ Incident closure notes interface
```

---

## 🎨 Asset Requirements

### 3D Models Needed
**Equipment** (100-1000+ instances):
- LED luminaires (1K-2K triangles)
- Ventilation fans (5K-10K triangles, animated)
- CCTV cameras (3K-5K triangles, PTZ movable)
- Fire extinguishers (500-1K triangles)
- VMS signs (2K-3K triangles)
- Electrical equipment (transformers, UPS, panels)

**Structural** (modular, tileable):
- Tunnel segments (10K-20K triangles per 100m)
- Cross-passages
- Emergency exits
- Service galleries

**Vehicles** (10-20 types):
- Sedan, SUV, Van, Truck (5K-10K triangles each)
- Emergency vehicles

### Textures (PBR Workflow)
- Concrete wall: 4K tileable (clean + weathered)
- Asphalt road: 4K tileable (clean + worn)
- Painted metal: 2K (various colors)
- Road markings: 2K with alpha

### Particles (Niagara/VFX Graph)
- Smoke (fire incident) - volumetric
- Fire - particles + light + heat distortion
- Water spray (deluge) - particle streams
- Airflow visualization - color-coded particles
- Vehicle exhaust

### Audio
- Ambient tunnel sound
- Traffic noise
- Ventilation fan (various speeds)
- Alarms (urgent, alert, record)
- Fire crackling
- Equipment sounds (pumps, doors)
- Spatial audio for VR

---

## 🚀 Implementation Timeline

### Phase 1: Core Setup (2 weeks)
- Project setup (UE5/Unity)
- Version control (Git LFS)
- Input system (VR controllers)
- Basic camera system
- Material library

### Phase 2: 3D Tunnel Model (3 weeks)
- Import CAD data
- Procedural tunnel generation
- Equipment models
- LOD system
- Lighting zones

### Phase 3: Data Integration (3 weeks)
- REST API client
- WebSocket client
- MQTT client
- JSON parsing
- Data caching

### Phase 4: Visualization Systems (4 weeks)
- Lighting system
- Ventilation (airflow particles)
- Fire safety (fire/smoke)
- Traffic simulation
- CCTV system
- Environmental sensors

### Phase 5: UI Implementation (4 weeks)
- 12 core screens
- Floating UI panels
- Alarm system
- Incident management UI
- MIS reporting

### Phase 6: VR Implementation (4 weeks)
- VR optimization (90fps)
- Locomotion (teleport + smooth)
- Hand controller interactions
- VR-specific UI
- Gesture recognition
- Spatial audio
- Multiplayer VR

### Phase 7: AR Implementation (3 weeks)
- Unity AR project
- AR Foundation
- Spatial anchoring
- Field operator AR app
- QR code scanning
- Hand gestures

### Phase 8: Testing & Optimization (4 weeks)
- Performance profiling
- User acceptance testing
- Stress testing
- VR comfort testing
- Network latency testing
- Bug fixing

**Total**: 27 weeks (~6.5 months)

---

## 💻 System Requirements

### Control Room Workstation (Desktop)
**Minimum**:
- CPU: Intel i7-10700K / AMD Ryzen 7 3700X
- GPU: NVIDIA RTX 3060 / AMD RX 6700 XT
- RAM: 16GB DDR4
- Storage: 512GB NVMe SSD
- Network: 1 Gbps Ethernet

**Recommended**:
- CPU: Intel i9-12900K / AMD Ryzen 9 5900X
- GPU: NVIDIA RTX 4070 / AMD RX 7800 XT
- RAM: 32GB DDR5
- Storage: 1TB NVMe SSD
- Network: 10 Gbps Ethernet (if available)

### VR-Ready Workstation
**Minimum**:
- CPU: Intel i7-11700K / AMD Ryzen 7 5800X
- GPU: NVIDIA RTX 3070 / AMD RX 6800
- RAM: 16GB DDR4
- VR Headset: Quest 2, PSVR 2, or Valve Index

**Recommended**:
- CPU: Intel i9-12900K / AMD Ryzen 9 5900X
- GPU: NVIDIA RTX 4080 / AMD RX 7900 XT
- RAM: 32GB DDR5
- VR Headset: Valve Index or PSVR 2

### AR Devices
- **HoloLens 2**: Integrated hardware
- **AR Tablet**: iPad Pro (2020+) or Android with Snapdragon 8 Gen 2+

---

## 💰 Budget Estimate

### Development (28 weeks)
- Team cost: **$800K - $1.2M**
- Software licenses: **$20K - $40K**
- Hardware (dev): **$50K - $80K**
- **Total Development**: **$870K - $1.32M**

### Per-Site Deployment
- Workstations (3x): **$15K - $25K**
- VR headsets (2x): **$2K - $4K**
- AR tablets (2x): **$2K - $4K**
- **Total Per-Site**: **$19K - $33K**

### Ongoing
- Maintenance: **$100K - $150K/year**
- Training: **$20K - $40K/year**

---

## 📚 Essential Plugins

### Unreal Engine 5
1. **VaRest** - REST API integration
2. **MQTT++** - MQTT pub/sub
3. **OpenCV** - Computer vision (optional)
4. **Advanced Sessions** - Multiplayer VR
5. **UXTools** - HoloLens 2 support
6. **Spatial Audio** - 3D positional audio
7. **Cesium for Unreal** - Geographic data (optional)

### Unity
1. **AR Foundation** - Core AR
2. **ARCore/ARKit XR Plugins** - Platform AR
3. **MRTK** - HoloLens 2
4. **XR Interaction Toolkit** - VR/AR interactions
5. **Best HTTP** - REST API
6. **M2MQTT** - MQTT client
7. **UniRx** - Reactive programming

---

## ⚠️ Critical Success Factors

### Performance
- ✅ Maintain 90 FPS in VR (critical for comfort)
- ✅ <100ms latency for sensor data updates
- ✅ Aggressive LOD system for large tunnels

### Usability
- ✅ Intuitive VR interactions (avoid motion sickness)
- ✅ Clear visual hierarchy in 3D space
- ✅ Comprehensive training program

### Reliability
- ✅ Desktop fallback mode (if VR fails)
- ✅ Network reconnection logic
- ✅ Data caching for offline display

### Integration
- ✅ Well-defined API contracts with backend
- ✅ Robust error handling
- ✅ Comprehensive logging

---

## 📖 References

**Full Documentation**:
- `MMI-UI-Specifications.md` (774 lines) - General specifications
- `MMI-UI-Unreal-Unity-Specifications.md` (2390 lines) - Game engine implementation
- `MMI-UI-Executive-Summary.md` (476 lines) - Executive overview

**Source Documents**:
- `docs/SyRS_extracted.txt` (96 KB) - System Requirements
- `docs/URS_extracted.txt` (93 KB) - User Requirements

---

## 🎯 Next Actions

1. **Stakeholder Presentation** - Present specifications, gather feedback
2. **Technology Decision** - Finalize Unreal vs Unity vs Hybrid
3. **Hardware Selection** - Choose VR/AR devices
4. **Team Formation** - Hire developers, artists, QA
5. **Pilot Project** - Select one tunnel for initial implementation
6. **Prototype Development** - 8-week vertical slice

---

**Quick Reference Version**: 1.0  
**Last Updated**: October 27, 2025  
**Compiled By**: Mary (Business Analyst)

