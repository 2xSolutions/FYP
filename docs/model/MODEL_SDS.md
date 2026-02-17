# Smart Lens AI Detection Model - Software Design Specification (SDS)

## Document Information
- **Project**: Smart Lens CCTV Surveillance System - AI Detection Model
- **Version**: 1.0
- **Last Updated**: 2025-11-04

## Table of Contents
1. [Introduction](#1-introduction)
2. [System Architecture](#2-system-architecture)
3. [Component Design](#3-component-design)
4. [Class Diagrams](#4-class-diagrams)
5. [Data Flow](#5-data-flow)
6. [Interface Specifications](#6-interface-specifications)
7. [Processing Pipeline](#7-processing-pipeline)
8. [Deployment Architecture](#8-deployment-architecture)
9. [Appendices](#9-appendices)

---

## 1. Introduction

### 1.1 Purpose of Document
This Software Design Specification (SDS) provides a detailed technical design for the **AI Detection Model** component of Smart Lens. It translates the requirements from the Model SRS into a concrete architectural and implementation blueprint.

### 1.2 Scope
This document covers:
- Model architecture and component design
- Processing pipeline and data flow
- Interface specifications
- Class structures and relationships
- Deployment configuration

### 1.3 Definitions and Acronyms
| Term | Definition |
|------|------------|
| YOLO | You Only Look Once - Object Detection Framework |
| CNN | Convolutional Neural Network |
| GPU | Graphics Processing Unit |
| RTSP | Real-Time Streaming Protocol |
| NMS | Non-Maximum Suppression |
| IOU | Intersection Over Union |

---

## 2. System Architecture

### 2.1 High-Level Architecture

The AI Detection Model follows a **Pipeline Architecture** with the following stages:

```
[Video Stream Input] → [Frame Extractor] → [Motion Detector] → [Threat Classifier] → [Output Generator]
                                ↓                    ↓                    ↓
                          [Frame Buffer]      [Motion Filter]    [Confidence Filter]
```

### 2.2 Architectural Components

#### Component 1: Video Stream Handler
- **Purpose**: Capture and manage RTSP video streams
- **Responsibilities**:
  - Connect to IP cameras via RTSP
  - Extract frames at configured FPS
  - Handle connection failures and reconnection
  - Buffer frames for processing

#### Component 2: Motion Detection Engine
- **Purpose**: Identify frames with significant motion
- **Responsibilities**:
  - Compare consecutive frames
  - Calculate motion intensity
  - Filter noise and camera shake
  - Output motion flags and intensity scores

#### Component 3: Threat Classification Engine (AI Core)
- **Purpose**: Detect and classify security threats
- **Responsibilities**:
  - Load and manage YOLO model weights
  - Perform object detection inference
  - Apply Non-Maximum Suppression (NMS)
  - Generate confidence scores
  - Output detection results with bounding boxes

#### Component 4: Configuration Manager
- **Purpose**: Manage model configuration and parameters
- **Responsibilities**:
  - Load configuration from files
  - Manage confidence thresholds
  - Handle model version updates
  - Provide runtime parameter updates

#### Component 5: Output Formatter
- **Purpose**: Format and deliver detection results
- **Responsibilities**:
  - Convert detections to JSON format
  - Generate annotated frames
  - Trigger event notifications
  - Log processing metrics

### 2.3 Component Interaction Diagram

```
┌─────────────────────────────────────────────────────────┐
│                 AI Detection Model                       │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌─────────────┐      ┌──────────────┐                 │
│  │   Video     │─────→│    Frame     │                 │
│  │   Stream    │      │   Extractor  │                 │
│  │   Handler   │      └──────┬───────┘                 │
│  └─────────────┘             │                          │
│                               ↓                          │
│                      ┌───────────────┐                  │
│                      │    Motion     │                  │
│                      │   Detector    │                  │
│                      └───────┬───────┘                  │
│                              │                           │
│                              ↓                           │
│                   ┌─────────────────────┐              │
│                   │     Threat          │              │
│                   │   Classifier        │              │
│                   │   (YOLO Engine)     │              │
│                   └─────────┬───────────┘              │
│                             │                           │
│                             ↓                           │
│                   ┌─────────────────┐                  │
│                   │     Output      │                  │
│                   │    Formatter    │                  │
│                   └─────────────────┘                  │
│                             │                           │
└─────────────────────────────┼───────────────────────────┘
                              │
                              ↓
                    [App Backend / Storage]
```

---

## 3. Component Design

### 3.1 Video Stream Handler

**Class**: `VideoStreamHandler`

**Attributes**:
- `camera_id`: string - Unique camera identifier
- `rtsp_url`: string - RTSP stream URL
- `target_fps`: int - Target frames per second
- `connection_status`: enum - Connected, Disconnected, Error
- `frame_buffer`: Queue - Buffer for extracted frames

**Methods**:
```python
def __init__(camera_id: str, rtsp_url: str, target_fps: int)
def connect() -> bool
def disconnect() -> void
def extract_frame() -> Frame
def is_connected() -> bool
def reconnect() -> bool
def get_stream_info() -> dict
```

**Design Decisions**:
- Uses OpenCV `cv2.VideoCapture` for RTSP stream handling
- Implements exponential backoff for reconnection attempts
- Thread-safe frame buffer with maximum capacity of 30 frames
- Automatic frame dropping if buffer is full to prevent memory overflow

### 3.2 Motion Detection Engine

**Class**: `MotionDetector`

**Attributes**:
- `sensitivity`: float - Motion detection sensitivity (0-1)
- `noise_threshold`: int - Minimum pixel change threshold
- `previous_frame`: ndarray - Previous frame for comparison
- `motion_kernel_size`: tuple - Kernel size for morphological operations

**Methods**:
```python
def __init__(sensitivity: float, noise_threshold: int)
def detect_motion(current_frame: Frame, previous_frame: Frame) -> MotionResult
def calculate_motion_intensity(frame_diff: ndarray) -> float
def filter_noise(motion_mask: ndarray) -> ndarray
def reset() -> void
```

**Algorithm**:
1. Convert frames to grayscale
2. Calculate absolute difference between frames
3. Apply Gaussian blur to reduce noise
4. Threshold the difference to create motion mask
5. Apply morphological operations (erosion/dilation)
6. Calculate motion intensity as percentage of changed pixels
7. Return motion flag (True/False) and intensity score

**Design Decisions**:
- Uses frame differencing instead of background subtraction for efficiency
- Adaptive thresholding based on environmental conditions
- Morphological operations to connect nearby motion regions

### 3.3 Threat Classification Engine

**Class**: `ThreatClassifier`

**Attributes**:
- `model`: YOLOModel - Loaded YOLO model instance
- `model_version`: string - Current model version
- `confidence_thresholds`: dict - Thresholds per threat category
- `class_names`: list - List of detectable threat classes
- `device`: string - "cuda" or "cpu"

**Methods**:
```python
def __init__(model_path: str, device: str)
def load_model(model_path: str) -> bool
def classify(frame: Frame) -> DetectionResult
def apply_nms(detections: list, iou_threshold: float) -> list
def filter_by_confidence(detections: list) -> list
def update_thresholds(thresholds: dict) -> void
def get_model_info() -> dict
```

**Detection Pipeline**:
1. Preprocess frame (resize to 640x640, normalize)
2. Run YOLO inference
3. Apply Non-Maximum Suppression (NMS)
4. Filter detections by confidence threshold
5. Map class IDs to threat categories
6. Generate bounding box coordinates
7. Return structured detection results

**Design Decisions**:
- Uses YOLOv8 for state-of-art accuracy and speed
- Implements batch processing for multiple frames
- Supports GPU acceleration with automatic fallback to CPU
- Model weights loaded in memory for fast inference
- Configurable confidence thresholds per threat type

### 3.4 Configuration Manager

**Class**: `ConfigManager`

**Attributes**:
- `config_file_path`: string - Path to configuration file
- `config_data`: dict - Loaded configuration data
- `default_config`: dict - Default configuration values

**Methods**:
```python
def __init__(config_file_path: str)
def load_config() -> dict
def save_config(config: dict) -> bool
def get_parameter(key: str) -> any
def update_parameter(key: str, value: any) -> void
def validate_config() -> bool
def get_confidence_thresholds() -> dict
```

**Configuration Structure** (YAML):
```yaml
model:
  version: "1.0.0"
  weights_path: "./models/yolov8_threats.pt"
  device: "cuda"
  input_size: 640

detection:
  confidence_thresholds:
    theft: 0.70
    violence: 0.75
    weapon: 0.80
    fire: 0.85
  nms_threshold: 0.45
  max_detections: 100

motion:
  sensitivity: 0.6
  noise_threshold: 25
  min_motion_area: 500

processing:
  target_fps: 15
  batch_size: 1
  buffer_size: 30
```

### 3.5 Output Formatter

**Class**: `OutputFormatter`

**Attributes**:
- `output_format`: string - "json" or "binary"
- `include_annotations`: bool - Whether to include annotated frames

**Methods**:
```python
def __init__(output_format: str)
def format_detection(detection: DetectionResult) -> dict
def generate_json(detection: DetectionResult) -> string
def annotate_frame(frame: Frame, detections: list) -> Frame
def encode_frame(frame: Frame) -> string
def create_event_payload(detection: DetectionResult) -> dict
```

**Output JSON Schema**:
```json
{
  "timestamp": "ISO-8601 datetime",
  "camera_id": "string",
  "motion_detected": "boolean",
  "motion_intensity": "float (0-100)",
  "threats_detected": [
    {
      "event_type": "theft|violence|weapon|fire",
      "confidence": "float (0-1)",
      "bounding_box": {
        "x": "int",
        "y": "int",
        "width": "int",
        "height": "int"
      },
      "class": "string"
    }
  ],
  "frame_metadata": {
    "frame_number": "int",
    "resolution": "string",
    "processing_time_ms": "float"
  }
}
```

---

## 4. Class Diagrams

### 4.1 Core Model Classes

```
┌─────────────────────────┐
│  VideoStreamHandler     │
├─────────────────────────┤
│ - camera_id: string     │
│ - rtsp_url: string      │
│ - frame_buffer: Queue   │
├─────────────────────────┤
│ + connect(): bool       │
│ + extract_frame(): Frame│
│ + disconnect(): void    │
└──────────┬──────────────┘
           │
           │ provides frames to
           ↓
┌─────────────────────────┐
│  MotionDetector         │
├─────────────────────────┤
│ - sensitivity: float    │
│ - previous_frame: array │
├─────────────────────────┤
│ + detect_motion(): bool │
│ + get_intensity(): float│
└──────────┬──────────────┘
           │
           │ triggers
           ↓
┌─────────────────────────┐
│  ThreatClassifier       │
├─────────────────────────┤
│ - model: YOLOModel      │
│ - thresholds: dict      │
│ - device: string        │
├─────────────────────────┤
│ + classify(): Detection │
│ + load_model(): bool    │
│ + apply_nms(): list     │
└──────────┬──────────────┘
           │
           │ uses
           ↓
┌─────────────────────────┐
│  OutputFormatter        │
├─────────────────────────┤
│ - format: string        │
├─────────────────────────┤
│ + format_detection():str│
│ + annotate_frame(): img │
└─────────────────────────┘
```

### 4.2 Supporting Classes

```
┌─────────────────────────┐
│  ConfigManager          │
├─────────────────────────┤
│ - config_data: dict     │
│ - config_path: string   │
├─────────────────────────┤
│ + load_config(): dict   │
│ + get_parameter(): any  │
│ + update_parameter():   │
└─────────────────────────┘

┌─────────────────────────┐
│  Frame                  │
├─────────────────────────┤
│ - data: ndarray         │
│ - timestamp: datetime   │
│ - camera_id: string     │
│ - frame_number: int     │
└─────────────────────────┘

┌─────────────────────────┐
│  DetectionResult        │
├─────────────────────────┤
│ - event_type: string    │
│ - confidence: float     │
│ - bounding_boxes: list  │
│ - timestamp: datetime   │
└─────────────────────────┘

┌─────────────────────────┐
│  MotionResult           │
├─────────────────────────┤
│ - motion_detected: bool │
│ - intensity: float      │
│ - timestamp: datetime   │
└─────────────────────────┘
```

---

## 5. Data Flow

### 5.1 Processing Flow Sequence

```
1. Video Input
   ↓
2. Frame Extraction (15-30 FPS)
   ↓
3. Motion Detection
   ├─→ No Motion → Drop Frame
   └─→ Motion Detected
       ↓
4. Threat Classification (YOLO)
   ↓
5. Confidence Filtering
   ├─→ Below Threshold → Log & Continue
   └─→ Above Threshold
       ↓
6. Output Generation
   ├─→ JSON Event Data
   ├─→ Annotated Frame
   └─→ Processing Metrics
   ↓
7. Event Notification (to App Backend)
```

### 5.2 Data Structures

**Frame Object**:
```python
@dataclass
class Frame:
    data: np.ndarray          # Image data (H, W, C)
    timestamp: datetime       # Capture timestamp
    camera_id: str           # Source camera ID
    frame_number: int        # Sequential frame number
    resolution: tuple        # (width, height)
```

**Detection Result**:
```python
@dataclass
class DetectionResult:
    event_type: str          # theft, violence, weapon, fire
    confidence: float        # 0.0 to 1.0
    bounding_box: dict       # {x, y, width, height}
    class_name: str          # Detected object class
    timestamp: datetime      # Detection timestamp
    camera_id: str          # Source camera
```

**Motion Result**:
```python
@dataclass
class MotionResult:
    motion_detected: bool    # True/False
    intensity: float         # 0-100
    changed_pixels: int      # Number of changed pixels
    motion_regions: list     # List of motion bounding boxes
    timestamp: datetime      # Detection timestamp
```

---

## 6. Interface Specifications

### 6.1 Model API Interface

The model exposes a RESTful API for integration with the application backend.

**Base URL**: `http://localhost:5000/api/model`

#### Endpoint 1: Process Frame
```
POST /api/model/process
Content-Type: application/json

Request Body:
{
  "camera_id": "CAM_001",
  "frame_data": "base64_encoded_image",
  "timestamp": "2025-11-04T12:30:45Z"
}

Response:
{
  "success": true,
  "motion_detected": true,
  "motion_intensity": 68.5,
  "threats": [
    {
      "event_type": "theft",
      "confidence": 0.87,
      "bounding_box": {"x": 245, "y": 120, "width": 80, "height": 150},
      "class": "person"
    }
  ],
  "processing_time_ms": 45.2
}
```

#### Endpoint 2: Update Configuration
```
POST /api/model/config
Content-Type: application/json

Request Body:
{
  "confidence_thresholds": {
    "theft": 0.75,
    "violence": 0.80
  }
}

Response:
{
  "success": true,
  "message": "Configuration updated successfully"
}
```

#### Endpoint 3: Get Model Status
```
GET /api/model/status

Response:
{
  "model_version": "1.0.0",
  "device": "cuda",
  "active_streams": 4,
  "avg_fps": 16.8,
  "gpu_utilization": 45.2,
  "uptime_hours": 72.5
}
```

#### Endpoint 4: Load Model Weights
```
POST /api/model/load
Content-Type: multipart/form-data

Request: Model weights file (.pt)

Response:
{
  "success": true,
  "model_version": "1.1.0",
  "loaded_at": "2025-11-04T14:20:00Z"
}
```

### 6.2 Integration Interface with App Backend

**Event Notification Interface**:

The model sends detection events to the app backend via HTTP POST:

```
POST http://backend-url/api/events/detection
Content-Type: application/json

{
  "event_id": "uuid",
  "timestamp": "2025-11-04T12:30:45Z",
  "camera_id": "CAM_001",
  "event_type": "theft",
  "confidence": 0.87,
  "motion_intensity": 68.5,
  "frame_url": "http://storage/frames/CAM_001_12304512345.jpg",
  "metadata": {
    "bounding_boxes": [...],
    "processing_time_ms": 45.2
  }
}
```

---

## 7. Processing Pipeline

### 7.1 Main Processing Loop

```python
def process_camera_stream(camera_id, rtsp_url):
    # Initialize components
    stream_handler = VideoStreamHandler(camera_id, rtsp_url)
    motion_detector = MotionDetector()
    threat_classifier = ThreatClassifier()
    output_formatter = OutputFormatter()
    
    while True:
        # Extract frame
        frame = stream_handler.extract_frame()
        if frame is None:
            continue
        
        # Motion detection
        motion_result = motion_detector.detect_motion(frame)
        
        if not motion_result.motion_detected:
            continue  # Skip frames with no motion
        
        # Threat classification
        detection_result = threat_classifier.classify(frame)
        
        if detection_result.has_threats():
            # Format and send event
            event_data = output_formatter.format_detection(detection_result)
            send_to_backend(event_data)
            
            # Generate annotated frame
            annotated_frame = output_formatter.annotate_frame(
                frame, detection_result.threats
            )
            save_frame(annotated_frame)
```

### 7.2 Optimization Strategies

1. **Frame Skipping**: Process every Nth frame during low-motion periods
2. **Batch Processing**: Group frames for batch inference when multiple streams active
3. **GPU Memory Management**: Dynamically adjust batch size based on GPU availability
4. **Asynchronous Processing**: Use thread pools for parallel stream processing
5. **Caching**: Cache frequently used model components in memory

---

## 8. Deployment Architecture

### 8.1 Deployment Options

**Option 1: Standalone Server**
```
┌─────────────────────────────┐
│   Model Server (FastAPI)    │
│  ┌────────────────────────┐ │
│  │  AI Detection Model    │ │
│  │  (4 concurrent streams)│ │
│  └────────────────────────┘ │
│  GPU: NVIDIA RTX 3060      │
│  RAM: 16GB                 │
└─────────────────────────────┘
```

**Option 2: Cloud Deployment**
```
┌─────────────────────────────┐
│   Cloud Instance (AWS/GCP)  │
│  ┌────────────────────────┐ │
│  │  Model Container       │ │
│  │  (Docker)              │ │
│  └────────────────────────┘ │
│  GPU: Tesla T4             │
│  Auto-scaling enabled      │
└─────────────────────────────┘
```

### 8.2 Docker Configuration

**Dockerfile**:
```dockerfile
FROM nvidia/cuda:11.8.0-cudnn8-runtime-ubuntu22.04

# Install Python and dependencies
RUN apt-get update && apt-get install -y \
    python3.10 \
    python3-pip \
    libgl1-mesa-glx

# Install Python packages
COPY requirements.txt /app/
RUN pip install -r /app/requirements.txt

# Copy model code
COPY . /app/
WORKDIR /app

# Expose API port
EXPOSE 5000

# Run model server
CMD ["python3", "model_server.py"]
```

**requirements.txt**:
```
torch==2.0.1
torchvision==0.15.2
ultralytics==8.0.200
opencv-python==4.8.0
fastapi==0.103.0
uvicorn==0.23.2
pyyaml==6.0
numpy==1.24.3
```

### 8.3 Resource Requirements

**Minimum**:
- GPU: NVIDIA GPU with 4GB VRAM (e.g., GTX 1650)
- CPU: 4 cores
- RAM: 8GB
- Storage: 20GB SSD
- Network: 100 Mbps

**Recommended**:
- GPU: NVIDIA RTX 3060 or better (6GB+ VRAM)
- CPU: 8 cores
- RAM: 16GB
- Storage: 50GB NVMe SSD
- Network: 1 Gbps

---

## 9. Appendices

### Appendix A: YOLO Model Training

**Training Script**:
```python
from ultralytics import YOLO

# Load pretrained model
model = YOLO('yolov8n.pt')

# Train on custom dataset
results = model.train(
    data='config.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    device='cuda',
    patience=20,
    save=True,
    project='smart_lens',
    name='threat_detector'
)

# Validate
metrics = model.val()
print(f"mAP50: {metrics.box.map50}")
print(f"mAP50-95: {metrics.box.map}")
```

**Dataset Configuration (config.yaml)**:
```yaml
path: /datasets/smart_lens
train: images/train
val: images/val
test: images/test

names:
  0: person
  1: weapon_gun
  2: weapon_knife
  3: fire
  4: smoke
  5: fighting
```

### Appendix B: Performance Benchmarks

| Configuration | FPS | Latency | GPU Usage | Accuracy (mAP) |
|---------------|-----|---------|-----------|----------------|
| Single stream | 30  | 33ms    | 25%       | 85%            |
| 2 streams     | 25  | 40ms    | 45%       | 84%            |
| 4 streams     | 18  | 55ms    | 75%       | 82%            |
| CPU only      | 5   | 200ms   | N/A       | 85%            |

### Appendix C: Error Handling

**Error Codes**:
- `E001`: RTSP connection failed
- `E002`: Model loading failed
- `E003`: GPU out of memory
- `E004`: Invalid frame format
- `E005`: Configuration error

**Recovery Strategies**:
- Automatic reconnection with exponential backoff
- Graceful degradation to CPU if GPU fails
- Frame dropping under high load
- Model reload on corruption detection

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| AI/ML Architect | _______________ | _______________ | ______ |
| Software Engineer | _______________ | _______________ | ______ |
| Project Supervisor | _______________ | _______________ | ______ |

---

**End of Document**
