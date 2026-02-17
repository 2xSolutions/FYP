# Smart Lens AI Detection Model - Software Requirements Specification (SRS)

## Document Information
- **Project**: Smart Lens CCTV Surveillance System - AI Detection Model
- **Version**: 1.0
- **Last Updated**: 2025-11-04

## Table of Contents
1. [Introduction](#1-introduction)
2. [Model Overview](#2-model-overview)
3. [Functional Requirements](#3-functional-requirements)
4. [Non-Functional Requirements](#4-non-functional-requirements)
5. [Model Interfaces](#5-model-interfaces)
6. [Performance Requirements](#6-performance-requirements)
7. [Appendices](#7-appendices)

---

## 1. Introduction

### 1.1 Purpose of Document
This Software Requirements Specification (SRS) document defines the requirements for the **AI Detection Model** component of the Smart Lens CCTV Surveillance System. The model is responsible for real-time video analysis, threat detection, and classification of security events.

### 1.2 Scope
The AI Detection Model is a standalone component that:
- Processes live video streams from IP cameras
- Detects motion and suspicious activities
- Classifies threats (theft, violence, fire hazards)
- Provides confidence scores for detected events
- Operates independently from the application layer

### 1.3 Intended Audience
- AI/ML Development Team
- System Architects
- Integration Engineers
- Testing and QA Team

### 1.4 Definitions and Acronyms
| Term | Definition |
|------|------------|
| AI | Artificial Intelligence |
| CNN | Convolutional Neural Network |
| YOLO | You Only Look Once (Object Detection Algorithm) |
| RTSP | Real-Time Streaming Protocol |
| FPS | Frames Per Second |
| IoU | Intersection over Union |
| mAP | Mean Average Precision |

---

## 2. Model Overview

### 2.1 Model Purpose
The AI Detection Model serves as the core intelligence component that enables proactive threat detection by continuously analyzing video feeds and identifying suspicious behavioral patterns.

### 2.2 Key Capabilities
- **Real-time Video Processing**: Process video frames at minimum 15 FPS
- **Motion Detection**: Identify motion events to trigger recording
- **Threat Classification**: Detect and classify multiple threat types
- **Confidence Scoring**: Provide probability scores for each detection
- **Batch Processing**: Support multiple concurrent video streams

### 2.3 Threat Categories
The model is trained to detect the following threat categories:
1. **Theft**: Suspicious movements near sensitive zones
2. **Violence**: Fighting, physical altercations, weapon detection (guns, knives)
3. **Fire Hazards**: Smoke and fire detection
4. **Crowd Disturbances**: Abnormal crowd behavior

---

## 3. Functional Requirements

### FR-M1: Video Stream Processing

**FR-M1.1**: The model shall accept video input via RTSP protocol from IP cameras.

**FR-M1.2**: The model shall process video frames at a minimum rate of 15 FPS for each camera stream.

**FR-M1.3**: The model shall support concurrent processing of at least 4 camera streams simultaneously.

**FR-M1.4**: The model shall handle video resolutions from 480p to 1080p.

**FR-M1.5**: The model shall perform frame preprocessing including resizing and normalization.

### FR-M2: Motion Detection

**FR-M2.1**: The model shall detect motion in video frames using frame differencing or optical flow techniques.

**FR-M2.2**: The model shall generate a motion flag (True/False) for each processed frame.

**FR-M2.3**: The model shall provide motion intensity scores (0-100) indicating the amount of movement detected.

**FR-M2.4**: The model shall filter out noise and insignificant movements (e.g., camera shake, lighting changes).

**FR-M2.5**: The model shall output motion-detected frames for further threat analysis.

### FR-M3: Threat Detection and Classification

**FR-M3.1**: The model shall analyze motion-detected frames using a deep learning CNN architecture (YOLO-based).

**FR-M3.2**: The model shall detect and classify the following event types:
- **FR-M3.2.1**: Theft (suspicious behavior near sensitive zones)
- **FR-M3.2.2**: Violence (fighting, physical altercations)
- **FR-M3.2.3**: Weapon Detection (guns, knives)
- **FR-M3.2.4**: Fire Hazards (smoke, flames)

**FR-M3.3**: The model shall output bounding box coordinates for detected objects/events.

**FR-M3.4**: The model shall provide a confidence score (0-100%) for each detection.

**FR-M3.5**: The model shall support multi-object detection within a single frame.

### FR-M4: Confidence Thresholding

**FR-M4.1**: The model shall apply configurable confidence thresholds for each threat category.

**FR-M4.2**: The model shall only output detections that exceed the minimum confidence threshold.

**FR-M4.3**: Default confidence thresholds shall be:
- Theft: 70%
- Violence: 75%
- Weapon Detection: 80%
- Fire Hazards: 85%

**FR-M4.4**: The model shall allow threshold adjustments without requiring retraining.

### FR-M5: Model Training and Updates

**FR-M5.1**: The model shall support training on custom datasets using transfer learning.

**FR-M5.2**: The model shall accept annotated datasets in YOLO format (class, x_center, y_center, width, height).

**FR-M5.3**: The model shall support model weight updates without system downtime.

**FR-M5.4**: The model shall maintain versioning for all trained models.

**FR-M5.5**: The model shall log training metrics including loss, precision, recall, and mAP.

### FR-M6: Output Generation

**FR-M6.1**: The model shall output detection results in JSON format containing:
- Timestamp
- Camera ID
- Event type
- Confidence score
- Bounding box coordinates
- Frame metadata

**FR-M6.2**: The model shall generate annotated video frames with bounding boxes overlaid.

**FR-M6.3**: The model shall trigger event flags for integration with the storage and alerting systems.

---

## 4. Non-Functional Requirements

### NFR-M1: Performance

**NFR-M1.1**: The model shall process each video frame within 66ms (15 FPS minimum).

**NFR-M1.2**: The model shall maintain processing latency under 100ms per frame on GPU-enabled hardware.

**NFR-M1.3**: The model shall support 24/7 continuous operation without performance degradation.

**NFR-M1.4**: The model shall process 4 concurrent video streams with less than 5% CPU/GPU overhead per additional stream.

### NFR-M2: Accuracy and Reliability

**NFR-M2.1**: The model shall achieve a minimum mean Average Precision (mAP) of 80% on the validation dataset.

**NFR-M2.2**: The model shall minimize false positives to maintain a precision rate above 75%.

**NFR-M2.3**: The model shall minimize false negatives to maintain a recall rate above 70%.

**NFR-M2.4**: The model shall handle challenging environmental conditions:
- Variable lighting (low light, bright sunlight)
- Partial occlusions
- Crowded scenes
- Camera motion/vibration

**NFR-M2.5**: The model shall maintain detection accuracy above 75% in sub-optimal conditions.

### NFR-M3: Scalability

**NFR-M3.1**: The model architecture shall support horizontal scaling to handle additional camera streams.

**NFR-M3.2**: The model shall efficiently utilize GPU resources with batch processing.

**NFR-M3.3**: The model memory footprint shall not exceed 4GB per instance.

### NFR-M4: Maintainability

**NFR-M4.1**: The model codebase shall follow modular design principles with clear separation of concerns.

**NFR-M4.2**: The model shall include comprehensive logging for debugging and monitoring.

**NFR-M4.3**: The model configuration shall be externalized in configuration files (YAML/JSON).

**NFR-M4.4**: The model shall support A/B testing for model version comparisons.

### NFR-M5: Robustness

**NFR-M5.1**: The model shall gracefully handle corrupted or incomplete video frames.

**NFR-M5.2**: The model shall recover from temporary stream disconnections without crashing.

**NFR-M5.3**: The model shall continue processing other streams if one stream fails.

**NFR-M5.4**: The model shall implement timeout mechanisms for stuck processing operations.

---

## 5. Model Interfaces

### 5.1 Input Interfaces

**Interface I-M1: Video Stream Input**
- **Source**: IP Cameras via RTSP
- **Format**: H.264/H.265 encoded video stream
- **Resolution**: 480p - 1080p
- **Frame Rate**: 15-30 FPS
- **Protocol**: RTSP (Real-Time Streaming Protocol)

**Interface I-M2: Configuration Input**
- **Source**: Configuration files or API
- **Format**: JSON/YAML
- **Parameters**:
  - Confidence thresholds
  - Model weights path
  - Camera metadata (ID, location)
  - Processing parameters

### 5.2 Output Interfaces

**Interface O-M1: Detection Events**
- **Consumer**: Application Backend / Storage Manager
- **Format**: JSON
- **Content**:
```json
{
  "timestamp": "2025-11-04T12:30:45Z",
  "camera_id": "CAM_001",
  "event_type": "theft",
  "confidence": 0.87,
  "bounding_boxes": [
    {
      "x": 245,
      "y": 120,
      "width": 80,
      "height": 150,
      "class": "person",
      "confidence": 0.92
    }
  ],
  "motion_intensity": 65
}
```

**Interface O-M2: Annotated Frames**
- **Consumer**: Application Backend / Storage Manager
- **Format**: Base64-encoded JPEG or raw frame buffer
- **Content**: Video frames with overlaid bounding boxes and labels

**Interface O-M3: Processing Metrics**
- **Consumer**: Monitoring/Admin Dashboard
- **Format**: JSON
- **Content**:
  - FPS (frames per second)
  - Inference time
  - GPU utilization
  - Detection counts
  - Error rates

### 5.3 API Endpoints

**API-M1: Process Frame**
```
POST /api/model/process
Input: Video frame (base64 or binary)
Output: Detection results (JSON)
```

**API-M2: Update Configuration**
```
POST /api/model/config
Input: Configuration parameters (JSON)
Output: Success/failure status
```

**API-M3: Load Model Weights**
```
POST /api/model/load
Input: Model file path or binary
Output: Success/failure status
```

**API-M4: Get Model Metrics**
```
GET /api/model/metrics
Output: Performance metrics (JSON)
```

---

## 6. Performance Requirements

### 6.1 Processing Speed
- **Single Stream**: Minimum 15 FPS, Target 30 FPS
- **Multiple Streams (4)**: Minimum 12 FPS per stream
- **Frame Processing Latency**: Maximum 100ms per frame

### 6.2 Detection Accuracy
- **Precision**: Minimum 75%
- **Recall**: Minimum 70%
- **mAP (Mean Average Precision)**: Minimum 80%
- **False Positive Rate**: Maximum 25%

### 6.3 Resource Utilization
- **GPU Memory**: Maximum 4GB per model instance
- **CPU Utilization**: Maximum 60% during peak processing
- **Inference Time**: Maximum 66ms per frame (15 FPS)

### 6.4 Reliability
- **Uptime**: 99% availability (24/7 operation)
- **Error Recovery**: Automatic recovery within 5 seconds
- **Mean Time Between Failures**: Minimum 720 hours (30 days)

---

## 7. Appendices

### Appendix A: Training Dataset Requirements

The model requires diverse datasets covering:
- **Theft Scenarios**: Shoplifting, grab-and-run, loitering near valuable items
- **Violence**: Physical altercations, fighting, aggressive behavior
- **Weapons**: Guns, knives, sharp objects
- **Fire Hazards**: Smoke, flames, burning objects
- **Environmental Variations**: Different lighting conditions, camera angles, occlusions

**Dataset Sources**:
- Kaggle public datasets
- Roboflow annotated datasets
- Custom-labeled footage from deployment sites

### Appendix B: Model Architecture

**Recommended Architecture**: YOLOv8 (You Only Look Once, Version 8)
- **Backbone**: CSPDarknet with spatial pyramid pooling
- **Neck**: Path Aggregation Network (PAN)
- **Head**: Detection head with anchor-free approach
- **Input Size**: 640x640 pixels
- **Output**: Bounding boxes with class probabilities

### Appendix C: Dependencies

**Core Libraries**:
- Python 3.8+
- PyTorch or TensorFlow
- OpenCV (cv2) for video processing
- NumPy for numerical operations
- Ultralytics (YOLOv8 implementation)

**Hardware Requirements**:
- GPU: NVIDIA GPU with CUDA support (minimum 4GB VRAM)
- CPU: Multi-core processor (4+ cores recommended)
- RAM: Minimum 8GB, recommended 16GB

### Appendix D: Model Versioning

Model versions shall follow semantic versioning:
- **Major**: Breaking changes in input/output format
- **Minor**: New threat categories or improved accuracy
- **Patch**: Bug fixes and minor optimizations

Example: v1.2.3
- v1: Current architecture (YOLOv8)
- v2: New threat category added
- v3: Bug fix in motion detection

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| AI/ML Lead | _______________ | _______________ | ______ |
| System Architect | _______________ | _______________ | ______ |
| Project Supervisor | _______________ | _______________ | ______ |

---

**End of Document**
