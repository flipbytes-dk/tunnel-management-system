# Tunnel Management System - MMI/UI Specifications
**Extracted from SyRS v20 Feb 2025 & URS v30 Jan 2025**

## Document Information
- **Document Type**: MMI/UI Technical Specifications
- **System**: Tunnel Management Control System (TMCS)
- **Source Documents**: 
  - System Requirements Specifications (SyRS) - 133 pages
  - User Requirements Specifications (URS) - 99 pages
- **Date Compiled**: October 27, 2025

---

## Table of Contents
1. [System Overview](#1-system-overview)
2. [Architecture](#2-architecture)
3. [Control Room Hardware](#3-control-room-hardware)
4. [UI Components & Modules](#4-ui-components--modules)
5. [Visualization Requirements](#5-visualization-requirements)
6. [User Interaction Patterns](#6-user-interaction-patterns)
7. [Operating Modes](#7-operating-modes)
8. [Incident Management UI](#8-incident-management-ui)
9. [Standards & Compliance](#9-standards--compliance)
10. [Technical Specifications](#10-technical-specifications)

---

## 1. System Overview

### 1.1 Purpose
The MMI/UI provides the human-machine interface for the Tunnel Management Control System, enabling operators to monitor and control tunnel operations across multiple control room levels (TSB, TOCC, RAMC).

### 1.2 Key Stakeholders
- **TSB (Tunnel Services Building)**: Local tunnel control
- **TOCC (Tunnel Operations Control Centre)**: Primary tunnel monitoring & control
- **RAMC (Road Asset Management Centre)**: Regional/corridor level traffic management

---

## 2. Architecture

### 2.1 Top-Level Software Modules

#### MMI/UI Module
- **Network Monitoring System** integrated to Platform Software
- **Own Product backend & UI's** integrated to Platform Software
- **Synthetic Layout** capable of underlays & overlays (e.g., AutoCAD, xGIS, etc.)
- Integration with Rule Engine via Platform Software
- MIS (Management Information System) reporting capabilities

#### Platform Software Integration
- All standard protocols (MQTT, OPC, MODBUS, etc.)
- Non-standard protocols (self-developed fast API, vendor proprietary protocols)
- Integrated to Database, Rule Engine, UI
- Integrated to VaaaN Product CR dockerized backend & Product UI's
- Prometheus for resource monitoring & alert generation (on-screen & email)
- DevOps capable
- Clustering / managing active-active DC-DR handling (e.g., Hadoop)

#### Database
- Shared resource for Platform Software, Rule Engine, UI
- MySQL database system
- Stores: Raw Sensor Data, Log Data, Transactional Data, System Log Data

#### Rule Engine
- Module containing Project Specific Rule Engine combining data from disparate sensors
- Conversion of raw sensor data to actionable insights / automated automation
- Integrated to UI via Platform Software

### 2.2 Operating System & Environment
- **OS**: Linux
- **Clustering**: Hadoop for redundancy
- **Containerization**: Docker
- **Runtime**: Python
- Any other necessary components/runtime

### 2.3 System Redundancy
- **Active-Active DC-DR Architecture**: Clustered environment for fall-back
- **Redundant Power**: Sub-station + Generator + UPS + Equipment with battery inbuilt
- **Redundant Network**: 
  - Fiber backhaul
  - Wireless back-haul (Microwave, 5G, WiMAX)
  - Local Ops (Wi-Fi, Bluetooth)

---

## 3. Control Room Hardware

### 3.1 Tunnel Services Building (TSB)

#### Category < B Tunnels:
- 1 Workstation
- 1 SCADA terminal
- 1 CCTV Monitor & Controller
- Any other necessary Console
- Access Control

#### Category ≥ B Tunnels:
- 2 Operator workstations
- 1 Supervisor workstation
- 2 SCADA terminals with monitors
- 1 Video Wall with Controller
- PA System Console
- Any other necessary Console
- Access Control

### 3.2 Tunnel Operations Control Centre (TOCC)
- **Video Wall & Controller**
- **Operator consoles** (minimum 2)
- **Supervisor console**
- Server Room
- Visitor Gallery
- Welfare facilities for 24x7 Operations
- Access Control

### 3.3 Road Asset Management Centre (RAMC)
- Similar to TOCC but at larger scale depending upon number of TOCC's to be handled
- TOCC & RAMC may be co-located but roles clearly demarcated from operational view-point

### 3.4 Tunnel Categories Reference

| Tunnel Length (m) | NFPA 502 & IAN 020 | CD 352 | EU Directive 2004/54 |
|-------------------|---------------------|---------|----------------------|
| <90               | X                   | /       | /                    |
| <150              | A                   | /       | /                    |
| >150, <240        | A                   | D       | /                    |
| >240, <300        | B                   | D       | /                    |
| >300, <1000       | C                   | 5 x C / 5 x D | 5 x Class I    |
| >1000             | D                   | B       | Class II             |

---

## 4. UI Components & Modules

### 4.1 Core UI Modules

#### Dashboard Module
- Real-time sensor data display
- Analytics views
- Reports generation
- Status indicators for all subsystems

#### Configuration Module
- Human configuration interface
- Command interface to actuators
- System configuration controls
- Subsystem enable/disable functionality

#### Logging Module
- Human log data entry
- Automated system log capture
- Transaction logging
- Incident closure notes with file upload facility

#### Alert & Notification System
- On-screen alerts
- Email notifications
- Audio alerts (PA system integration)
- Popup confirmation dialogs

### 4.2 Specialized UI Components

#### Network Monitoring
- Service Level Monitoring Software UI
- Network status visualization
- SLA monitoring and reporting

#### CCTV/Video Management
- PTZ (Pan-Tilt-Zoom) camera control
- Auto-zoom to incident areas
- Multi-camera display
- Video wall management

#### Emergency Communication
- PA (Public Address) System Console interface
- Pre-recorded message selection
- Radio Rebroadcast control interface

---

## 5. Visualization Requirements

### 5.1 Synthetic 2D/3D Visualization

#### Base Requirements (Prelim_FR0_e, Prelim_FR0_i_a)
- **Format Support**: AutoCAD, Shape files, self-created overlay files
- **Visualization Type**: Synthetic 2D and 3D capable
- **Underlay/Overlay System**: Support for multiple layers
- **Base Synthetic Map**: Project-specific tunnel layout as over/underlay file

### 5.2 Color-Coded Zone Visualization

#### Lighting Zones (Prelim_FR0_i_b)
- **Threshold Zone** - Color coded overlay
- **Transition Zone** - Color coded overlay
- **Interior Zone** - Color coded overlay
- **Output Format**: Overlay/underlay file

#### Fire Life Safety & Security System (FLSSS) Zones (Prelim_FR0_i_c)
- **Alarm Zone** - Color coded overlay
- **Activated Zone** - Color coded overlay
- **Output Format**: Overlay/underlay file

#### ITS (VIDS, etc.) Zones (Prelim_FR0_i_d)
- **Lane monitoring** - Color coded overlay
- **Monitoring sections** - Color coded overlay
- **Output Format**: Overlay/underlay file

### 5.3 Display Capabilities
- Real-time sensor data overlay on synthetic map
- Traffic flow visualization
- Environmental data (temperature, CO, visibility, wind speed)
- Equipment status indicators
- Incident location markers
- Camera field-of-view indicators

---

## 6. User Interaction Patterns

### 6.1 Control Room Hierarchy (Prelim_FR1)

**Priority Order**: Local Panel > TSB > TOCC > RAMC

**UI Behavior**:
- UI receives input from Human
- UI publishes to Platform & Database
- Platform publishes to Rule Engine
- Rule Engine maintains hierarchy

### 6.2 Role-Based Override (Prelim_FR2)

**Override Capability**:
- May or may not be feasible to override Local Panel
- Requires role-based access control
- Must maintain audit trail

**UI Workflow**:
1. Human initiates override request via UI
2. UI publishes to Platform & Database
3. Platform publishes to UI
4. Rule Engine overrides CR Hierarchy

**Recover to Normal Hierarchy**:
- Pop-up confirmation to cancel role-based override
- If confirmed (Yes), return to Prelim_FR1 (standard hierarchy)

### 6.3 System Operating Mode Selection (Prelim_FR3)

**UI Functionality**:
- Human selects operating mode via UI
- UI switches to specific mode-appropriate interface (if applicable)
- Platform publishes mode change
- Rule Engine switches to prescribed operation mode (software sub-module)

**Software Sub-Module Activation**:
- May further activate specific incident-based sub-module
- Functions reference Incident-Response (IR) Matrix

### 6.4 Data Archiving & Retrieval (Prelim_FR4)
- UI provides human interface for Output/Input to/from External/Shared Storage
- Archive historical data
- Retrieve reports and logs

---

## 7. Operating Modes

### 7.1 Normal System Operating Mode

#### Automatic System Operating Mode
- All Tunnel Systems operating automatically in response to various controlling parameters
- Ready to respond to changed conditions
- Minimal operator intervention required

#### Pre-determined System Operating Mode
- Systems operated in predetermined or pre-programmed sequence
- **Examples**:
  - Morning and afternoon rush hour periods
  - VIP transit operations
  - Ventilation pre-programmed modes

### 7.2 Incident Operating Mode

**UI Requirements**:
- Pre-programmed modes for quick operator response
- Minimal number of operations required
- Reduced potential for operator error
- **One-command capability**: Operator should be able to implement partial or total tunnel bore closure including appropriate tunnel diversion with one command on the HMI screen

**Examples of Incident Modes**:
- Traffic control systems activation
- DMS (Dynamic Message Signs) control
- Lane usage signals during tunnel incidents
- Ventilation, Public Address, Fire Fighting systems during fire incidents

### 7.3 Maintenance Mode

#### Tunnel Maintenance Mode
- Predetermined operation specifically for maintenance activities
- Specific and detailed closure plan
- Specific operation or shutdown of various systems

#### Partial Systems Capacity Operations
- UI must display:
  - Systems operating at reduced capacity
  - Acceptable level of reduced capacity
  - Triggers for tunnel closure
  - Mitigating provisions in place

### 7.4 Tunnel Closure Mode

#### Unplanned Closure
**RAMC Operator Interface Must Support**:
- Full tunnel closure activation
- Partial tunnel closure activation
- Control of: DMS, LCS (Lane Control Signals), automatic closure systems

**Closure Triggers** (UI must alert on):
- Fire in the tunnel
- Tunnel Flooded
- Hazardous spillage
- Total failure of mains power (including Emergency Power Supplies)
- Total failure of lighting
- Dangerously high levels of pollution
- Other safety critical reasons (road traffic collision, etc.)

#### Planned Closure
- Schedule closure during minimum traffic impact periods
- UI for closure planning and scheduling

---

## 8. Incident Management UI

### 8.1 General Incident Response Pattern

**Automated Detection & Operator Confirmation Flow**:

1. **System Detection** (Platform receives sensor input)
2. **Rule Engine Analysis** 
   - Auto-zoom PTZ camera to incident area
3. **Operator Alert** (UI)
   - **PTZ camera pop-up** with incident view
   - Confirmation / rejection options
4. **Upon Operator Confirmation**:
   - Execute automated response sequence
   - Log human actions to database
   - Provide incident closure notes interface (with file upload facility)

### 8.2 Operator Interface for Incident Confirmation

**Pop-up Dialog Requirements**:

#### Event Confirmation
- **Action**: Operator to confirm popup
- **Result**: Following interface elements appear:

#### Signages Control
- **VMS message no.** - Selection from message list
- **Lane sign** - Green / Red toggle
- **Speed Limit** - Numeric input field
- **Blinkers** - On / Off toggle

#### Public Announcement
- **PA Console Control** - Interface to PA system
- **Pre-recorded message no.** - Selection from message list

#### Environmental Controls
- **Ventilation** - On / Off control
- **Lighting** - Luminance value input field

#### Timeout Behavior
- **IF no input received**: All automated procedures start as defined in incident response rules

### 8.3 Incident-Specific UI Requirements

#### Vehicle Breakdown (Prelim_FR5)
**Detection Sources**:
- VAID (Vehicle Automatic Incident Detection)
- ECB (Emergency Call Box) / Emergency Panel
- Manual operator input

**UI Outputs**:
1. PTZ pop-up with incident location
2. Confirmation/rejection dialog
3. Incident logging interface
4. Closure notes with file upload

**Automated Actions (upon confirmation)**:
- Set Traffic Management plan
- Increase lighting
- PA system message
- Radio Rebroadcast Message
- Set speed limit in VSLS (Variable Speed Limit Signs)
- Lane sign switched to close on affected lane
- Display relevant message on VMS and advisory signs
- Switch on blinkers

#### Loss of Load / Objects in Carriageway (Prelim_FR6)
**Same UI pattern as Vehicle Breakdown**

#### Hazardous Fluid Spillage / Toxic Gas Release (Prelim_FR7)
**Additional UI Elements**:
- **Drainage Control**: Inhibit drainage pumps (checkbox/toggle)
- **Ventilation Mode**: Start appropriate ventilation fans as per defined rules (mode selector)

**Automated Actions**:
- Set Traffic Management plan
- Increase lighting
- PA system message
- Radio Rebroadcast Message
- Inhibit drainage pumps
- Set messages in VMS and advisory signs
- Start appropriate ventilation fans

#### Fire Incident (Prelim_FR9)
**Additional Monitoring Displays**:
- Fire Extinguisher pressure monitoring
- Visibility monitor readings

**Additional UI Controls**:
- Fire suppression system activation
- Emergency ventilation mode selection
- Emergency evacuation procedures

#### Vehicle Tunnel Equipment / Structure Hit & Damaged (Prelim_FR10)
**Same general UI pattern with**:
- CCTV camera pop-up focused on damaged equipment
- Maintenance notification interface
- Equipment status override controls

#### Traffic Queue (Prelim_FR11)
**UI Displays**:
- Traffic density visualization
- Queue length indicators
- VMS message selection for queue warning

**Incident Closure**:
- Record incident closure notes
- Log resolution actions

#### Over-height Vehicle (Prelim_FR12)
**Detection Display**:
- Over-height detection sensor alert
- Vehicle height measurement display
- PTZ camera pop-up

**Control Options**:
- Lane closure controls
- Warning sign activation
- Traffic diversion plan selection

#### Loss or Overhanging Tunnel Equipment (Prelim_FR13)
**UI Requirements**:
- CCTV pop-up focused on affected equipment
- Equipment status display
- Maintenance alert generation

---

## 9. Standards & Compliance

### 9.1 Referenced Standards

#### Control Room Design
- **NFPA 502**: Control Responsibility demarcation of ITS Network and ITS Tunnel responsibility
  - Tunnel ITS monitoring & controlling using Tunnel SCADA system
  - Tunnel ITS includes all equipment related to stopping traffic approaching tunnels & stopping traffic from entering tunnels direct approaches
  
- **CD 352**: Design of Road Tunnels

- **ISO 11064**: Ergonomic design of Control Centre
  - Workstation ergonomics
  - Display positioning
  - Operator comfort and safety

- **PIARC Technical Committee Report**: Road Tunnels: Vehicle Emissions and Air Demand for Ventilation

### 9.2 UI Design Principles

#### Usability
- Minimal operator intervention for routine operations
- Quick response capability in incidents (single-command operations where possible)
- Reduced potential for operator error
- Clear visual hierarchy

#### Safety
- Fail-safe design
- Confirmation dialogs for critical operations
- Audit trail for all operator actions
- Clear indication of system status

#### Accessibility
- 24x7 operational capability
- Role-based access control with logging
- Multiple control room level support (TSB, TOCC, RAMC)
- Support for operator training and drill modes

---

## 10. Technical Specifications

### 10.1 Protocol Support
**Vital Protocols**:
- MODBUS
- MQTT (Publish-Subscribe Middleware)
- OPC
- NTCIP
- OEM proprietary protocols

**Essential Protocols**:
- CoAP
- REST-API
- AMQP

### 10.2 Data Communication Patterns

#### Platform Software to UI
- **Publish-Subscribe Model** via MQTT or others
- Raw Sensor Data
- System alerts and status
- Actuator feedback

#### UI to Platform Software
- **Configuration Commands**
- **Actuator Commands**
- Human Log Data
- Incident confirmation/rejection

#### UI to Database
- Direct access for reporting
- Historical data retrieval
- Incident logging

### 10.3 Security Requirements

#### IT Security
- **Firewall**: Protection at network boundaries
- **Virus/Malware Protection**: Real-time scanning
- **VPN**: Secure remote access
- **Encrypted Inter-module Transfer**: All data exchange encrypted
- **Access Control**: Role-based with logging
- **Audit Trail**: All operator actions logged

### 10.4 System Configuration

#### Configurable Subsystems (Prelim_FR0_h)
- System must be configurable to include/exclude sub-systems
- Quantity adjustable per project-specific needs
- Human input via UI published to all modules for self-configuration
- Must estimate minimum compute configuration for each system configuration mode

### 10.5 DevOps & Deployment

#### Environment Management
- Sandboxed Applications using Docker / VM Environment
- DevOps capable for continuous deployment
- Version control and rollback capabilities

#### Monitoring
- **Prometheus** for resource monitoring
- Alert generation (on-screen & email)
- Performance metrics tracking

### 10.6 Training & Testing

#### Operator Training / Drill Module (Prelim_FR0_f)
- **Status**: Desirable feature
- Allows operator training without affecting live systems
- Drill scenarios for incident response

#### Emulator Capabilities (Prelim_FR0_g)
- **Platform Software**: Emulator for sensor inputs
- **Rule Engine**: Emulator for actuator outputs
- **UI**: Input-Output, MIS (Report) Templates

---

## 11. UI Input/Output Specifications

### 11.1 Human Inputs (via UI)
- System configuration
- Operating mode selection
- Incident confirmation/rejection
- Manual control commands to actuators
- Override commands (role-based)
- Incident closure notes
- Data archiving requests
- Report generation parameters

### 11.2 UI Outputs (to Human)
- **Dashboards**: Real-time system status
- **Analytics**: Trend analysis and predictions
- **Reports**: MIS reports, incident reports, maintenance logs
- **Alerts**: Pop-up notifications for incidents
- **Video Feeds**: PTZ camera views, CCTV monitoring
- **Synthetic Maps**: Tunnel visualization with overlays
- **System Status**: Equipment status, network status, power status

### 11.3 Data Flow Architecture

```
┌─────────────────────────────────────────────────┐
│          Sensors, Products, Actuators           │
│         (Field & Control Rooms)                 │
└───────────────┬────────────────┬────────────────┘
                │                │
         Input  │                │ Output
    (Modbus/    │                │ (Modbus/
     Others)    ▼                ▼  Others)
        ┌───────────────────────────────┐
        │    Platform Software          │
        │  ┌─────────────────────────┐  │
        │  │ MQTT/OPC/MODBUS/REST    │  │
        │  │ Protocol Handlers       │  │
        │  └─────────────────────────┘  │
        └───┬──────────┬──────────┬─────┘
            │          │          │
      Publish│    ◄────┤          │Subscribe
            │          │          │
    ┌───────▼──────┐   │   ┌──────▼────────┐
    │   Database   │   │   │  Rule Engine  │
    │   (MySQL)    │◄──┘   │               │
    │              │       │               │
    │ - Sensor Data│       │ - Analytics   │
    │ - Logs       │       │ - Automation  │
    │ - Config     │       │ - Rules       │
    └──────▲───────┘       └───────┬───────┘
           │                       │
           │                       │
      ┌────┴───────────────────────▼────┐
      │         MMI / UI, MIS            │
      │                                  │
      │  ┌────────────────────────────┐  │
      │  │  Dashboards & Analytics    │  │
      │  ├────────────────────────────┤  │
      │  │  Configuration Interface   │  │
      │  ├────────────────────────────┤  │
      │  │  Incident Management       │  │
      │  ├────────────────────────────┤  │
      │  │  Synthetic Visualization   │  │
      │  ├────────────────────────────┤  │
      │  │  Reports & Logging         │  │
      │  └────────────────────────────┘  │
      └────────────▲─────────────────────┘
                   │
                   │ Human Interaction
                   │ (Configuration, Commands,
                   │  Monitoring, Responses)
                   │
              ┌────▼─────┐
              │ Operators│
              │Supervisors│
              └──────────┘
```

---

## 12. Implementation Priorities

### 12.1 Critical (Must Have)
- Control room hierarchy implementation
- Incident detection and alerting
- PTZ camera integration and pop-ups
- Operator confirmation workflow
- Synthetic map visualization with overlays
- Equipment control interfaces (VMS, Lane signs, Lighting, Ventilation)
- Data logging and audit trail
- System operating mode switching
- Emergency closure controls

### 12.2 Essential (Should Have)
- Network monitoring and SLA tracking
- MIS reporting capabilities
- Role-based access control with override
- PA system integration
- Advanced analytics and trending
- Incident history and retrieval
- Configuration management interface
- DevOps integration

### 12.3 Desirable (Nice to Have)
- Operator training / drill module
- 3D visualization capabilities
- Advanced prediction analytics
- Mobile operator interface
- Voice control integration
- AI-assisted decision support

---

## 13. Next Steps

### 13.1 UI/UX Design Phase
1. Create detailed wireframes for each control room level (TSB, TOCC, RAMC)
2. Design incident response workflow mockups
3. Develop synthetic map visualization prototypes
4. Create operator workstation layout designs
5. Design role-based dashboard configurations

### 13.2 Technical Architecture
1. Select UI framework (Web-based vs. Desktop application)
2. Define API contracts between UI and Platform Software
3. Design database schema for UI-specific data
4. Plan WebSocket/MQTT subscription patterns for real-time updates
5. Define authentication and authorization mechanisms

### 13.3 Integration Planning
1. Platform Software API integration points
2. Database connection and query optimization
3. CCTV/PTZ camera system integration
4. PA system interface specifications
5. Email and notification service integration

### 13.4 Testing Strategy
1. Usability testing with operators
2. Load testing for multiple simultaneous incidents
3. Failover testing (DC-DR scenarios)
4. Role-based access control testing
5. End-to-end incident response workflow testing
6. Performance testing for real-time data updates

---

## 14. Appendices

### Appendix A: Glossary

- **TSB**: Tunnel Services Building - Local tunnel control facility
- **TOCC**: Tunnel Operations Control Centre - Primary tunnel monitoring and control
- **RAMC**: Road Asset Management Centre - Regional/corridor level traffic management
- **SCADA**: Supervisory Control and Data Acquisition
- **MMI**: Man-Machine Interface
- **HMI**: Human-Machine Interface
- **PTZ**: Pan-Tilt-Zoom (camera)
- **VMS**: Variable Message Sign / Dynamic Message Sign
- **VSLS**: Variable Speed Limit Sign
- **LCS**: Lane Control Signal
- **PA**: Public Address (system)
- **VAID**: Vehicle Automatic Incident Detection
- **ECB**: Emergency Call Box
- **FLSSS**: Fire Life Safety & Security System
- **ITS**: Intelligent Transportation System
- **VIDS**: Video Incident Detection System
- **PMCS**: Plant Monitoring & Control System
- **TMCS**: Tunnel Management Control System
- **DC-DR**: Data Center - Disaster Recovery
- **MIS**: Management Information System
- **MQTT**: Message Queuing Telemetry Transport
- **OPC**: OLE for Process Control
- **MODBUS**: Serial communication protocol
- **REST**: Representational State Transfer
- **CoAP**: Constrained Application Protocol
- **AMQP**: Advanced Message Queuing Protocol

### Appendix B: Reference Documents
1. System Requirements Specifications (SyRS) Ver 20 Feb 2025 - 133 pages
2. User Requirements Specifications (URS) Ver 30 Jan 2025 - 99 pages
3. NFPA 502 - Standard for Road Tunnels, Bridges, and Other Limited Access Highways
4. CD 352 - Design of Road Tunnels
5. ISO 11064 - Ergonomic design of Control Centres

### Appendix C: Incident Response Matrix
*(Detailed incident response matrix should be extracted separately from the URS document - refer to incident-specific sections in URS pages 12-99)*

---

**Document End**

---

*This specification document has been compiled from the SyRS and URS documents. For detailed system-specific requirements, integration specifications, and vendor-specific implementations, please refer to the complete source documents and subsequent technical design documents.*

