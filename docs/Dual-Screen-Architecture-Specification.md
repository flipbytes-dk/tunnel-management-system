# Dual-Screen Control Room Architecture Specification
**Info Screen + Action Screen with 2D/3D UI Modes**

## Document Information
- **Architecture**: Dual-screen control room with Info/Action paradigm
- **UI Modes**: 2D SCADA-style + 3D Digital Twin
- **Purpose**: Situational awareness (Info) + Decision support (Action)
- **Integration**: Unified layer above all SCADA subsystems
- **Date**: October 27, 2025

---

## Table of Contents
1. [Architecture Overview](#1-architecture-overview)
2. [Info Screen Specification](#2-info-screen-specification)
3. [Action Screen Specification](#3-action-screen-specification)
4. [Screen Transition Logic](#4-screen-transition-logic)
5. [2D UI Mode](#5-2d-ui-mode)
6. [3D Digital Twin Mode](#6-3d-digital-twin-mode)
7. [Decision Support System](#7-decision-support-system)
8. [SCADA Integration Layer](#8-scada-integration-layer)
9. [Implementation Details](#9-implementation-details)
10. [Hardware Configuration](#10-hardware-configuration)

---

## 1. Architecture Overview

### 1.1 Dual-Screen Philosophy

```
┌─────────────────────────────────────────────────────────────┐
│                    CONTROL ROOM SETUP                        │
│                                                              │
│  ┌────────────────────────┐  ┌────────────────────────────┐ │
│  │   LEFT SCREEN          │  │   RIGHT SCREEN             │ │
│  │   INFO SCREEN          │  │   ACTION SCREEN            │ │
│  │                        │  │                            │ │
│  │  • Total Overview      │  │  • Incident Focus          │ │
│  │  • All Events          │  │  • 2-4 Event Areas         │ │
│  │  • High-level Status   │  │  • Equipment Details       │ │
│  │  • Situational         │  │  • Decision Support        │ │
│  │    Awareness           │  │  • Control Actions         │ │
│  │                        │  │                            │ │
│  │  [2D or 3D mode]       │  │  [2D or 3D mode]          │ │
│  └────────────────────────┘  └────────────────────────────┘ │
│                                                              │
│              ↑                           ↑                   │
│              │                           │                   │
│         Info Feed                   Action Context           │
│                                                              │
└──────────────────────────┬───────────────────────────────────┘
                           │
                           │ Unified Data Layer
                           │
┌──────────────────────────▼───────────────────────────────────┐
│            CONTROL ROOM APP (Holistic Layer)                 │
│    (Sits on top of all SCADA subsystems)                     │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Event    │  │ Decision │  │ Screen   │  │ Mode     │   │
│  │ Manager  │  │ Support  │  │ Routing  │  │ Switch   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
└──────────────────────────┬───────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                   SCADA INTEGRATION LAYER                     │
│           (OPC UA, MODBUS, Proprietary Protocols)            │
│                                                              │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌────────┐│
│  │Lighting │ │Ventila- │ │  Fire   │ │ Traffic │ │Drainage││
│  │ SCADA   │ │tion     │ │ Safety  │ │ SCADA   │ │ SCADA  ││
│  │         │ │ SCADA   │ │ SCADA   │ │         │ │        ││
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └────────┘│
└───────────────────────────────────────────────────────────────┘
```

### 1.2 Key Principles

**Info Screen (Left)**:
- **Purpose**: Situational awareness, continuous monitoring
- **Content**: Entire tunnel, all active events, high-level status
- **Interaction**: Minimal - mainly observation, event selection
- **Updates**: Continuous, real-time overview
- **Metaphor**: "Air traffic control radar" - see everything at once

**Action Screen (Right)**:
- **Purpose**: Incident response, decision support, control actions
- **Content**: Focused on 2-4 incident areas (or 1 if only 1 incident)
- **Interaction**: High - control actions, parameter adjustment, confirmations
- **Updates**: Context-driven based on selected incidents
- **Metaphor**: "Cockpit instruments" - detailed, actionable information

### 1.3 Dual UI Mode Philosophy

Both screens support **2D mode** and **3D Digital Twin mode**:

**2D Mode** (Traditional SCADA):
- Schematic/symbolic representation
- Familiar to operators with SCADA experience
- High information density
- Fast cognitive processing
- Lower GPU requirements

**3D Digital Twin Mode** (Realistic):
- Photorealistic tunnel representation
- Spatial understanding
- Better for training and complex scenarios
- Immersive decision making
- Higher GPU requirements

Operators can **independently switch** each screen between 2D and 3D modes.

---

## 2. Info Screen Specification

### 2.1 Purpose
**Primary Function**: Provide holistic situational awareness of entire tunnel system.

**Operator Questions Answered**:
- What is the overall status of the tunnel?
- Where are the active incidents?
- What systems are running normally vs. alarmed?
- What is the traffic situation across the entire tunnel?
- Are there any patterns or correlations between events?

### 2.2 Layout - 2D Mode

```
┌────────────────────────────────────────────────────────────┐
│ INFO SCREEN - 2D MODE                    [Switch to 3D →] │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  Tunnel Overview - Linear Schematic                        │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                       │   │
│  │  Entry ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ Exit  │   │
│  │  Portal                                       Portal │   │
│  │         ⚠️1                  🔴2        ⚠️3           │   │
│  │    Ch:0.5km             Ch:1.2km    Ch:2.8km        │   │
│  │    Breakdown            Fire        Over-height     │   │
│  │                                                       │   │
│  │  [====|====|====|====|====|====|====|====|====]     │   │
│  │   0km  0.5  1.0  1.5  2.0  2.5  3.0  3.5  4.0       │   │
│  │                                                       │   │
│  │  Traffic: ████████░░ (80%)                           │   │
│  │  Air Quality: ███████░░░ (Good)                      │   │
│  │  Lighting: ██████████ (OK)                           │   │
│  │  Ventilation: ████████░░ (Stage 1)                   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Active Events (3)                                         │
│  ┌───┬──────────┬──────────┬─────────────┬──────────────┐ │
│  │Pri│ Event    │ Time     │ Location    │ Status       │ │
│  ├───┼──────────┼──────────┼─────────────┼──────────────┤ │
│  │🔴 │ Fire     │ 16:23:45 │ Ch 1.2km L1 │ ACTIVE       │ │
│  │⚠️ │ Breakdown│ 16:20:12 │ Ch 0.5km L2 │ RESPONDING   │ │
│  │⚠️ │ Over-ht  │ 16:25:00 │ Ch 2.8km L1 │ DETECTED     │ │
│  └───┴──────────┴──────────┴─────────────┴──────────────┘ │
│  [Click event to view in Action Screen →]                  │
│                                                             │
│  System Status Summary                                     │
│  ┌──────────┬────────┬──────────┬─────────────────────┐   │
│  │ System   │ Status │ Active   │ Notes               │   │
│  ├──────────┼────────┼──────────┼─────────────────────┤   │
│  │ Lighting │ ✓ OK   │ 987/1000 │ 13 faults           │   │
│  │ Ventilat.│ ✓ OK   │ 12/15    │ Stage 1 active      │   │
│  │ Fire     │ 🔴 ALRM│ Zone 3   │ Incident active     │   │
│  │ Drainage │ ✓ OK   │ 4/6      │ 2 standby           │   │
│  │ CCTV     │ ✓ OK   │ 48/50    │ 2 offline           │   │
│  │ Traffic  │ ⚠️ MOD │ Normal   │ Queue at Ch 1.2km   │   │
│  │ Electrical│ ✓ OK  │ Normal   │ UPS: 98%           │   │
│  └──────────┴────────┴──────────┴─────────────────────┘   │
│                                                             │
│  Operator: John Smith (Supervisor)    TSB-1    16:30:45   │
└────────────────────────────────────────────────────────────┘
```

### 2.3 Layout - 3D Digital Twin Mode

```
┌────────────────────────────────────────────────────────────┐
│ INFO SCREEN - 3D MODE                    [Switch to 2D →] │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  [         3D Tunnel View - Bird's Eye / Isometric       ] │
│  [                                                         ]│
│  [    ╔════════════════════════════════════════╗          ]│
│  [    ║  🟢 Entry                       Exit 🟢║          ]│
│  [    ║                                        ║          ]│
│  [    ║     ⚠️🚗                                ║          ]│
│  [    ║   Ch:0.5km                             ║          ]│
│  [    ║                                        ║          ]│
│  [    ║              🔥🔴                       ║          ]│
│  [    ║            Ch:1.2km                    ║          ]│
│  [    ║                                        ║          ]│
│  [    ║                          ⚠️🚛          ║          ]│
│  [    ║                        Ch:2.8km        ║          ]│
│  [    ╚════════════════════════════════════════╝          ]│
│  [                                                         ]│
│  [  Color overlays: Lighting zones, fire zones visible    ]│
│  [  Equipment: Simplified icons, status color-coded       ]│
│  [  Traffic: Vehicle count heat map                       ]│
│                                                             │
│  Mini Event Panel (Overlay)                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🔴 Fire @ 1.2km  ⚠️ Breakdown @ 0.5km  ⚠️ Over-ht   │   │
│  │ [View in Action] [View in Action]   [View in Action]│   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Camera Controls: [Reset] [Zoom +/-] [Rotate]             │
└────────────────────────────────────────────────────────────┘
```

### 2.4 Info Screen Behaviors

**Always-On Display**:
- Never goes blank
- Continuous real-time updates
- All events visible simultaneously
- No modal dialogs (all overlays)

**Event Visualization**:
- **Critical (Fire, Hazmat)**: 🔴 Red pulsing icon
- **Alert (Breakdown, Collision)**: ⚠️ Yellow/orange icon
- **Info (Maintenance, Low priority)**: 🔵 Blue icon
- Events positioned at exact chainage
- Click event → highlight in Action Screen

**Status Indicators**:
- Color-coded system status bars
- Traffic density heat map (green → yellow → red)
- Air quality zones (blue → yellow → red)
- Equipment health summary

**Interaction**:
- **Primary**: Event selection (click to send to Action Screen)
- **Secondary**: Zoom/pan to view specific tunnel section
- **Tertiary**: System status drill-down (opens popup overlay)
- **No control actions** - read-only monitoring

---

## 3. Action Screen Specification

### 3.1 Purpose
**Primary Function**: Provide detailed, actionable information for incident response with decision support.

**Operator Questions Answered**:
- What exactly is happening at this incident?
- What equipment is involved and what is its status?
- What actions should I take?
- What are the consequences of my actions?
- What is the recommended response?

### 3.2 Display Modes

**Mode 1: Multi-Incident (2-4 incidents)**
When multiple incidents are active, screen splits into 2-4 zones:

```
┌────────────────────────────────────────────────────────────┐
│ ACTION SCREEN - MULTI-INCIDENT           [Switch to 3D →] │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────────────┬──────────────────────────┐   │
│  │ INCIDENT 1 (Priority)    │ INCIDENT 2               │   │
│  │ 🔴 Fire @ Ch 1.2km L1    │ ⚠️ Breakdown @ Ch 0.5km  │   │
│  │                          │                          │   │
│  │ [Equipment Status]       │ [Equipment Status]       │   │
│  │ • Fire Detector: ALARM   │ • VAID: Detected        │   │
│  │ • Deluge: STANDBY        │ • Lane Sign: CLOSED     │   │
│  │ • Ventilation: STAGE 2   │ • Lighting: +20%        │   │
│  │ • Cameras: PTZ Auto      │ • VMS: "Lane Closed"    │   │
│  │                          │                          │   │
│  │ [Video Feed]             │ [Video Feed]             │   │
│  │   Camera 12 - PTZ        │   Camera 5 - Fixed      │   │
│  │                          │                          │   │
│  │ [Action Panel]           │ [Action Panel]           │   │
│  │ [Activate Deluge]        │ [Confirm Breakdown]      │   │
│  │ [Close Tunnel]           │ [Send Recovery]          │   │
│  │ [Emergency Ventilation]  │ [Clear Lane]            │   │
│  └──────────────────────────┴──────────────────────────┘   │
│                                                             │
│  ┌──────────────────────────┬──────────────────────────┐   │
│  │ INCIDENT 3               │ INCIDENT 4               │   │
│  │ ⚠️ Over-height @ 2.8km   │ 🔵 Maintenance @ 3.5km   │   │
│  │ [Simplified view]        │ [Simplified view]        │   │
│  └──────────────────────────┴──────────────────────────┘   │
│                                                             │
│  Decision Support: Recommended Actions                      │
│  1. Activate deluge system for Incident 1 [Auto in 30s]   │
│  2. Confirm breakdown for Incident 2 [Awaiting operator]  │
│  3. Close tunnel entry [Recommended] [Execute]            │
└────────────────────────────────────────────────────────────┘
```

**Mode 2: Single Incident (Full Screen Focus)**
When only 1 incident active, entire screen dedicated:

```
┌────────────────────────────────────────────────────────────┐
│ ACTION SCREEN - SINGLE INCIDENT          [Switch to 3D →] │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  🔴 FIRE INCIDENT @ Ch 1.2km Lane 1                        │
│  Detected: 16:23:45 | Duration: 00:07:23 | Status: ACTIVE │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                       │   │
│  │          PRIMARY VIDEO FEED                          │   │
│  │          Camera 12 - PTZ (Auto-tracking)             │   │
│  │                                                       │   │
│  │          [Large video display area]                  │   │
│  │          Showing fire location with smoke            │   │
│  │                                                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────┬─────────────┬─────────────┬────────────┐  │
│  │ Camera 11   │ Camera 13   │ Camera 10   │ Thermal    │  │
│  │ [thumbnail] │ [thumbnail] │ [thumbnail] │ [thumbnail]│  │
│  │ Upstream    │ Downstream  │ Cross-pass  │ Camera     │  │
│  └─────────────┴─────────────┴─────────────┴────────────┘  │
│                                                             │
│  EQUIPMENT STATUS                                          │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ System         │ Status    │ Value          │ Action │  │
│  ├──────────────────────────────────────────────────────┤  │
│  │ Fire Detector  │ 🔴 ALARM  │ Smoke: High    │ -      │  │
│  │ Deluge System  │ ⏸️ STANDBY│ Pressure: 12bar│ [ACTIV]│  │
│  │ Ventilation    │ ✓ ACTIVE  │ Stage 2, 100%  │ [ADJ]  │  │
│  │ └─ Fan 1       │ ✓ ON      │ 1450 RPM       │ -      │  │
│  │ └─ Fan 2       │ ✓ ON      │ 1445 RPM       │ -      │  │
│  │ └─ Fan 3       │ ✓ ON      │ 1455 RPM       │ -      │  │
│  │ Lighting       │ ✓ ACTIVE  │ 100% (Emerg)   │ -      │  │
│  │ Lane Signs     │ ✓ CLOSED  │ L1: Red        │ [CHG]  │  │
│  │ VMS Signs      │ ✓ ACTIVE  │ "TUNNEL CLOSED"│ [EDIT] │  │
│  │ Emergency Exit │ ✓ OPEN    │ Door 12-A      │ -      │  │
│  │ Drainage       │ 🔴 INHIBIT│ Pumps: OFF     │ -      │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  ENVIRONMENTAL DATA                                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ CO: 125ppm ⚠️  CO2: 850ppm ⚠️  NOx: 2.3ppm ⚠️       │  │
│  │ Temp: 45°C 🔴  Visibility: 12m 🔴  Wind: 2.1m/s ✓   │  │
│  │                                                       │  │
│  │ [Real-time Graph - Last 10 minutes]                  │  │
│  │  CO ────────────────────────────────▲─────           │  │
│  │  Temp ─────────────────────────▲────────────         │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  DECISION SUPPORT SYSTEM                                   │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ⚠️ RECOMMENDED ACTIONS (Automated in 15 seconds)     │  │
│  │                                                       │  │
│  │ 1. [AUTO] Activate deluge system                     │  │
│  │    └─ Rationale: Fire confirmed, smoke high          │  │
│  │    └─ [APPROVE] [DELAY 30s] [CANCEL]                │  │
│  │                                                       │  │
│  │ 2. [AUTO] Increase ventilation to emergency mode     │  │
│  │    └─ Rationale: Smoke clearance required            │  │
│  │    └─ [APPROVED ✓]                                   │  │
│  │                                                       │  │
│  │ 3. [MANUAL] Close tunnel to all traffic              │  │
│  │    └─ Rationale: Safety critical incident            │  │
│  │    └─ [EXECUTE] [PARTIAL] [CANCEL]                  │  │
│  │                                                       │  │
│  │ 4. [MANUAL] Notify emergency services                │  │
│  │    └─ Fire brigade, ambulance dispatched             │  │
│  │    └─ [COMPLETED ✓]                                  │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  ACTIONS                                                   │
│  [Incident Response Plan] [Manual Control] [Close Screen] │
└────────────────────────────────────────────────────────────┘
```

### 3.3 Layout - 3D Digital Twin Mode (Single Incident)

```
┌────────────────────────────────────────────────────────────┐
│ ACTION SCREEN - 3D MODE                  [Switch to 2D →] │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                       │   │
│  │        3D DIGITAL TWIN - INCIDENT FOCUS              │   │
│  │                                                       │   │
│  │    ╔═══════════════════════════════════════╗         │   │
│  │    ║         🔥🔴 FIRE HERE                 ║         │   │
│  │    ║    Ch: 1.2km Lane 1                   ║         │   │
│  │    ║                                        ║         │   │
│  │    ║  • Smoke particles visible (red)      ║         │   │
│  │    ║  • Fire glow effect                   ║         │   │
│  │    ║  • Ventilation airflow (blue arrows)  ║         │   │
│  │    ║  • Deluge nozzles highlighted         ║         │   │
│  │    ║  • Emergency exit doors (green)       ║         │   │
│  │    ║  • Cameras with FOV cones             ║         │   │
│  │    ║                                        ║         │   │
│  │    ║  Only ~200m section of tunnel shown   ║         │   │
│  │    ║  All equipment in this zone detailed  ║         │   │
│  │    ╚═══════════════════════════════════════╝         │   │
│  │                                                       │   │
│  │  [Rotate] [Zoom] [Walk Mode] [Reset View]           │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  EQUIPMENT STATUS (3D Overlay)                             │
│  Click equipment in 3D view → Status panel appears         │
│                                                             │
│  Selected: Deluge Nozzle #12                               │
│  • Status: STANDBY                                         │
│  • Pressure: 12 bar                                        │
│  • Last Test: 2024-10-15                                   │
│  • [ACTIVATE NOW] [TEST] [CLOSE]                          │
│                                                             │
│  DECISION SUPPORT (Overlay)                                │
│  [Same as 2D mode - floating panel in 3D space]           │
└────────────────────────────────────────────────────────────┘
```

### 3.4 Action Screen Behaviors

**Context-Driven Display**:
- Content changes based on selected incidents from Info Screen
- If 1 incident: Full screen dedicated
- If 2-4 incidents: Split screen (quadrants)
- If >4 incidents: Operator selects top 4 priority

**Real-Time Equipment Status**:
- All equipment related to incident area shown
- Status updates every 1-5 seconds
- Color-coded: 🟢 OK, ⚠️ Warning, 🔴 Alarm, ⚪ Offline
- Interactive: Click equipment → control panel appears

**Decision Support Integration**:
- Analyzes incident type, severity, equipment status
- Recommends actions based on rules (from Rule Engine)
- Shows automation countdown (e.g., "Auto in 15s")
- Operator can approve, delay, or cancel automated actions

**Control Actions**:
- All incident-related controls accessible
- Confirmation required for critical actions
- Action logging automatic
- Consequences shown before execution

---

## 4. Screen Transition Logic

### 4.1 Event-Driven Transitions

**New Incident Detected**:
```
1. Info Screen: New incident appears on overview
2. Audio alert + visual notification
3. Action Screen: 
   - If empty → Auto-populate with new incident
   - If occupied → Add to queue, show notification
4. Operator can click incident in Info Screen to force display in Action Screen
```

**Incident Resolution**:
```
1. Operator marks incident as resolved in Action Screen
2. Incident removed from Action Screen
3. Next priority incident auto-populates (if queue exists)
4. Info Screen: Incident changes color to resolved (green checkmark)
5. After 60s: Resolved incident fades from Info Screen
```

**Manual Selection**:
```
1. Operator clicks incident in Info Screen
2. Action Screen: 
   - If <4 incidents shown → Add clicked incident
   - If 4 incidents shown → Replace lowest priority with clicked incident
3. Action Screen: Highlight newly added incident
```

### 4.2 Priority Rules

**Incident Priority Order** (for Action Screen display):
```
1. 🔴 Critical (Fire, Hazmat, Explosion, Structural)
2. ⚠️ High (Collision, Multi-vehicle, Flooding)
3. ⚠️ Medium (Breakdown, Over-height, Debris)
4. 🔵 Low (Maintenance, Equipment fault)
```

**Display Logic**:
- Action Screen always shows highest priority incidents first
- If 5 incidents but only 4 can be shown → lowest priority goes to queue
- Operator can manually override priority (pin incident)

### 4.3 Mode Synchronization

**2D/3D Mode Switching**:
- Info Screen and Action Screen modes are **independent**
- Common combinations:
  - Both 2D (traditional SCADA operators)
  - Info: 2D, Action: 3D (situational awareness + immersive response)
  - Both 3D (training mode, advanced operators)
  - Info: 3D, Action: 2D (rare, but supported)

**Mode Switch Behavior**:
- Toggle button on each screen: `[Switch to 3D →]` / `[Switch to 2D →]`
- Transition animation: 500ms fade
- State preserved: Selected incidents, zoom level, camera position
- GPU load rebalances (3D mode uses more GPU)

---

## 5. 2D UI Mode

### 5.1 Design Philosophy

**SCADA-Style Interface**:
- Familiar to operators with traditional SCADA experience
- High information density
- Schematic/symbolic representation
- Fast cognitive processing
- Proven in industrial control environments

### 5.2 Visual Language

**Symbols & Icons**:
```
Equipment Symbols (ISO/IEC standard where applicable):
• Luminaire:       ⦿ (circle with center dot)
• Fan:             ◐ (half-filled circle with arrows)
• Pump:            ⊛ (circle with cross)
• Camera:          📷 (camera icon)
• Fire Detector:   🔥 (flame icon)
• VMS Sign:        🚦 (traffic light icon)
• Emergency Exit:  🚪 (door icon)
• Sensor:          ⊕ (circle with plus)

Status Colors:
• Green (#4CAF50):  Normal operation
• Yellow (#FFC107): Warning/degraded
• Red (#F44336):    Alarm/fault
• Gray (#9E9E9E):   Offline/disabled
• Blue (#2196F3):   Selected/active

Line Types:
• Solid line:      Active connection
• Dashed line:     Inactive/standby
• Thick line:      Power flow/high importance
• Thin line:       Data connection
• Animated line:   Active data flow
```

### 5.3 Info Screen - 2D Layout Details

**Linear Tunnel Schematic**:
- Tunnel represented as horizontal line (left = entry, right = exit)
- Chainage markers every 100m or 500m (depending on tunnel length)
- Equipment symbols positioned at exact chainage
- Color-coded zones as background rectangles
- Incidents as overlaid icons with labels

**System Status Panel**:
- Table format with columns: System | Status | Active Count | Notes
- Rows for each subsystem (Lighting, Ventilation, Fire, etc.)
- Click row → expanded view with detailed equipment list
- Status icon changes color based on worst equipment in that system

**Event List**:
- Chronological list, newest first
- Color-coded priority indicator
- Click event → highlight in schematic and send to Action Screen
- Filter buttons: All | Critical | Active | Resolved

### 5.4 Action Screen - 2D Layout Details

**Equipment Status Table**:
- Hierarchical tree structure:
  - System (e.g., Ventilation)
    - Equipment group (e.g., Fans)
      - Individual equipment (e.g., Fan 1, Fan 2)
- Each row shows: Name | Status | Current Value | Units | Control Button
- Click control button → opens control dialog
- Real-time value updates (highlighted briefly when changed)

**Video Feed Integration**:
- RTSP/WebRTC video streams
- PTZ controls: Pan/Tilt/Zoom sliders
- Preset positions: Dropdown selector
- Recording indicator
- Snapshot button

**Control Panels**:
- Context-sensitive based on selected equipment
- Example (Fan):
  - On/Off toggle
  - Speed slider (if VFD)
  - Auto/Manual mode switch
  - Status indicators (temp, vibration, run hours)
- Example (Lighting):
  - Brightness slider
  - Zone selection dropdown
  - Schedule override checkbox
- Confirmation dialog for critical actions

---

## 6. 3D Digital Twin Mode

### 6.1 Design Philosophy

**Photorealistic Digital Twin**:
- 1:1 accurate representation of physical tunnel
- Real-time sensor data visualization
- Spatial understanding and context
- Immersive decision making
- Better for complex scenarios and training

### 6.2 Digital Twin Fidelity Levels

**Level of Detail**:
```
LOD 0 (Close-up, <50m): 
  - High poly models
  - 4K textures
  - Real-time reflections
  - Particle effects
  - Individual bolts and rivets visible

LOD 1 (Medium, 50-200m):
  - Medium poly models
  - 2K textures
  - Simplified materials
  - Basic particle effects

LOD 2 (Far, 200m+):
  - Low poly models
  - 1K textures
  - No particle effects
  - Color-coded status only
```

**Photorealism Elements**:
- PBR (Physically Based Rendering) materials
- Real-time global illumination (Lumen in UE5)
- Accurate lighting simulation (matches actual lux levels)
- Weathering and wear on surfaces
- Realistic concrete, metal, asphalt textures
- Dynamic shadows
- Post-processing (bloom, color grading)

### 6.3 Info Screen - 3D Layout Details

**Camera Modes**:
```
1. Bird's Eye View (Default)
   - Top-down orthographic projection
   - See entire tunnel at once
   - Good for situational awareness

2. Isometric View
   - 45° angle, see tunnel depth
   - Better spatial understanding
   - Can see equipment on walls

3. Fly-Through
   - Smooth camera movement along tunnel
   - Auto-rotate to show all angles
   - Useful for presentation/training

4. Free Camera
   - WASD movement, mouse look
   - Explore tunnel freely
   - Good for detailed inspection
```

**Equipment Representation**:
- All equipment as 3D models (not icons)
- Real-time status indicated by:
  - Model color (green/yellow/red glow)
  - Emissive materials (lights actually glow)
  - Particle effects (smoke from fire, steam from leaks)
  - Animated components (fan blades spinning)
- Floating labels appear on hover
- Click equipment → focus camera on it

**Incident Visualization**:
- Fire: Realistic fire and smoke particle systems
- Breakdown vehicle: 3D vehicle model with hazard lights
- Flooding: Water surface simulation
- Smoke: Volumetric fog with physics simulation
- Hazmat: Colored gas particles spreading

### 6.4 Action Screen - 3D Layout Details

**Focused View**:
- Only ~200m section of tunnel shown (incident area)
- Camera closer to incident than Info Screen
- All equipment in focus area rendered at highest LOD
- Background tunnel fades to low detail

**Interactive Equipment**:
- Click equipment → 3D bounding box highlight
- Status panel appears as 3D floating widget near equipment
- Control actions available in floating panel
- Equipment animates when controlled:
  - Fan: Blades speed up/slow down
  - Pump: Vibration animation when running
  - Deluge: Water spray particles when activated
  - Lights: Brightness changes in real-time

**Decision Support in 3D**:
- Recommended actions shown as floating panels
- Action consequences previewed in 3D:
  - "Activate deluge" → show water spray preview (semi-transparent)
  - "Close lane" → show lane signs turning red preview
  - "Increase ventilation" → show airflow particles increasing
- Operator can see impact before confirming

**Environmental Effects**:
- Air quality visualization:
  - CO particles (colored spheres, density = concentration)
  - Heat waves (distortion shader for high temp)
  - Wind direction (particle streamers)
- Lighting changes reflected in real-time
- Smoke spreads with physics simulation

---

## 7. Decision Support System

### 7.1 Purpose

**Assist operators in making informed decisions**:
- Analyze incident data
- Recommend actions based on rules
- Show consequences of actions
- Automate routine responses (with operator approval)
- Provide situational awareness

### 7.2 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                DECISION SUPPORT SYSTEM                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐      ┌──────────────┐                    │
│  │ Rule Engine  │◄────►│ ML/AI Engine │                    │
│  │              │      │  (Optional)  │                    │
│  └──────┬───────┘      └──────────────┘                    │
│         │                                                    │
│         │ Incident Analysis                                 │
│         ▼                                                    │
│  ┌────────────────────────────────────────┐                │
│  │  Incident Context                      │                │
│  │  • Type, severity, location            │                │
│  │  • Equipment involved                  │                │
│  │  • Environmental data                  │                │
│  │  • Traffic situation                   │                │
│  │  • Historical patterns                 │                │
│  └────────────┬───────────────────────────┘                │
│               │                                              │
│               │ Action Recommendation                        │
│               ▼                                              │
│  ┌────────────────────────────────────────┐                │
│  │  Recommended Actions                   │                │
│  │  • Prioritized list                    │                │
│  │  • Rationale for each                  │                │
│  │  • Automation level (auto/manual)      │                │
│  │  • Estimated consequences              │                │
│  │  • Countdown timer for auto actions    │                │
│  └────────────┬───────────────────────────┘                │
│               │                                              │
│               │ Operator Interaction                         │
│               ▼                                              │
│  ┌────────────────────────────────────────┐                │
│  │  Operator Actions                      │                │
│  │  • Approve / Delay / Cancel            │                │
│  │  • Manual override                     │                │
│  │  • Parameter adjustment                │                │
│  └────────────┬───────────────────────────┘                │
│               │                                              │
│               │ Execution                                    │
│               ▼                                              │
│  ┌────────────────────────────────────────┐                │
│  │  SCADA Commands                        │                │
│  │  • Equipment control                   │                │
│  │  • Logging                             │                │
│  │  • Feedback to operator                │                │
│  └────────────────────────────────────────┘                │
└─────────────────────────────────────────────────────────────┘
```

### 7.3 Recommendation Types

**Automated Actions** (AUTO):
- Routine, well-defined responses
- Low risk, high confidence
- Execute automatically after countdown (operator can cancel)
- Examples:
  - Increase lighting for breakdown
  - Activate specific ventilation stage for air quality
  - Display standard VMS messages

**Manual Actions** (MANUAL):
- High-impact decisions
- Require operator judgment
- System recommends but waits for operator approval
- Examples:
  - Close tunnel to traffic
  - Activate deluge system
  - Call emergency services
  - Evacuate tunnel

**Advisory** (INFO):
- Informational recommendations
- No immediate action required
- Helps with situational awareness
- Examples:
  - "Traffic building up at Ch 2.5km"
  - "Air quality degrading, consider ventilation increase"
  - "Similar incident occurred here 3 months ago"

### 7.4 Decision Support Display (Action Screen)

**Recommendation Panel**:
```
┌────────────────────────────────────────────────────────────┐
│ 💡 DECISION SUPPORT - RECOMMENDED ACTIONS                  │
├────────────────────────────────────────────────────────────┤
│                                                             │
│  1. [AUTO] Increase lighting to 100%                       │
│     ├─ Rationale: Fire incident, visibility critical       │
│     ├─ Confidence: High (95%)                              │
│     ├─ Impact: Low risk                                    │
│     ├─ Equipment: 23 luminaires in zone                    │
│     ├─ ETA: Immediate                                      │
│     └─ ⏱️ Auto-executing in: 08s [APPROVE] [DELAY] [CANCEL]│
│                                                             │
│  2. [AUTO] Activate ventilation Stage 2                    │
│     ├─ Rationale: Smoke detection, CO rising               │
│     ├─ Confidence: High (92%)                              │
│     ├─ Impact: Medium noise, normal procedure              │
│     ├─ Equipment: Fans 1-6                                 │
│     ├─ ETA: 30 seconds to full speed                       │
│     └─ ✅ APPROVED (Executing)                             │
│                                                             │
│  3. [MANUAL] Activate deluge system                        │
│     ├─ Rationale: Fire confirmed, temperature rising       │
│     ├─ Confidence: Medium (78%)                            │
│     ├─ Impact: HIGH - Water damage possible                │
│     ├─ Equipment: Deluge zone 3 (12 nozzles)              │
│     ├─ Considerations:                                     │
│     │  • No vehicles detected in spray area ✓             │
│     │  • Water supply pressure: OK (12 bar) ✓            │
│     │  • Drainage pumps: INHIBITED (as required) ✓       │
│     ├─ ETA: Immediate upon activation                      │
│     └─ ⚠️ AWAITING OPERATOR DECISION                       │
│        [EXECUTE NOW] [DELAY 60s] [CANCEL]                 │
│                                                             │
│  4. [MANUAL] Close tunnel to all traffic                   │
│     ├─ Rationale: Safety critical incident                 │
│     ├─ Confidence: High (89%)                              │
│     ├─ Impact: HIGH - Traffic disruption major             │
│     ├─ Equipment: VMS, Lane signs, barriers                │
│     ├─ Considerations:                                     │
│     │  • ~45 vehicles currently in tunnel                 │
│     │  • Exit clearance time: 3-5 minutes                 │
│     │  • Alternative route: Highway 2 available           │
│     ├─ Closure Plan: Standard fire incident closure        │
│     └─ ⚠️ RECOMMENDED - AWAITING OPERATOR DECISION         │
│        [FULL CLOSURE] [PARTIAL (1 lane)] [DELAY] [CANCEL] │
│                                                             │
│  5. [MANUAL] Notify emergency services                     │
│     ├─ Rationale: Fire incident requires fire brigade      │
│     ├─ Services: Fire brigade, Ambulance (standby)         │
│     ├─ ETA: Fire brigade 8 minutes                         │
│     └─ ✅ COMPLETED (16:24:12) - Fire brigade en route     │
│                                                             │
│  6. [INFO] Traffic queue building at entry                 │
│     ├─ Location: Ch -0.2km (before entry)                  │
│     ├─ Length: ~800m, 45 vehicles                          │
│     ├─ Suggestion: Activate variable message signs on      │
│     │              approach roads                           │
│     └─ 💬 Advisory only - no action required               │
└────────────────────────────────────────────────────────────┘
```

### 7.5 Consequence Visualization

**Before Action Execution**:
- Show predicted outcome in UI
- 2D Mode: Animated diagram showing affected equipment
- 3D Mode: Semi-transparent preview of action
  - Deluge activation → show water spray particles (50% opacity)
  - Lane closure → show lane signs turning red (preview overlay)
  - Ventilation increase → show airflow particles speeding up

**During Action Execution**:
- Progress bar for actions taking time
- Real-time feedback from equipment
- Status updates as equipment responds

**After Action Execution**:
- Confirmation message
- Actual outcome vs. predicted outcome
- Effectiveness assessment (from Rule Engine)
- Option to reverse action if appropriate

### 7.6 Learning & Adaptation

**Incident History Analysis**:
- Store all incidents with actions taken and outcomes
- ML/AI engine analyzes patterns:
  - Which actions were most effective?
  - How long did resolution take?
  - Were predictions accurate?
- Improve recommendations over time

**Operator Feedback Loop**:
- After incident resolution, operator can rate:
  - Recommendation quality (1-5 stars)
  - Automation appropriateness
  - Missing recommendations
- Feedback used to tune Rule Engine

---

## 8. SCADA Integration Layer

### 8.1 Architecture Overview

**Control Room App Position in Stack**:
```
┌─────────────────────────────────────────────────────────────┐
│                    OPERATOR INTERFACE                        │
│              (Dual-screen: Info + Action)                    │
│                 (2D mode + 3D Digital Twin)                  │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           │ Application API
                           │
┌──────────────────────────▼──────────────────────────────────┐
│              CONTROL ROOM APP (Holistic Layer)              │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Event    │  │ Decision │  │ Screen   │  │ Data     │   │
│  │ Manager  │  │ Support  │  │ Manager  │  │ Fusion   │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│                                                              │
│  ┌────────────────────────────────────────────────────┐     │
│  │         SCADA ABSTRACTION LAYER                    │     │
│  │  • Protocol translation                            │     │
│  │  • Data normalization                              │     │
│  │  • Command routing                                 │     │
│  │  • Status aggregation                              │     │
│  └────────────────────────────────────────────────────┘     │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           │ OPC UA, MODBUS, Proprietary
                           │
┌──────────────────────────▼──────────────────────────────────┐
│                  SCADA SYSTEMS (Subsystems)                  │
│                                                              │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────────┐ │
│  │ Lighting  │ │Ventilation│ │   Fire    │ │  Traffic   │ │
│  │  SCADA    │ │  SCADA    │ │  Safety   │ │   SCADA    │ │
│  │           │ │           │ │  SCADA    │ │            │ │
│  │ • Vendor A│ │ • Vendor B│ │ • Vendor C│ │ • Vendor D │ │
│  │ • MODBUS  │ │ • OPC UA  │ │ • BACnet  │ │ • NTCIP    │ │
│  └─────┬─────┘ └─────┬─────┘ └─────┬─────┘ └──────┬─────┘ │
│        │             │             │              │        │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────────┐ │
│  │ Drainage  │ │Electrical │ │   CCTV    │ │   PMCS     │ │
│  │  SCADA    │ │Distribution│ │  System   │ │  (General) │ │
│  │ • Vendor E│ │ • Vendor F │ │ • Vendor G│ │ • Vendor H │ │
│  │ • MODBUS  │ │ • IEC 61850│ │ • Onvif   │ │ • OPC UA   │ │
│  └─────┬─────┘ └─────┬─────┘ └─────┬─────┘ └──────┬─────┘ │
└────────┼─────────────┼─────────────┼──────────────┼────────┘
         │             │             │              │
         ▼             ▼             ▼              ▼
   Field Devices  Switchgear     Cameras      General PLCs
```

### 8.2 SCADA Abstraction Layer

**Purpose**: Provide unified interface to disparate SCADA systems

**Key Functions**:

1. **Protocol Translation**
   - Convert between protocols (OPC UA ↔ MODBUS ↔ BACnet ↔ NTCIP)
   - Handle vendor-specific proprietary protocols
   - Maintain protocol drivers for each SCADA system

2. **Data Normalization**
   - Convert all data to common format
   - Standardize units (lux, RPM, ppm, °C, etc.)
   - Synchronize timestamps to common time base
   - Handle data quality flags

3. **Command Routing**
   - Route control commands to appropriate SCADA system
   - Translate high-level commands to SCADA-specific commands
   - Handle command acknowledgment and feedback
   - Implement command priority and override logic

4. **Status Aggregation**
   - Combine status from multiple SCADA systems
   - Calculate derived values (e.g., average air quality across all sensors)
   - Detect conflicts (e.g., two systems reporting different values)
   - Provide system health status

### 8.3 Example Data Flows

**Reading Sensor Data**:
```
1. Field Sensor (CO detector) → Measured value: 45 ppm
   ↓
2. Environmental Monitoring SCADA (Vendor-specific format)
   ↓ MODBUS protocol
3. SCADA Abstraction Layer
   • Receives MODBUS message
   • Extracts value: 45
   • Normalizes: {"sensor_id": "CO_12", "value": 45, "unit": "ppm", "timestamp": 1234567890, "quality": "good"}
   ↓ JSON over MQTT
4. Control Room App - Data Fusion
   • Updates sensor database
   • Publishes to UI layer
   ↓ WebSocket
5. UI (Info Screen & Action Screen)
   • 2D Mode: Updates sensor value in table
   • 3D Mode: Updates color of sensor 3D model (green/yellow/red)
```

**Sending Control Command**:
```
1. Operator clicks [ACTIVATE] button for Deluge System (Action Screen)
   ↓
2. UI sends command to Control Room App
   {"command": "activate_deluge", "zone": 3, "operator": "john_smith"}
   ↓
3. Decision Support System validates
   • Check prerequisites (e.g., drainage pumps inhibited)
   • Log command
   ↓
4. SCADA Abstraction Layer receives command
   • Looks up deluge zone 3 → Fire Safety SCADA (BACnet)
   • Translates to BACnet command: WriteProperty(Deluge_Zone_3, Status, Active)
   ↓ BACnet protocol
5. Fire Safety SCADA
   • Receives command
   • Activates deluge nozzles
   • Sends acknowledgment
   ↓ BACnet acknowledgment
6. SCADA Abstraction Layer
   • Receives acknowledgment
   • Normalizes: {"result": "success", "equipment": "Deluge_Zone_3", "status": "active"}
   ↓ JSON over MQTT
7. Control Room App
   • Updates equipment status in database
   • Publishes to UI
   ↓ WebSocket
8. UI (Action Screen)
   • Shows confirmation message: "Deluge Zone 3 activated"
   • 2D Mode: Status changes to "ACTIVE" with green checkmark
   • 3D Mode: Water spray particles start animating
```

### 8.4 SCADA System Details

**Typical SCADA Systems in Tunnel**:

1. **Lighting SCADA**
   - Protocol: MODBUS RTU/TCP or DALI
   - Vendor: Philips, Osram, Schreder, etc.
   - Control: Individual luminaire brightness, zone control
   - Monitoring: Status, run hours, faults

2. **Ventilation SCADA**
   - Protocol: OPC UA or MODBUS
   - Vendor: ABB, Siemens, Schneider Electric
   - Control: Fan on/off, speed (VFD), damper position
   - Monitoring: RPM, motor temp, vibration, airflow

3. **Fire Safety SCADA**
   - Protocol: BACnet or proprietary
   - Vendor: Siemens, Honeywell, Johnson Controls
   - Control: Deluge activation, alarm acknowledgment
   - Monitoring: Detector status, extinguisher pressure

4. **Traffic SCADA**
   - Protocol: NTCIP (National Transportation Communications for ITS Protocol)
   - Vendor: SWARCO, Econolite, etc.
   - Control: VMS messages, lane control signals, speed limits
   - Monitoring: Traffic counts, vehicle detection

5. **Drainage SCADA**
   - Protocol: MODBUS or OPC UA
   - Vendor: Grundfos, Flygt, Xylem
   - Control: Pump on/off, speed
   - Monitoring: Sump level, flow rate, run hours

6. **Electrical Distribution SCADA**
   - Protocol: IEC 61850 or MODBUS
   - Vendor: Schneider Electric, ABB, Siemens
   - Monitoring: Voltage, current, power, breaker status, UPS status
   - Control: Breaker control (limited, manual switch priority)

7. **CCTV System**
   - Protocol: Onvif, RTSP, vendor-specific
   - Vendor: Axis, Hikvision, Bosch
   - Control: PTZ, presets, recording
   - Monitoring: Camera online status, video analytics

8. **PMCS (Plant Monitoring & Control System)**
   - Protocol: OPC UA or MODBUS
   - General purpose PLC-based system
   - Monitors building services, access control, etc.

---

## 9. Implementation Details

### 9.1 Technical Stack

**Frontend (Dual-screen UI)**:
```
Option 1: Unreal Engine 5
• Both 2D and 3D modes in same engine
• UMG (Unreal Motion Graphics) for 2D UI
• 3D Digital Twin as main viewport
• High performance, photorealistic
• C++ backend, Blueprint for UI logic

Option 2: Web-based (for 2D) + Unreal (for 3D)
• 2D Mode: React + WebGL (Three.js)
• 3D Mode: Unreal Engine 5 (Pixel Streaming)
• Advantage: 2D more flexible, easier to update
• Disadvantage: Two tech stacks to maintain

Recommendation: Unreal Engine 5 for both modes
• Single codebase
• Seamless mode switching
• Can use Slate/UMG for 2D (powerful enough)
```

**Backend (Control Room App)**:
```
Node.js or Python FastAPI
• REST API for UI
• WebSocket for real-time updates
• MQTT client for SCADA integration
• OPC UA client library
• MODBUS TCP client
• PostgreSQL or MySQL for database
• Redis for caching
• Docker for deployment
```

**SCADA Integration**:
```
OPC UA: Open62541 library (C++) or node-opcua (Node.js)
MODBUS: pymodbus (Python) or node-modbus (Node.js)
BACnet: bacpypes (Python)
NTCIP: Custom implementation or vendor SDK
```

### 9.2 Data Flow Performance

**Info Screen Update Frequency**:
- Overview schematic: 2 Hz (500ms)
- Event list: Real-time (immediate on new event)
- System status: 1 Hz (1 second)
- 3D Digital Twin: 60 FPS (16ms) rendering, data updates 2-10 Hz depending on sensor

**Action Screen Update Frequency**:
- Equipment status: 1-5 Hz depending on criticality
  - Fire/smoke sensors: 5 Hz (200ms)
  - Ventilation: 2 Hz (500ms)
  - Lighting: 1 Hz (1 second)
- Video feeds: 30 FPS (RTSP)
- Environmental graphs: 1 Hz with 10-minute history
- Decision support: Updated on incident change or every 5 seconds

**Network Bandwidth**:
- Per screen: 5-10 Mbps (with video)
- Without video: <1 Mbps
- SCADA polling: <500 Kbps
- Total control room: 20-30 Mbps (comfortable margin)

### 9.3 Screen Management Logic

**Screen Routing Algorithm**:
```python
class ScreenManager:
    def __init__(self):
        self.info_screen = InfoScreen()
        self.action_screen = ActionScreen()
        self.active_incidents = []
        
    def on_new_incident(self, incident):
        # Add to Info Screen immediately
        self.info_screen.add_incident(incident)
        
        # Decision: Should this go to Action Screen?
        if incident.priority == "CRITICAL":
            # Critical always goes to Action Screen
            self.action_screen.add_incident(incident)
        elif self.action_screen.incident_count() < 4:
            # Auto-populate if space available
            self.action_screen.add_incident(incident)
        else:
            # Queue for later
            self.incident_queue.append(incident)
            self.info_screen.show_notification("Incident queued for Action Screen")
    
    def on_incident_resolved(self, incident):
        # Remove from both screens
        self.info_screen.remove_incident(incident)
        self.action_screen.remove_incident(incident)
        
        # If queue has incidents, pop next priority
        if len(self.incident_queue) > 0:
            next_incident = self.incident_queue.pop(0)
            self.action_screen.add_incident(next_incident)
    
    def on_operator_click_incident(self, incident):
        # Operator clicked incident in Info Screen
        # Force display in Action Screen
        if self.action_screen.incident_count() >= 4:
            # Replace lowest priority incident
            lowest = self.action_screen.get_lowest_priority_incident()
            self.action_screen.remove_incident(lowest)
            self.incident_queue.insert(0, lowest)  # Move to front of queue
        
        self.action_screen.add_incident(incident)
        self.action_screen.focus_incident(incident)  # Highlight it
```

### 9.4 Decision Support Integration

**Rule Engine Connection**:
```python
class DecisionSupportSystem:
    def __init__(self, rule_engine):
        self.rule_engine = rule_engine
        self.active_recommendations = {}
        
    def analyze_incident(self, incident):
        # Get incident context
        context = {
            "incident_type": incident.type,
            "location": incident.location,
            "equipment_nearby": self.get_equipment_in_area(incident.location, radius=200),
            "environmental_data": self.get_environmental_data(incident.location),
            "traffic_situation": self.get_traffic_data(incident.location),
            "time_of_day": datetime.now(),
            "weather": self.get_weather()
        }
        
        # Get recommendations from Rule Engine
        recommendations = self.rule_engine.get_recommendations(context)
        
        # Classify recommendations
        for rec in recommendations:
            if rec.confidence > 0.85 and rec.risk == "LOW":
                rec.automation_level = "AUTO"
                rec.countdown = 15  # seconds
            elif rec.confidence > 0.70 and rec.impact == "MEDIUM":
                rec.automation_level = "MANUAL"
            else:
                rec.automation_level = "ADVISORY"
        
        return recommendations
    
    def start_automation_countdown(self, recommendation):
        # Start countdown timer for automated action
        timer = CountdownTimer(recommendation.countdown)
        timer.on_complete = lambda: self.execute_action(recommendation)
        timer.on_cancel = lambda: self.cancel_action(recommendation)
        
        # Display in Action Screen
        self.action_screen.show_countdown(recommendation, timer)
    
    def execute_action(self, recommendation):
        # Operator approved or countdown finished
        # Send command to SCADA
        command = self.translate_recommendation_to_command(recommendation)
        result = self.scada_layer.send_command(command)
        
        # Log action
        self.log_action(recommendation, result, operator=self.current_operator)
        
        # Update UI
        self.action_screen.show_result(recommendation, result)
```

---

## 10. Hardware Configuration

### 10.1 Control Room Workstation Setup

**Dual-Screen Configuration**:
```
┌─────────────────────────────────────────────────────┐
│  CONTROL ROOM OPERATOR WORKSTATION                  │
├─────────────────────────────────────────────────────┤
│                                                      │
│  ┌────────────────────┐  ┌────────────────────────┐│
│  │  Monitor 1 (Left)  │  │  Monitor 2 (Right)    ││
│  │  Info Screen       │  │  Action Screen        ││
│  │  32" 4K Display    │  │  32" 4K Display       ││
│  │  3840 x 2160       │  │  3840 x 2160          ││
│  │  DisplayPort 1     │  │  DisplayPort 2        ││
│  └────────────────────┘  └────────────────────────┘│
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │  Workstation PC                              │   │
│  │  • CPU: Intel i9-12900K / AMD Ryzen 9 5900X │   │
│  │  • GPU: NVIDIA RTX 4070 (12GB VRAM)         │   │
│  │        (for dual 4K + 3D Digital Twin)      │   │
│  │  • RAM: 32GB DDR4/DDR5                      │   │
│  │  • Storage: 1TB NVMe SSD                    │   │
│  │  • Network: Dual 1 Gbps Ethernet (redundant)│   │
│  │  • OS: Windows 11 Pro or Linux              │   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │  Peripherals                                 │   │
│  │  • Keyboard: Standard                        │   │
│  │  • Mouse: Ergonomic                          │   │
│  │  • Headset: Noise-cancelling (for comms)    │   │
│  │  • Foot pedal (optional - for PTZ control)  │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

**Alternative: Video Wall Configuration** (for TOCC/RAMC):
```
┌─────────────────────────────────────────────────────────┐
│  VIDEO WALL SETUP                                       │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────┐  ┌──────────────────┐            │
│  │   Display 1      │  │   Display 2      │            │
│  │   (Info Screen)  │  │   (Action Screen)│            │
│  │   55" 4K         │  │   55" 4K         │            │
│  └──────────────────┘  └──────────────────┘            │
│                                                          │
│  Video Wall Controller                                  │
│  • Input: 2x DisplayPort from workstation               │
│  • Output: 2x HDMI to displays                         │
│  • Bezel compensation                                   │
│                                                          │
│  Workstation: Same as above but GPU: RTX 4080          │
│              (for larger displays + 3D)                 │
└─────────────────────────────────────────────────────────┘
```

### 10.2 Network Architecture

**Control Room Network**:
```
┌─────────────────────────────────────────────────────────┐
│                    CONTROL ROOM LAN                      │
│                (1 Gbps or 10 Gbps switch)                │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐  │
│  │Operator  │ │Operator  │ │Supervisor│ │  Video   │  │
│  │Workst. 1 │ │Workst. 2 │ │Workst. 3 │ │  Wall    │  │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘  │
│       │            │            │            │         │
│       └────────────┴────────────┴────────────┘         │
│                        │                                │
├────────────────────────┼────────────────────────────────┤
│  ┌────────────────────▼──────────────────────────┐     │
│  │    Control Room App Server (Backend)          │     │
│  │    • Node.js / Python FastAPI                 │     │
│  │    • Database: PostgreSQL                     │     │
│  │    • MQTT Broker                              │     │
│  │    • Redis Cache                              │     │
│  └────────────────────┬──────────────────────────┘     │
│                       │                                 │
├───────────────────────┼─────────────────────────────────┤
│  ┌────────────────────▼──────────────────────────┐     │
│  │    SCADA Integration Gateway                  │     │
│  │    • OPC UA Server/Client                     │     │
│  │    • MODBUS TCP Master                        │     │
│  │    • Protocol converters                      │     │
│  └────────────────────┬──────────────────────────┘     │
│                       │                                 │
├───────────────────────┼─────────────────────────────────┤
│               SCADA Network                             │
│       (Separate VLAN or physical network)               │
│                       │                                 │
│  ┌─────────┐ ┌───────▼───┐ ┌─────────┐ ┌─────────┐   │
│  │Lighting │ │Ventilation│ │  Fire   │ │ Traffic │   │
│  │ SCADA   │ │  SCADA    │ │ Safety  │ │  SCADA  │   │
│  └─────────┘ └───────────┘ └─────────┘ └─────────┘   │
└─────────────────────────────────────────────────────────┘
```

### 10.3 Redundancy & Failover

**Active-Active Configuration**:
- Two identical control rooms (TSB primary, TOCC secondary)
- Both systems receive all data simultaneously
- Either can take control at any time
- Database replication between sites
- Automatic failover if primary site fails

**GPU Redundancy**:
- If GPU fails, fall back to 2D mode only
- 2D mode requires minimal GPU (integrated graphics OK)
- Operator can continue operations in 2D until GPU replaced

---

## 11. Summary & Next Steps

### 11.1 Key Decisions Made

✅ **Dual-Screen Architecture**:
- Info Screen (left): Overview, situational awareness
- Action Screen (right): Incident focus, decision support

✅ **Dual-Mode UI**:
- 2D Mode: Traditional SCADA schematic
- 3D Mode: Photorealistic digital twin
- Independent mode switching per screen

✅ **Event-Driven Display**:
- Info Screen: Always shows all events
- Action Screen: Shows 1-4 events based on priority
- Automatic population + manual override

✅ **Decision Support Integration**:
- Automated actions with countdown
- Manual actions with recommendations
- Consequence visualization before execution

✅ **SCADA Integration**:
- Control Room App sits on top of all SCADA systems
- Unified abstraction layer
- Protocol translation and data normalization

### 11.2 Implementation Priorities

**Phase 1 (Critical)**:
1. Backend SCADA integration layer
2. Info Screen - 2D mode
3. Action Screen - 2D mode (single incident)
4. Basic decision support (manual actions only)
5. Dual-screen window management

**Phase 2 (Essential)**:
6. Action Screen - Multi-incident support (2-4 incidents)
7. Decision support - Automated actions with countdown
8. 3D Digital Twin - Info Screen
9. 3D Digital Twin - Action Screen (single incident)
10. Advanced equipment status displays

**Phase 3 (Advanced)**:
11. 3D Digital Twin - Action Screen multi-incident
12. ML/AI-based recommendations
13. VR mode for training
14. Mobile/tablet operator interface

### 11.3 Next Actions

1. **Approve Architecture** - Stakeholder sign-off on dual-screen approach
2. **SCADA System Inventory** - List all SCADA systems, protocols, vendors
3. **Prototype Development** - 8-week prototype of Info + Action screens (2D mode)
4. **Operator Workshop** - Gather feedback on UI mockups
5. **Procurement** - Order dual-screen workstations and GPUs

---

**Document Complete**

**Version**: 1.0  
**Last Updated**: October 27, 2025  
**Analyst**: Mary (Business Analyst)

