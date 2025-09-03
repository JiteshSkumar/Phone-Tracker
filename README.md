# 📱 Phone Activity Tracker

## Overview
This project is a **video tool** that finds and tracks mobile phones in videos and marks when the phone is being **used**.  
It uses **YOLOv8** for object detection, **OpenCLIP** to double-check that a box is really a phone (not a POS/card reader), and simple motion/visual cues to mark each phone as **Active** (in use) or **Idle**.

---

## Features
- Detects phones in videos **frame by frame**.
- **Ignores static phones** (e.g., lying on a table).
- Tracks phones across frames with small, stable IDs.
- Classifies each phone as **Active** (in use) or **Idle**.
- Filters out non-phone objects (e.g., POS terminals, card readers) with CLIP text prompts.
- Exports:
  - An **annotated video** with bounding boxes and labels.
  - A per-video **summary** (`summary.csv`).
  - A per-phone **tracking report** (`tracks.csv`).

---

## Technologies Used
- [YOLOv8](https://github.com/ultralytics/ultralytics) — object detection  
- [OpenCV](https://opencv.org/) — video processing  
- [OpenCLIP](https://github.com/mlfoundations/open_clip) — text-image check  
- **Pandas** — result storage  
- **PyTorch** — model runtime  
- **Python 3.10+**

---

## Project Structure
phone-tracker/
```
├─ Phone_Tracker.ipynb # Main notebook (10 cells)
├─ README.md # This file
└─ outputs/
├─ summary.csv # Per-video summary
└─ tracks.csv # Per-phone timeline (IDs, frames, status)
```

---

## Quick Start (Colab)
1. Open **Phone_Tracker.ipynb** in Google Colab and turn on a GPU.
2. Put your input videos in Drive (e.g., `MyDrive/Task Video`).
3. Run the notebook **top to bottom**:
   - **Cell 1:** install + imports  
   - **Cell 2:** mount Drive  
   - **Cell 3:** set `INPUT_DIR` and `OUTPUT_DIR`  
   - **Cell 4:** config (thresholds, weights)  
   - **Cell 5:** load **YOLO** and **CLIP**, set `PHONE_TEXTS` prompts  
   - **Cell 6:** helpers (IoU, WBF, box utils)  
   - **Cell 7:** activity scoring + hysteresis  
   - **Cell 8:** simple IoU tracker  
   - **Cell 9:** main pipeline (read → detect → verify → track → label → draw)  
   - **Cell 10:** save annotated video + CSVs

All outputs are saved into a timestamped folder in `OUTPUT_DIR`.

---

## Configuration (Cell 4)
Key knobs you can tweak:
- `DETECTOR_WEIGHTS` — e.g., `yolov8m.pt` for better accuracy (default in notebook).
- `CONF_THRES` — detection confidence (e.g., **0.27**).
- `IOU_THRES` — NMS IoU (e.g., **0.55**).
- `MIN_BOX_AREA` — ignore tiny boxes.
- Activity weights (brightness/entropy/frame-diff/flow) and **hysteresis** on/off thresholds.
- Tracker settings (IoU match threshold, max age).

> Defaults are set in the notebook and work well for the sample videos.

---

## How It Works
1. **Read frames** from the input video (keep original size and FPS).
2. **YOLO** proposes boxes for possible phones.
3. **OpenCLIP** scores each crop against text prompts in `PHONE_TEXTS`
   (e.g., “a handheld smartphone”, “a person using a mobile phone”) and drops non-phone lookalikes.
4. **Weighted Boxes Fusion** merges overlapping boxes to reduce jitter.
5. A light **IoU tracker** keeps the same phone ID across frames.
6. **Activity scoring** combines brightness change, entropy, frame difference, and optical flow.
7. **Hysteresis** smooths the final label so it doesn’t flip Active/Idle too fast.
8. **Static filter:** phones that don’t move for several frames are treated as **Idle**.
9. Draw boxes + labels and **export** the results.

---

## Model Choice (Why YOLO, not a brand-new model?)
- **Fast:** runs close to real-time on a GPU.
- **Accurate:** good priors from large-scale pretraining.
- **Practical:** no need to collect thousands of custom images.
- Pairing YOLO with **CLIP prompts** boosts precision and helps avoid false positives (e.g., POS/card readers).

---

## Success Criteria (Matched to Task)
- Boxes appear **only** when a phone is in **active use**.  
- **No false positives** on unrelated objects (aimed; tune thresholds if needed).  
- **Smooth playback** with original resolution and FPS.

---

## Tuning & Known Issues
- **Card reader flagged as phone (edge case):**  
  Add/adjust prompts in `PHONE_TEXTS`, raise `CONF_THRES` slightly, and keep `MIN_BOX_AREA` + aspect-ratio checks.
- **Low light / motion blur:**  
  Try the **medium** YOLO model, or enable the brightness/contrast adjust step.
- **Partial occlusion (hand covers phone):**  
  The tracker + hysteresis help; you can relax the activity cutoff a bit.
- **Reflections / mirrors:**  
  Keep CLIP prompts specific to “handheld / near face / on lap”.

---

## Outputs
- **Annotated video:** with boxes + `Active/Idle` labels.
- **summary.csv:** per-video stats and time ranges.
- **tracks.csv:** per-phone timeline with IDs and frame indexes.

---

## Author
**Jitesh Kumar** — AI/Computer Vision



