# Tunnel Management System - UI Specifications for Unreal/Unity with AR/VR
**Game Engine Implementation Guide**

## Document Information
- **Target Platforms**: Unreal Engine 5 / Unity
- **UI Paradigm**: Real-time 3D with AR/VR capabilities
- **Source**: SyRS v20 Feb 2025 & URS v30 Jan 2025
- **Date**: October 27, 2025

---

## Table of Contents
1. [Architecture for Game Engine Implementation](#1-architecture-for-game-engine-implementation)
2. [3D Visualization Requirements](#2-3d-visualization-requirements)
3. [UI Component Modules (Detailed)](#3-ui-component-modules-detailed)
4. [AR/VR Use Cases](#4-arvr-use-cases)
5. [Real-time Data Integration](#5-real-time-data-integration)
6. [Subsystem Visualization Specifications](#6-subsystem-visualization-specifications)
7. [Interaction Design for VR/AR](#7-interaction-design-for-vrar)
8. [Technical Implementation Strategy](#8-technical-implementation-strategy)
9. [Performance Requirements](#9-performance-requirements)
10. [Asset Requirements](#10-asset-requirements)

---

## 1. Architecture for Game Engine Implementation

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    UNREAL/UNITY FRONTEND                     │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              VR HEADSET MODE                           │  │
│  │  - Immersive 3D Tunnel Environment                     │  │
│  │  - First-person walkthrough                            │  │
│  │  - Hand controller interactions                        │  │
│  │  - Spatial audio for alerts                            │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              AR TABLET/HEADSET MODE                    │  │
│  │  - Overlay data on physical control room               │  │
│  │  - Holographic sensor data                             │  │
│  │  - Spatial incident markers                            │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              DESKTOP/VIDEO WALL MODE                   │  │
│  │  - Traditional 2D/3D synthetic map                     │  │
│  │  - Multi-monitor support                               │  │
│  │  - Mouse/keyboard/touch interaction                    │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │              COMMON RENDERING ENGINE                   │  │
│  │  - Real-time 3D tunnel model                           │  │
│  │  - Dynamic lighting simulation                         │  │
│  │  - Particle systems (smoke, fire, ventilation)        │  │
│  │  - Traffic simulation                                  │  │
│  │  - Equipment state visualization                       │  │
│  │  - Sensor data overlays                                │  │
│  └────────────────────────────────────────────────────────┘  │
└───────────────────────────┬───────────────────────────────────┘
                            │
                   REST API / WebSocket
                            │
┌───────────────────────────▼───────────────────────────────────┐
│                    BACKEND SERVICES                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │  Platform    │  │ Rule Engine  │  │  Database    │       │
│  │  Software    │  │              │  │   (MySQL)    │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│         MQTT/MODBUS/OPC/REST                                  │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│              FIELD SENSORS & ACTUATORS                        │
│  CCTV | VAID | Environmental | Lighting | Ventilation | ...  │
└───────────────────────────────────────────────────────────────┘
```

### 1.2 Game Engine Selection Criteria

#### Unreal Engine 5 (Recommended for High-Fidelity)
**Advantages**:
- **Nanite**: Virtual geometry for massive tunnel models without performance loss
- **Lumen**: Real-time global illumination (perfect for lighting visualization)
- **MetaHuman**: For operator avatars in training mode
- **Niagara**: Advanced particle systems (smoke, fire, ventilation airflow)
- **Blueprint + C++**: Rapid prototyping with performance optimization
- **Built-in VR/AR**: Native support for VR/AR with XR templates
- **Chaos Physics**: Realistic destruction simulation for incident scenarios

**Use Cases**:
- High-fidelity 3D tunnel visualization
- Photorealistic training simulations
- Complex particle effects (fire, smoke, ventilation)

#### Unity (Recommended for Cross-Platform & AR)
**Advantages**:
- **AR Foundation**: Excellent AR support (HoloLens, ARKit, ARCore)
- **Universal Render Pipeline (URP)**: Optimized for multi-platform
- **XR Interaction Toolkit**: Comprehensive VR/AR interaction system
- **Asset Store**: Extensive library of UI components and assets
- **C#**: More accessible for rapid development
- **WebGL Export**: Can deploy to web browsers for remote access

**Use Cases**:
- AR overlays in physical control rooms
- Multi-platform deployment (Desktop, Mobile, VR, AR)
- Rapid iteration and deployment

### 1.3 Hybrid Architecture Recommendation

**Primary System**: Unreal Engine 5 for main control room displays
**Secondary System**: Unity for AR/mobile operator interfaces
**Integration**: Both communicate with same backend via REST API/WebSocket

---

## 2. 3D Visualization Requirements

### 2.1 Tunnel 3D Model Specifications

#### Base Geometry
- **Format**: Import from AutoCAD (DWG/DXF), Civil3D, Revit, or GIS (Shapefile)
- **Level of Detail (LOD)**:
  - LOD0: Full detail for camera close-ups (0-50m)
  - LOD1: Medium detail for general view (50-200m)
  - LOD2: Low detail for distant sections (200m+)
  - LOD3: Simplified for mini-map/overview

#### Tunnel Components to Model
1. **Road Surface**
   - Lane markings (dynamic color based on lane status)
   - Distance markers
   - Road texture with wear/damage simulation

2. **Tunnel Structure**
   - Walls (concrete texture, weathering)
   - Ceiling/roof
   - Emergency exits
   - Cross-passages
   - Escape galleries
   - Service galleries

3. **Equipment (All must be interactive)**
   - Luminaires (individual addressable)
   - CCTV cameras (with field-of-view cones)
   - Ventilation fans
   - Fire extinguishers
   - Emergency call boxes (ECB)
   - Variable Message Signs (VMS)
   - Lane Control Signals (LCS)
   - Sensors (visible as small devices or icons)
   - Deluge/suppression systems
   - Emergency panels

4. **Dynamic Elements**
   - Traffic (vehicles with AI movement)
   - Smoke/fire particle systems
   - Water (drainage, flooding)
   - Lighting changes (brightness, color temperature)
   - Ventilation airflow visualization

### 2.2 Color-Coded Zone System

#### Lighting Zones (Layer 1)
```cpp
// Zone definitions for material system
enum LightingZone {
    THRESHOLD_ZONE,     // Color: Yellow (#FFC107)
    TRANSITION_ZONE,    // Color: Orange (#FF9800)
    INTERIOR_ZONE       // Color: Blue (#2196F3)
}
```

**Visual Representation**:
- Semi-transparent overlay on floor/ceiling
- Animated boundary lines
- Brightness indicators showing lux levels
- Real-time updates based on sensor data

#### Fire Life Safety Zones (Layer 2)
```cpp
enum FireSafetyZone {
    ALARM_ZONE,         // Color: Red (#F44336) - Pulsing
    ACTIVATED_ZONE,     // Color: Dark Red (#B71C1C) - Solid
    NORMAL_ZONE         // Color: Green (#4CAF50) - Transparent
}
```

**Visual Representation**:
- Floor/wall colored overlay
- 3D volumetric boundaries
- Emergency exit highlighting
- Cross-passage door status indicators

#### Traffic Monitoring Zones (Layer 3)
```cpp
enum TrafficZone {
    LANE_1,             // Color: Cyan (#00BCD4)
    LANE_2,             // Color: Light Blue (#03A9F4)
    LANE_3,             // Color: Blue (#2196F3)
    MONITORING_SECTION  // Color: Purple (#9C27B0)
}
```

**Visual Representation**:
- Lane dividers with color glow
- Section boundaries with markers
- Traffic density heat map overlay
- Vehicle count indicators

### 2.3 Overlay/Underlay System

#### Layer Stack (Bottom to Top)
```
Layer 0: Base Tunnel 3D Model
Layer 1: Structural Elements (walls, ceiling, exits)
Layer 2: Road Surface & Lane Markings
Layer 3: Equipment Models (lights, cameras, fans)
Layer 4: Lighting Zone Overlay (semi-transparent)
Layer 5: Fire Safety Zone Overlay (semi-transparent)
Layer 6: Traffic Zone Overlay (semi-transparent)
Layer 7: Sensor Data Visualization (icons, values)
Layer 8: Incident Markers (pulsing icons)
Layer 9: UI Widgets (2D overlays, labels)
Layer 10: Alert Popups (modal dialogs)
```

#### Dynamic Layer Control
- Operators can toggle layers on/off via UI
- Layer opacity adjustable per layer
- "Focus Mode" - highlights only relevant layers for current incident
- "Training Mode" - shows all layers with annotations

### 2.4 Camera System

#### Camera Modes

**1. Free Camera (Default)**
- WASD movement, mouse look
- Scroll to adjust speed
- Q/E for up/down
- Shift for sprint, Ctrl for slow

**2. Orbit Camera**
- Click equipment → auto-orbit around it
- Inspect mode for detailed status

**3. CCTV Camera**
- Switch to any physical CCTV camera view
- PTZ controls (Pan, Tilt, Zoom)
- Preset positions
- Auto-follow incidents

**4. Section View Camera**
- Predefined views for each monitoring section
- Hotkey navigation (1-9 for sections)

**5. Overview Camera (Bird's Eye)**
- Top-down strategic view
- Zoom from full tunnel to section detail
- Mini-map navigation

**6. VR First-Person**
- Walk through tunnel at operator height
- Teleport locomotion option
- Hand controllers for equipment interaction

**7. AR Camera (Passthrough)**
- Physical world with data overlays
- Spatial anchoring to control room displays
- Hand gesture interactions

---

## 3. UI Component Modules (Detailed)

### 3.1 Minimum UI Screens (PMCS_FR1)

Based on requirements, implement these 12 core screens:

#### 1. Overview Screen
**3D Implementation**:
- Full 3D tunnel model view
- Geographic map underlay (Google Maps style)
- All ITS equipment superimposed as 3D models or icons
- Real-time traffic flow visualization
- Status summary panel (2D overlay)

**VR Mode**:
- 360° panoramic view from control room
- Holographic tunnel floating in center
- Gesture to rotate/zoom

**AR Mode**:
- Projects 3D tunnel onto table/wall
- Pinch to zoom, swipe to rotate

**UI Elements**:
```
┌──────────────────────────────────────────────────┐
│  Overview: Tunnel A - Main Bore                  │
│  ┌──────────────────────────────────────┐        │
│  │                                      │        │
│  │      [3D Tunnel View]                │        │
│  │                                      │        │
│  │                                      │        │
│  └──────────────────────────────────────┘        │
│  Status: NORMAL | Incidents: 0 | Traffic: MEDIUM │
│  ┌────────┬────────┬────────┬────────┐          │
│  │Lighting│Ventila │Fire    │Traffic │          │
│  │  OK ✓  │tion OK │Safety  │ MEDIUM │          │
│  │        │   ✓    │  OK ✓  │        │          │
│  └────────┴────────┴────────┴────────┘          │
└──────────────────────────────────────────────────┘
```

#### 2. Schematic Map
**3D Implementation**:
- Simplified tunnel schematic (technical drawing style)
- All safety systems as icons/symbols
- Color-coded by system type
- Interactive (click for details)

**Layout**:
- Linear tunnel representation (unfolded)
- Equipment positioned at correct chainage
- Status indicators (green/yellow/red)
- Expandable sections for detail

#### 3. Lighting Screen
**3D Implementation**:
- Lighting zones color-coded overlay
- Individual luminaire models (LED representations)
- Brightness heat map
- Failed luminaires highlighted in red

**Interactive Elements**:
- Click luminaire → status popup
- Zone selection → control panel appears
- Brightness slider → real-time preview
- Manual override controls

**Visualization**:
- Real-time lux level simulation using Lumen/lighting system
- Day/night cycle simulation
- Emergency lighting mode visualization

#### 4. Electrical Distribution Screen
**3D Implementation**:
- Single-line diagram in 3D space
- Transformers, breakers, UPS as 3D models or enhanced icons
- Power flow animation (flowing particles)
- Status LEDs on equipment

**UI Elements**:
```
┌────────────────────────────────────────┐
│  HV Transformer                         │
│  ├─ Incoming: 33kV | 1250A | OK ✓      │
│  └─ Outgoing: 11kV | 850A | OK ✓       │
│                                         │
│  LV Distribution                        │
│  ├─ Main Breaker: CLOSED | 2000A       │
│  ├─ Sub-circuit 1: 850A | OK ✓         │
│  └─ Sub-circuit 2: 620A | OK ✓         │
│                                         │
│  UPS Status                             │
│  ├─ Inverter: ONLINE | 98% Efficiency  │
│  ├─ Battery: 100% | 4.2h Runtime       │
│  └─ Bypass: AVAILABLE                  │
│                                         │
│  Generator                              │
│  ├─ Status: STANDBY | Test: OK         │
│  ├─ Fuel: 85% (680L)                   │
│  └─ Run Hours: 1,247h                  │
└────────────────────────────────────────┘
```

#### 5. Tunnel Ventilation Screen
**3D Implementation**:
- Ventilation fans as 3D rotating models
- Airflow visualization (particle system)
  - Direction indicated by particle flow
  - Speed indicated by particle velocity
  - Color indicates air quality (blue=clean, yellow=moderate, red=poor)
- Anemometer readings displayed spatially
- Pressure differential visualization

**Air Quality Management Levels**:
```
Level 1: Clear - No operation
  └─ Visualization: Blue particles, slow flow

Level 2: Ventilation Stage 1
  └─ Visualization: Yellow particles, predetermined fans spinning

Level 3: Ventilation Stage 2  
  └─ Visualization: Orange particles, all fans spinning fast

Level 4: Urgent Alarm
  └─ Visualization: Red particles, pulsing alert, tunnel closure warning
```

**VR Mode**:
- "Feel" the airflow (haptic feedback if supported)
- See actual wind direction and speed
- Walk through airflow visualization

#### 6. Service Building Security Screen
**3D Implementation**:
- 3D floor plan of service building
- Door status (open/closed/locked)
- Access control points highlighted
- Camera coverage cones
- Motion detection zones

**Security Events**:
- Door breach → flashing door in 3D
- Unauthorized access → alarm overlay
- Camera tamper → camera turns red

#### 7. Environmental Screen
**3D Implementation**:
- Sensor positions in 3D tunnel
- Data displayed as floating HUD elements near sensors
- Heat map overlay for:
  - Temperature
  - CO/CO2/NOx levels
  - Visibility (fog/smoke)
  - Wind speed

**Sensor Types to Visualize**:
- Air quality sensors (CO, CO2, NOx, visibility)
- Anemometers (wind speed & direction)
- Temperature sensors
- Flood monitoring sensors
- Gas detection sensors (sump areas)

**Real-time Graphs**:
- Time-series data for each sensor
- Threshold lines (warning/alarm levels)
- Multi-sensor comparison

#### 8. Pumping Screen
**3D Implementation**:
- Sump locations in 3D
- Water level visualization (animated rising/falling)
- Pump models (animated when running)
- Drainage flow animation

**Status Indicators**:
- Sump level (percentage, color-coded)
- Pump status (running/standby/fault)
- Flow rate
- Run hours

#### 9. Fire Safety Screen
**3D Implementation**:
- Fire detection zones color-coded
- Fire extinguisher locations (3D models)
- Deluge system coverage visualization
- Escape route highlighting
- Smoke detection sensors

**Incident Visualization**:
- Fire detected → flames particle effect at location
- Smoke spread simulation (volumetric fog)
- Heat visualization (red glow expanding)
- Activation of suppression systems (water spray particles)

**Emergency Response**:
- Escape route illumination
- Cross-passage door auto-open visualization
- Ventilation mode change (airflow direction change)

#### 10. System Network Communications Screen
**3D Implementation**:
- Network topology as 3D graph
- Devices as nodes (servers, switches, PLCs)
- Connections as lines (color = status, thickness = bandwidth)
- Data flow animation (packets moving along lines)

**Status Monitoring**:
- Latency heat map
- Bandwidth utilization
- Device health status
- Failover status

#### 11. Parameter Input Screens
**3D Implementation**:
- Context-sensitive input panels
- Appears near relevant equipment in 3D space
- Slider controls, numeric inputs, dropdowns
- Real-time preview of changes

**VR Interaction**:
- Laser pointer selection
- Virtual keyboard for text input
- Hand gesture for sliders (grab & drag)

#### 12. Lighting Control Popup Window
**3D Implementation**:
- Modal dialog in VR/AR (floating panel)
- Desktop: traditional popup
- Controls for selected zone/luminaire
- Brightness slider with preview
- Schedule settings
- Emergency override button

### 3.2 Alarm & Event Monitoring (PMCS_FR2)

#### Alarm Priority System

**URGENT (Red) - Safety Related**
```
Visual: Pulsing red border, alarm sound
VR: Haptic pulse, spatial audio from incident direction
AR: Red holographic marker at incident location
Actions: Auto-zoom to location, operator acknowledgment required
```

**ALERT (Yellow) - System/Maintenance**
```
Visual: Steady yellow border, notification sound
VR: Gentle haptic, notification in peripheral vision
AR: Yellow holographic marker
Actions: Operator acknowledgment required
```

**RECORD (Green) - Normal Operation**
```
Visual: Subtle notification
VR: No interruption, added to log
AR: Small green indicator
Actions: Auto-logged, no acknowledgment needed
```

#### Alarm List Window (Floating Panel)
```
┌────────────────────────────────────────────────────────┐
│  Active Alarms                    [Filter ▼] [Clear]   │
├─────┬──────────┬────────────┬────────────┬────────────┤
│Pri  │ System   │ Time       │ Location   │ Status     │
├─────┼──────────┼────────────┼────────────┼────────────┤
│URGT │Fire      │16:23:45    │Ch 1.2km    │ACTIVE  🔴  │
│ALRT │Lighting  │16:20:12    │Ch 0.8km    │ACK     🟡  │
│RECD │Ventilat. │16:15:00    │Fan 3       │RESOLVED ✓  │
└─────┴──────────┴────────────┴────────────┴────────────┘
```

**VR Mode**:
- Floating tablet in operator's virtual space
- Can be repositioned/resized
- Tap to acknowledge
- Swipe to dismiss

**AR Mode**:
- Appears as holographic panel in physical space
- Hand gesture to acknowledge
- Voice command: "Acknowledge alarm"

---

## 4. AR/VR Use Cases

### 4.1 VR Control Room Operator Mode

#### Primary Use Cases

**1. Training & Drill Scenarios**
- **Scenario**: Fire in tunnel at chainage 1.2km
- **VR Experience**:
  - Operator sees smoke rising from tunnel in 3D view
  - Fire alarm popup appears with camera feed
  - Walk around tunnel to inspect from different angles
  - Use hand controllers to interact with controls
  - Practice emergency procedures without affecting live system
  
**Implementation**:
- Training mode flag in system
- Simulated sensor data
- No commands sent to actual actuators
- Performance scoring system
- Replay functionality to review actions

**2. Immersive Incident Response**
- **Scenario**: Multiple simultaneous incidents
- **VR Experience**:
  - Multi-incident view with spatial separation
  - Turn head to switch between incidents
  - Prioritize using gesture (point & select)
  - Voice commands: "Zoom to incident 2", "Activate ventilation"
  
**3. Remote Operation**
- **Scenario**: Operator working from home
- **VR Experience**:
  - Full virtual control room environment
  - Same interface as physical control room
  - Secure VPN connection to backend
  - Multi-user (see other operators as avatars)

**4. 3D Tunnel Inspection**
- **Scenario**: Pre-maintenance planning
- **VR Experience**:
  - Walk through virtual tunnel
  - Inspect equipment close-up
  - Identify faulty equipment visually (highlighted in red)
  - Plan maintenance route
  - Place virtual notes/markers for maintenance crew

#### VR Interaction Patterns

**Hand Controllers**:
- **Trigger**: Select/confirm
- **Grip**: Grab & move UI panels
- **Thumbstick**: Navigate/teleport
- **A/X Button**: Context menu
- **B/Y Button**: Back/cancel

**Gesture Recognition**:
- **Point**: Select equipment
- **Pinch**: Zoom in/out
- **Swipe**: Navigate through menus
- **Palm Up**: Open menu
- **Wave**: Dismiss alerts

**Voice Commands**:
```
"Show lighting"           → Switch to lighting screen
"Zoom to incident"        → Camera to incident location
"Acknowledge alarm"       → Acknowledge active alarm
"Activate ventilation"    → Open ventilation control
"Close tunnel"            → Initiate tunnel closure procedure
"Show traffic"            → Display traffic monitoring
"Camera one"              → Switch to CCTV camera 1
"Emergency mode"          → Switch to emergency operating mode
```

### 4.2 AR Field Operator Mode

#### Primary Use Cases

**1. On-Site Maintenance with AR Assistance**
- **Scenario**: Technician replacing faulty luminaire
- **AR Experience** (via tablet or AR glasses):
  - Point device at luminaire
  - AR overlay shows:
    - Equipment ID and status
    - Wiring diagram
    - Step-by-step replacement instructions
    - Safety warnings
    - Last maintenance date
  - Hands-free guidance (AR glasses)
  - Voice confirmation when task complete

**2. AR Control Room Enhancement**
- **Scenario**: Operator in physical control room
- **AR Experience** (via HoloLens/Magic Leap):
  - Look at video wall → see 3D tunnel projected
  - Holographic sensor data floating in space
  - Spatial incident markers
  - X-ray view (see equipment behind walls)
  - Multi-user collaboration (multiple operators see same AR view)

**3. Emergency Response Coordination**
- **Scenario**: Fire brigade entering tunnel
- **AR Experience** (via tablet):
  - Real-time smoke spread visualization
  - Safe route highlighting
  - Location of fire extinguishers
  - Hazard warnings
  - Communication with control room

**4. Training Mode AR**
- **Scenario**: New operator training in physical control room
- **AR Experience**:
  - AR annotations on physical equipment
  - Virtual incidents overlaid on displays
  - Step-by-step guided procedures
  - Performance feedback

#### AR Interaction Patterns

**Gaze & Gesture (HoloLens)**:
- **Air Tap**: Select
- **Bloom**: Open menu
- **Voice**: Commands
- **Head Tracking**: Look to navigate

**Tablet AR**:
- **Point**: See equipment status
- **Tap**: Select/interact
- **Pinch**: Zoom
- **Drag**: Move virtual objects

**Spatial Anchors**:
- AR content persists in physical space
- Multiple operators see same holographic objects
- Anchors tied to GPS coordinates for tunnel entrances
- Indoor positioning for tunnel interior

### 4.3 Mixed Reality Collaboration

#### Multi-User VR Control Room
- Multiple operators in same virtual space
- See each other as avatars
- Voice chat with spatial audio
- Shared view of tunnel
- Collaborative incident response
- Supervisor can oversee all operators

#### Remote Expert AR Assistance
- Expert in VR control room
- Field technician with AR device
- Expert sees what technician sees
- Expert can draw AR annotations
- Real-time guidance
- Record session for training

---

## 5. Real-time Data Integration

### 5.1 Data Flow Architecture

```cpp
// Unreal Engine C++ Example
class ATunnelDataManager : public AActor
{
    // WebSocket connection to backend
    TSharedPtr<IWebSocket> WebSocket;
    
    // MQTT client for pub/sub
    TSharedPtr<FMQTTClient> MQTTClient;
    
    // Data update events
    UPROPERTY(BlueprintAssignable)
    FOnSensorDataReceived OnSensorDataReceived;
    
    UPROPERTY(BlueprintAssignable)
    FOnIncidentDetected OnIncidentDetected;
    
    UPROPERTY(BlueprintAssignable)
    FOnAlarmTriggered OnAlarmTriggered;
    
    // Subscribe to sensor topics
    void SubscribeToSensors()
    {
        MQTTClient->Subscribe("tunnel/sensors/#", 
            [this](const FString& Topic, const FString& Payload)
            {
                ProcessSensorData(Topic, Payload);
            });
    }
    
    // Update 3D visualizations based on sensor data
    void ProcessSensorData(const FString& Topic, const FString& Payload)
    {
        // Parse JSON
        TSharedPtr<FJsonObject> JsonObject;
        TSharedRef<TJsonReader<>> Reader = 
            TJsonReaderFactory<>::Create(Payload);
        FJsonSerializer::Deserialize(Reader, JsonObject);
        
        // Extract sensor type and value
        FString SensorType = JsonObject->GetStringField("type");
        float Value = JsonObject->GetNumberField("value");
        FString Location = JsonObject->GetStringField("location");
        
        // Update visualization
        if (SensorType == "temperature")
        {
            UpdateTemperatureVisualization(Location, Value);
        }
        else if (SensorType == "co")
        {
            UpdateAirQualityVisualization(Location, Value);
        }
        // ... more sensor types
    }
    
    // Update temperature visualization
    void UpdateTemperatureVisualization(const FString& Location, float Temp)
    {
        // Find sensor actor in scene
        ASensorActor* Sensor = FindSensorByLocation(Location);
        if (Sensor)
        {
            // Update color based on temperature
            FLinearColor Color = GetTempColor(Temp);
            Sensor->SetSensorColor(Color);
            
            // Update floating text
            Sensor->SetDisplayText(
                FString::Printf(TEXT("%.1f°C"), Temp));
        }
        
        // Update heat map
        HeatMapManager->UpdateHeatMap("temperature", Location, Temp);
    }
};
```

### 5.2 MQTT Topic Structure

```
tunnel/
├── sensors/
│   ├── environmental/
│   │   ├── co/{sensor_id}          → {"value": 12.5, "unit": "ppm", "timestamp": 1234567890}
│   │   ├── co2/{sensor_id}         → {"value": 450, "unit": "ppm", "timestamp": 1234567890}
│   │   ├── nox/{sensor_id}         → {"value": 0.8, "unit": "ppm", "timestamp": 1234567890}
│   │   ├── visibility/{sensor_id}  → {"value": 85, "unit": "m", "timestamp": 1234567890}
│   │   ├── temperature/{sensor_id} → {"value": 24.5, "unit": "C", "timestamp": 1234567890}
│   │   └── wind/{sensor_id}        → {"speed": 2.3, "direction": 270, "unit": "m/s"}
│   ├── traffic/
│   │   ├── vaid/{detector_id}      → {"event": "breakdown", "lane": 1, "chainage": 1200}
│   │   ├── speed/{sensor_id}       → {"speed": 65, "lane": 1, "unit": "km/h"}
│   │   └── count/{sensor_id}       → {"count": 142, "period": "1min"}
│   ├── fire/
│   │   ├── detector/{sensor_id}    → {"status": "alarm", "type": "smoke", "confidence": 0.95}
│   │   └── extinguisher/{id}       → {"pressure": 12.5, "status": "ok", "unit": "bar"}
│   └── electrical/
│       ├── power/{meter_id}        → {"voltage": 230, "current": 45, "power": 10350}
│       └── ups/{ups_id}            → {"battery": 100, "runtime": 240, "status": "online"}
├── equipment/
│   ├── lighting/{luminaire_id}     → {"status": "on", "brightness": 80, "fault": false}
│   ├── ventilation/{fan_id}        → {"status": "running", "rpm": 1450, "temp": 45.2}
│   ├── pumps/{pump_id}             → {"status": "standby", "run_hours": 1247}
│   └── cameras/{camera_id}         → {"status": "online", "ptz": {"pan": 45, "tilt": -20, "zoom": 1.5}}
├── incidents/
│   ├── detected                    → {"type": "breakdown", "location": 1200, "confidence": 0.92}
│   ├── confirmed                   → {"incident_id": "INC-001", "type": "breakdown", "operator": "OP123"}
│   └── resolved                    → {"incident_id": "INC-001", "duration": 1234, "notes": "..."}
└── alarms/
    ├── urgent                      → {"system": "fire", "message": "Fire detected", "location": 1200}
    ├── alert                       → {"system": "lighting", "message": "Luminaire fault", "id": "L-045"}
    └── record                      → {"event": "normal_operation", "system": "ventilation"}
```

### 5.3 Data Update Frequencies

| Data Type | Update Frequency | Priority | Visualization Update |
|-----------|-----------------|----------|---------------------|
| Environmental sensors | 1-5 seconds | High | Real-time smooth interpolation |
| Traffic detection | Real-time | Critical | Immediate |
| Lighting status | 10 seconds | Medium | Batched updates |
| Ventilation | 5 seconds | High | Real-time with smooth transitions |
| Electrical meters | 30 seconds | Low | Trend graphs |
| Camera PTZ position | Real-time | Medium | Immediate (on user control) |
| Equipment health | 60 seconds | Low | Status indicator update |
| Alarms | Real-time | Critical | Immediate with audio |

### 5.4 REST API Endpoints

```
GET /api/v1/tunnel/{tunnel_id}/status
  → Returns overall tunnel status

GET /api/v1/tunnel/{tunnel_id}/sensors
  → Returns all sensor data

GET /api/v1/tunnel/{tunnel_id}/incidents
  → Returns active incidents

GET /api/v1/tunnel/{tunnel_id}/incidents/{incident_id}
  → Returns incident details with timeline

POST /api/v1/tunnel/{tunnel_id}/incidents/{incident_id}/confirm
  → Operator confirms incident

POST /api/v1/tunnel/{tunnel_id}/incidents/{incident_id}/resolve
  → Operator resolves incident with notes

GET /api/v1/tunnel/{tunnel_id}/equipment/{equipment_id}
  → Returns equipment status

POST /api/v1/tunnel/{tunnel_id}/equipment/{equipment_id}/control
  → Send control command to equipment

GET /api/v1/tunnel/{tunnel_id}/alarms
  → Returns active alarms

POST /api/v1/tunnel/{tunnel_id}/alarms/{alarm_id}/acknowledge
  → Acknowledge alarm

GET /api/v1/tunnel/{tunnel_id}/cameras/{camera_id}/stream
  → Returns RTSP/WebRTC stream URL

POST /api/v1/tunnel/{tunnel_id}/cameras/{camera_id}/ptz
  → Control PTZ camera

GET /api/v1/tunnel/{tunnel_id}/reports/history?from={timestamp}&to={timestamp}
  → Historical data query

POST /api/v1/tunnel/{tunnel_id}/closure
  → Initiate tunnel closure procedure
```

---

## 6. Subsystem Visualization Specifications

### 6.1 Lighting System Visualization

#### Individual Luminaire Representation
```cpp
// Luminaire class
class ALuminaireActor : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* LuminaireMesh;
    
    UPROPERTY(VisibleAnywhere)
    UPointLightComponent* LightComponent;
    
    UPROPERTY(VisibleAnywhere)
    UWidgetComponent* StatusWidget; // Shows ID and status
    
    FString LuminaireID;
    float BrightnessPercent = 100.0f;
    bool bIsFaulty = false;
    int32 RunHours = 0;
    
    void UpdateBrightness(float NewBrightness)
    {
        BrightnessPercent = NewBrightness;
        LightComponent->SetIntensity(
            BaseIntensity * (BrightnessPercent / 100.0f));
    }
    
    void SetFault(bool bFault)
    {
        bIsFaulty = bFault;
        if (bFault)
        {
            // Flash red
            LuminaireMesh->SetVectorParameterValueOnMaterials(
                "EmissiveColor", FVector(1, 0, 0));
        }
        else
        {
            // Normal glow
            LuminaireMesh->SetVectorParameterValueOnMaterials(
                "EmissiveColor", FVector(1, 1, 0.8));
        }
    }
};
```

#### Lighting Zone Material
```cpp
// Dynamic material for lighting zones
UMaterialInstanceDynamic* LightingZoneMaterial;

void UpdateLightingZone(ELightingZone Zone, float LuxLevel)
{
    FLinearColor ZoneColor;
    switch(Zone)
    {
        case ELightingZone::THRESHOLD:
            ZoneColor = FLinearColor(1.0f, 0.76f, 0.03f, 0.3f); // Yellow
            break;
        case ELightingZone::TRANSITION:
            ZoneColor = FLinearColor(1.0f, 0.6f, 0.0f, 0.3f); // Orange
            break;
        case ELightingZone::INTERIOR:
            ZoneColor = FLinearColor(0.13f, 0.59f, 0.95f, 0.3f); // Blue
            break;
    }
    
    // Adjust opacity based on lux level
    ZoneColor.A = FMath::Clamp(LuxLevel / 100.0f, 0.1f, 0.5f);
    
    LightingZoneMaterial->SetVectorParameterValue("ZoneColor", ZoneColor);
}
```

### 6.2 Ventilation System Visualization

#### Airflow Particle System
```cpp
// Niagara particle system for airflow
class AVentilationVisualization : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UNiagaraComponent* AirflowParticles;
    
    void UpdateAirflow(FVector Direction, float Speed, float AirQuality)
    {
        // Set particle velocity
        AirflowParticles->SetVectorParameter("FlowDirection", Direction);
        AirflowParticles->SetFloatParameter("FlowSpeed", Speed);
        
        // Set color based on air quality
        // Good: Blue, Moderate: Yellow, Poor: Red
        FLinearColor Color;
        if (AirQuality > 0.8f)
            Color = FLinearColor::Blue;
        else if (AirQuality > 0.5f)
            Color = FLinearColor::Yellow;
        else
            Color = FLinearColor::Red;
            
        AirflowParticles->SetColorParameter("ParticleColor", Color);
    }
};
```

#### Fan Model with Animation
```cpp
class AVentilationFan : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* FanBladeMesh;
    
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* FanHousingMesh;
    
    bool bIsRunning = false;
    float RPM = 0.0f;
    float MotorTemperature = 25.0f;
    
    void Tick(float DeltaTime) override
    {
        if (bIsRunning)
        {
            // Rotate fan blades
            float RotationSpeed = RPM * 6.0f; // Convert RPM to degrees/sec
            FRotator Rotation = FanBladeMesh->GetRelativeRotation();
            Rotation.Yaw += RotationSpeed * DeltaTime;
            FanBladeMesh->SetRelativeRotation(Rotation);
            
            // Change color if overheating
            if (MotorTemperature > 70.0f)
            {
                FanHousingMesh->SetVectorParameterValueOnMaterials(
                    "EmissiveColor", FVector(1, 0, 0));
            }
        }
    }
};
```

### 6.3 Fire Safety Visualization

#### Fire & Smoke Particle Systems
```cpp
class AFireIncident : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UNiagaraComponent* FireParticles;
    
    UPROPERTY(VisibleAnywhere)
    UNiagaraComponent* SmokeParticles;
    
    UPROPERTY(VisibleAnywhere)
    UPointLightComponent* FireLight; // Flickering orange light
    
    UPROPERTY(VisibleAnywhere)
    UAudioComponent* FireAudio; // Fire crackling sound
    
    float FireIntensity = 1.0f;
    float SmokeSpread = 1.0f;
    
    void UpdateFireIntensity(float Intensity)
    {
        FireIntensity = Intensity;
        
        // Scale fire particles
        FireParticles->SetFloatParameter("Intensity", Intensity);
        
        // Update light brightness
        FireLight->SetIntensity(1000.0f * Intensity);
        
        // Update audio volume
        FireAudio->SetVolumeMultiplier(Intensity);
    }
    
    void UpdateSmokeSpread(float Spread)
    {
        SmokeSpread = Spread;
        
        // Expand smoke particles
        SmokeParticles->SetFloatParameter("SpreadRadius", Spread * 100.0f);
        
        // Reduce visibility in smoke area
        UpdateVolumetricFog(Spread);
    }
};
```

#### Deluge System Activation
```cpp
class ADelugeSystem : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UNiagaraComponent* WaterSprayParticles;
    
    bool bIsActivated = false;
    
    void Activate()
    {
        bIsActivated = true;
        WaterSprayParticles->Activate();
        
        // Play activation sound
        UGameplayStatics::PlaySoundAtLocation(
            this, ActivationSound, GetActorLocation());
    }
};
```

### 6.4 Traffic Visualization

#### Vehicle AI System
```cpp
class AVehicleAI : public APawn
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* VehicleMesh;
    
    int32 Lane = 1;
    float Speed = 60.0f; // km/h
    float Chainage = 0.0f;
    
    // Follow spline path
    UPROPERTY(EditAnywhere)
    USplineComponent* LaneSpline;
    
    void Tick(float DeltaTime) override
    {
        if (!bIsStopped)
        {
            // Move along spline
            Chainage += (Speed / 3.6f) * DeltaTime; // Convert km/h to m/s
            
            FVector NewLocation = LaneSpline->GetLocationAtDistanceAlongSpline(
                Chainage, ESplineCoordinateSpace::World);
            
            FRotator NewRotation = LaneSpline->GetRotationAtDistanceAlongSpline(
                Chainage, ESplineCoordinateSpace::World);
            
            SetActorLocationAndRotation(NewLocation, NewRotation);
        }
    }
    
    void HandleIncident(FVector IncidentLocation, int32 IncidentLane)
    {
        if (IncidentLane == Lane)
        {
            // Brake
            float DistanceToIncident = 
                FVector::Dist(GetActorLocation(), IncidentLocation);
            
            if (DistanceToIncident < 100.0f)
            {
                bIsStopped = true;
                Speed = 0.0f;
            }
        }
    }
};
```

#### Traffic Density Heat Map
```cpp
void UpdateTrafficHeatMap()
{
    // Divide tunnel into sections
    const int32 NumSections = 50;
    const float SectionLength = TunnelLength / NumSections;
    
    TArray<int32> VehicleCounts;
    VehicleCounts.SetNumZeroed(NumSections);
    
    // Count vehicles in each section
    for (AVehicleAI* Vehicle : AllVehicles)
    {
        int32 Section = FMath::FloorToInt(Vehicle->Chainage / SectionLength);
        if (Section >= 0 && Section < NumSections)
        {
            VehicleCounts[Section]++;
        }
    }
    
    // Update heat map material
    for (int32 i = 0; i < NumSections; i++)
    {
        float Density = VehicleCounts[i] / MaxVehiclesPerSection;
        FLinearColor Color;
        
        if (Density < 0.3f)
            Color = FLinearColor::Green;
        else if (Density < 0.7f)
            Color = FLinearColor::Yellow;
        else
            Color = FLinearColor::Red;
        
        RoadSurfaceMaterial->SetVectorParameterValue(
            FName(*FString::Printf(TEXT("Section_%d_Color"), i)), Color);
    }
}
```

### 6.5 CCTV Camera Visualization

#### PTZ Camera with FOV Cone
```cpp
class ACCTVCamera : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* CameraMesh;
    
    UPROPERTY(VisibleAnywhere)
    USceneCaptureComponent2D* CameraCapture; // For live feed
    
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* FOVCone; // Field of view visualization
    
    FString CameraID;
    float Pan = 0.0f;    // -180 to 180
    float Tilt = 0.0f;   // -90 to 90
    float Zoom = 1.0f;   // 1x to 20x
    
    void SetPTZ(float NewPan, float NewTilt, float NewZoom)
    {
        Pan = FMath::Clamp(NewPan, -180.0f, 180.0f);
        Tilt = FMath::Clamp(NewTilt, -90.0f, 90.0f);
        Zoom = FMath::Clamp(NewZoom, 1.0f, 20.0f);
        
        // Update camera rotation
        FRotator Rotation = FRotator(Tilt, Pan, 0);
        CameraMesh->SetRelativeRotation(Rotation);
        
        // Update FOV
        float FOV = 90.0f / Zoom;
        CameraCapture->FOVAngle = FOV;
        
        // Update FOV cone scale
        UpdateFOVCone(FOV);
    }
    
    void UpdateFOVCone(float FOV)
    {
        // Scale cone mesh based on FOV
        float ConeScale = FMath::Tan(FMath::DegreesToRadians(FOV / 2.0f));
        FOVCone->SetRelativeScale3D(FVector(10.0f, ConeScale, ConeScale));
        
        // Color: Green if no alerts, Yellow if detecting, Red if incident
        FLinearColor ConeColor = bIsDetectingIncident ? 
            FLinearColor::Red : FLinearColor::Green;
        ConeColor.A = 0.2f; // Semi-transparent
        
        FOVCone->SetVectorParameterValueOnMaterials("Color", ConeColor);
    }
    
    void AutoZoomToLocation(FVector TargetLocation)
    {
        // Calculate pan/tilt to point at target
        FVector Direction = (TargetLocation - GetActorLocation()).GetSafeNormal();
        FRotator LookAtRotation = Direction.Rotation();
        
        SetPTZ(LookAtRotation.Yaw, LookAtRotation.Pitch, 5.0f);
    }
};
```

### 6.6 Environmental Sensor Visualization

#### Sensor with Data Display
```cpp
class AEnvironmentalSensor : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* SensorMesh;
    
    UPROPERTY(VisibleAnywhere)
    UWidgetComponent* DataDisplayWidget; // 3D widget showing values
    
    FString SensorID;
    ESensorType SensorType;
    float CurrentValue = 0.0f;
    float WarningThreshold = 0.0f;
    float AlarmThreshold = 0.0f;
    
    void UpdateSensorValue(float NewValue)
    {
        CurrentValue = NewValue;
        
        // Update display widget
        if (DataDisplayWidget && DataDisplayWidget->GetUserWidgetObject())
        {
            USensorDisplayWidget* Widget = 
                Cast<USensorDisplayWidget>(DataDisplayWidget->GetUserWidgetObject());
            
            Widget->SetSensorValue(CurrentValue, GetUnitString());
        }
        
        // Update sensor color based on thresholds
        FLinearColor Color;
        if (CurrentValue >= AlarmThreshold)
            Color = FLinearColor::Red;
        else if (CurrentValue >= WarningThreshold)
            Color = FLinearColor::Yellow;
        else
            Color = FLinearColor::Green;
        
        SensorMesh->SetVectorParameterValueOnMaterials("StatusColor", Color);
    }
    
    FString GetUnitString()
    {
        switch(SensorType)
        {
            case ESensorType::CO: return "ppm";
            case ESensorType::CO2: return "ppm";
            case ESensorType::Temperature: return "°C";
            case ESensorType::Visibility: return "m";
            case ESensorType::WindSpeed: return "m/s";
            default: return "";
        }
    }
};
```

---

## 7. Interaction Design for VR/AR

### 7.1 VR Interaction System

#### Hand Controller Mapping (Oculus/Vive/Index)

**Left Controller**:
```
Trigger (Index Finger):
- Point at object → Show tooltip
- Squeeze → Grab & move UI panels

Grip (Middle/Ring/Pinky):
- Hold → Activate navigation mode (WASD with thumbstick)

Thumbstick:
- Up/Down → Forward/Backward movement
- Left/Right → Strafe left/right
- Click → Toggle walk/fly mode

Button A/X:
- Press → Open context menu
- Hold → Open quick actions

Button B/Y:
- Press → Go back / Cancel
- Hold → Return to overview screen
```

**Right Controller**:
```
Trigger (Index Finger):
- Point & squeeze → Select / Confirm
- At UI element → Click button

Grip (Middle/Ring/Pinky):
- Hold → Activate tool mode (depends on selected tool)

Thumbstick:
- Up/Down → Zoom in/out
- Left/Right → Rotate view
- Click → Reset camera

Button A/X:
- Press → Acknowledge alarm
- Hold → Acknowledge all alarms

Button B/Y:
- Press → Open alarm list
- Hold → Open incident list
```

#### Gesture Recognition

**Pinch Gesture** (Thumb + Index):
```cpp
bool DetectPinch(UHandTrackingComponent* Hand)
{
    float Distance = FVector::Dist(
        Hand->GetThumbTipPosition(),
        Hand->GetIndexFingerTipPosition());
    
    return Distance < PinchThreshold; // e.g., 2cm
}

// Use case: Pinch to select small objects
```

**Point Gesture** (Index extended, others curled):
```cpp
// Use case: Point at object to highlight it
void OnPointGesture(FVector PointDirection)
{
    FHitResult Hit;
    FVector Start = HandLocation;
    FVector End = Start + (PointDirection * 1000.0f);
    
    if (GetWorld()->LineTraceSingleByChannel(Hit, Start, End, ECC_Visibility))
    {
        HighlightActor(Hit.GetActor());
    }
}
```

**Palm Up Gesture**:
```cpp
// Use case: Summon menu
bool DetectPalmUp(UHandTrackingComponent* Hand)
{
    FVector PalmNormal = Hand->GetPalmNormal();
    return FVector::DotProduct(PalmNormal, FVector::UpVector) > 0.8f;
}
```

### 7.2 AR Interaction System

#### HoloLens 2 Gestures

**Air Tap** (Near/Far):
```
Near interaction: Touch holographic button with finger
Far interaction: Point at object, bring index and thumb together
```

**Hand Ray**:
```
Extend hand → Ray projects from palm
Point at UI element → Highlight
Air tap → Select
```

**Direct Touch**:
```
Reach out and touch holographic objects
Physically press buttons
Grab and move objects
```

**Voice Commands**:
```
"Select" → Confirm selection
"Place here" → Place holographic object at gaze point
"Show menu" → Open menu
"Hide" → Hide current holographic object
```

#### Tablet AR Interactions

**Touch Gestures**:
```
Single Tap: Select object in AR view
Double Tap: Open details
Long Press: Context menu
Pinch: Zoom in/out
Two-finger Rotate: Rotate 3D model
Swipe: Navigate between screens
```

**Camera-Based**:
```
Point at Equipment → Show AR overlay with status
Move closer → More detailed information appears
QR Code Scan → Jump to specific equipment control
```

### 7.3 Locomotion in VR

#### Teleportation (Comfort Mode)
```cpp
void TeleportLocomotion()
{
    // Show arc trajectory
    TArray<FVector> ArcPoints = CalculateArcTrajectory(
        ControllerLocation, 
        ControllerForward,
        TeleportDistance);
    
    // Draw arc spline
    DrawArcSpline(ArcPoints);
    
    // Show landing marker
    FVector LandingPoint = ArcPoints.Last();
    ShowTeleportMarker(LandingPoint);
    
    // On trigger release, teleport
    if (TriggerReleased)
    {
        FadeOutAndTeleport(LandingPoint);
    }
}
```

#### Smooth Locomotion (Advanced Mode)
```cpp
void SmoothLocomotion(float DeltaTime)
{
    FVector MovementDirection = GetThumbstickDirection();
    float Speed = WalkSpeed;
    
    if (IsSprintButtonHeld)
        Speed = SprintSpeed;
    
    FVector NewLocation = GetActorLocation() + 
        (MovementDirection * Speed * DeltaTime);
    
    SetActorLocation(NewLocation);
}
```

#### Grab & Pull Locomotion
```cpp
void GrabWorld()
{
    if (GripButtonPressed)
    {
        // Grab the world
        GrabbedWorldPosition = GetControllerLocation();
        bIsGrabbingWorld = true;
    }
    
    if (bIsGrabbingWorld)
    {
        // Pull yourself in direction opposite to controller movement
        FVector ControllerDelta = GetControllerLocation() - GrabbedWorldPosition;
        FVector PlayerMovement = -ControllerDelta;
        
        MovePlayer(PlayerMovement);
    }
    
    if (GripButtonReleased)
    {
        bIsGrabbingWorld = false;
    }
}
```

### 7.4 UI Interaction in 3D Space

#### Floating UI Panels
```cpp
class AFloatingUIPanel : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UWidgetComponent* UIWidget;
    
    bool bIsFollowingPlayer = false;
    float FollowDistance = 100.0f;
    float FollowSmoothness = 5.0f;
    
    void Tick(float DeltaTime) override
    {
        if (bIsFollowingPlayer)
        {
            // Panel follows player's gaze at fixed distance
            APawn* Player = GetWorld()->GetFirstPlayerController()->GetPawn();
            FVector PlayerLocation = Player->GetActorLocation();
            FVector PlayerForward = Player->GetActorForwardVector();
            
            FVector TargetLocation = PlayerLocation + 
                (PlayerForward * FollowDistance);
            
            // Smooth interpolation
            FVector NewLocation = FMath::VInterpTo(
                GetActorLocation(),
                TargetLocation,
                DeltaTime,
                FollowSmoothness);
            
            SetActorLocation(NewLocation);
            
            // Always face player
            FRotator LookAtRotation = (PlayerLocation - GetActorLocation()).Rotation();
            SetActorRotation(LookAtRotation);
        }
    }
    
    void Pin()
    {
        // Pin in world space
        bIsFollowingPlayer = false;
    }
    
    void Unpin()
    {
        // Unpin and follow player
        bIsFollowingPlayer = true;
    }
};
```

#### 3D Button Interaction
```cpp
class A3DButton : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* ButtonMesh;
    
    UPROPERTY(EditAnywhere)
    FLinearColor NormalColor = FLinearColor(0.2f, 0.2f, 0.2f);
    
    UPROPERTY(EditAnywhere)
    FLinearColor HoverColor = FLinearColor(0.4f, 0.4f, 0.4f);
    
    UPROPERTY(EditAnywhere)
    FLinearColor PressedColor = FLinearColor(0.6f, 0.6f, 0.6f);
    
    UPROPERTY(BlueprintAssignable)
    FOnButtonClicked OnButtonClicked;
    
    void OnHoverBegin()
    {
        ButtonMesh->SetVectorParameterValueOnMaterials("Color", HoverColor);
        
        // Play haptic feedback
        PlayHapticFeedback(0.3f);
        
        // Play hover sound
        UGameplayStatics::PlaySoundAtLocation(
            this, HoverSound, GetActorLocation());
    }
    
    void OnPressed()
    {
        ButtonMesh->SetVectorParameterValueOnMaterials("Color", PressedColor);
        
        // Animate press (move down slightly)
        FVector Offset = FVector(0, 0, -0.5f);
        ButtonMesh->AddRelativeLocation(Offset);
        
        // Strong haptic feedback
        PlayHapticFeedback(0.8f);
        
        // Play click sound
        UGameplayStatics::PlaySoundAtLocation(
            this, ClickSound, GetActorLocation());
        
        // Trigger click event
        OnButtonClicked.Broadcast();
    }
    
    void OnReleased()
    {
        ButtonMesh->SetVectorParameterValueOnMaterials("Color", HoverColor);
        
        // Animate release (move back up)
        ButtonMesh->SetRelativeLocation(FVector::ZeroVector);
    }
};
```

#### Radial Menu in VR
```cpp
class ARadialMenu : public AActor
{
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* MenuBase;
    
    TArray<URadialMenuItem*> MenuItems;
    
    void SpawnMenu(FVector Location, TArray<FMenuItem> Items)
    {
        SetActorLocation(Location);
        
        int32 NumItems = Items.Num();
        float AngleStep = 360.0f / NumItems;
        
        for (int32 i = 0; i < NumItems; i++)
        {
            float Angle = i * AngleStep;
            FVector ItemOffset = FVector(
                FMath::Cos(FMath::DegreesToRadians(Angle)) * MenuRadius,
                FMath::Sin(FMath::DegreesToRadians(Angle)) * MenuRadius,
                0);
            
            URadialMenuItem* MenuItem = 
                CreateMenuItem(Items[i], ItemOffset);
            MenuItems.Add(MenuItem);
        }
    }
    
    void SelectItemAtAngle(float Angle)
    {
        int32 NumItems = MenuItems.Num();
        float AngleStep = 360.0f / NumItems;
        
        int32 SelectedIndex = FMath::RoundToInt(Angle / AngleStep) % NumItems;
        
        // Highlight selected item
        for (int32 i = 0; i < MenuItems.Num(); i++)
        {
            MenuItems[i]->SetHighlighted(i == SelectedIndex);
        }
    }
};
```

---

## 8. Technical Implementation Strategy

### 8.1 Unreal Engine 5 Implementation Plan

#### Phase 1: Core Setup (Weeks 1-2)
```
1. Create UE5 project with VR template
2. Set up version control (Git LFS for large assets)
3. Configure input system for VR controllers
4. Implement basic camera system
5. Set up networking for multiplayer VR
6. Create material library (PBR materials)
7. Set up lighting (Lumen for real-time GI)
```

#### Phase 2: 3D Tunnel Model (Weeks 3-5)
```
1. Import CAD data from AutoCAD/Civil3D
2. Create procedural tunnel generation system
3. Model equipment (lights, cameras, fans, etc.)
4. Create LOD system for performance
5. Implement lighting zones with dynamic materials
6. Create spline-based road system
7. Add texture details and weathering
```

#### Phase 3: Data Integration (Weeks 6-8)
```
1. Implement REST API client (HTTP requests)
2. Implement WebSocket client for real-time data
3. Implement MQTT client (using MQTT++ or VaRest)
4. Create data parsing system (JSON)
5. Implement data caching and buffering
6. Create update manager for different data frequencies
7. Error handling and reconnection logic
```

#### Phase 4: Visualization Systems (Weeks 9-12)
```
1. Lighting system visualization
   - Dynamic light intensity
   - Faulty luminaire indicators
2. Ventilation system visualization
   - Niagara particle system for airflow
   - Animated fan models
3. Fire safety visualization
   - Fire and smoke particles
   - Heat glow post-process
4. Traffic simulation
   - AI vehicle movement
   - Traffic density heat map
5. CCTV system
   - PTZ camera controls
   - FOV visualization
   - Scene capture for live feed
6. Environmental sensors
   - 3D widgets for data display
   - Color-coded status indicators
```

#### Phase 5: UI Implementation (Weeks 13-16)
```
1. Create 12 core UI screens (UMG widgets)
2. Implement floating UI panels in 3D
3. Create alarm and event system
4. Implement incident management UI
5. Create configuration screens
6. Implement MIS reporting UI
7. Create help system and tooltips
```

#### Phase 6: VR Implementation (Weeks 17-20)
```
1. Optimize for VR performance (90fps minimum)
2. Implement VR locomotion (teleport & smooth)
3. Implement hand controller interactions
4. Create VR-specific UI (floating panels, radial menus)
5. Implement gesture recognition
6. Add spatial audio
7. Create VR comfort settings (vignette, FOV reduction)
8. Implement multiplayer VR (see other operators)
```

#### Phase 7: AR Implementation (Weeks 21-23)
```
1. Create Unity project for AR (recommended for AR)
2. Implement AR Foundation for multi-platform
3. Create AR overlays for control room
4. Implement spatial anchoring
5. Create field operator AR app
6. Implement QR code scanning for equipment
7. Hand gesture recognition
```

#### Phase 8: Testing & Optimization (Weeks 24-28)
```
1. Performance profiling and optimization
2. User acceptance testing with operators
3. Stress testing (multiple simultaneous incidents)
4. VR comfort testing (prevent motion sickness)
5. Network latency testing
6. Failover testing
7. Bug fixing
8. Documentation
```

### 8.2 Recommended Unreal Engine Plugins

#### Essential Plugins
1. **VaRest** - REST API integration
   - HTTP requests
   - JSON parsing
   - WebSocket support

2. **MQTT++ / MQTT Client** - MQTT integration
   - Pub/sub messaging
   - QoS support
   - Reconnection logic

3. **OpenCV** - Computer vision (if needed for AR)
   - Image processing
   - QR code detection
   - Camera calibration

4. **Advanced Sessions** - Multiplayer VR
   - Session management
   - Voice chat
   - Player replication

5. **UXTools** (for HoloLens 2)
   - Hand tracking
   - Button components
   - Bounds control

6. **Spatial Audio** - 3D positional audio
   - Ambisonics
   - Audio occlusion
   - Reverb zones

7. **Google Maps / Cesium for Unreal** - Geographic data
   - 3D terrain
   - Satellite imagery
   - GIS integration

8. **RapidJSON** - Fast JSON parsing
   - High performance
   - Low memory footprint

### 8.3 Unity Implementation (AR-Focused)

#### Recommended Unity Packages
1. **AR Foundation** - Core AR functionality
2. **ARCore XR Plugin** - Android AR
3. **ARKit XR Plugin** - iOS AR
4. **Mixed Reality Toolkit (MRTK)** - HoloLens 2
5. **XR Interaction Toolkit** - VR/AR interactions
6. **TextMeshPro** - High-quality text rendering
7. **DOTween** - Animation tweening
8. **UniRx** - Reactive programming for data streams
9. **Best HTTP** - REST API client
10. **M2MQTT** - MQTT client

### 8.4 Development Best Practices

#### Performance Targets
```
Desktop (4K Video Wall):
- 60 FPS @ 3840x2160
- GPU: RTX 3080 or equivalent
- CPU: i7-10700K or equivalent
- RAM: 32GB

VR (Quest 2, PSVR 2, Index):
- 90 FPS @ 1832x1920 per eye (Quest 2)
- 120 FPS for Index/PSVR 2 (optional)
- GPU: RTX 3070 or equivalent
- CPU: i7-10700K or equivalent
- RAM: 16GB

AR Tablet (iPad Pro, Android):
- 60 FPS @ 2388x1668
- Snapdragon 8 Gen 2 or Apple A14 or better

AR Headset (HoloLens 2):
- 60 FPS @ 1280x720 per eye
- Optimized for low power
```

#### Optimization Strategies
1. **LOD System**
   - Use Nanite (UE5) for automatic LOD
   - Manual LODs for critical objects
   - Aggressive culling for distant objects

2. **Instancing**
   - Instanced static meshes for repeated elements (luminaires, lane markers)
   - Hardware instancing for traffic vehicles

3. **Texture Optimization**
   - Virtual texturing (UE5)
   - Texture streaming
   - Compressed formats (BC7 for high quality, BC1 for simple textures)

4. **Particle Optimization**
   - GPU particles (Niagara)
   - LOD for particle systems
   - Limit particle count based on distance

5. **Lighting Optimization**
   - Lumen (UE5) for dynamic GI
   - Baked lighting for static elements
   - Light importance for prioritization

6. **Network Optimization**
   - Delta compression for sensor data
   - Batching updates
   - Client-side prediction for smooth visuals

---

## 9. Performance Requirements

### 9.1 Rendering Performance

#### Frame Rate Targets
| Platform | Target FPS | Minimum FPS | Resolution |
|----------|-----------|-------------|------------|
| Desktop (Video Wall) | 60 | 45 | 3840x2160 (4K) |
| Desktop (Workstation) | 60 | 45 | 1920x1080 (FHD) |
| VR (Quest 2) | 90 | 72 | 1832x1920/eye |
| VR (Index/PSVR2) | 120 | 90 | 2016x2240/eye |
| AR (HoloLens 2) | 60 | 45 | 1280x720/eye |
| AR (Tablet) | 60 | 45 | Device native |

#### Draw Call Budget
```
Desktop: Up to 5000 draw calls
VR: Up to 2000 draw calls (critical for performance)
AR Headset: Up to 1000 draw calls
AR Tablet: Up to 1500 draw calls
```

#### Triangle Budget
```
Desktop: 10-15 million triangles/frame
VR: 3-5 million triangles/frame
AR Headset: 1-2 million triangles/frame
AR Tablet: 2-3 million triangles/frame
```

### 9.2 Network Performance

#### Data Transfer Requirements
| Data Type | Bandwidth | Latency | Update Rate |
|-----------|-----------|---------|-------------|
| Sensor Data (MQTT) | 1-5 Mbps | <100ms | 1-5 Hz |
| Video Stream (RTSP) | 4-8 Mbps/camera | <500ms | 30 fps |
| Control Commands | <1 Mbps | <50ms | On-demand |
| Alarms/Events | <1 Mbps | <50ms | Real-time |
| Historical Data | 10-20 Mbps | <1s | On-demand |

#### Network Requirements
- **Minimum**: 100 Mbps LAN
- **Recommended**: 1 Gbps LAN
- **Redundancy**: Dual network paths
- **Wireless**: 802.11ac (Wi-Fi 5) or better for AR tablets

### 9.3 System Requirements

#### Control Room Workstation
```
Minimum:
- CPU: Intel i7-10700K / AMD Ryzen 7 3700X
- GPU: NVIDIA RTX 3060 / AMD RX 6700 XT
- RAM: 16GB DDR4
- Storage: 512GB NVMe SSD
- Network: 1 Gbps Ethernet
- Display: 2x 1920x1080 monitors

Recommended:
- CPU: Intel i9-12900K / AMD Ryzen 9 5900X
- GPU: NVIDIA RTX 4070 / AMD RX 7800 XT
- RAM: 32GB DDR4/DDR5
- Storage: 1TB NVMe SSD
- Network: 10 Gbps Ethernet (if available)
- Display: 3x 2560x1440 or 1x 3840x2160 monitor
```

#### VR-Ready Workstation
```
Minimum:
- CPU: Intel i7-11700K / AMD Ryzen 7 5800X
- GPU: NVIDIA RTX 3070 / AMD RX 6800
- RAM: 16GB DDR4
- Storage: 512GB NVMe SSD
- Network: 1 Gbps Ethernet
- VR Headset: Quest 2, PSVR 2, or Valve Index

Recommended:
- CPU: Intel i9-12900K / AMD Ryzen 9 5900X
- GPU: NVIDIA RTX 4080 / AMD RX 7900 XT
- RAM: 32GB DDR4/DDR5
- Storage: 1TB NVMe SSD
- Network: 1 Gbps Ethernet
- VR Headset: Valve Index or PSVR 2
```

#### AR Device Requirements
```
HoloLens 2:
- Integrated hardware
- Network: Wi-Fi 6

AR Tablet:
- iPad Pro (2020 or later) with A14 Bionic or better
- Android tablet with Snapdragon 8 Gen 2 or better
- 6GB RAM minimum
- Network: Wi-Fi 6
```

---

## 10. Asset Requirements

### 10.1 3D Models

#### Equipment Models to Create

**Lighting (100-1000+ instances)**:
- LED Luminaire (multiple types)
  - Poly count: 1000-2000 triangles (LOD0)
  - Textures: 2K diffuse, normal, metallic/roughness
- Emergency lighting unit
- Portal lighting control panel

**Ventilation (10-50 instances)**:
- Axial flow fan (with animated blades)
  - Poly count: 5000-10000 triangles (LOD0)
  - Textures: 4K diffuse, normal, metallic/roughness
- Jet fan
- Anemometer
- Air quality sensor housing

**Fire Safety (50-200 instances)**:
- Fire extinguisher
  - Poly count: 500-1000 triangles
  - Textures: 2K diffuse, normal, metallic/roughness
- Deluge nozzle
- Fire detector
- Emergency call box (ECB)
- Cross-passage door
- Fireman's panel

**Traffic Control (50-100 instances)**:
- Variable Message Sign (VMS)
  - Poly count: 2000-3000 triangles
  - LED display: Emissive material
- Lane Control Signal (LCS)
- Speed limit sign
- Blinker/beacon

**CCTV (50-200 instances)**:
- PTZ camera (with movable parts)
  - Poly count: 3000-5000 triangles
  - Textures: 2K diffuse, normal, metallic/roughness
- Fixed camera
- Camera housing/mount

**Electrical (20-50 instances)**:
- Transformer
- UPS unit
- Generator
- Electrical panel
- Cable tray

**Drainage (10-30 instances)**:
- Sump pump
- Drainage grate
- Water level sensor

**Structural Elements**:
- Tunnel segments (modular, tileable)
  - Poly count: 10000-20000 triangles per 100m section
  - Textures: 4K tileable concrete, 4K normal, 2K roughness
- Cross-passage (modular)
- Escape gallery
- Service gallery
- Emergency exit door
- Service access door

**Vehicles for Traffic Simulation (10-20 types)**:
- Sedan
- SUV
- Van
- Truck
- Emergency vehicle (ambulance, fire truck, police)
  - Poly count: 5000-10000 triangles each (LOD0)
  - Textures: 2K diffuse, normal, metallic/roughness

### 10.2 Textures & Materials

#### PBR Material System
```
All materials should follow PBR workflow:
- Diffuse/Albedo (no lighting information)
- Normal map
- Metallic map
- Roughness map
- Ambient Occlusion (optional, can be combined)
- Emissive (for lights, signs, displays)
```

#### Required Texture Sets

**Tunnel Surfaces**:
- Concrete wall (clean)
  - 4K tileable
  - Variations: light/dark, weathered
- Concrete wall (dirty/weathered)
- Asphalt road surface
  - 4K tileable
  - Variations: worn, cracked
- Road markings (white/yellow)
  - 2K, with alpha for line shapes

**Equipment**:
- Painted metal (various colors)
  - 2K non-tileable
  - Clean and worn versions
- Stainless steel
- Aluminum
- Plastic housings
- Electronic displays (LED, LCD)

**Effects**:
- Fire (animated flipbook texture)
- Smoke (volumetric or billboard)
- Water/puddles
- Decals (oil stains, tire marks, graffiti, warning signs)

### 10.3 Particles & VFX

#### Particle Systems Required

**Environmental**:
- Smoke (fire incident)
  - Niagara system
  - Volumetric rendering
  - Multiple smoke densities
- Fire
  - Combined particle + light + heat distortion
- Water spray (deluge system)
- Steam/vapor
- Dust particles (ambient)

**Airflow Visualization**:
- Particle stream for ventilation
  - Color-coded by air quality
  - Speed varies with fan speed
  - Direction matches airflow

**Vehicle Effects**:
- Exhaust smoke
- Tire smoke (braking)
- Debris (for collision simulation)

**UI Effects**:
- Selection highlight
- Attention pulse (for alerts)
- Spawn/despawn effects

### 10.4 Audio Assets

#### Sound Effects Required

**Environmental**:
- Ambient tunnel sound (low rumble, ventilation)
- Traffic noise (vehicle pass-by)
- Ventilation fan running (various speeds)
- Water flowing (drainage pumps)

**Equipment**:
- Fan startup/shutdown
- Pump startup/shutdown
- Electrical hum
- Door open/close
- Button click

**Alerts & Alarms**:
- Urgent alarm (loud, attention-grabbing)
- Alert notification (moderate)
- Record notification (subtle)
- Confirmation sound (button press)
- Error sound

**Incidents**:
- Fire crackling
- Fire alarm bell
- PA system message (voice)
- Emergency siren

**VR Specific**:
- Spatial audio positioning for all sounds
- Reverb zones for realistic acoustics
- Haptic audio (low frequency for hand controllers)

#### Voice Overs (Text-to-Speech or Recorded)
- System status announcements
- Incident notifications
- PA system messages
- Tutorial/training narration

### 10.5 UI Assets

#### Icons (2D, high resolution)
- Equipment types (100+ icons)
  - Lighting, ventilation, fire, traffic, etc.
- Status indicators (OK, Warning, Alarm, Offline)
- Action buttons (Confirm, Cancel, Close, etc.)
- Navigation icons
- System icons

#### UI Elements
- Panels/backgrounds (semi-transparent)
- Buttons (normal, hover, pressed states)
- Sliders
- Checkboxes
- Radio buttons
- Dropdown menus
- Progress bars
- Gauges/meters

#### Fonts
- Primary UI font (clear, readable)
  - Recommended: Roboto, Open Sans, or similar
- Monospace font (for data display)
  - Recommended: Roboto Mono, Source Code Pro
- Icon font (for symbols)

### 10.6 Data Files

#### 3D Tunnel Geometry Import
- AutoCAD files (.dwg, .dxf)
- Civil 3D alignment data
- Revit models (.rvt) - may need conversion
- IFC (Industry Foundation Classes) for BIM
- Shapefile (.shp) for GIS data

#### Conversion Pipeline
```
AutoCAD (.dwg) 
  → Export as FBX/OBJ
  → Import to Unreal/Unity
  → Clean up geometry
  → Apply materials
  → Create LODs
  → Set up collision
  → Add metadata (chainage, type, etc.)
```

#### Metadata (JSON/CSV)
- Equipment locations (X, Y, Z coordinates + chainage)
- Equipment IDs and types
- Sensor locations and types
- Camera locations and FOV
- Zone definitions
- Threshold values

---

## 11. Next Steps & Recommendations

### 11.1 Immediate Actions (Week 1-2)

1. **Stakeholder Workshop**
   - Present this specification to tunnel operators
   - Gather feedback on VR/AR use cases
   - Prioritize features
   - Identify any missing requirements

2. **Technology Selection**
   - Final decision: Unreal vs Unity vs Hybrid
   - Select VR/AR hardware (headsets, tablets)
   - Select development workstations

3. **Team Formation**
   - Unreal/Unity developers (3-4)
   - 3D artists (2-3)
   - UI/UX designer (1-2)
   - Backend integration specialist (1)
   - QA testers with VR experience (2)
   - Project manager (1)

4. **Pilot Project Setup**
   - Select one tunnel for initial implementation
   - Obtain CAD files and equipment data
   - Set up development environment
   - Create project repository

### 11.2 Prototype Development (Week 3-8)

**Goal**: Create vertical slice demonstrating core functionality

**Features to Include**:
- 3D tunnel model (one section, ~500m)
- Real-time sensor data display (temperature, CO)
- One incident type (vehicle breakdown)
- Basic VR interaction (walk through, select equipment)
- Desktop mode with same functionality

**Success Criteria**:
- Runs at 60 FPS on desktop, 90 FPS in VR
- Real-time data updates (<100ms latency)
- Operator can confirm incident and see automated response
- Positive feedback from operators in user testing

### 11.3 Risk Mitigation

**Technical Risks**:
1. **Performance in VR**
   - Mitigation: Aggressive optimization, LOD system, early performance testing
2. **Network Latency**
   - Mitigation: Client-side prediction, data caching, quality degradation
3. **CAD Import Issues**
   - Mitigation: Establish conversion pipeline early, manual cleanup process

**User Adoption Risks**:
1. **VR Motion Sickness**
   - Mitigation: Comfort settings, teleportation option, extensive testing
2. **Complex Interface**
   - Mitigation: Comprehensive training program, gradual feature introduction
3. **Hardware Reliability**
   - Mitigation: Desktop fallback mode, redundant systems

### 11.4 Budget Estimate

**Development (28 weeks)**:
- Team cost: $800K - $1.2M (depending on location)
- Software licenses: $20K - $40K (Unreal free, Unity Pro, plugins)
- Hardware (dev workstations + VR): $50K - $80K
- **Total Development**: $870K - $1.32M

**Per-Site Deployment**:
- Control room workstations (3x): $15K - $25K
- VR headsets (2x): $2K - $4K
- AR tablets (2x): $2K - $4K
- **Total Per-Site**: $19K - $33K

**Ongoing**:
- Maintenance & updates: $100K - $150K/year
- Training: $20K - $40K/year
- **Total Ongoing**: $120K - $190K/year

---

## 12. Conclusion

This specification provides a comprehensive foundation for developing a cutting-edge Tunnel Management System UI using Unreal Engine or Unity with full AR/VR capabilities.

**Key Differentiators**:
- Immersive 3D visualization replacing traditional 2D SCADA
- VR for training and remote operation
- AR for field maintenance and control room enhancement
- Real-time data integration with visual feedback
- Intuitive interaction paradigm (natural gestures, voice)

**Expected Benefits**:
- **Improved Situational Awareness**: 3D visualization provides better spatial understanding
- **Faster Incident Response**: Immersive interface allows quicker assessment
- **Better Training**: VR simulations without risk to operations
- **Enhanced Collaboration**: Multi-user VR/AR for coordinated response
- **Future-Proof**: Platform ready for AI/ML integration, digital twin capabilities

**Next Milestone**: Stakeholder presentation and prototype approval → Move to development phase

---

**Document End**

