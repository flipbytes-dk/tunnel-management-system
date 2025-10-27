# Tunnel Management System - MMI/UI Specifications

[![Documentation](https://img.shields.io/badge/docs-comprehensive-blue.svg)](docs/)
[![Architecture](https://img.shields.io/badge/architecture-dual--screen-green.svg)](docs/Dual-Screen-Architecture-Specification.md)
[![UI](https://img.shields.io/badge/UI-2D%20%2B%203D-orange.svg)](docs/MMI-UI-Unreal-Unity-Specifications.md)

> **Comprehensive specifications and architecture for a modern tunnel management control room system with dual-screen setup, 2D SCADA interface, 3D digital twin visualization, and intelligent decision support.**

---

## 📋 Overview

This repository contains complete technical specifications for a **Tunnel Management Control System** designed for modern tunnel operations. The system provides operators with:

- **🖥️ Dual-Screen Interface**: Info Screen (situational awareness) + Action Screen (incident response)
- **🎨 Dual-Mode UI**: 2D SCADA-style interface + 3D Photorealistic Digital Twin
- **🤖 Decision Support System**: AI-powered recommendations with automated and manual actions
- **🔗 Unified SCADA Integration**: Holistic layer above all tunnel subsystems
- **🥽 VR/AR Capabilities**: Training simulations and field operator assistance

---

## 📚 Documentation

### Core Specifications (5 Documents, 5,638 lines, 308 KB)

| Document | Size | Lines | Description |
|----------|------|-------|-------------|
| **[Dual-Screen Architecture](docs/Dual-Screen-Architecture-Specification.md)** | 77 KB | 1,489 | **⭐ START HERE** - Info + Action screen detailed specs, 2D/3D modes, decision support, SCADA integration |
| **[Unreal/Unity Implementation](docs/MMI-UI-Unreal-Unity-Specifications.md)** | 72 KB | 2,390 | Game engine specs, VR/AR use cases, 3D digital twin, technical implementation, asset requirements |
| **[General Specifications](docs/MMI-UI-Specifications.md)** | 28 KB | 774 | Technical specs, 12 core UI screens, control room hardware, standards compliance |
| **[Executive Summary](docs/MMI-UI-Executive-Summary.md)** | 16 KB | 476 | Strategic overview, implementation roadmap, budget estimates, risk analysis |
| **[Quick Reference](docs/Quick-Reference-Unreal-Unity.md)** | 16 KB | 509 | Developer quick reference, platform recommendations, code examples |

### Source Data
- **[SyRS Extracted](docs/SyRS_extracted.txt)** (100 KB, 133 pages) - System Requirements Specifications
- **[URS Extracted](docs/URS_extracted.txt)** (96 KB, 99 pages) - User Requirements Specifications

---

## 🏗️ System Architecture

### Dual-Screen Control Room Setup

```
┌────────────────────────────────────────────────────────────┐
│                  CONTROL ROOM WORKSTATION                   │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────────┐  ┌──────────────────────────┐   │
│  │   INFO SCREEN (Left) │  │  ACTION SCREEN (Right)   │   │
│  │                      │  │                          │   │
│  │  • Tunnel Overview   │  │  • Incident Focus        │   │
│  │  • All Events        │  │  • 2-4 Event Areas       │   │
│  │  • High-level Status │  │  • Equipment Details     │   │
│  │  • Situational       │  │  • Decision Support      │   │
│  │    Awareness         │  │  • Control Actions       │   │
│  │                      │  │                          │   │
│  │  [2D or 3D mode]     │  │  [2D or 3D mode]        │   │
│  │   32" 4K Display     │  │   32" 4K Display        │   │
│  └──────────────────────┘  └──────────────────────────┘   │
│                                                             │
│  Workstation: i9-12900K + RTX 4070 + 32GB RAM             │
└────────────────────────────────────────────────────────────┘
```

### Software Stack

```
┌─────────────────────────────────────────────────────────────┐
│                    OPERATOR INTERFACE                        │
│         (Dual-screen: Info + Action, 2D + 3D modes)         │
└──────────────────────────┬──────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────┐
│              CONTROL ROOM APP (Holistic Layer)              │
│  • Event Manager  • Decision Support  • Screen Manager      │
│  • Data Fusion    • SCADA Abstraction Layer                 │
└──────────────────────────┬──────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────┐
│                   SCADA SYSTEMS (Subsystems)                 │
│  Lighting │ Ventilation │ Fire │ Traffic │ Drainage │ CCTV  │
│  (MODBUS) │  (OPC UA)   │(BACnet)│(NTCIP)│(MODBUS)│(Onvif)  │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Key Features

### Info Screen - Situational Awareness
- **Full tunnel overview** - See entire tunnel at once
- **All events visible** - Fire, breakdowns, over-height, traffic
- **System health summary** - Lighting, ventilation, fire safety, drainage
- **Traffic monitoring** - Density heat map, queue warnings
- **Click to focus** - Select event to view in Action Screen

### Action Screen - Incident Response
- **Multi-incident mode** - Split screen showing 2-4 incidents
- **Single incident mode** - Full screen for complex scenarios
- **Equipment details** - Real-time status, control actions
- **Video feeds** - PTZ camera views with controls
- **Environmental data** - CO, temperature, visibility, wind speed
- **Decision support** - AI-powered action recommendations

### Dual-Mode UI
#### 2D Mode (Traditional SCADA)
- Schematic/symbolic representation
- High information density
- Familiar to SCADA operators
- Low GPU requirements

#### 3D Digital Twin (Photorealistic)
- 1:1 accurate tunnel model
- Real-time sensor visualization
- Particle effects (fire, smoke, ventilation)
- Spatial understanding
- Immersive decision making

### Decision Support System
**Automated Actions** (with countdown):
- Routine responses to common incidents
- Countdown timer (e.g., 15 seconds)
- Operator can approve, delay, or cancel
- Auto-executes if no input

**Manual Actions** (operator approval required):
- High-impact decisions (close tunnel, activate deluge)
- Shows consequences before execution
- Detailed rationale and impact assessment

**Advisory** (informational):
- Situational awareness updates
- No immediate action required

---

## 🎮 Technology Stack

### Recommended Platforms

**Primary: Unreal Engine 5** (Control Room Displays)
- Nanite (automatic LOD for massive models)
- Lumen (real-time global illumination)
- Niagara (advanced particle systems)
- Native VR support
- Blueprint + C++ development

**Secondary: Unity** (AR Field Tools)
- AR Foundation (excellent AR support)
- XR Interaction Toolkit
- Cross-platform deployment
- WebGL export for remote access

### Backend
- **Node.js** or **Python FastAPI** (REST API + WebSocket)
- **MQTT** (Real-time sensor data)
- **OPC UA / MODBUS / BACnet** (SCADA protocols)
- **PostgreSQL / MySQL** (Database)
- **Redis** (Caching)

---

## 🚀 Implementation Roadmap

### Phase 1 - Critical (8-12 weeks)
- [ ] Backend SCADA integration layer
- [ ] Info Screen - 2D mode
- [ ] Action Screen - 2D mode (single incident)
- [ ] Basic decision support (manual actions)
- [ ] Dual-screen window management

### Phase 2 - Essential (8-12 weeks)
- [ ] Action Screen - Multi-incident support (2-4 incidents)
- [ ] Decision support - Automated actions with countdown
- [ ] 3D Digital Twin - Info Screen
- [ ] 3D Digital Twin - Action Screen (single incident)
- [ ] Advanced equipment status displays

### Phase 3 - Advanced (12-16 weeks)
- [ ] 3D Digital Twin - Action Screen multi-incident
- [ ] ML/AI-based recommendations
- [ ] VR mode for training
- [ ] Mobile/tablet operator interface
- [ ] AR field operator tools

**Total Development Time**: 28-40 weeks (~7-10 months)

---

## 💰 Budget Estimate

### Development
| Item | Cost |
|------|------|
| Team (8-10 people, 28 weeks) | $800K - $1.2M |
| Software licenses & plugins | $20K - $40K |
| Development hardware (workstations + VR/AR) | $50K - $80K |
| **Total Development** | **$870K - $1.32M** |

### Per-Site Deployment
| Item | Cost |
|------|------|
| Control room workstations (3x) | $15K - $25K |
| VR headsets (2x) | $2K - $4K |
| AR tablets (2x) | $2K - $4K |
| **Total Per-Site** | **$19K - $33K** |

### Annual Ongoing Costs
- Maintenance & updates: $100K - $150K/year
- Operator training: $20K - $40K/year

---

## 🎨 UI Components

### 12 Minimum Screens (from SyRS requirements)
1. **Overview Screen** - Full tunnel + status dashboard
2. **Schematic Map** - Technical drawing with all systems
3. **Lighting** - Zone overlays + individual luminaire control
4. **Electrical Distribution** - Power flow visualization
5. **Ventilation** - Airflow particles + fan control
6. **Service Building Security** - Access control + cameras
7. **Environmental** - Sensor heat maps (CO, temp, visibility)
8. **Pumping** - Sump levels + water flow
9. **Fire Safety** - Detection zones + suppression systems
10. **Network Communications** - Topology + status
11. **Parameter Input** - Context-sensitive controls
12. **Lighting Control Popup** - Zone/luminaire adjustment

---

## 🥽 VR/AR Use Cases

### VR Control Room Operator Mode
- **Training & Drill Scenarios** - Practice incidents without risk
- **Immersive Incident Response** - Multi-incident spatial view
- **Remote Operation** - Work from home in virtual control room
- **3D Tunnel Inspection** - Walk through for maintenance planning

### AR Field Operator Mode
- **On-Site Maintenance** - AR overlays with instructions
- **AR Control Room Enhancement** - Holographic displays
- **Emergency Response** - Real-time smoke spread visualization
- **Training Mode** - AR annotations in physical space

---

## 📊 Performance Targets

| Platform | Target FPS | Resolution | Triangle Budget |
|----------|-----------|------------|-----------------|
| Desktop (Video Wall) | 60 | 3840x2160 (4K) | 10-15M |
| VR (Quest 2) | 90 | 1832x1920/eye | 3-5M |
| VR (Index/PSVR2) | 120 | 2016x2240/eye | 3-5M |
| AR (HoloLens 2) | 60 | 1280x720/eye | 1-2M |
| AR (Tablet) | 60 | Device native | 2-3M |

---

## 🔧 Hardware Requirements

### Control Room Workstation (Recommended)
- **CPU**: Intel i9-12900K / AMD Ryzen 9 5900X
- **GPU**: NVIDIA RTX 4070 (12GB VRAM) - for dual 4K + 3D
- **RAM**: 32GB DDR5
- **Storage**: 1TB NVMe SSD
- **Network**: Dual 1 Gbps Ethernet (redundant)
- **Displays**: 2x 32" 4K monitors (3840x2160)

### VR-Ready Workstation
- **CPU**: Intel i9-12900K / AMD Ryzen 9 5900X
- **GPU**: NVIDIA RTX 4080 / AMD RX 7900 XT
- **RAM**: 32GB DDR5
- **VR Headset**: Quest 2, Valve Index, or PSVR 2

---

## 📖 Standards & Compliance

- **NFPA 502** - Road Tunnels, Bridges, and Other Limited Access Highways
- **CD 352** - Design of Road Tunnels
- **ISO 11064** - Ergonomic design of Control Centres
- **PIARC** - Road Tunnels: Vehicle Emissions and Air Demand for Ventilation
- **IEC 61850** - Electrical substations
- **OPC UA** - Industrial automation
- **MODBUS** - Serial communication protocol
- **BACnet** - Building automation
- **NTCIP** - Transportation management protocols

---

## 🤝 Contributing

This is a specification repository. For implementation:
1. Review the [Dual-Screen Architecture Specification](docs/Dual-Screen-Architecture-Specification.md)
2. Check the [Unreal/Unity Implementation Guide](docs/MMI-UI-Unreal-Unity-Specifications.md)
3. Use the [Quick Reference](docs/Quick-Reference-Unreal-Unity.md) for development

---

## 📄 License

Copyright © 2025 FlipBytes DK. All rights reserved.

This repository contains proprietary specifications and documentation. Unauthorized copying, distribution, or use is strictly prohibited.

---

## 📧 Contact

For inquiries about implementation, consulting, or licensing:
- **Company**: FlipBytes DK
- **Repository**: [https://github.com/flipbytes-dk/tunnel-management-system](https://github.com/flipbytes-dk/tunnel-management-system)

---

## 🎯 Quick Start

1. **Read the Architecture**: Start with [Dual-Screen Architecture Specification](docs/Dual-Screen-Architecture-Specification.md)
2. **Review Technical Specs**: See [Unreal/Unity Implementation Guide](docs/MMI-UI-Unreal-Unity-Specifications.md)
3. **Check Requirements**: Review extracted [SyRS](docs/SyRS_extracted.txt) and [URS](docs/URS_extracted.txt)
4. **Plan Development**: Follow the implementation roadmap in Phase 1-3
5. **Estimate Budget**: Use provided budget estimates for planning

---

**Generated by**: Mary (Business Analyst) using AI-powered analysis  
**Source Documents**: SyRS v20 Feb 2025 (133 pages), URS v30 Jan 2025 (99 pages)  
**Total Analysis**: 232 pages of requirements → 5,638 lines of comprehensive specifications  
**Date**: October 27, 2025

---

<p align="center">
  <strong>🏗️ Comprehensive | 📊 Data-Driven | 🎯 Implementation-Ready</strong>
</p>

