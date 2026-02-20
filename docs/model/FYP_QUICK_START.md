# Smart Lens FYP: Quick Start Guide

## 🎯 For Students Starting This FYP

This is your quick reference guide to get started with the Smart Lens AI surveillance project. Everything you need is documented - just follow the steps!

---

## 📚 Where to Find Information

| What You Need | Document | Section |
|---------------|----------|---------|
| **Project Overview** | [README.md](../README.md) | All |
| **Dataset Sources** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 2 |
| **Model Selection** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 3 |
| **Advanced YOLO Variants** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 3.6 |
| **Non-YOLO Models** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 3.7 |
| **Model Decision Guide** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 3.8 |
| **Training Guide** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Sections 3.5, 6 |
| **Storage Setup** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 5 |
| **Implementation Plan** | [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md) | Section 7 |
| **Model Requirements** | [MODEL_SRS.md](MODEL_SRS.md) | All |
| **Model Architecture** | [MODEL_SDS.md](MODEL_SDS.md) | All |

---

## ⚡ Quick Decisions (TL;DR)

**For FYP Scope:**

| Decision | Recommendation | Reason |
|----------|---------------|---------|
| **Dataset Size** | 3,000-4,000 images | Achievable in 4-6 weeks, good accuracy |
| **Model** | YOLOv8s or YOLO11s | Best speed/accuracy for FYP demo |
| **Training Platform** | Google Colab (free) | Free T4 GPU, 3-4 hours training |
| **Annotation Tool** | Roboflow | Free tier, YOLO-ready export |
| **Storage** | Local HDD + Backblaze B2 | $1.50/month, motion-based recording |
| **Expected Accuracy** | 75-85% mAP | Suitable for FYP demonstration |
| **Total Budget** | <$100 | Extremely affordable |
| **Timeline** | 14 weeks | Detailed in Section 7 |
| **Want higher accuracy?** | RT-DETR-l | Transformer model, better context |
| **Impressive demo?** | YOLO-World | Zero-shot detection, no retraining |

---

## 🚀 6-Week Fast Track

### Week 1: Setup & Dataset Collection
**Monday-Tuesday**: Account Setup
- [ ] Create Roboflow account (free)
- [ ] Create Google Colab account
- [ ] Create GitHub account (if needed)
- [ ] Set up project folder structure

**Wednesday-Friday**: Dataset Download
- [ ] Search Roboflow Universe for:
  - Shoplifting/theft datasets (target: 800 images)
  - Violence/fighting datasets (target: 700 images)
  - Weapon detection datasets (target: 600 images)
  - Fire/smoke datasets (target: 400 images)
- [ ] Download and organize into folders
- [ ] **Goal**: 2,500+ images by end of week

**Resources**:
- Roboflow Universe: https://universe.roboflow.com/
- Search terms: "shoplifting", "fighting", "weapon detection", "fire detection"

---

### Week 2: Data Preparation
**Monday-Wednesday**: Upload & Organize
- [ ] Create Roboflow project "Smart-Lens-Detection"
- [ ] Upload all images to Roboflow
- [ ] Organize into classes:
  - theft
  - violence
  - weapon_gun
  - weapon_knife
  - fire
  - smoke

**Thursday**: Annotation Verification
- [ ] Random sample 100 images
- [ ] Verify bounding boxes are correct
- [ ] Fix any annotation errors
- [ ] **Quality check**: 95%+ accuracy

**Friday**: Augmentation & Export
- [ ] Configure augmentation:
  - Horizontal flip: 50%
  - Brightness: ±25%
  - Rotation: ±10°
  - Blur: 1px
- [ ] Split dataset: 70/20/10 (train/val/test)
- [ ] Export in YOLOv8 format
- [ ] Download zip file

**Deliverable**: 3,000-4,000 images ready for training

---

### Week 3: Model Training (Part 1)
**Monday**: Environment Setup
```python
# In Google Colab
!pip install ultralytics roboflow
!nvidia-smi  # Check GPU

from roboflow import Roboflow
rf = Roboflow(api_key="YOUR_KEY")
project = rf.workspace().project("smart-lens-detection")
dataset = project.version(1).download("yolov8")
```

**Tuesday-Wednesday**: Baseline Training
```python
from ultralytics import YOLO

# Load pretrained model
model = YOLO('yolov8n.pt')

# Quick training (baseline)
results = model.train(
    data='dataset/data.yaml',
    epochs=50,
    imgsz=640,
    batch=16,
    device='cuda'
)

# Check results
model.val()
```

**Thursday-Friday**: Full Training
```python
# Better model
model = YOLO('yolov8s.pt')

# Full training
results = model.train(
    data='dataset/data.yaml',
    epochs=100,
    imgsz=640,
    batch=16,
    patience=20,
    device='cuda',
    project='smart_lens',
    name='v1'
)
```

**Deliverable**: Trained model weights (.pt file)

---

### Week 4: Model Evaluation
**Monday-Tuesday**: Testing
```python
# Load best model
model = YOLO('smart_lens/v1/weights/best.pt')

# Test on validation set
metrics = model.val()
print(f"mAP50: {metrics.box.map50}")
print(f"mAP50-95: {metrics.box.map}")

# Test on individual images
results = model.predict('test_images/', save=True)
```

**Wednesday**: Analysis
- [ ] Review confusion matrix
- [ ] Check per-class accuracy
- [ ] Identify weak areas
- [ ] Document findings

**Thursday-Friday**: Optimization (if needed)
- [ ] Adjust confidence thresholds
- [ ] Try different augmentation
- [ ] Fine-tune hyperparameters
- [ ] Re-train if time permits

**Deliverable**: Model evaluation report with metrics

---

### Week 5: Integration Setup
**Monday-Tuesday**: FastAPI Server
```python
# app.py
from fastapi import FastAPI, File, UploadFile
from ultralytics import YOLO
import cv2
import numpy as np

app = FastAPI()
model = YOLO('best.pt')

@app.post("/detect")
async def detect(file: UploadFile):
    # Read image
    contents = await file.read()
    nparr = np.frombuffer(contents, np.uint8)
    img = cv2.imdecode(nparr, cv2.IMREAD_COLOR)
    
    # Detect
    results = model(img)
    
    # Format response
    detections = []
    for r in results:
        for box in r.boxes:
            detections.append({
                "class": model.names[int(box.cls)],
                "confidence": float(box.conf),
                "bbox": box.xyxy.tolist()
            })
    
    return {"detections": detections}
```

**Wednesday**: Video Processing
```python
# video_processor.py
import cv2
from ultralytics import YOLO

model = YOLO('best.pt')

def process_video(video_path):
    cap = cv2.VideoCapture(video_path)
    
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break
        
        # Detect
        results = model(frame)
        
        # Annotate
        annotated = results[0].plot()
        
        # Display
        cv2.imshow('Smart Lens', annotated)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    
    cap.release()
    cv2.destroyAllWindows()
```

**Thursday-Friday**: Motion Detection
```python
# motion_detector.py
import cv2

class MotionDetector:
    def __init__(self):
        self.bg_subtractor = cv2.createBackgroundSubtractorMOG2()
    
    def detect(self, frame):
        fg_mask = self.bg_subtractor.apply(frame)
        motion_pixels = cv2.countNonZero(fg_mask)
        motion_percent = (motion_pixels / fg_mask.size) * 100
        return motion_percent > 5  # Threshold
```

**Deliverable**: Working detection system on video

---

### Week 6: Demo Preparation
**Monday-Tuesday**: Storage Setup
- [ ] Set up local folder structure
- [ ] Create Backblaze B2 account (free 10GB)
- [ ] Configure retention policy (7 days local, 30 days cloud)
- [ ] Test video upload/download

**Wednesday**: Testing
- [ ] Test on different CCTV footage
- [ ] Measure FPS and latency
- [ ] Test storage saving (motion vs continuous)
- [ ] Document results

**Thursday-Friday**: Documentation & Demo
- [ ] Create demo video showing:
  - Live detection
  - Different threat types
  - Storage optimization
  - Alert generation
- [ ] Prepare presentation slides
- [ ] Write final report sections
- [ ] Practice demo

**Deliverable**: Complete working demo

---

## 💰 Budget Breakdown

**One-Time Costs**:
- Local storage (2TB HDD): $50
- Total: **$50**

**Monthly Costs**:
- Cloud storage (Backblaze B2): $1.50
- Google Colab: $0 (free tier)
- Roboflow: $0 (free tier)
- Total: **$1.50/month**

**6-Week Total**: $50 + ($1.50 × 1.5) ≈ **$52**

---

## 📊 Expected Results

| Metric | Expected Value | Notes |
|--------|---------------|-------|
| **mAP50** | 75-85% | Mean Average Precision at 50% IoU |
| **Precision** | 70-80% | Correct detections / All detections |
| **Recall** | 70-75% | Correct detections / All actual threats |
| **FPS** | 15-25 | Frames per second (YOLOv8s on RTX 3060) |
| **Latency** | 40-60ms | Per frame processing time |
| **Storage Savings** | 90%+ | With motion-based recording |

---

## 🎓 Learning Outcomes

By completing this FYP, you will:

✅ Understand deep learning for computer vision  
✅ Master YOLO object detection (v8 through v11)  
✅ Evaluate and compare alternative pretrained models  
✅ Gain experience with real-world datasets  
✅ Learn data annotation and augmentation  
✅ Implement cloud storage solutions  
✅ Build production-ready AI systems  
✅ Optimize for cost and performance  

---

## 🆘 Common Issues & Solutions

### Issue 1: "Not enough images"
**Solution**: 
- Use augmentation (2-3x multiplier)
- Download from multiple Roboflow projects
- Minimum viable: 2,000 images

### Issue 2: "Training taking too long"
**Solution**:
- Use YOLOv8n instead of YOLOv8s
- Reduce epochs to 50
- Use smaller image size (416 instead of 640)

### Issue 3: "Low accuracy"
**Solution**:
- Check annotation quality
- Add more diverse images
- Increase training epochs
- Try different augmentation

### Issue 4: "Google Colab disconnecting"
**Solution**:
- Save checkpoints frequently
- Use Colab Pro ($10/month)
- Split training into sessions
- Use Kaggle as backup (also free)

### Issue 5: "Storage costs too high"
**Solution**:
- Implement motion detection first
- Use local storage for recent data
- Set aggressive retention policies
- Use Backblaze B2 (cheapest cloud)

### Issue 6: "I want to try a different model (not YOLOv8)"
**Solution**:
- See [DATASET_AND_MODEL_RESEARCH.md Section 3.6](DATASET_AND_MODEL_RESEARCH.md#36-advanced-yolo-variants-beyond-yolov8) for advanced YOLO variants
- See [Section 3.7](DATASET_AND_MODEL_RESEARCH.md#37-non-yolo-pretrained-models) for non-YOLO alternatives
- Use [Section 3.8 Decision Guide](DATASET_AND_MODEL_RESEARCH.md#38-model-selection-decision-guide) to choose
- All YOLO variants (v8/v9/v10/v11/World) and RT-DETR use the same Ultralytics API — it's a one-line change

---

## 📞 Need Help?

**Documentation**:
1. Check [DATASET_AND_MODEL_RESEARCH.md](DATASET_AND_MODEL_RESEARCH.md)
2. Review [MODEL_SDS.md](MODEL_SDS.md) for architecture
3. See [DOCUMENTATION_MAP.md](../DOCUMENTATION_MAP.md) for navigation

**Resources**:
- YOLOv8 Docs: https://docs.ultralytics.com/
- Roboflow Docs: https://docs.roboflow.com/
- OpenCV Tutorials: https://docs.opencv.org/

**Community**:
- Ultralytics Discord: https://discord.com/invite/ultralytics
- Roboflow Forum: https://discuss.roboflow.com/

---

## ✅ Final Checklist

Before submitting FYP:

- [ ] Dataset collected and annotated (3,000+ images)
- [ ] Model trained (75%+ mAP50)
- [ ] Model tested on video
- [ ] Storage optimization implemented
- [ ] Demo video recorded
- [ ] Documentation complete
- [ ] Presentation slides ready
- [ ] Code committed to GitHub
- [ ] Report written

---

## 🎯 Success Metrics for FYP

**Minimum (Pass)**:
- 2,000+ annotated images
- 60%+ mAP50
- Working detection on video
- Basic documentation

**Good (B Grade)**:
- 3,000+ annotated images
- 70%+ mAP50
- Motion-based recording
- Complete documentation
- Working demo

**Excellent (A Grade)**:
- 4,000+ annotated images
- 75%+ mAP50
- Hybrid storage (local + cloud)
- Comprehensive documentation
- Professional demo
- Performance analysis
- Cost comparison

---

**Good Luck! 🚀**

Remember: This is achievable in 6-14 weeks with consistent effort. Follow the roadmap, ask for help when stuck, and iterate based on results. You've got this!

---

**Document Version**: 1.0  
**Last Updated**: 2026-02-17  
**Maintained By**: Smart Lens Team
