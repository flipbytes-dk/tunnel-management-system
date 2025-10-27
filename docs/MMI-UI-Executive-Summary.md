# MMI/UI Specifications - Executive Summary

## Analysis Overview

**Analyst**: Mary (Business Analyst)  
**Date**: October 27, 2025  
**Source Documents Analyzed**:
- System Requirements Specifications (SyRS) v20 Feb 2025 (133 pages)
- User Requirements Specifications (URS) v30 Jan 2025 (99 pages)

---

## Key Deliverables

### 1. Comprehensive MMI/UI Specification Document
**Location**: `docs/MMI-UI-Specifications.md`  
**Size**: 774 lines  
**Sections**: 14 major sections with appendices

---

## Critical UI Components Identified

### Control Room Hierarchy (3 Levels)
1. **TSB (Tunnel Services Building)** - Local control
   - 1-2 workstations depending on tunnel category
   - SCADA terminals
   - CCTV monitoring
   - PA system console

2. **TOCC (Tunnel Operations Control Centre)** - Primary operations
   - Video walls
   - Multiple operator consoles (minimum 2)
   - Supervisor console
   - 24x7 operations capability

3. **RAMC (Road Asset Management Centre)** - Regional management
   - Large-scale TOCC equivalent
   - Multi-tunnel coordination
   - Regional traffic routing

**Priority**: Local Panel > TSB > TOCC > RAMC

---

## Core UI Requirements

### 1. Visualization System
- **Synthetic 2D/3D Maps** with overlay/underlay capability
- **File Format Support**: AutoCAD, Shape files, GIS formats
- **Color-Coded Zones**:
  - Lighting zones (Threshold, Transition, Interior)
  - Fire safety zones (Alarm, Activated)
  - Traffic monitoring zones (Lanes, sections)

### 2. Incident Management Interface
**Automated Detection → Operator Confirmation → Automated Response**

**UI Pattern**:
1. PTZ camera auto-zoom to incident
2. Pop-up alert with incident details
3. Operator confirms/rejects
4. Upon confirmation: Control interface appears
5. Operator can adjust automated actions or accept defaults
6. All actions logged with incident closure notes

**Incidents Handled**:
- Vehicle breakdown
- Objects in carriageway
- Hazardous spillage
- Fire events
- Equipment damage
- Over-height vehicles
- Traffic queuing
- Equipment failure

### 3. Control Interfaces
**Signage Controls**:
- VMS (Variable Message Signs) - message selection from library
- Lane control signs - red/green status
- Speed limit signs - numeric input
- Blinkers - on/off toggle

**Environmental Controls**:
- Lighting - luminance level input
- Ventilation - on/off and mode selection
- Drainage - pump controls

**Communication Controls**:
- PA system - pre-recorded message selection
- Radio rebroadcast control

### 4. Operating Modes
1. **Normal Mode**
   - Automatic operations
   - Pre-determined sequences (rush hour, VIP)

2. **Incident Mode**
   - Quick response workflows
   - One-command closure capability
   - Pre-programmed incident responses

3. **Maintenance Mode**
   - Planned maintenance operations
   - Partial capacity management

4. **Closure Mode**
   - Planned closures
   - Emergency unplanned closures

---

## Technical Architecture

### Platform Stack
- **OS**: Linux
- **Clustering**: Hadoop (Active-Active DC-DR)
- **Containerization**: Docker
- **Database**: MySQL
- **Runtime**: Python
- **Monitoring**: Prometheus

### Integration Protocols
**Vital**: MODBUS, MQTT, OPC, NTCIP, OEM proprietary  
**Essential**: CoAP, REST-API, AMQP

### Communication Pattern
```
Sensors → Platform Software ↔ Database
                ↕              ↕
        Rule Engine    ←→    MMI/UI
                              ↕
                          Operators
```

### Data Flows
- **Platform → UI**: MQTT Publish-Subscribe, real-time sensor data, alerts
- **UI → Platform**: Configuration commands, actuator commands, human inputs
- **UI ↔ Database**: Direct access for logging, reporting, historical data

---

## Security & Compliance

### Security Requirements
- **Access Control**: Role-based with full audit logging
- **Network Security**: Firewall, VPN, encrypted transfers
- **Malware Protection**: Real-time scanning
- **Override Control**: Role-based override with confirmation dialogs

### Standards Compliance
- **NFPA 502**: Control responsibility demarcation
- **CD 352**: Road tunnel design
- **ISO 11064**: Ergonomic control centre design
- **PIARC**: Ventilation and emissions standards

---

## Implementation Priorities

### CRITICAL (Must Have) - Phase 1
✓ Control room hierarchy  
✓ Incident detection & alerting  
✓ PTZ camera integration  
✓ Operator confirmation workflows  
✓ Synthetic map visualization  
✓ Equipment control interfaces  
✓ Data logging & audit trails  
✓ Operating mode switching  
✓ Emergency closure controls  

### ESSENTIAL (Should Have) - Phase 2
✓ Network monitoring & SLA tracking  
✓ MIS reporting  
✓ Role-based access control  
✓ PA system integration  
✓ Advanced analytics  
✓ Incident history & retrieval  
✓ Configuration management  

### DESIRABLE (Nice to Have) - Phase 3
✓ Operator training/drill module  
✓ 3D visualization  
✓ Predictive analytics  
✓ Mobile interface  
✓ AI decision support  

---

## Hardware Requirements Summary

### TSB (Category ≥ B)
- 2 Operator workstations
- 1 Supervisor workstation
- 2 SCADA terminals with monitors
- 1 Video wall with controller
- PA system console
- Access control system

### TOCC
- Video wall & controller
- Minimum 2 operator consoles
- Supervisor console
- Server room infrastructure
- Visitor gallery
- 24x7 welfare facilities

### RAMC
- Scaled-up TOCC configuration
- Multi-tunnel monitoring capability
- Regional coordination center

---

## Key Design Principles Identified

### Usability
1. **Minimal Operator Intervention** - Automated operations for routine tasks
2. **Quick Response** - One-command operations for critical incidents
3. **Error Reduction** - Pre-programmed sequences, confirmation dialogs
4. **Clear Visual Hierarchy** - Synthetic maps with color-coded overlays

### Safety
1. **Fail-Safe Design** - Default to safe state
2. **Confirmation Dialogs** - For critical operations
3. **Audit Trail** - All operator actions logged
4. **Status Indication** - Clear system status at all times

### Accessibility
1. **24x7 Operations** - Designed for continuous operation
2. **Role-Based Access** - Appropriate access levels with logging
3. **Multi-Level Support** - TSB, TOCC, RAMC coordination
4. **Training Capability** - Operator training and drill modes

---

## Incident Response UI Pattern (Standard)

```
┌─────────────────────────────────────────────────┐
│  1. AUTOMATED DETECTION                         │
│     - Sensor triggers alert                     │
│     - Rule engine analyzes                      │
└────────────────┬────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────┐
│  2. AUTO PTZ ZOOM                               │
│     - Camera auto-focuses on incident           │
└────────────────┬────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────┐
│  3. OPERATOR ALERT (POP-UP)                     │
│     - Video feed from PTZ camera                │
│     - Incident details                          │
│     - [CONFIRM] or [REJECT] buttons             │
└────────────────┬────────────────────────────────┘
                 ▼ (if confirmed)
┌─────────────────────────────────────────────────┐
│  4. CONTROL INTERFACE APPEARS                   │
│     ┌─────────────────────────────────────┐     │
│     │ Signages                            │     │
│     │  □ VMS: [Select Message ▼]         │     │
│     │  □ Lane Sign: ● Red ○ Green        │     │
│     │  □ Speed Limit: [___] km/h         │     │
│     │  □ Blinkers: ● On ○ Off            │     │
│     ├─────────────────────────────────────┤     │
│     │ Public Announcement                 │     │
│     │  □ [Select PA Message ▼]           │     │
│     ├─────────────────────────────────────┤     │
│     │ Environmental                       │     │
│     │  □ Ventilation: ● On ○ Off         │     │
│     │  □ Lighting: [____] lux            │     │
│     └─────────────────────────────────────┘     │
│                                                  │
│     ⏱ Timeout: If no input, automated actions   │
│                execute after [X] seconds         │
└────────────────┬────────────────────────────────┘
                 ▼
┌─────────────────────────────────────────────────┐
│  5. EXECUTION & LOGGING                         │
│     - Commands sent to actuators                │
│     - All actions logged to database            │
│     - Incident closure notes interface          │
│     - File upload capability                    │
└─────────────────────────────────────────────────┘
```

---

## Critical UI Elements Detailed

### Pop-Up Dialogs
**Requirements**:
- Non-blocking (can be moved)
- Always on top
- Audio alert option
- Color-coded by severity
- Timestamp display
- Incident ID reference

### Synthetic Map Display
**Layers**:
1. Base tunnel layout
2. Lighting zones overlay
3. Fire safety zones overlay
4. Traffic zones overlay
5. Equipment status icons
6. Active incident markers
7. Camera field-of-view indicators

**Interactions**:
- Click equipment icon → status details
- Click incident marker → incident details
- Click camera icon → video feed
- Double-click zone → control interface

### Video Wall Management
**Capabilities**:
- Multi-camera display (grid layout)
- Auto-switch to incident camera
- Manual camera selection
- PTZ controls per camera
- Preset positions
- Tour sequences
- Recording controls

---

## Data Logging Requirements

### Operator Action Logging
- **Who**: User ID and role
- **What**: Action taken
- **When**: Timestamp (millisecond precision)
- **Where**: Control room level (TSB/TOCC/RAMC)
- **Why**: Incident ID or routine operation
- **Result**: Success/failure status

### Incident Logging
- **Detection**: Automated detection source
- **Confirmation**: Operator confirmation/rejection with timestamp
- **Actions**: All automated and manual actions taken
- **Timeline**: Complete incident timeline
- **Closure**: Closure notes with file attachments
- **Duration**: Time from detection to resolution

### System Logging
- All configuration changes
- Operating mode switches
- System status changes
- Network events
- Database transactions
- Error conditions

---

## Recommended Next Steps

### Phase 1: Requirements Refinement (2-3 weeks)
1. **Stakeholder Workshops** with tunnel operators
2. **Wireframe Design** for each control room type
3. **Workflow Mapping** for each incident type
4. **Hardware Selection** for workstations and video walls
5. **API Specification** for Platform Software integration

### Phase 2: Detailed Design (4-6 weeks)
1. **UI/UX Design** - High-fidelity mockups
2. **Technical Architecture** - Detailed system design
3. **Database Schema** - UI-specific data structures
4. **Integration Specifications** - All external system interfaces
5. **Security Design** - Authentication, authorization, audit

### Phase 3: Prototype Development (6-8 weeks)
1. **Core UI Framework** - Basic shell with navigation
2. **Synthetic Map Module** - Visualization prototype
3. **Incident Management Module** - Single incident type workflow
4. **CCTV Integration** - PTZ camera control
5. **Operator Testing** - Usability validation

### Phase 4: Full Development & Testing (12-16 weeks)
1. **Complete UI Implementation**
2. **Integration Testing** with all subsystems
3. **Load & Performance Testing**
4. **Operator Training** - Comprehensive training program
5. **Pilot Deployment** - Single tunnel trial

---

## Risk Considerations

### Technical Risks
- **Real-time Performance**: High-frequency sensor data updates
- **Network Latency**: Multi-site coordination (TSB-TOCC-RAMC)
- **Video Streaming**: Multiple HD camera feeds simultaneously
- **Database Performance**: High-volume logging requirements

**Mitigations**:
- Load testing early in development
- CDN for video streaming
- Database optimization and partitioning
- Caching strategies for frequently accessed data

### Operational Risks
- **Operator Training**: Complex system with many features
- **Human Error**: Critical safety operations
- **Handover Procedures**: 24x7 shift changes

**Mitigations**:
- Comprehensive training program
- Confirmation dialogs for critical operations
- Automated handover checklists
- Operator drill/training mode

### Compliance Risks
- **Standards Adherence**: NFPA 502, ISO 11064, CD 352
- **Audit Requirements**: Complete action logging

**Mitigations**:
- Engage compliance experts early
- Regular compliance audits during development
- Comprehensive audit trail design

---

## Budget Considerations

### Development Effort Estimate
- **UI/UX Design**: 4-6 weeks (1-2 designers)
- **Frontend Development**: 16-20 weeks (3-4 developers)
- **Backend Integration**: 8-12 weeks (2-3 developers)
- **Testing & QA**: 8-10 weeks (2 QA engineers)
- **Documentation**: 4-6 weeks (1 technical writer)

### Hardware Investment (per Control Room)
- **TSB (Category B)**: $50,000 - $80,000
- **TOCC**: $150,000 - $250,000
- **RAMC**: $200,000 - $400,000

### Software Licensing
- **Operating System**: Open-source (Linux) - Minimal cost
- **Database**: MySQL - Open-source option available
- **Monitoring**: Prometheus - Open-source
- **Containerization**: Docker - Open-source
- **CCTV/SCADA Integration**: Vendor-specific licensing

---

## Conclusion

The MMI/UI specifications extracted from the SyRS and URS documents provide a comprehensive foundation for developing a modern, scalable, and safe Tunnel Management Control System interface. 

**Key Strengths**:
- Well-defined control room hierarchy
- Clear incident response workflows
- Strong emphasis on operator safety
- Compliance with international standards
- Scalable architecture with redundancy

**Areas Requiring Further Definition**:
- Detailed UI mockups and screen layouts
- Specific CCTV vendor integration protocols
- Custom synthetic map file format specifications
- Exact timing parameters for automated responses
- Mobile/remote access requirements

**Overall Assessment**:
The specification provides a solid foundation for proceeding with detailed design and development. The emphasis on automation with operator oversight, combined with fail-safe defaults and comprehensive logging, aligns well with modern tunnel management best practices.

---

**Document Generated By**: Mary - Business Analyst  
**Source Documents**: SyRS v20 Feb 2025 (133 pages), URS v30 Jan 2025 (99 pages)  
**Comprehensive Specification**: `docs/MMI-UI-Specifications.md` (774 lines, 14 sections)  
**Extracted Text Files**: `docs/SyRS_extracted.txt`, `docs/URS_extracted.txt`


