# Smart Lens Documentation Map

## Quick Navigation Guide

This document provides a quick reference to navigate the Smart Lens documentation suite.

---

## 📋 Documentation Overview

| Component | Type | File | Lines | Purpose |
|-----------|------|------|-------|---------|
| **Model** | SRS | [MODEL_SRS.md](model/MODEL_SRS.md) | ~400 | AI Model Requirements |
| **Model** | SDS | [MODEL_SDS.md](model/MODEL_SDS.md) | ~700 | AI Model Design |
| **Model** | Research | [DATASET_AND_MODEL_RESEARCH.md](model/DATASET_AND_MODEL_RESEARCH.md) | ~1,100 | Datasets, Models, Storage |
| **App** | SRS | [APP_SRS.md](app/APP_SRS.md) | ~850 | App Requirements |
| **App** | SDS | [APP_SDS.md](app/APP_SDS.md) | ~1100 | App Design |

**Total Documentation**: ~4,400 lines across 5 comprehensive documents

---

## 🎯 Where to Start

### For Project Managers / Stakeholders
1. Read [README.md](../README.md) for system overview
2. Review [MODEL_SRS.md](model/MODEL_SRS.md) - Section 2 & 3 (Model capabilities & requirements)
3. Review [APP_SRS.md](app/APP_SRS.md) - Section 2 & 3 (App features & requirements)

### For AI/ML Developers
1. Start with [MODEL_SRS.md](model/MODEL_SRS.md) - Complete document
2. **Review [DATASET_AND_MODEL_RESEARCH.md](model/DATASET_AND_MODEL_RESEARCH.md) - Datasets & Pretrained Models** ⭐ NEW!
3. Deep dive into [MODEL_SDS.md](model/MODEL_SDS.md) - Sections 2-7
4. Review interface specifications in MODEL_SDS.md - Section 6

### For Mobile App Developers
1. Start with [APP_SRS.md](app/APP_SRS.md) - Complete document
2. Deep dive into [APP_SDS.md](app/APP_SDS.md) - Sections 2-5
3. Review UI specifications in APP_SDS.md - Section 5

### For Integration Engineers
1. Read [README.md](../README.md) - Section "Component Interaction"
2. Review [MODEL_SDS.md](model/MODEL_SDS.md) - Section 6 (Interfaces)
3. Review [APP_SDS.md](app/APP_SDS.md) - Section 7 (API Integration)

### For Security Auditors
1. Review [MODEL_SRS.md](model/MODEL_SRS.md) - Section 4 (NFR-M5: Robustness)
2. Review [APP_SRS.md](app/APP_SRS.md) - Section 7 (Security Requirements)
3. Review [APP_SDS.md](app/APP_SDS.md) - Section 9 (Security Design)

---

## 📚 Document Summaries

### MODEL_SRS.md - AI Detection Model Requirements

**Key Sections**:
- **Section 3**: Functional Requirements (FR-M1 to FR-M6)
  - Video stream processing
  - Motion detection
  - Threat classification
  - Model training and updates
- **Section 4**: Non-Functional Requirements
  - Performance: 15+ FPS, <100ms latency
  - Accuracy: 80%+ mAP, 75%+ precision
  - Scalability: 4+ concurrent streams
- **Section 5**: Model Interfaces
  - Input: RTSP streams
  - Output: JSON detection events
  - API endpoints for integration

**Critical Requirements**:
- ✅ Process 4+ camera streams simultaneously
- ✅ Detect theft, violence, weapons, and fire
- ✅ Achieve 80%+ mean Average Precision (mAP)
- ✅ Maintain <100ms processing latency
- ✅ Support 24/7 continuous operation

---

### MODEL_SDS.md - AI Detection Model Design

**Key Sections**:
- **Section 2**: System Architecture
  - Pipeline architecture (Video → Motion → Classifier → Output)
  - Component interaction diagrams
- **Section 3**: Component Design
  - VideoStreamHandler
  - MotionDetector
  - ThreatClassifier (YOLO-based)
  - ConfigurationManager
  - OutputFormatter
- **Section 7**: Processing Pipeline
  - Main processing loop
  - Optimization strategies

**Technology Stack**:
- Python 3.8+, PyTorch/TensorFlow
- YOLOv8 for object detection
- OpenCV for video processing
- FastAPI for model server
- GPU: NVIDIA with CUDA support

**Key Algorithms**:
- Frame differencing for motion detection
- YOLOv8 for threat classification
- Non-Maximum Suppression (NMS)
- Confidence thresholding

---

### DATASET_AND_MODEL_RESEARCH.md - Research Guide ⭐ NEW!

**Key Sections**:
- **Section 2**: Dataset Research
  - Public datasets for theft, violence, weapons, fire
  - Recommended sizes: 3,000-5,000 images for FYP
  - Annotation tools and strategies
  - Data augmentation techniques
- **Section 3**: Pretrained Models
  - YOLOv8 variants (nano, small, medium)
  - Transfer learning approaches
  - Training configurations and timelines
  - Model selection criteria
- **Section 4**: Existing Solutions
  - Commercial systems (Deep Sentinel, Solink, Verkada)
  - Academic research approaches
  - Open source projects
  - Best practices from industry
- **Section 5**: Storage Optimization
  - Motion-based vs AI-triggered recording
  - Hybrid storage strategies (local + cloud)
  - Video compression techniques
  - Cost analysis and budgeting
- **Section 6**: FYP Recommendations
  - Realistic dataset sizes (2,000-5,000 images)
  - Expected model performance
  - Implementation timeline (14 weeks)
  - Budget: <$100 total

**Critical Recommendations**:
- ✅ Use YOLOv8s pretrained on COCO dataset
- ✅ Collect 3,000-4,000 annotated images
- ✅ Train on Google Colab (free T4 GPU)
- ✅ Implement motion-based recording (90% storage savings)
- ✅ Use Backblaze B2 for cloud storage (~$1.50/month)
- ✅ Expected 75-85% mAP with proper training

**Technology Stack**:
- Datasets: Roboflow Universe, Kaggle, UCF Crime
- Annotation: Roboflow (free tier: 1,000 images)
- Training: Google Colab / Kaggle (free GPU)
- Storage: Backblaze B2, local HDD
- Model: YOLOv8 (Ultralytics)

---

### APP_SRS.md - Mobile Application Requirements

**Key Sections**:
- **Section 3**: Functional Requirements (FR-A1 to FR-A8)
  - User authentication with 2FA
  - Camera management (add/edit/remove)
  - Live feed monitoring
  - Alert and notification system
  - Event recording and playback
  - Alert forwarding and sharing
- **Section 4**: Non-Functional Requirements
  - Performance: <2s launch, <5s live feed load
  - Usability: Simple interface for non-technical users
  - Security: HTTPS, encrypted storage, 2FA mandatory
- **Section 5**: User Interface Requirements
  - Navigation structure
  - Screen specifications
  - Design guidelines

**Critical Requirements**:
- ✅ Mandatory Two-Factor Authentication (2FA)
- ✅ Support up to 10 cameras per user
- ✅ Receive alerts within 30 seconds of detection
- ✅ Multi-camera live feed dashboard
- ✅ User-friendly for limited technical expertise

---

### APP_SDS.md - Mobile Application Design

**Key Sections**:
- **Section 2**: Application Architecture
  - Layered architecture (Presentation → Application → Domain → Data)
  - Clean architecture principles
  - Folder structure
- **Section 3**: Component Design
  - Authentication module
  - Camera management module
  - Alert module
  - Live feed module
- **Section 5**: User Interface Design
  - Screen specifications (Login, Dashboard, Alerts, etc.)
  - Widget components
  - Design system (colors, typography, spacing)
- **Section 8**: State Management
  - Provider pattern
  - AuthProvider, CameraProvider, AlertProvider

**Technology Stack**:
- Flutter 3.10+, Dart
- Provider for state management
- Dio for HTTP networking
- Hive for local storage
- Firebase Cloud Messaging (FCM)
- video_player for playback

**Architecture Pattern**:
- Clean Architecture with layered approach
- MVVM-like pattern with Providers
- Repository pattern for data access

---

## 🔗 Interface Specifications

### Model → Backend API

**Endpoint**: `POST http://backend/api/events/detection`

**Payload**:
```json
{
  "timestamp": "ISO-8601",
  "camera_id": "string",
  "event_type": "theft|violence|weapon|fire",
  "confidence": 0.87,
  "bounding_boxes": [...],
  "motion_intensity": 68.5
}
```

### Backend → App (Push Notification)

**Service**: Firebase Cloud Messaging (FCM)

**Payload**:
```json
{
  "notification": {
    "title": "Smart Lens Security Alert!",
    "body": "Theft detected at Front Door"
  },
  "data": {
    "alert_id": "uuid",
    "event_type": "theft",
    "camera_id": "CAM_001",
    "video_url": "https://...",
    "timestamp": "ISO-8601"
  }
}
```

### App → Backend API

**Authentication**: JWT Bearer token

**Key Endpoints**:
- `POST /auth/login` - User authentication
- `POST /auth/verify-2fa` - 2FA verification
- `GET /cameras` - List user cameras
- `POST /cameras` - Add new camera
- `GET /alerts` - List alerts with filters
- `DELETE /alerts/{id}` - Delete alert

---

## 📊 Requirements Traceability

### Model Requirements Coverage

| Requirement ID | Description | Covered in SDS |
|---------------|-------------|----------------|
| FR-M1 | Video Stream Processing | Section 3.1 |
| FR-M2 | Motion Detection | Section 3.2 |
| FR-M3 | Threat Classification | Section 3.3 |
| FR-M4 | Confidence Thresholding | Section 3.3 |
| FR-M5 | Model Training | Appendix A |
| FR-M6 | Output Generation | Section 3.5 |

### App Requirements Coverage

| Requirement ID | Description | Covered in SDS |
|---------------|-------------|----------------|
| FR-A1 | User Authentication | Section 3.1 |
| FR-A2 | Camera Management | Section 3.2 |
| FR-A3 | Live Feed Monitoring | Section 3.4 |
| FR-A4 | Alert System | Section 3.3 |
| FR-A5 | Event Playback | Section 3.3 |
| FR-A6 | Alert Forwarding | Section 3.3 |
| FR-A7 | Settings | Section 5.1 |
| FR-A8 | Search & Filter | Section 3.3 |

---

## 🎨 Visual Documentation

### Architecture Diagrams

| Diagram | Location | Description |
|---------|----------|-------------|
| System Architecture | README.md | High-level system overview |
| Model Architecture | MODEL_SDS.md - Section 2 | Model component interaction |
| App Architecture | APP_SDS.md - Section 2 | App layer architecture |
| Data Flow | MODEL_SDS.md - Section 5 | Processing pipeline flow |
| Class Diagram | MODEL_SDS.md - Section 4 | Model classes and relationships |
| UI Flow | APP_SDS.md - Section 5 | App screen navigation |

### Sequence Diagrams

Referenced in original SDS document:
- Threat Detection & Alert Flow
- Two-Factor Authentication Flow
- Administrator AI Model Update Flow

---

## 🔍 Search Quick Reference

### Looking for...

**Performance Requirements?**
- Model: MODEL_SRS.md → Section 6
- App: APP_SRS.md → NFR-A1

**Security Requirements?**
- Model: MODEL_SRS.md → NFR-M5
- App: APP_SRS.md → Section 7

**API Specifications?**
- Model: MODEL_SDS.md → Section 6
- App: APP_SDS.md → Section 7

**UI Design?**
- App: APP_SDS.md → Section 5

**Database Schema?**
- App: APP_SDS.md → Section 6

**Testing Requirements?**
- Model: MODEL_SRS.md → Section 6.4
- App: APP_SRS.md → Appendix D

**Deployment?**
- Model: MODEL_SDS.md → Section 8
- App: APP_SDS.md → Section 10

**Datasets & Training?**  ⭐ NEW!
- DATASET_AND_MODEL_RESEARCH.md → Section 2 (Datasets)
- DATASET_AND_MODEL_RESEARCH.md → Section 3 (Pretrained Models)
- DATASET_AND_MODEL_RESEARCH.md → Section 6 (FYP Recommendations)

**Storage Optimization?**  ⭐ NEW!
- DATASET_AND_MODEL_RESEARCH.md → Section 5 (Storage Optimization)
- Cost analysis and cloud storage options
- Motion-based vs AI-triggered recording

**Existing Solutions?**  ⭐ NEW!
- DATASET_AND_MODEL_RESEARCH.md → Section 4 (Existing Solutions & Strategies)
- Commercial systems and academic research
- Best practices from industry

---

## 📝 Document Conventions

### Requirement IDs

- **FR-M**: Functional Requirement - Model
- **FR-A**: Functional Requirement - App
- **NFR-M**: Non-Functional Requirement - Model
- **NFR-A**: Non-Functional Requirement - App

### Interface IDs

- **I-M**: Input Interface - Model
- **O-M**: Output Interface - Model
- **API-M**: API Endpoint - Model

---

## 🔄 Updates and Versioning

All documents are version 1.0 (Last Updated: 2025-11-04)

When making updates:
1. Update the version number
2. Update the "Last Updated" date
3. Document changes in the commit message
4. Update this map if new sections are added

---

## 📞 Getting Help

For questions about:
- **Model Architecture**: Review MODEL_SDS.md Section 2-3
- **App Architecture**: Review APP_SDS.md Section 2-3
- **Integration**: Review README.md and interface sections
- **Requirements**: Start with respective SRS documents

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-04  
**Maintained By**: Smart Lens Development Team
