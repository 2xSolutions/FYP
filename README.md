# Smart Lens CCTV Surveillance System - Documentation

## Overview

This repository contains the complete documentation for the **Smart Lens CCTV Surveillance System**, a comprehensive AI-powered surveillance solution designed for small and medium-scale businesses. The system has been architecturally separated into two distinct components:

1. **AI Detection Model** - The core intelligence component responsible for video processing and threat detection
2. **Mobile Application** - The user-facing interface for monitoring, alerts, and system management

## Documentation Structure

```
docs/
├── model/                    # AI Detection Model Documentation
│   ├── MODEL_SRS.md         # Software Requirements Specification
│   └── MODEL_SDS.md         # Software Design Specification
└── app/                      # Mobile Application Documentation
    ├── APP_SRS.md           # Software Requirements Specification
    └── APP_SDS.md           # Software Design Specification
```

---

## Component Separation

### Why Separate Model and App?

The separation of the AI Detection Model from the Mobile Application follows the **Separation of Concerns** principle, providing several key benefits:

1. **Independent Development**: Teams can work on model improvements and app features simultaneously
2. **Scalability**: The model can be scaled independently based on processing demands
3. **Technology Flexibility**: Each component can use different technology stacks optimized for its purpose
4. **Testing & Deployment**: Easier to test, debug, and deploy each component separately
5. **Maintainability**: Changes to one component don't necessarily impact the other

### Architectural Separation

```
┌─────────────────────────────────────────────────────────────────┐
│                     Smart Lens System                            │
└─────────────────────────────────────────────────────────────────┘
                              │
                 ┌────────────┴────────────┐
                 │                         │
    ┌────────────▼──────────┐   ┌─────────▼──────────┐
    │  AI Detection Model   │   │  Mobile Application │
    │  ─────────────────    │   │  ─────────────────  │
    │  • Video Processing   │   │  • User Interface   │
    │  • Motion Detection   │   │  • Camera Mgmt      │
    │  • Threat Detection   │   │  • Alert Display    │
    │  • Classification     │   │  • Event Review     │
    │  • Confidence Scoring │   │  • User Auth        │
    └───────────┬───────────┘   └─────────┬───────────┘
                │                         │
                └────────┬────────────────┘
                         │
                ┌────────▼────────┐
                │  Backend API    │
                │  (Integration)  │
                └─────────────────┘
```

---

## AI Detection Model

### Purpose
The AI Detection Model is a standalone Python-based component that processes video streams in real-time to detect security threats.

### Key Responsibilities
- Accept RTSP video streams from IP cameras
- Perform motion detection to filter relevant frames
- Use deep learning (YOLOv8) to classify threats
- Generate confidence scores for detections
- Output structured detection events

### Technology Stack
- **Language**: Python 3.8+
- **Framework**: PyTorch/TensorFlow
- **Detection**: YOLOv8
- **Video Processing**: OpenCV
- **API**: FastAPI (for model server)

### Documentation
- **[MODEL_SRS.md](docs/model/MODEL_SRS.md)**: Complete requirements specification
- **[MODEL_SDS.md](docs/model/MODEL_SDS.md)**: Detailed design specification

### Key Features
✅ Real-time video processing at 15+ FPS  
✅ Multi-camera support (4+ concurrent streams)  
✅ Threat classification (theft, violence, fire, weapons)  
✅ Configurable confidence thresholds  
✅ GPU-accelerated inference  
✅ Model versioning and updates  

---

## Mobile Application

### Purpose
The Mobile Application provides an intuitive interface for shopkeepers to monitor their surveillance system, receive alerts, and manage cameras.

### Key Responsibilities
- User authentication with 2FA
- Camera registration and management
- Live feed monitoring (multi-camera view)
- Real-time alert notifications
- Event clip playback and management
- Alert forwarding to third parties

### Technology Stack
- **Framework**: Flutter 3.10+
- **Language**: Dart
- **Architecture**: Clean Architecture with Provider
- **Platform**: Android (iOS future)
- **Notifications**: Firebase Cloud Messaging (FCM)

### Documentation
- **[APP_SRS.md](docs/app/APP_SRS.md)**: Complete requirements specification
- **[APP_SDS.md](docs/app/APP_SDS.md)**: Detailed design specification

### Key Features
✅ Secure authentication with mandatory 2FA  
✅ Multi-camera live feed dashboard  
✅ Instant push notifications for threats  
✅ Event history with search and filters  
✅ Video playback with controls  
✅ Alert forwarding via email/SMS  
✅ User-friendly interface for non-technical users  

---

## Component Interaction

### Communication Flow

```
1. Video Input Flow:
   IP Camera → [RTSP] → AI Model → [Process] → Detection Event

2. Alert Flow:
   AI Model → [HTTP API] → Backend → [FCM] → Mobile App → User

3. User Action Flow:
   User → Mobile App → [REST API] → Backend → [Action] → System

4. Live Feed Flow:
   IP Camera → [RTSP] → Mobile App (direct streaming)
```

### Interface Contracts

#### Model Output to Backend
```json
{
  "timestamp": "2025-11-04T12:30:45Z",
  "camera_id": "CAM_001",
  "event_type": "theft",
  "confidence": 0.87,
  "bounding_boxes": [...],
  "motion_intensity": 65
}
```

#### Backend to App (Push Notification)
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
    "timestamp": "2025-11-04T12:30:45Z"
  }
}
```

#### App to Backend (API Calls)
```http
GET /api/cameras
Authorization: Bearer <JWT_TOKEN>

Response:
[
  {
    "id": "CAM_001",
    "name": "Front Door",
    "rtsp_url": "rtsp://...",
    "status": "online",
    "location": "Main Entrance"
  }
]
```

---

## System Requirements

### AI Detection Model Requirements

**Hardware**:
- GPU: NVIDIA GPU with 4GB+ VRAM (e.g., RTX 3060)
- CPU: 4+ cores
- RAM: 8GB minimum, 16GB recommended
- Storage: 20GB SSD

**Software**:
- Python 3.8+
- CUDA 11.8+ (for GPU acceleration)
- Linux (Ubuntu 22.04 recommended)

### Mobile Application Requirements

**Development**:
- Flutter SDK 3.10+
- Android Studio / VS Code
- Android SDK (API 26+)

**User Devices**:
- Android 8.0 (API 26) or higher
- 2GB RAM minimum
- 100MB available storage
- Active internet connection

---

## Dependencies Between Components

### Model → Backend
- **Type**: HTTP REST API
- **Protocol**: HTTPS
- **Format**: JSON
- **Frequency**: Real-time (per detection event)

### Backend → App
- **Type**: REST API + Push Notifications
- **Protocol**: HTTPS + FCM
- **Format**: JSON
- **Frequency**: On-demand + real-time alerts

### App → Model
- **Type**: No direct connection
- **Note**: All interactions are mediated through the Backend API

---

## Development Workflow

### Model Development
1. Train/update AI model on labeled datasets
2. Test model accuracy and performance
3. Deploy model to server/cloud instance
4. Expose model via FastAPI endpoints
5. Integrate with backend API

### App Development
1. Design UI/UX mockups
2. Implement screens and widgets
3. Integrate with backend APIs
4. Test on physical devices
5. Deploy to Play Store

### Integration Testing
1. Test end-to-end flow (camera → model → app)
2. Verify alert delivery latency
3. Test multi-camera performance
4. Validate security mechanisms
5. User acceptance testing

---

## Security Considerations

### Model Security
- Secure API endpoints with authentication
- Rate limiting to prevent abuse
- Input validation for video frames
- Model weights protection
- Audit logging for all operations

### App Security
- Encrypted data transmission (HTTPS/TLS)
- Secure token storage (Keychain/Keystore)
- Mandatory Two-Factor Authentication
- Input validation and sanitization
- Session management and timeout

### Shared Security
- JWT-based authentication
- Role-based access control
- Data encryption at rest and in transit
- Regular security audits
- Compliance with privacy regulations

---

## Performance Benchmarks

### Model Performance
- **Processing Speed**: 15-30 FPS per camera
- **Latency**: <100ms per frame
- **Accuracy**: 80%+ mAP
- **Concurrent Streams**: 4+ cameras

### App Performance
- **Launch Time**: <2 seconds
- **Live Feed Load**: <5 seconds
- **Notification Delay**: <30 seconds
- **UI Responsiveness**: <100ms touch response

---

## Future Enhancements

### Model Improvements
- [ ] Add more threat categories
- [ ] Improve accuracy in low-light conditions
- [ ] Support for 8K video resolution
- [ ] Edge device deployment (Jetson Nano)
- [ ] Facial recognition (with privacy controls)

### App Enhancements
- [ ] iOS version
- [ ] Web dashboard
- [ ] Video analytics and reports
- [ ] Integration with smart home devices
- [ ] Multi-language support
- [ ] Offline mode capabilities

---

## Documentation Standards

Each component's documentation follows IEEE standards for SRS and SDS documents:

### SRS (Software Requirements Specification)
- Functional requirements
- Non-functional requirements
- Interface requirements
- Performance criteria
- Security requirements

### SDS (Software Design Specification)
- System architecture
- Component design
- Class diagrams and data models
- Interface specifications
- Deployment architecture

---

## Quick Links

### Model Documentation
- [Model SRS - Requirements](docs/model/MODEL_SRS.md)
- [Model SDS - Design](docs/model/MODEL_SDS.md)

### App Documentation
- [App SRS - Requirements](docs/app/APP_SRS.md)
- [App SDS - Design](docs/app/APP_SDS.md)

### Original Documentation
- [Original SRS (DOCX)](Smart%20Lens%20SRS%20Finalized%20(1).docx)
- [Original SDS (PDF)](Smart%20Lens%20SDS%20Finalized%20(1).pdf)

---

## Contributing

When contributing to either component, please:

1. Review the relevant SRS and SDS documents
2. Follow the architectural separation guidelines
3. Maintain backward compatibility of interfaces
4. Update documentation to reflect changes
5. Add appropriate tests
6. Follow coding standards for the respective technology

---

## Contact & Support

**Project Team**:
- Ali Raza Memon (023-22-0200)
- Aadil Shah (023-22-0106)
- Waseem Mazari (023-22-0102)

**Supervisor**: Madam Faryal Shamsi

**Project Code**: 22-F-49

---

## License

[Include appropriate license information]

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-04  
**Status**: Active Development
