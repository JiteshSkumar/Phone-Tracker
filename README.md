# 📱 Phone Activity Tracker  

## 📌 Overview  
This project is a **video analysis tool** that automatically detects and tracks mobile phones in videos.  
It uses **YOLOv8** for object detection, **OpenCLIP** to distinguish phones from POS/card readers, and custom logic to classify phones as **Active** or **Idle**.  

---

## 🚀 Features  
- Detects mobile phones in videos frame by frame.  
- Filters out non-phone objects such as POS terminals and card readers.  
- Tracks phones across frames with unique IDs.  
- Classifies each phone as **Active** (in use) or **Idle** (not in use).  
- Exports results as:  
  - Annotated videos with bounding boxes and labels.  
  - A per-video summary (`summary.csv`).  
  - A detailed per-phone tracking report (`tracks.csv`).  

---

## 🛠️ Technologies Used  
- [YOLOv8](https://github.com/ultralytics/ultralytics) – object detection  
- [OpenCV](https://opencv.org/) – video processing  
- [OpenCLIP](https://github.com/mlfoundations/open_clip) – semantic classification  
- [Pandas](https://pandas.pydata.org/) – result storage  
- Python 3.10+  

---

## 📂 Project Structure 
phone-tracker/
│── Phone_Tracker.ipynb # Main notebook
└── README.md # Project documentation
