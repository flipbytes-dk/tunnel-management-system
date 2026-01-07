# LiDAR Scanner Decision Matrix
## Tunnel Management System - Equipment Selection

**Document Version:** 1.0
**Date:** December 2024
**Prepared By:** Technical Consultant
**Decision Required By:** VaaaN Infra Private Limited
**Related Procurement:** PROC2.4c (LiDAR Mapping System)

---

## 1. Executive Summary

VaaaN requires a LiDAR scanner for tunnel mapping to support:
1. **SLAM Reference Map** - High-accuracy 3D model for AR positioning
2. **Virtual Walkthrough** - "Street View" style navigation in TMS application
3. **Future Projects** - Reusable asset for other tunnel projects

This document provides a structured comparison of scanner options to facilitate VaaaN's purchase decision.

### Quick Recommendation

| Priority | Best Choice | Total Cost (INR) | Key Advantage |
|----------|-------------|------------------|---------------|
| **Budget** | GeoSLAM ZEB Horizon | 22,50,000 | Lowest cost |
| **Balanced** | **Leica BLK2GO** | **28,00,000** | **Best value - fast + camera** |
| **Premium** | Leica RTC360 | 42,00,000 | Highest accuracy + HDR |

**Consultant Recommendation:** Leica BLK2GO (Balanced option)

---

## 2. Requirements Analysis

### 2.1 Functional Requirements

| Requirement | Priority | Notes |
|-------------|----------|-------|
| Capture tunnel geometry | Critical | Walls, floor, ceiling, equipment |
| Accuracy for SLAM | Critical | < 3cm required for AR positioning |
| Colored point cloud | High | Enables virtual walkthrough without 360° cameras |
| Fast capture | High | Minimize tunnel closure time |
| Site team operable | High | VaaaN staff, not survey specialists |
| Reusable for future projects | Medium | Asset value consideration |
| Indoor/GPS-denied operation | Critical | Tunnel environment |

### 2.2 Environmental Conditions

| Condition | Impact on Scanner Selection |
|-----------|----------------------------|
| Low light | Requires good low-light camera (if color needed) |
| Dust/particulates | IP rating consideration |
| Long distances (>100m) | Range capability |
| Reflective surfaces | Handling of specular reflections |
| Temperature 10-35°C | Standard operating range |

---

## 3. Scanner Options Comparison

### 3.1 Shortlisted Scanners

| # | Scanner | Type | Manufacturer | Country |
|---|---------|------|--------------|---------|
| A | GeoSLAM ZEB Horizon | Handheld SLAM | GeoSLAM | UK |
| B | Leica BLK2GO | Handheld SLAM | Leica Geosystems | Switzerland |
| C | Faro Focus S 150 | Tripod TLS | Faro | USA |
| D | Leica RTC360 | Tripod TLS | Leica Geosystems | Switzerland |
| E | Faro Focus Premium | Tripod TLS | Faro | USA |

### 3.2 Technical Specifications

| Specification | ZEB Horizon | BLK2GO | Focus S 150 | RTC360 | Focus Premium |
|---------------|-------------|--------|-------------|--------|---------------|
| **Type** | Handheld | Handheld | Tripod | Tripod | Tripod |
| **Range** | 100m | 25m | 150m | 130m | 150m |
| **Accuracy** | ±1-3cm | ±1-2cm | ±1mm | ±1mm | ±1mm |
| **Points/sec** | 300,000 | 420,000 | 976,000 | 2,000,000 | 976,000 |
| **Scan Speed** | Real-time | Real-time | 3-5 min/scan | 2 min/scan | 3-5 min/scan |
| **Weight** | 1.1 kg | 0.7 kg | 4.2 kg | 5.4 kg | 4.2 kg |
| **Battery** | 4 hrs | 55 min | 4.5 hrs | 2 hrs | 4.5 hrs |
| **Color Camera** | No | Yes (basic) | Optional | Yes (36MP HDR) | Yes (HDR) |
| **IP Rating** | IP64 | IP54 | IP54 | IP54 | IP54 |
| **Operating Temp** | -10 to 45°C | 5 to 40°C | 5 to 40°C | -5 to 40°C | 5 to 40°C |

### 3.3 Capture Method Comparison

```
HANDHELD SCANNERS (ZEB Horizon, BLK2GO):
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│    Operator walks through tunnel ──────────────────────────►           │
│    Scanner captures continuously                                        │
│    Real-time SLAM builds map                                           │
│                                                                         │
│    Time for 1km tunnel: ~15-30 minutes                                 │
│    Accuracy: ±1-3cm                                                    │
│    Ease of use: Very Easy                                              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘

TRIPOD SCANNERS (Focus S, RTC360, Focus Premium):
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│    ●────────●────────●────────●────────●────────●                      │
│   Scan 1   Scan 2   Scan 3   Scan 4   Scan 5   Scan 6                  │
│                                                                         │
│    Setup tripod at each position (10-15m apart)                        │
│    Wait 2-5 minutes per scan                                           │
│    Move to next position                                               │
│                                                                         │
│    Time for 1km tunnel: ~4-8 hours                                     │
│    Accuracy: ±1mm                                                      │
│    Ease of use: Moderate                                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Scoring Matrix

### 4.1 Evaluation Criteria & Weights

| Criterion | Weight | Rationale |
|-----------|--------|-----------|
| Accuracy | 20% | Critical for AR positioning |
| Capture Speed | 20% | Minimize tunnel disruption |
| Ease of Use | 15% | Site team (non-specialists) will operate |
| Color Capability | 15% | Virtual walkthrough requirement |
| Total Cost | 15% | Budget consideration |
| Reliability/Support | 10% | Long-term asset |
| Portability | 5% | Tunnel access logistics |

### 4.2 Detailed Scoring (1-10 scale)

| Criterion | Weight | ZEB Horizon | BLK2GO | Focus S 150 | RTC360 | Focus Premium |
|-----------|--------|-------------|--------|-------------|--------|---------------|
| Accuracy | 20% | 6 | 7 | 10 | 10 | 10 |
| Capture Speed | 20% | 10 | 10 | 4 | 6 | 4 |
| Ease of Use | 15% | 9 | 9 | 6 | 7 | 6 |
| Color Capability | 15% | 2 | 7 | 5 | 10 | 8 |
| Total Cost | 15% | 9 | 7 | 8 | 4 | 6 |
| Reliability/Support | 10% | 7 | 9 | 8 | 9 | 8 |
| Portability | 5% | 10 | 10 | 5 | 5 | 5 |
| **WEIGHTED SCORE** | **100%** | **7.35** | **8.10** | **6.55** | **7.55** | **6.70** |

### 4.3 Score Visualization

```
WEIGHTED SCORES:

BLK2GO      ████████████████████████████████████████░░░░  8.10  ★ RECOMMENDED
RTC360      ███████████████████████████████████░░░░░░░░░  7.55
ZEB Horizon ████████████████████████████████░░░░░░░░░░░░  7.35
Focus Prem  ██████████████████████████░░░░░░░░░░░░░░░░░░  6.70
Focus S 150 █████████████████████████░░░░░░░░░░░░░░░░░░░  6.55

            0    1    2    3    4    5    6    7    8    9    10
```

---

## 5. Cost Analysis

### 5.1 Total Cost of Ownership (5 Years)

| Cost Component | ZEB Horizon | BLK2GO | Focus S 150 | RTC360 | Focus Premium |
|----------------|-------------|--------|-------------|--------|---------------|
| Scanner | 20,00,000 | 25,00,000 | 24,00,000 | 38,00,000 | 32,00,000 |
| Registration Software | 50,000/yr | 50,000/yr | 3,00,000 | 4,00,000 | 3,00,000 |
| Targets & Accessories | 2,00,000 | 2,00,000 | 2,50,000 | 2,50,000 | 2,50,000 |
| Training | 50,000 | 50,000 | 1,00,000 | 1,00,000 | 1,00,000 |
| Maintenance (5yr) | 2,00,000 | 2,50,000 | 3,00,000 | 4,00,000 | 3,50,000 |
| **Year 1 Total** | **22,50,000** | **28,00,000** | **30,50,000** | **45,50,000** | **39,00,000** |
| **5-Year TCO** | **24,50,000** | **30,00,000** | **33,50,000** | **49,50,000** | **42,00,000** |

### 5.2 Cost vs Value Plot

```
                        HIGH VALUE
                            │
                            │           ★ BLK2GO
                            │              (Best Value)
                            │
                            │                      ● RTC360
                            │                        (Premium)
              ● ZEB Horizon │
                 (Budget)   │
                            │       ● Focus S 150
                            │
                            │              ● Focus Premium
                            │
         LOW VALUE ─────────┼─────────────────────────────────►
                            │                              HIGH COST
                     LOW COST
```

### 5.3 Impact on 360° Camera Decision

| Scanner | Has Color Camera | Can Replace PROC2.3? | Net Project Cost Impact |
|---------|------------------|---------------------|------------------------|
| ZEB Horizon | No | No - need 360° cameras | +18,50,000 |
| **BLK2GO** | **Yes (basic)** | **Possibly** | **+24,00,000** |
| Focus S 150 | Optional (+cost) | Possibly | +26,50,000 |
| RTC360 | Yes (36MP HDR) | Yes - eliminates need | +37,50,000 |
| Focus Premium | Yes (HDR) | Yes - eliminates need | +35,00,000 |

---

## 6. Pros & Cons Summary

### Option A: GeoSLAM ZEB Horizon

| Pros | Cons |
|------|------|
| ✅ Lowest cost | ❌ No color camera |
| ✅ Very fast capture | ❌ Lower accuracy (±1-3cm) |
| ✅ Easiest to use | ❌ Limited software ecosystem |
| ✅ Good for quick surveys | ❌ Still need 360° cameras |

**Best for:** Budget-constrained projects where color walkthrough not required.

---

### Option B: Leica BLK2GO (RECOMMENDED)

| Pros | Cons |
|------|------|
| ✅ Fast handheld capture | ⚠️ Medium cost |
| ✅ Good accuracy (±1-2cm) | ⚠️ Basic color (not HDR) |
| ✅ Built-in color camera | ⚠️ 25m range (sufficient for tunnel) |
| ✅ Lightweight (0.7 kg) | ⚠️ 55 min battery (swap needed) |
| ✅ Leica support & ecosystem | |
| ✅ Easy for non-specialists | |

**Best for:** Balanced requirement of speed, accuracy, and value. Recommended for this project.

---

### Option C: Faro Focus S 150

| Pros | Cons |
|------|------|
| ✅ Survey-grade accuracy (±1mm) | ❌ Slow capture (tripod setup) |
| ✅ Long range (150m) | ❌ No color camera (base model) |
| ✅ Good battery life | ❌ More complex operation |
| ✅ Established brand | ❌ Higher learning curve |

**Best for:** Projects requiring highest accuracy where time is not critical.

---

### Option D: Leica RTC360

| Pros | Cons |
|------|------|
| ✅ Survey-grade accuracy (±1mm) | ❌ Highest cost |
| ✅ Fast for tripod (2 min/scan) | ❌ Tripod setup still required |
| ✅ Excellent 36MP HDR camera | ❌ Heavier (5.4 kg) |
| ✅ Eliminates need for 360° cams | |
| ✅ Auto-registration VIS technology | |
| ✅ Best Leica software support | |

**Best for:** Premium projects requiring both accuracy and photorealistic walkthrough.

---

### Option E: Faro Focus Premium

| Pros | Cons |
|------|------|
| ✅ Survey-grade accuracy (±1mm) | ❌ High cost |
| ✅ HDR color camera | ❌ Slow capture |
| ✅ Long range | ❌ Faro-specific ecosystem |

**Best for:** Organizations already using Faro ecosystem.

---

## 7. Scenario Analysis

### Scenario 1: Minimum Budget

**Choice:** GeoSLAM ZEB Horizon + 360° Cameras (PROC2.3)

| Item | Cost (INR) |
|------|------------|
| ZEB Horizon + software | 22,50,000 |
| 360° Cameras (Insta360 Pro 2 × 2) | 4,00,000 |
| **Total** | **26,50,000** |

*Note: Requires two capture processes (LiDAR + 360° photos)*

---

### Scenario 2: Best Value (RECOMMENDED)

**Choice:** Leica BLK2GO

| Item | Cost (INR) |
|------|------------|
| BLK2GO + software | 28,00,000 |
| 360° Cameras | 0 - 4,00,000 (evaluate after test) |
| **Total** | **28,00,000 - 32,00,000** |

*Note: Test colored point cloud quality; add 360° cameras only if needed*

---

### Scenario 3: Premium Quality

**Choice:** Leica RTC360

| Item | Cost (INR) |
|------|------------|
| RTC360 + Cyclone | 42,00,000 |
| 360° Cameras | 0 (not needed - HDR camera) |
| **Total** | **42,00,000** |

*Note: Eliminates 360° camera requirement; highest quality output*

---

## 8. Decision Framework

### 8.1 Decision Questions for VaaaN

Please answer the following to finalize selection:

| # | Question | Option A | Option B |
|---|----------|----------|----------|
| 1 | Budget priority vs quality priority? | Budget | Quality |
| 2 | How important is virtual walkthrough quality? | Basic | High-fidelity |
| 3 | Will this scanner be used for other projects? | No | Yes |
| 4 | Is minimizing tunnel closure time critical? | No | Yes |
| 5 | Site team survey experience level? | Beginner | Experienced |

### 8.2 Decision Matrix

| If answers are... | Recommended Scanner |
|-------------------|---------------------|
| 1:Budget, 2:Basic, 3:No, 4:Yes, 5:Beginner | ZEB Horizon |
| 1:Budget, 2:Basic, 3:Yes, 4:Yes, 5:Beginner | **BLK2GO** |
| 1:Quality, 2:High, 3:No, 4:Yes, 5:Beginner | **BLK2GO** |
| 1:Quality, 2:High, 3:Yes, 4:Yes, 5:Any | **BLK2GO** |
| 1:Quality, 2:High, 3:Yes, 4:No, 5:Experienced | RTC360 |
| Any with highest accuracy requirement | RTC360 or Focus S |

---

## 9. Consultant Recommendation

### Primary Recommendation: Leica BLK2GO

**Rationale:**

1. **Best Value Score** (8.10/10) - Highest weighted score across criteria
2. **Speed** - Handheld capture minimizes tunnel disruption
3. **Ease of Use** - Site team can learn quickly (2-day training)
4. **Color Capability** - Built-in camera may eliminate 360° camera need
5. **Leica Ecosystem** - Compatible with Cyclone, ReCap, industry-standard workflows
6. **Future Value** - Useful for other VaaaN tunnel projects

### Alternative Recommendation: Leica RTC360

**If budget allows and highest quality walkthrough is required:**
- 36MP HDR camera produces photorealistic colored point clouds
- Definitively eliminates need for 360° cameras
- Survey-grade accuracy for all future projects
- Premium option justified if VaaaN has multiple tunnel projects planned

---

## 10. Next Steps

### Upon VaaaN Decision:

| Step | Action | Timeline |
|------|--------|----------|
| 1 | VaaaN reviews this document | Week 1 |
| 2 | VaaaN selects scanner option | Week 1 |
| 3 | Consultant arranges demo/quote | Week 2 |
| 4 | VaaaN procurement approval | Week 3 |
| 5 | Place order (PROC2.4c) | Week 4 |
| 6 | Delivery (6-8 weeks lead time) | Week 10-12 |
| 7 | Site team training (Task N.3.1) | Week 12-13 |
| 8 | First tunnel scan (Task N.3.3) | Week 14 |

### Required VaaaN Response:

Please provide:
1. ☐ Selected scanner option (A/B/C/D/E)
2. ☐ Budget approval confirmation
3. ☐ Site team members for training (2-3 names)
4. ☐ Preferred vendor contact (if any existing relationship)

---

## Appendix A: Vendor Contact Information

| Scanner | Vendor (India) | Contact |
|---------|----------------|---------|
| GeoSLAM ZEB | Trimble / Local distributors | geoslam.com |
| Leica BLK2GO | Leica Geosystems India | leica-geosystems.com |
| Leica RTC360 | Leica Geosystems India | leica-geosystems.com |
| Faro Focus | Faro India | faro.com |

---

## Appendix B: Sample Output Comparison

### Point Cloud Density (same tunnel section)

```
ZEB Horizon:    ●●●●●●●●●●●●●●●●●●●●  (Low-Medium density)
BLK2GO:         ●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●  (Medium density)
Focus S:        ●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●  (High density)
RTC360:         ●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●  (Very high)
```

### Color Quality (if applicable)

```
ZEB Horizon:    [No color]
BLK2GO:         ████████░░  (Good - basic camera)
Focus S:        ████████░░  (Good - optional camera)
RTC360:         ██████████  (Excellent - 36MP HDR)
Focus Premium:  █████████░  (Very Good - HDR)
```

---

## Appendix C: Glossary

| Term | Definition |
|------|------------|
| TLS | Terrestrial Laser Scanner (tripod-based) |
| SLAM | Simultaneous Localization and Mapping |
| HDR | High Dynamic Range (photography) |
| Point Cloud | 3D dataset of points captured by LiDAR |
| Registration | Process of aligning multiple scans |
| ICP | Iterative Closest Point (registration algorithm) |
| E57 | Standard point cloud file format |
| LAS | LiDAR data exchange format |

---

**Document Prepared By:** Technical Consultant
**Date:** December 2024
**Status:** Pending VaaaN Decision

---

*End of Document*
