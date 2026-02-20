# Smart Lens: Dataset, Model & Storage Research Guide

## Document Information
- **Project**: Smart Lens CCTV Surveillance System - AI Model Research
- **Version**: 1.0
- **Last Updated**: 2026-02-17
- **Purpose**: Comprehensive guide for datasets, pretrained models, and storage optimization

---

## Table of Contents
1. [Executive Summary](#1-executive-summary)
2. [Dataset Research](#2-dataset-research)
3. [Pretrained Models](#3-pretrained-models)
   - 3.1 [YOLOv8 Model Variants](#31-yolov8-model-variants)
   - 3.2 [Model Selection Criteria for FYP](#32-model-selection-criteria-for-fyp)
   - 3.3 [Pretrained Weights Sources](#33-pretrained-weights-sources)
   - 3.4 [Transfer Learning Strategy](#34-transfer-learning-strategy)
   - 3.5 [Training Recommendations for FYP](#35-training-recommendations-for-fyp)
   - 3.6 [Advanced YOLO Variants (Beyond YOLOv8)](#36-advanced-yolo-variants-beyond-yolov8)
   - 3.7 [Non-YOLO Pretrained Models](#37-non-yolo-pretrained-models)
   - 3.8 [Model Selection Decision Guide](#38-model-selection-decision-guide)
4. [Existing Solutions & Strategies](#4-existing-solutions--strategies)
5. [Storage Optimization](#5-storage-optimization)
6. [FYP Data Recommendations](#6-fyp-data-recommendations)
7. [Implementation Roadmap](#7-implementation-roadmap)
8. [References](#8-references)

> **New in this version**: Section 3.6 now includes detailed guidance on advanced YOLO variants (v9/v10/v11/World) and section 3.7 covers non-YOLO pretrained models with a full decision guide.

---

## 1. Executive Summary

### Quick Recommendations for FYP

**Dataset Size**: 3,000-5,000 annotated images total  
**Pretrained Model**: YOLOv8s (proven) or YOLO11s (latest, recommended)  
**Training Time**: 2-4 hours on Google Colab (T4 GPU)  
**Storage Strategy**: Motion-based recording + 30-day retention  
**Expected Accuracy**: 75-85% mAP with proper training  

> **New**: See [Section 3.6](#36-advanced-yolo-variants-beyond-yolov8) for advanced YOLO variants (v9/v10/v11/World) and [Section 3.7](#37-non-yolo-pretrained-models) for non-YOLO alternatives (RT-DETR, EfficientDet, MobileNet SSD, Faster R-CNN).

### Project Scope Alignment

This research focuses on achieving:
- ✅ Production-ready threat detection for FYP demonstration
- ✅ Feasible dataset collection and annotation within semester timeline
- ✅ Deployable model on modest hardware (RTX 3060 or cloud GPU)
- ✅ Cost-effective storage solutions for small businesses

---

## 2. Dataset Research

### 2.1 Required Threat Categories

Based on Smart Lens requirements, we need datasets for:

| Threat Type | Priority | Recommended Images |
|-------------|----------|-------------------|
| **Theft/Shoplifting** | High | 1,000-1,500 |
| **Violence/Fighting** | High | 800-1,200 |
| **Weapon Detection** | High | 600-800 |
| **Fire/Smoke** | Medium | 400-600 |
| **Total** | - | **~3,000-5,000** |

### 2.2 Public Datasets Available

#### A. Theft & Shoplifting Datasets

**1. UCF Crime Dataset**
- **Source**: University of Central Florida
- **Size**: 1,900 videos (untrimmed surveillance)
- **Annotations**: Temporal annotations for 13 anomaly types
- **Includes**: Shoplifting, robbery, theft scenarios
- **Format**: Video files (requires frame extraction)
- **Link**: https://www.crcv.ucf.edu/projects/real-world/
- **License**: Academic use
- **Quality**: ⭐⭐⭐⭐ (Real CCTV footage)

**Pros**: Real-world surveillance footage, diverse scenarios  
**Cons**: Requires manual annotation for YOLO format, large file sizes  
**FYP Suitability**: ⭐⭐⭐ (Good but needs preprocessing)

**2. Retail Store Dataset (Roboflow Universe)**
- **Source**: Roboflow Community
- **Size**: 500-2,000 images (multiple projects)
- **Annotations**: YOLO format, bounding boxes
- **Includes**: Person detection in retail, suspicious behavior
- **Format**: Images with YOLO annotations
- **Link**: https://universe.roboflow.com/ (search "shoplifting" or "retail")
- **License**: Varies by project (many CC BY 4.0)
- **Quality**: ⭐⭐⭐⭐⭐ (Ready to use)

**Pros**: Pre-annotated, YOLO-ready, diverse angles  
**Cons**: Smaller dataset, quality varies  
**FYP Suitability**: ⭐⭐⭐⭐⭐ (Highly recommended)

**3. Custom Collection via Roboflow (Your Project)**
- **Source**: Your Roboflow workspace "smart-survellaince-lens-2"
- **Current Status**: Version 1 available
- **Recommendation**: Expand to 1,000+ images
- **Annotation Tool**: Roboflow (free tier: 1,000 images)

#### B. Violence & Fighting Detection Datasets

**1. Violent Flows Dataset**
- **Source**: Academic research
- **Size**: 246 videos (violent and non-violent)
- **Annotations**: Video-level labels
- **Format**: Video files
- **Link**: https://www.openu.ac.il/home/hassner/data/violentflows/
- **License**: Academic use
- **Quality**: ⭐⭐⭐⭐

**Pros**: Specifically designed for violence detection  
**Cons**: Video-level labels, needs frame extraction  
**FYP Suitability**: ⭐⭐⭐

**2. Real Life Violence Situations Dataset**
- **Source**: Kaggle
- **Size**: 2,000 videos (1,000 violence, 1,000 non-violence)
- **Annotations**: Binary classification
- **Format**: Video files (.mp4)
- **Link**: https://www.kaggle.com/datasets/mohamedmustafa/real-life-violence-situations-dataset
- **License**: CC0: Public Domain
- **Quality**: ⭐⭐⭐⭐

**Pros**: Large dataset, real-world scenarios  
**Cons**: Requires annotation for object detection  
**FYP Suitability**: ⭐⭐⭐

**3. Fighting Detection Dataset (Roboflow)**
- **Source**: Roboflow Universe
- **Size**: 300-800 images (multiple projects)
- **Annotations**: YOLO format, person + fighting class
- **Format**: Images with annotations
- **Link**: https://universe.roboflow.com/ (search "fighting")
- **License**: Varies (mostly open)
- **Quality**: ⭐⭐⭐⭐⭐

**Pros**: Ready to use, proper annotations  
**Cons**: Smaller size  
**FYP Suitability**: ⭐⭐⭐⭐⭐

#### C. Weapon Detection Datasets

**1. Weapons Detection Dataset (Roboflow)**
- **Source**: Roboflow Universe
- **Size**: 3,000-5,000 images
- **Classes**: Gun, knife, pistol, rifle
- **Format**: YOLO, COCO, Pascal VOC
- **Link**: https://universe.roboflow.com/dataset/weapons-detection
- **License**: CC BY 4.0 (most projects)
- **Quality**: ⭐⭐⭐⭐⭐

**Pros**: Large, well-annotated, multiple weapon types  
**Cons**: May include synthetic/game images  
**FYP Suitability**: ⭐⭐⭐⭐⭐ (Highly recommended)

**2. IMFDB (Internet Movie Firearms Database)**
- **Source**: Movie stills with weapons
- **Size**: 10,000+ images
- **Annotations**: Requires manual annotation
- **Format**: Images only
- **Link**: http://www.imfdb.org/
- **Quality**: ⭐⭐⭐

**Pros**: Large variety of weapons  
**Cons**: Not real-world CCTV, needs annotation  
**FYP Suitability**: ⭐⭐

**3. Gun Detection Dataset (Kaggle)**
- **Source**: Kaggle
- **Size**: 3,000+ images
- **Annotations**: YOLO format
- **Format**: Images with bounding boxes
- **Link**: https://www.kaggle.com/datasets/issaisasank/guns-object-detection
- **License**: Various
- **Quality**: ⭐⭐⭐⭐

**Pros**: Ready to use, diverse scenarios  
**Cons**: Mix of real and synthetic  
**FYP Suitability**: ⭐⭐⭐⭐

#### D. Fire & Smoke Detection Datasets

**1. Fire Detection Dataset (Roboflow)**
- **Source**: Roboflow Universe
- **Size**: 2,000-3,000 images
- **Classes**: Fire, smoke, both
- **Format**: YOLO, COCO
- **Link**: https://universe.roboflow.com/ (search "fire detection")
- **License**: Mostly open
- **Quality**: ⭐⭐⭐⭐⭐

**Pros**: Excellent annotations, diverse scenarios  
**Cons**: Limited CCTV-style images  
**FYP Suitability**: ⭐⭐⭐⭐⭐

**2. Smoke & Fire Dataset (Kaggle)**
- **Source**: Kaggle
- **Size**: 1,000+ images
- **Annotations**: Classification and detection
- **Format**: Images with labels
- **Link**: https://www.kaggle.com/datasets/dataclusterlabs/fire-and-smoke-dataset
- **License**: CC0
- **Quality**: ⭐⭐⭐⭐

**Pros**: Public domain, good variety  
**Cons**: Mixed quality  
**FYP Suitability**: ⭐⭐⭐⭐

**3. D-Fire Dataset**
- **Source**: Academic research
- **Size**: 21,000 images
- **Classes**: Fire, smoke, neutral
- **Format**: Images with annotations
- **Link**: https://github.com/gaiasd/DFireDataset
- **License**: Academic
- **Quality**: ⭐⭐⭐⭐⭐

**Pros**: Very large, high quality  
**Cons**: May be overkill for FYP  
**FYP Suitability**: ⭐⭐⭐⭐

### 2.3 Data Collection Strategy for FYP

#### Option A: Public Datasets (Recommended for FYP)

**Timeline**: 2-3 weeks

1. **Download** pre-annotated datasets from Roboflow Universe
2. **Combine** datasets for each threat category
3. **Clean** and verify annotations (1-2 days)
4. **Augment** using Roboflow tools (automatic)
5. **Split** into train/val/test (70/20/10)

**Estimated Effort**: Low to Medium  
**Cost**: Free  
**Quality**: Good to Excellent

#### Option B: Custom Dataset Collection

**Timeline**: 6-8 weeks (Not recommended for FYP due to time constraints)

1. Record CCTV footage in local shops (requires permissions)
2. Extract frames from videos
3. Manually annotate using Roboflow/LabelImg
4. Quality control and verification
5. Data augmentation

**Estimated Effort**: Very High  
**Cost**: Low (time-intensive)  
**Quality**: Excellent (domain-specific)

#### Option C: Hybrid Approach (Best for FYP)

**Timeline**: 3-4 weeks

1. **Base**: Download 2,000-3,000 images from public datasets
2. **Supplement**: Collect 500-1,000 custom images from:
   - Simulated scenarios with consent
   - Public CCTV footage (with permission)
   - YouTube videos (citing sources)
3. **Annotate** custom data using Roboflow
4. **Merge** with public datasets
5. **Augment** to reach 4,000-5,000 total

**Estimated Effort**: Medium  
**Cost**: Free (Roboflow free tier)  
**Quality**: Very Good  
**FYP Suitability**: ⭐⭐⭐⭐⭐

### 2.4 Data Augmentation Techniques

Roboflow provides automatic augmentation:

- **Flip**: Horizontal (common in CCTV)
- **Rotation**: ±15° (camera angles)
- **Brightness**: ±25% (lighting variations)
- **Noise**: 2% (image quality)
- **Blur**: Up to 1px (motion blur)
- **Crop**: 0-10% (zoom simulation)

**Recommended Settings for CCTV**:
```yaml
augmentation:
  horizontal_flip: 0.5
  brightness: [-25%, 25%]
  blur: 1px
  noise: 2%
  rotation: 10°
```

This can increase dataset size by 2-3x effectively.

### 2.5 Annotation Tools

| Tool | Best For | Cost | Learning Curve |
|------|----------|------|----------------|
| **Roboflow** | YOLO projects | Free (1k images) | Easy |
| **LabelImg** | Custom annotation | Free | Easy |
| **CVAT** | Video annotation | Free | Medium |
| **Labelbox** | Team collaboration | Paid/Free tier | Medium |
| **VGG Image Annotator** | Simple tasks | Free | Easy |

**Recommendation for FYP**: Use **Roboflow** for its:
- YOLO export format
- Auto-augmentation
- Version control
- Team collaboration
- Free tier (sufficient for FYP)

---

## 3. Pretrained Models

### 3.1 YOLOv8 Model Variants

YOLOv8 comes in 5 sizes with different speed/accuracy tradeoffs:

| Model | Size (params) | mAP | Speed (ms) | GPU Memory | FYP Recommendation |
|-------|---------------|-----|------------|------------|-------------------|
| **YOLOv8n** | 3.2M | 37.3% | 1.5ms | ~2GB | ⭐⭐⭐⭐⭐ Best for FYP |
| **YOLOv8s** | 11.2M | 44.9% | 2.5ms | ~3GB | ⭐⭐⭐⭐⭐ Recommended |
| **YOLOv8m** | 25.9M | 50.2% | 5.0ms | ~5GB | ⭐⭐⭐ Good if GPU available |
| **YOLOv8l** | 43.7M | 52.9% | 8.0ms | ~8GB | ⭐⭐ Overkill for FYP |
| **YOLOv8x** | 68.2M | 53.9% | 12.0ms | ~12GB | ⭐ Not needed |

**Benchmark**: COCO dataset (80 classes)

### 3.2 Model Selection Criteria for FYP

**Choose YOLOv8n if**:
- Limited GPU (Google Colab free tier)
- Need real-time processing (30+ FPS)
- Deploying on edge devices
- Dataset < 3,000 images

**Choose YOLOv8s if**:
- Have RTX 3060 or better
- Need better accuracy
- Dataset 3,000-5,000 images
- Can accept 20-25 FPS

**Choose YOLOv8m if**:
- Have RTX 3070/3080
- Accuracy is critical
- Large dataset (5,000+)
- 15 FPS is acceptable

### 3.3 Pretrained Weights Sources

#### Official YOLOv8 Weights (Ultralytics)

```python
from ultralytics import YOLO

# Load pretrained on COCO dataset
model = YOLO('yolov8n.pt')  # nano
model = YOLO('yolov8s.pt')  # small
model = YOLO('yolov8m.pt')  # medium

# Auto-downloads from Ultralytics
```

**Source**: https://github.com/ultralytics/ultralytics  
**Pretrained on**: COCO dataset (80 classes)  
**Includes**: Person, knife, some weapons  
**License**: AGPL-3.0

#### Domain-Specific Pretrained Models

**1. Security/Surveillance YOLOv8**
- **Source**: Roboflow Universe (community models)
- **Pretrained on**: CCTV footage, security scenarios
- **Link**: https://universe.roboflow.com/browse/models
- **Quality**: ⭐⭐⭐⭐
- **FYP Suitability**: ⭐⭐⭐⭐

**2. Weapon Detection YOLOv8**
- **Source**: Kaggle Models / Roboflow
- **Pretrained on**: Weapon-specific datasets
- **Accuracy**: 85-90% on weapons
- **FYP Suitability**: ⭐⭐⭐⭐⭐

**3. Fire Detection YOLOv8**
- **Source**: GitHub repositories
- **Link**: https://github.com/search?q=yolov8+fire+detection
- **Pretrained on**: Fire/smoke datasets
- **FYP Suitability**: ⭐⭐⭐⭐

### 3.4 Transfer Learning Strategy

**Step 1: Start with COCO Pretrained**
```python
# Load base model
model = YOLO('yolov8n.pt')
```

**Step 2: Fine-tune on Your Data**
```python
# Train on custom dataset
results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    patience=20,
    device='cuda'
)
```

**Step 3: Evaluate and Iterate**
```python
# Validate
metrics = model.val()
print(f"mAP50: {metrics.box.map50}")
print(f"mAP50-95: {metrics.box.map}")
```

### 3.5 Training Recommendations for FYP

**Hardware Options**:

| Platform | GPU | Cost | Training Time (100 epochs) |
|----------|-----|------|---------------------------|
| **Google Colab Free** | T4 (16GB) | Free | 3-4 hours |
| **Google Colab Pro** | V100/A100 | $10/month | 1-2 hours |
| **Kaggle** | P100 (16GB) | Free | 3-4 hours |
| **Local RTX 3060** | 12GB | One-time | 2-3 hours |
| **Paperspace** | RTX 4000+ | ~$0.50/hr | 1-2 hours |

**Recommended for FYP**: Google Colab Free or Kaggle (no cost)

**Training Configuration**:
```yaml
# smart_lens.yaml
path: /content/datasets/smart_lens
train: images/train
val: images/val
test: images/test

nc: 6  # number of classes
names: ['person', 'weapon_gun', 'weapon_knife', 'fire', 'smoke', 'fighting']

# Training parameters
epochs: 100
batch: 16
imgsz: 640
patience: 20  # early stopping
```

**Training Script**:
```python
from ultralytics import YOLO

# Load pretrained model
model = YOLO('yolov8s.pt')

# Train
results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    patience=20,
    workers=4,
    device='cuda',
    project='smart_lens',
    name='threat_detection_v1',
    
    # Optimization
    optimizer='AdamW',
    lr0=0.01,
    momentum=0.937,
    weight_decay=0.0005,
    
    # Augmentation
    hsv_h=0.015,
    hsv_s=0.7,
    hsv_v=0.4,
    degrees=10.0,
    translate=0.1,
    scale=0.5,
    flipud=0.0,
    fliplr=0.5,
    mosaic=1.0,
    mixup=0.0
)

# Export model
model.export(format='onnx')  # For deployment
```

### 3.6 Advanced YOLO Variants (Beyond YOLOv8)

The YOLO family has continued to evolve after YOLOv8. These newer versions are all available through the same **Ultralytics** API, making the code migration minimal.

#### 3.6.1 YOLOv9 (2024)

**Key Improvements over YOLOv8**:
- Introduces **Programmable Gradient Information (PGI)** to preserve complete input information through the network
- Introduces **Generalized Efficient Layer Aggregation Network (GELAN)** for better parameter utilization
- ~15% better accuracy than YOLOv8 with similar or fewer parameters

| Variant | Params | mAP (COCO) | Speed (ms) | VRAM |
|---------|--------|------------|------------|------|
| YOLOv9t | 2.0M | 37.8% | 2.0ms | ~2GB |
| YOLOv9s | 7.1M | 46.8% | 3.0ms | ~3GB |
| YOLOv9m | 20.0M | 51.4% | 6.0ms | ~5GB |
| YOLOv9c | 25.3M | 53.0% | 7.0ms | ~6GB |

**Code (same Ultralytics API)**:
```python
from ultralytics import YOLO

model = YOLO('yolov9s.pt')  # auto-downloads

results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    device='cuda'
)
```

**FYP Suitability**: ⭐⭐⭐⭐⭐ (Drop-in replacement for YOLOv8, better accuracy)

---

#### 3.6.2 YOLOv10 (2024)

**Key Improvements**:
- Introduces **NMS-Free training** with dual assignments — removes Non-Maximum Suppression (NMS) at inference, reducing latency
- Consistent dual-assignment strategy makes it faster end-to-end
- Comparable accuracy to YOLOv9 with lower latency

| Variant | Params | mAP (COCO) | Speed (ms) | VRAM |
|---------|--------|------------|------------|------|
| YOLOv10n | 2.3M | 38.5% | 1.84ms | ~2GB |
| YOLOv10s | 7.2M | 46.3% | 2.49ms | ~3GB |
| YOLOv10m | 15.4M | 51.1% | 4.74ms | ~5GB |
| YOLOv10l | 24.4M | 53.3% | 7.28ms | ~6GB |

**When to choose YOLOv10 over YOLOv9**:
- You need the lowest possible inference latency (real-time on weak hardware)
- You are deploying on edge devices (Jetson Nano, Raspberry Pi)
- Post-processing speed matters (NMS removal is significant at high FPS)

```python
from ultralytics import YOLO

model = YOLO('yolov10s.pt')
results = model.train(data='smart_lens.yaml', epochs=100, imgsz=640, device='cuda')
```

**FYP Suitability**: ⭐⭐⭐⭐⭐ (Best latency if deploying on modest hardware)

---

#### 3.6.3 YOLOv11 / YOLO11 (2024–2025)

**Key Improvements**:
- Ultralytics' latest architecture: improved backbone with **C3k2** blocks (more efficient than C2f in v8/v9)
- Improved **multi-scale feature aggregation**
- ~22% fewer parameters than YOLOv8 with equal or better accuracy
- New **OBB (Oriented Bounding Box)** support for rotated objects (useful for overhead CCTV cameras)

| Variant | Params | mAP (COCO) | Speed (ms) | VRAM |
|---------|--------|------------|------------|------|
| YOLO11n | 2.6M | 39.5% | 1.5ms | ~2GB |
| YOLO11s | 9.4M | 47.0% | 2.5ms | ~3GB |
| YOLO11m | 20.1M | 51.5% | 4.7ms | ~4GB |
| YOLO11l | 25.3M | 53.4% | 6.2ms | ~6GB |

**Unique Feature for Smart Lens**: OBB support means you can detect the *orientation* of a person lying on the ground (after a violent event) or weapons at an angle — potentially improving false-positive reduction.

```python
from ultralytics import YOLO

# Standard detection
model = YOLO('yolo11s.pt')

# Oriented bounding box detection (for overhead/tilted cameras)
model_obb = YOLO('yolo11s-obb.pt')

results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    imgsz=640,
    device='cuda'
)
```

**FYP Suitability**: ⭐⭐⭐⭐⭐ (Recommended upgrade path from YOLOv8)

---

#### 3.6.4 YOLO-World (2024)

**What it is**: An open-vocabulary object detection model. Instead of fixed class names, you provide a **text prompt** describing what to detect. Built on a CLIP-style vision-language backbone combined with YOLO.

**Why it matters for Smart Lens**:
- Detect *any* new threat category **without retraining**
- Prompt: `"person stealing", "fire", "gun", "person fighting"` → instant detection
- Useful for rapid prototyping and demoing new threat types

**Limitations**:
- Slower than standard YOLO (not NMS-free)
- Lower accuracy on specific fine-tuned categories vs. a retrained YOLOv8
- Requires more GPU memory for the text encoder

```python
from ultralytics import YOLOWorld

model = YOLOWorld('yolov8s-world.pt')

# Set custom classes via text — NO retraining needed
model.set_classes(["person stealing", "fire", "smoke", "person with gun", "fighting"])

results = model.predict('cctv_frame.jpg', conf=0.3)

for r in results:
    for box in r.boxes:
        print(f"Detected: {r.names[int(box.cls)]}, confidence: {box.conf:.2f}")
```

**FYP Suitability**: ⭐⭐⭐⭐ (Excellent for demos and prototype; retrain with standard YOLO for production)

---

#### 3.6.5 Advanced YOLO Comparison Table

| Model | Best For | Speed | Accuracy | FYP Ease | Notes |
|-------|----------|-------|----------|----------|-------|
| **YOLOv8s** | Baseline, proven | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Most tutorials/examples |
| **YOLOv9s** | Better accuracy, same effort | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Drop-in v8 replacement |
| **YOLOv10s** | Lowest latency | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | NMS-free inference |
| **YOLO11s** | Latest, OBB support | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Best overall for new projects |
| **YOLO-World** | Open-vocabulary demos | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | No retraining needed |

**Recommended upgrade path for Smart Lens FYP**:
1. **Start** with YOLOv8s (most tutorials available)
2. **Upgrade** to YOLO11s once the pipeline works (easy drop-in, better accuracy)
3. **Demo** with YOLO-World to impress supervisors with zero-shot detection

---

### 3.7 Non-YOLO Pretrained Models

The following models can be used **instead of** YOLO. Each has specific advantages for particular deployment or research scenarios relevant to Smart Lens.

#### 3.7.1 RT-DETR (Real-Time Detection Transformer)

**What it is**: A transformer-based real-time detector from Baidu (2023). Replaces CNN backbone with a Vision Transformer. Now available via the Ultralytics API.

**Why consider it for Smart Lens**:
- Better **global context understanding** — transformers "see" the whole frame at once, useful for crowd scenes
- Outperforms YOLOv8 in accuracy while maintaining real-time speed
- No NMS required (transformer decoder handles it natively)

| Variant | Params | mAP (COCO) | Speed (ms) | VRAM |
|---------|--------|------------|------------|------|
| RT-DETR-l | 32M | 53.0% | 9.2ms | ~6GB |
| RT-DETR-x | 67M | 54.8% | 15.8ms | ~10GB |

```python
from ultralytics import RTDETR

model = RTDETR('rtdetr-l.pt')

results = model.train(
    data='smart_lens.yaml',
    epochs=72,          # Transformers converge in 50-72 epochs (vs 100+ for YOLO)
    imgsz=640,
    batch=4,            # Lower batch size — higher memory per image
    device='cuda',
    optimizer='AdamW',
    lr0=0.0001          # Transformers need lower learning rate
)
```

**Key differences from YOLO training**:
- Needs lower learning rate (`lr0=0.0001` vs YOLO's `0.01`)
- Smaller batch sizes recommended (transformer attention is memory-intensive)
- Fewer epochs converge (50-72 instead of 100+)

**FYP Suitability**: ⭐⭐⭐⭐ (Better accuracy, but needs more GPU memory and tuning)

---

#### 3.7.2 EfficientDet (Google, 2020)

**What it is**: A scalable object detection model using EfficientNet backbone and BiFPN (Bi-directional Feature Pyramid Network). Available via TensorFlow and via `efficientdet` PyTorch ports.

**Variants for Smart Lens**:

| Variant | Params | mAP (COCO) | Speed (ms) | VRAM |
|---------|--------|------------|------------|------|
| EfficientDet-D0 | 3.9M | 33.8% | 10.2ms | ~2GB |
| EfficientDet-D1 | 6.6M | 39.6% | 13.5ms | ~3GB |
| EfficientDet-D2 | 8.1M | 43.0% | 17.7ms | ~4GB |
| EfficientDet-D4 | 20.7M | 49.4% | 42.8ms | ~6GB |

**When to choose EfficientDet**:
- You are deploying on a **mobile or edge device** (D0/D1 are very lightweight)
- You are working in a **TensorFlow/Keras** ecosystem
- You need a well-researched, well-documented alternative to YOLO

```python
# Using TensorFlow Object Detection API (or automl-efficientdet)
# Option 1: TF2 Object Detection API
import tensorflow as tf

# Download EfficientDet-D1 from TF model zoo
# Fine-tune with transfer learning on your dataset

# Option 2: PyTorch port (zylo117/Yet-Another-EfficientDet-Pytorch)
from effdet import get_efficientdet_config, EfficientDet, DetBenchTrain
from effdet.config.model_config import efficientdet_model_param_dict

config = get_efficientdet_config('tf_efficientdet_d1')
config.num_classes = 6  # theft, violence, weapon_gun, weapon_knife, fire, smoke
config.image_size = [640, 640]

model = EfficientDet(config, pretrained_backbone=True)
```

**FYP Suitability**: ⭐⭐⭐ (Good but more setup than YOLO; best if you specifically need TF ecosystem)

---

#### 3.7.3 MobileNet + SSD (Lightweight Detection)

**What it is**: SSD (Single Shot MultiBox Detector) with a MobileNetV2 or MobileNetV3 backbone. Designed specifically for mobile and edge deployment.

**When to use for Smart Lens**:
- Deploying on a **Raspberry Pi, Jetson Nano, or old GPU** (GTX 1050 or weaker)
- Need 30+ FPS on CPU
- Memory-constrained environments (less than 2GB VRAM)

| Variant | Params | mAP (COCO) | Speed (CPU) | VRAM |
|---------|--------|------------|-------------|------|
| SSD MobileNetV2 | 16M | 22.1% | ~25ms | ~1GB |
| SSD MobileNetV3-Large | 5.1M | 29.9% | ~20ms | ~1GB |
| SSDLite MobileNetV3 | 3.4M | 22.3% | ~13ms | <1GB |

```python
import torch
import torchvision
from torchvision.models.detection import ssdlite320_mobilenet_v3_large
from torchvision.models.detection.ssdlite import SSDLite320_MobileNet_V3_Large_Weights

# Load pretrained
model = ssdlite320_mobilenet_v3_large(
    weights=SSDLite320_MobileNet_V3_Large_Weights.COCO_V1
)

# Modify for 6 custom classes (replace head)
num_classes = 7  # 6 threat classes (theft, violence, weapon_gun, weapon_knife, fire, smoke) + background
num_anchors = model.anchor_generator.num_anchors_per_location()

from torchvision.models.detection.ssd import SSDClassificationHead
model.head.classification_head = SSDClassificationHead(
    in_channels=in_channels,
    num_anchors=num_anchors,
    num_classes=num_classes
)

# Fine-tune
optimizer = torch.optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
```

**FYP Suitability**: ⭐⭐⭐ (Only if constrained hardware; YOLO is easier and more accurate on GPU)

---

#### 3.7.4 Faster R-CNN with ResNet/FPN

**What it is**: Two-stage detector — Region Proposal Network (RPN) proposes regions of interest, then a classification head classifies them. Available in `torchvision`.

**When to use for Smart Lens**:
- Accuracy is the **top priority** and speed is secondary (e.g., forensic review mode, not live streaming)
- You are writing a **research paper** comparing detection methods (Faster R-CNN is a standard baseline)
- Detecting **small objects** (concealed weapons, distant subjects) — two-stage approach is more thorough

| Variant | mAP (COCO) | Speed (GPU) | VRAM |
|---------|------------|-------------|------|
| Faster R-CNN R50-FPN | 42.0% | ~40ms | ~5GB |
| Faster R-CNN R101-FPN | 44.0% | ~55ms | ~6GB |
| Faster R-CNN X101-FPN | 47.0% | ~85ms | ~9GB |

```python
import torchvision
from torchvision.models.detection import fasterrcnn_resnet50_fpn_v2
from torchvision.models.detection import FasterRCNN_ResNet50_FPN_V2_Weights
from torchvision.models.detection.faster_rcnn import FastRCNNPredictor

# Load pretrained on COCO
model = fasterrcnn_resnet50_fpn_v2(
    weights=FasterRCNN_ResNet50_FPN_V2_Weights.COCO_V1
)

# Replace the head for 6 custom classes
num_classes = 7  # 6 threat classes (theft, violence, weapon_gun, weapon_knife, fire, smoke) + background
in_features = model.roi_heads.box_predictor.cls_score.in_features
model.roi_heads.box_predictor = FastRCNNPredictor(in_features, num_classes)

# Fine-tune
params = [p for p in model.parameters() if p.requires_grad]
optimizer = torch.optim.SGD(params, lr=0.005, momentum=0.9, weight_decay=0.0005)
```

**FYP Suitability**: ⭐⭐ (Too slow for real-time CCTV; use only for offline analysis or research comparison)

---

#### 3.7.5 DETR / Deformable DETR (Facebook, 2020–2021)

**What it is**: Detection Transformer — the first end-to-end transformer-based object detector. Uses self-attention over image patches to replace anchors and NMS entirely.

**When to consider**:
- You want a **cutting-edge** research approach for your FYP thesis
- You have access to significant GPU (16GB+ VRAM) for training
- You want to study transformer-based vision models

| Variant | mAP (COCO) | Speed | VRAM (training) |
|---------|------------|-------|-----------------|
| DETR-R50 | 42.0% | ~50ms | ~16GB |
| DETR-R101 | 43.5% | ~65ms | ~20GB |
| Deformable DETR | 46.2% | ~40ms | ~12GB |

```python
from transformers import DetrImageProcessor, DetrForObjectDetection
import torch

processor = DetrImageProcessor.from_pretrained("facebook/detr-resnet-50")
model = DetrForObjectDetection.from_pretrained(
    "facebook/detr-resnet-50",
    num_labels=6,           # Your threat classes
    ignore_mismatched_sizes=True
)

# Fine-tune using Hugging Face Trainer or custom loop
```

**FYP Suitability**: ⭐⭐ (Academically interesting but impractical for real-time CCTV on FYP hardware)

---

#### 3.7.6 Complete Model Comparison for Smart Lens

| Model | Speed (GPU) | mAP Range | Training Ease | FYP Suitable | Best Use Case |
|-------|-------------|-----------|---------------|--------------|---------------|
| **YOLOv8s** | ⭐⭐⭐⭐⭐ | 44–75% | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Baseline, proven approach |
| **YOLOv9s** | ⭐⭐⭐⭐⭐ | 47–78% | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Better accuracy, same effort |
| **YOLOv10s** | ⭐⭐⭐⭐⭐ | 46–76% | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Lowest latency |
| **YOLO11s** | ⭐⭐⭐⭐⭐ | 47–78% | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Latest, OBB support |
| **YOLO-World** | ⭐⭐⭐ | 45–70% | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Open-vocab demos |
| **RT-DETR-l** | ⭐⭐⭐⭐ | 53%+ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Higher accuracy + context |
| **EfficientDet-D1** | ⭐⭐⭐ | 40–65% | ⭐⭐⭐ | ⭐⭐⭐ | TF ecosystem, edge deployment |
| **SSD MobileNetV3** | ⭐⭐⭐⭐⭐ | 22–50% | ⭐⭐⭐ | ⭐⭐ | Very constrained hardware |
| **Faster R-CNN** | ⭐⭐ | 42–70% | ⭐⭐⭐ | ⭐⭐ | Offline forensic analysis |
| **DETR** | ⭐⭐ | 42–65% | ⭐⭐ | ⭐⭐ | Research/thesis comparison |

---

### 3.8 Model Selection Decision Guide

Use this guide to choose the right model for your specific situation:

```
Are you starting fresh or replacing an existing pipeline?
│
├─→ Starting fresh / upgrading
│     └─→ Use YOLO11s (latest, best overall, same Ultralytics API)
│
├─→ Already have YOLOv8 working
│     ├─→ Need better accuracy?  → YOLOv9s (drop-in swap)
│     ├─→ Need lower latency?    → YOLOv10s (NMS-free)
│     └─→ Adding overhead cameras or angled views? → YOLO11s-obb
│
├─→ Doing a demo / impressing supervisors
│     └─→ YOLO-World (zero-shot detection, no retraining)
│
├─→ Want higher accuracy and have 6GB+ VRAM?
│     └─→ RT-DETR-l (transformer, better contextual understanding)
│
├─→ Deploying on edge device (Jetson Nano, Raspberry Pi)?
│     └─→ YOLOv10n or SSD MobileNetV3 (smallest footprint)
│
└─→ Writing a research paper comparing approaches?
      └─→ Train YOLOv8s + Faster R-CNN + RT-DETR and compare mAP/FPS
```

#### Practical Decision Table for Smart Lens FYP

| Scenario | Recommended Model | Reason |
|----------|-------------------|--------|
| Default FYP (first time) | **YOLOv8s or YOLO11s** | Best documentation, community support |
| Upgrade from existing YOLOv8 | **YOLO11s** | Better accuracy, same API, fewer params |
| Maximum accuracy needed | **RT-DETR-l** | Transformer-based, better scene context |
| No GPU (CPU only) | **YOLOv10n** | NMS-free = faster CPU inference |
| Demo to supervisor | **YOLO-World** | Change classes without retraining |
| Research paper | **YOLOv8s + RT-DETR** | Compare YOLO family vs transformer |
| Mobile/edge device | **YOLOv10n or SSD MobileNetV3** | Smallest footprint |
| Outdoor/angled cameras | **YOLO11s-obb** | Oriented bounding boxes |

#### How to Switch Between Models (Code Template)

Because all recommended models use the Ultralytics API, switching is a **one-line change**:

```python
from ultralytics import YOLO, RTDETR, YOLOWorld

# ── Option A: Standard YOLO family ──────────────────────────────────────────
model = YOLO('yolov8s.pt')    # baseline
model = YOLO('yolov9s.pt')    # better accuracy
model = YOLO('yolov10s.pt')   # lower latency
model = YOLO('yolo11s.pt')    # latest (recommended)
model = YOLO('yolo11s-obb.pt')# oriented bounding boxes

# ── Option B: Transformer-based ─────────────────────────────────────────────
model = RTDETR('rtdetr-l.pt') # higher accuracy, more memory

# ── Option C: Open-vocabulary (no retraining) ───────────────────────────────
model = YOLOWorld('yolov8s-world.pt')
model.set_classes(["person stealing", "fire", "smoke", "gun", "fighting"])

# ── Training (same call for A and B) ────────────────────────────────────────
results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    device='cuda'
)

# ── Inference (same call for all) ───────────────────────────────────────────
results = model.predict('cctv_frame.jpg', conf=0.5)
for r in results:
    for box in r.boxes:
        print(f"{r.names[int(box.cls)]}: {box.conf:.2f}")
```

---

## 4. Existing Solutions & Strategies

### 4.1 Commercial Surveillance Systems

#### A. Deep Sentinel

**Technology Stack**:
- Custom CNN models
- Cloud-based processing
- Real-time person detection
- 24/7 monitoring with human guards

**Key Strategies**:
- Two-stage detection: AI filter + human verification
- Focus on person detection accuracy (99%+)
- Cloud storage with 30-day retention
- Motion-based triggering to reduce data

**Lessons for FYP**:
✅ Two-stage approach reduces false positives  
✅ Motion triggering saves storage  
✅ Cloud processing enables scalability  

#### B. Solink

**Technology Stack**:
- YOLO-based object detection
- Integration with POS systems
- Cloud analytics platform
- Video + transaction correlation

**Key Strategies**:
- Multi-modal detection (video + POS data)
- Focus on retail-specific scenarios
- Event-based storage (only suspicious events)
- 90-day retention for important events

**Lessons for FYP**:
✅ Event-based storage dramatically reduces costs  
✅ Focus on specific use cases (retail)  
✅ Correlation improves accuracy  

#### C. Verkada

**Technology Stack**:
- Edge AI processing (on-camera)
- Hybrid cloud storage
- Person of interest tracking
- Advanced search (face, license plate)

**Key Strategies**:
- Edge processing reduces bandwidth
- Tiered storage (edge + cloud)
- 30-day standard, 365-day archive
- Motion + AI triggers

**Lessons for FYP**:
✅ Edge processing viable for simple tasks  
✅ Tiered storage balances cost/access  
✅ Clear retention policies needed  

### 4.2 Academic Research Solutions

#### A. UCF Crime Detection (2018)

**Paper**: "Real-world Anomaly Detection in Surveillance Videos"  
**Approach**: 
- 3D ConvNets (C3D)
- Multiple Instance Learning (MIL)
- Temporal segments

**Dataset**: UCF Crime (1,900 videos)  
**Accuracy**: 75-80% AUC  

**Lessons for FYP**:
✅ Temporal information improves detection  
⚠️ 3D CNNs are computationally expensive  
❌ Not suitable for real-time on modest hardware  

#### B. Fighting Detection Systems (2020-2022)

**Common Approaches**:
- Transfer learning from ImageNet
- YOLO for person detection
- Pose estimation for fighting
- LSTM for temporal patterns

**Typical Accuracy**: 85-90% on controlled datasets  

**Lessons for FYP**:
✅ YOLO + pose estimation effective  
✅ Transfer learning essential  
⚠️ Pose estimation adds complexity  

#### C. Weapon Detection Research (2021-2023)

**State-of-the-Art**:
- YOLOv5/v7/v8 dominate
- Focus on small object detection
- Data augmentation critical
- Ensemble methods for high stakes

**Accuracy**: 90-95% on standard datasets  

**Lessons for FYP**:
✅ YOLO family is industry standard  
✅ Augmentation critical for small objects  
✅ Single model sufficient for FYP  

### 4.3 Open Source Projects

#### A. DeepStack (Free AI Server)

**Link**: https://deepstack.cc/  
**Technology**: Custom models, Docker deployment  
**Features**: Object detection, face recognition

**Pros**: Easy deployment, free  
**Cons**: Limited customization, generic models  

#### B. Frigate NVR

**Link**: https://frigate.video/  
**Technology**: TensorFlow Lite, YOLO  
**Features**: Real-time object detection, NVR integration

**Pros**: Excellent for home/small business  
**Cons**: Requires dedicated hardware  

**Lessons for FYP**:
✅ Docker deployment simplifies setup  
✅ Integration with existing CCTV systems important  

### 4.4 Best Practices from Industry

**1. Motion-First Architecture**
```
Video Stream → Motion Detection → AI Analysis → Alert
```
- Reduces processing by 80-90%
- Only analyze frames with movement
- Saves compute and storage

**2. Confidence Thresholding**
```python
# Multi-tier alerting
if confidence > 0.85:
    send_immediate_alert()
elif confidence > 0.70:
    add_to_review_queue()
else:
    log_for_analytics()
```

**3. Temporal Smoothing**
```python
# Reduce false positives
def confirm_detection(detections, window=30, threshold=5):
    """Require 5 detections in 30 frames"""
    if count_recent(detections, window) >= threshold:
        return True
    return False
```

**4. Zone-Based Detection**
```python
# Only alert for specific areas
high_value_zones = [
    {"name": "cash_register", "box": [x1, y1, x2, y2]},
    {"name": "entrance", "box": [x1, y1, x2, y2]}
]
```

---

## 5. Storage Optimization

### 5.1 Storage Challenge

**Scenario**: 4 cameras, 24/7 recording, 720p

| Strategy | Storage/Day | Storage/Month | Cost/Month |
|----------|-------------|---------------|------------|
| **Continuous** | 1.4TB | 42TB | $420 (cloud) |
| **Motion-based** | 140GB | 4.2TB | $42 (cloud) |
| **AI-triggered** | 14GB | 420GB | $4.2 (cloud) |

**Savings**: AI-triggered = **99% reduction** vs continuous

### 5.2 Motion-Based Recording Strategy

**Implementation**:

```python
class MotionBasedRecorder:
    def __init__(self, sensitivity=25):
        self.bg_subtractor = cv2.createBackgroundSubtractorMOG2()
        self.sensitivity = sensitivity
        self.recording = False
        self.buffer = []
        
    def detect_motion(self, frame):
        # Apply background subtraction
        fg_mask = self.bg_subtractor.apply(frame)
        
        # Count moving pixels
        motion_pixels = cv2.countNonZero(fg_mask)
        motion_percent = (motion_pixels / fg_mask.size) * 100
        
        return motion_percent > self.sensitivity
    
    def process_frame(self, frame):
        has_motion = self.detect_motion(frame)
        
        if has_motion:
            if not self.recording:
                # Start recording with pre-buffer
                self.start_recording(self.buffer)
            self.record_frame(frame)
        else:
            if self.recording:
                # Continue for post-buffer (5 sec)
                self.post_buffer_frames -= 1
                if self.post_buffer_frames <= 0:
                    self.stop_recording()
        
        # Maintain circular buffer (last 30 frames)
        self.buffer.append(frame)
        if len(self.buffer) > 30:
            self.buffer.pop(0)
```

**Benefits**:
- 80-90% storage reduction
- Captures pre/post motion context
- Simple and reliable

### 5.3 AI-Triggered Recording

**Implementation**:

```python
class AITriggeredRecorder:
    def __init__(self, model, confidence_threshold=0.7):
        self.model = model
        self.threshold = confidence_threshold
        
    def should_record(self, frame):
        # Run AI detection
        results = self.model(frame)
        
        # Check for threats
        for detection in results:
            if detection.confidence > self.threshold:
                threat_type = detection.class_name
                if threat_type in ['weapon', 'fire', 'fighting']:
                    return True, threat_type
        
        return False, None
    
    def process_stream(self, stream):
        for frame in stream:
            should_save, threat = self.should_record(frame)
            
            if should_save:
                # Save to cloud with metadata
                self.save_to_cloud(
                    frame,
                    metadata={
                        'threat_type': threat,
                        'timestamp': time.time(),
                        'camera_id': stream.id
                    }
                )
```

**Benefits**:
- 95-99% storage reduction
- Only stores actual threats
- Includes threat metadata

### 5.4 Hybrid Approach (Recommended)

**Two-Tier Storage**:

1. **Tier 1: Local HDD (Motion-based)**
   - All motion events
   - 720p resolution
   - 7-day retention
   - Cheap storage (~$50 for 2TB)
   - 140GB/day × 7 = ~1TB needed

2. **Tier 2: Cloud (AI-triggered)**
   - Only threat detections
   - 1080p resolution
   - 30-day retention
   - 14GB/day × 30 = ~420GB
   - Cost: ~$4-8/month (S3, Backblaze B2)

**Implementation**:

```python
class HybridStorage:
    def __init__(self):
        self.local = LocalStorage('/mnt/surveillance')
        self.cloud = CloudStorage('backblaze_b2')
        
    def save_event(self, frames, metadata):
        # Always save to local
        local_path = self.local.save(
            frames,
            retention_days=7,
            quality='medium'
        )
        
        # Save to cloud if AI-confirmed threat
        if metadata.get('threat_confirmed'):
            cloud_url = self.cloud.save(
                frames,
                retention_days=30,
                quality='high',
                metadata=metadata
            )
            
            # Update database with both paths
            self.db.update({
                'local_path': local_path,
                'cloud_url': cloud_url,
                'threat_type': metadata['threat_type']
            })
```

### 5.5 Video Compression

**Codec Comparison**:

| Codec | Compression | Quality | CPU Usage | Compatibility |
|-------|-------------|---------|-----------|---------------|
| **H.264** | Good | Excellent | Low | ⭐⭐⭐⭐⭐ |
| **H.265** | Excellent | Excellent | Medium | ⭐⭐⭐⭐ |
| **VP9** | Excellent | Good | High | ⭐⭐⭐ |
| **AV1** | Best | Excellent | Very High | ⭐⭐ |

**Recommendation**: **H.264** for FYP
- Widely supported
- Good compression (50-60% vs raw)
- Low CPU overhead
- Compatible with all players

**Settings**:
```python
# OpenCV H.264 encoding
fourcc = cv2.VideoWriter_fourcc(*'H264')
fps = 15
out = cv2.VideoWriter(
    'output.mp4',
    fourcc,
    fps,
    (1280, 720),
    params=[
        cv2.VIDEOWRITER_PROP_QUALITY, 80,  # 0-100
        cv2.VIDEOWRITER_PROP_BITRATE, 2000000  # 2 Mbps
    ]
)
```

### 5.6 Cloud Storage Options

| Provider | Price/GB/Month | Egress Cost | Recommended For |
|----------|----------------|-------------|-----------------|
| **Backblaze B2** | $0.005 | $0.01/GB | ⭐⭐⭐⭐⭐ Best value |
| **AWS S3 IA** | $0.0125 | $0.09/GB | Large scale |
| **Google Cloud** | $0.020 | $0.12/GB | Integration |
| **Wasabi** | $0.0059 | Free | High download |

**Recommendation for FYP**: **Backblaze B2**
- Cheapest for storage
- 10GB free tier
- Simple API
- No minimum retention

**Cost Estimate (4 cameras)**:
- 420GB/month × $0.005 = **$2.10/month**
- First 10GB free = **~$1.50/month**

### 5.7 Retention Policies

**Recommended Tiers**:

| Event Type | Local Retention | Cloud Retention | Reason |
|------------|----------------|-----------------|---------|
| **Motion** | 7 days | None | Review recent activity |
| **Low Confidence** | 3 days | None | Might be false positive |
| **High Confidence** | 30 days | 90 days | Legal evidence |
| **Incident** | Indefinite | Indefinite | Investigation |

**Auto-deletion Script**:
```python
import datetime

def cleanup_old_recordings():
    now = datetime.datetime.now()
    
    # Delete local motion older than 7 days
    for file in local_storage.list():
        if file.type == 'motion':
            age = (now - file.created).days
            if age > 7:
                local_storage.delete(file)
    
    # Delete cloud low-confidence older than 30 days
    for file in cloud_storage.list():
        if file.confidence < 0.8:
            age = (now - file.created).days
            if age > 30:
                cloud_storage.delete(file)
```

### 5.8 Storage Budget for FYP

**Monthly Storage Costs**:

| Component | Storage | Monthly Cost |
|-----------|---------|--------------|
| Local HDD (2TB) | 1TB used | $0 (one-time $50) |
| Cloud (Backblaze) | 420GB | $1.50 |
| Database (MongoDB) | 5GB | $0 (free tier) |
| **Total** | - | **$1.50/month** |

**One-Time Costs**:
- 2TB HDD: $50
- Setup & config: $0

**Total First Year**: $50 + ($1.50 × 12) = **$68**

---

## 6. FYP Data Recommendations

### 6.1 Realistic Dataset Size for FYP

**Minimum Viable Dataset**: 2,000 images
- 500 theft scenarios
- 500 violence/fighting
- 400 weapons
- 300 fire/smoke
- 300 normal (negative samples)

**Recommended Dataset**: 3,000-4,000 images
- 800-1,000 theft scenarios
- 700-900 violence/fighting
- 600-800 weapons
- 400-600 fire/smoke
- 500-700 normal scenarios

**Optimal Dataset**: 5,000-6,000 images
- 1,200-1,500 theft
- 1,000-1,200 violence
- 800-1,000 weapons
- 600-800 fire/smoke
- 1,000-1,500 normal

### 6.2 Data Split Recommendations

**70/20/10 Split** (Standard):
- Training: 70% (2,100 images from 3,000)
- Validation: 20% (600 images)
- Testing: 10% (300 images)

**80/15/5 Split** (For smaller datasets):
- Training: 80% (1,600 images from 2,000)
- Validation: 15% (300 images)
- Testing: 5% (100 images)

**With Augmentation**:
- Original: 3,000 images
- After 2x augmentation: 6,000 training images
- Validation/test: Keep original (no augmentation)

### 6.3 Timeline for Data Collection

**Week 1-2: Dataset Research & Download**
- Search Roboflow Universe
- Download public datasets
- Organize by category
- Total: 2,000-2,500 images

**Week 3-4: Custom Collection (Optional)**
- Record scenarios with friends/family
- Extract frames from videos
- Add: 500-1,000 images

**Week 5: Annotation & Verification**
- Upload to Roboflow
- Verify/fix annotations
- Remove poor quality images
- Final count: 3,000-4,000 images

**Week 6: Augmentation & Splitting**
- Apply augmentation settings
- Create train/val/test splits
- Export YOLO format
- Final dataset ready

**Total Time**: 6 weeks (can be done in 3-4 weeks if using only public data)

### 6.4 Expected Model Performance

**With 2,000 images**:
- mAP50: 60-70%
- Precision: 65-75%
- Recall: 60-70%
- Suitable for: Proof of concept

**With 3,000-4,000 images**:
- mAP50: 70-80%
- Precision: 70-80%
- Recall: 70-75%
- Suitable for: FYP demonstration

**With 5,000+ images**:
- mAP50: 75-85%
- Precision: 75-85%
- Recall: 75-80%
- Suitable for: Production prototype

### 6.5 Class Balance Recommendations

**Balanced Dataset** (Ideal):
```
Theft: 25% (750 images)
Violence: 20% (600 images)
Weapons: 20% (600 images)
Fire: 15% (450 images)
Normal: 20% (600 images)
Total: 3,000 images
```

**Imbalanced Dataset** (Real-world):
```
Theft: 30% (900 images)
Violence: 25% (750 images)
Weapons: 20% (600 images)
Fire: 10% (300 images)
Normal: 15% (450 images)
Total: 3,000 images
```

**Handling Imbalance**:
1. **Data Augmentation**: Apply more to minority classes
2. **Class Weights**: Use in training loss function
3. **Oversampling**: Duplicate minority class samples

```python
# Class weights in YOLOv8
results = model.train(
    data='smart_lens.yaml',
    epochs=100,
    # Automatic class weighting
    cls_weight=1.0,  # Adjust if needed
)
```

### 6.6 Annotation Quality Guidelines

**Minimum Standards**:
- Bounding boxes cover entire object
- At least 10% margin around object
- No overlapping boxes for same class
- Consistent labeling across images
- Include partially visible objects (>30% visible)

**Quality Metrics**:
- Inter-annotator agreement: >90%
- Re-annotation consistency: >95%
- Missing objects: <5%

**Verification Process**:
1. Random sample 10% of annotations
2. Re-annotate independently
3. Compare and fix discrepancies
4. Repeat until quality threshold met

---

## 7. Implementation Roadmap

### 7.1 Phase 1: Data Collection (Weeks 1-4)

**Tasks**:
- [ ] Create Roboflow account
- [ ] Download public datasets
- [ ] Organize by category
- [ ] Initial annotation review
- [ ] Collect custom data (optional)

**Deliverables**:
- 2,000-3,000 annotated images
- Roboflow project with version 1

### 7.2 Phase 2: Model Training (Weeks 5-6)

**Tasks**:
- [ ] Set up Google Colab
- [ ] Configure YOLOv8 environment
- [ ] Train baseline model (YOLOv8n)
- [ ] Evaluate on validation set
- [ ] Train improved model (YOLOv8s)

**Deliverables**:
- Trained model weights (.pt file)
- Training metrics and graphs
- Validation results

### 7.3 Phase 3: Integration (Weeks 7-9)

**Tasks**:
- [ ] Set up FastAPI server
- [ ] Implement video stream processing
- [ ] Add motion detection
- [ ] Integrate YOLO model
- [ ] Test on live camera feeds

**Deliverables**:
- Working API server
- Demo with live camera
- Performance benchmarks

### 7.4 Phase 4: Storage & Deployment (Weeks 10-12)

**Tasks**:
- [ ] Implement local storage
- [ ] Set up Backblaze B2
- [ ] Configure retention policies
- [ ] Deploy to cloud/server
- [ ] End-to-end testing

**Deliverables**:
- Deployed system
- Storage cost analysis
- Final documentation

### 7.5 Phase 5: Testing & Refinement (Weeks 13-14)

**Tasks**:
- [ ] User acceptance testing
- [ ] Performance optimization
- [ ] Bug fixes
- [ ] Documentation updates
- [ ] Presentation preparation

**Deliverables**:
- Polished demo
- Test results
- Final report
- Presentation slides

---

## 8. References

### 8.1 Datasets

1. **UCF Crime Dataset**: https://www.crcv.ucf.edu/projects/real-world/
2. **Roboflow Universe**: https://universe.roboflow.com/
3. **Kaggle Datasets**: https://www.kaggle.com/datasets
4. **D-Fire Dataset**: https://github.com/gaiasd/DFireDataset
5. **Weapons Detection**: Various Roboflow projects

### 8.2 Models & Frameworks

1. **YOLOv8 (Ultralytics)**: https://github.com/ultralytics/ultralytics
2. **YOLOv9**: https://github.com/WongKinYiu/yolov9
3. **YOLOv10**: https://github.com/THU-MIG/yolov10
4. **YOLO11 (Ultralytics)**: https://docs.ultralytics.com/models/yolo11/
5. **YOLO-World**: https://docs.ultralytics.com/models/yolo-world/
6. **RT-DETR (Ultralytics)**: https://docs.ultralytics.com/models/rtdetr/
7. **EfficientDet (PyTorch)**: https://github.com/zylo117/Yet-Another-EfficientDet-Pytorch
8. **DETR (Hugging Face)**: https://huggingface.co/facebook/detr-resnet-50
9. **YOLOv5**: https://github.com/ultralytics/yolov5
10. **PyTorch**: https://pytorch.org/
11. **TensorFlow**: https://www.tensorflow.org/

### 8.3 Research Papers

1. "Real-world Anomaly Detection in Surveillance Videos" (2018)
2. "YOLOv8: State-of-the-Art Object Detection" (2023)
3. "Deep Learning for Video Surveillance: A Survey" (2021)
4. "Weapon Detection in Surveillance Videos" (2022)
5. "Fire Detection using Deep Learning" (2023)

### 8.4 Tools & Platforms

1. **Roboflow**: https://roboflow.com/
2. **LabelImg**: https://github.com/heartexlabs/labelImg
3. **CVAT**: https://github.com/opencv/cvat
4. **Google Colab**: https://colab.research.google.com/
5. **Kaggle Notebooks**: https://www.kaggle.com/code

### 8.5 Cloud Storage

1. **Backblaze B2**: https://www.backblaze.com/b2/
2. **AWS S3**: https://aws.amazon.com/s3/
3. **Google Cloud Storage**: https://cloud.google.com/storage
4. **Wasabi**: https://wasabi.com/

---

## Appendix A: Quick Start Checklist

### For Students Starting FYP

**Week 1**:
- [ ] Create Roboflow account
- [ ] Search and bookmark 5-10 relevant datasets
- [ ] Download 500 images from each category
- [ ] Upload to Roboflow project

**Week 2**:
- [ ] Verify annotations (fix at least 100 images)
- [ ] Apply augmentation (2x multiplier)
- [ ] Split dataset (70/20/10)
- [ ] Export YOLO format

**Week 3**:
- [ ] Set up Google Colab
- [ ] Install Ultralytics YOLOv8
- [ ] Download pretrained yolov8s.pt
- [ ] Upload dataset to Colab

**Week 4**:
- [ ] Configure training script
- [ ] Train for 100 epochs
- [ ] Evaluate results
- [ ] Export model

**Week 5-6**:
- [ ] Set up FastAPI server
- [ ] Test model on sample videos
- [ ] Implement motion detection
- [ ] Demo preparation

---

## Appendix B: Cost Breakdown

**One-Time Costs**:
- Local storage (2TB HDD): $50
- Domain name (optional): $12/year
- Total: ~$60

**Monthly Costs**:
- Cloud storage (Backblaze): $1.50
- Database (MongoDB Atlas): $0 (free tier)
- Compute (Google Colab): $0 (free tier)
- Total: ~$1.50/month

**Annual Cost**: $60 + ($1.50 × 12) = **$78**

**Note**: Extremely affordable for FYP budget!

---

## Appendix C: Common Pitfalls & Solutions

**Pitfall 1: Dataset Too Small**
- Solution: Use augmentation, transfer learning
- Minimum: 2,000 images

**Pitfall 2: Poor Annotations**
- Solution: Spend time on quality control
- Budget: 20-30% of annotation time for verification

**Pitfall 3: Class Imbalance**
- Solution: Oversample minority classes, use class weights

**Pitfall 4: Overfitting**
- Solution: More data, augmentation, early stopping
- Monitor val_loss vs train_loss

**Pitfall 5: Slow Training**
- Solution: Use Google Colab Pro, reduce batch size
- YOLOv8n trains faster than YOLOv8s

**Pitfall 6: Storage Costs**
- Solution: Motion-based recording, compression
- Start with local storage

**Pitfall 7: False Positives**
- Solution: Higher confidence threshold, temporal smoothing
- Collect more negative samples

---

**Document Status**: Ready for FYP Implementation  
**Recommended First Step**: Create Roboflow account and download 1,000 images  
**Estimated Time to Working Prototype**: 6-8 weeks  
**Budget Required**: <$100 for entire FYP  

---

**End of Document**
