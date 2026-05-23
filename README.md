# 🧍 Real-Time Seated Posture Assessment using MediaPipe

A real-time computer vision application that monitors and evaluates **seated posture** using a webcam. Built with MediaPipe Pose and OpenCV, the system calibrates to each user's natural upright posture and then continuously flags deviations.

---

## 📌 Project Overview

Poor sitting posture over long periods leads to chronic back and neck pain. This project uses pose landmark detection to assess whether a person is sitting correctly — no wearable sensors required, just a webcam.

The system:
1. **Calibrates** to the user's correct posture over 3 seconds
2. **Continuously monitors** the ear-shoulder-hip geometry in real time
3. **Displays feedback** directly on the video feed — green for correct, red for incorrect

---

## 🎯 How It Works

### Landmarks Used
Three key MediaPipe Pose landmarks are tracked on the **left side** of the body:

| Landmark | Role |
|---|---|
| `LEFT_EAR` | Head/neck position indicator |
| `LEFT_SHOULDER` | Anchor/reference point |
| `LEFT_HIP` | Spine base indicator |

### Geometry
Two metrics are computed every frame:

- **Spine Angle** — angle between the ear-to-shoulder vector and the hip-to-shoulder vector (using `atan2`), capturing spinal tilt and forward lean
- **Head Drop** — vertical distance (`y`) between the ear and the shoulder, capturing neck slouch

### Calibration (3 seconds)
The user sits upright for 3 seconds while the system records the reference angle and head-drop values. These become the personal baseline.

### Posture Evaluation
Each frame after calibration:

```
angle_diff = |current_angle − ref_angle|
head_diff  = |current_head_drop − ref_head_drop|

if angle_diff < 10° AND head_diff < 0.05:
    → CORRECT POSTURE  ✅
else:
    → INCORRECT POSTURE ❌
```

---

## 🛠️ Tech Stack

- **Python 3.10**
- **MediaPipe** `0.10.31` — pose landmark detection
- **OpenCV** `4.12` — webcam capture and frame rendering
- **Math / Time** — angle computation and calibration timer

---

## 📋 Requirements

```bash
pip install mediapipe opencv-python
```

> The project uses `cv2.VideoCapture(1, cv2.CAP_DSHOW)` targeting an **external webcam** on Windows. Change the index to `0` for the built-in webcam.

---

## 🚀 Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   cd <repo-name>
   ```

2. Install dependencies:
   ```bash
   pip install mediapipe opencv-python
   ```

3. Launch the notebook:
   ```bash
   jupyter notebook Mediapipe_project.ipynb
   ```

4. Run the cell and follow the on-screen instructions:
   - Sit **upright** and look straight ahead
   - Hold still for **3 seconds** during calibration
   - The system will then monitor your posture in real time
   - Press **`Q`** to quit

---

## 🖥️ On-Screen Display

| Overlay | Meaning |
|---|---|
| `SIT STRAIGHT (CALIBRATING)` — yellow | Calibration in progress |
| `CALIBRATION DONE` — green | Baseline captured |
| `CORRECT POSTURE` — green | Posture matches baseline |
| `INCORRECT POSTURE` — red | Significant deviation detected |
| `Angle: <value>` | Live spine angle (debug info) |

---

## 📁 Project Structure

```
├── Mediapipe_project.ipynb   # Main notebook
└── README.md
```

---

## 🔧 Customization

| Parameter | Location | Default | What it controls |
|---|---|---|---|
| Webcam index | `cv2.VideoCapture(1, ...)` | `1` | Switch between cameras |
| Calibration duration | `time.time() - calib_start_time > 3` | `3 sec` | How long to hold still |
| Angle threshold | `angle_diff < 10` | `10°` | Sensitivity to spinal tilt |
| Head drop threshold | `head_diff < 0.05` | `0.05` | Sensitivity to neck drop |
| Model complexity | `model_complexity=1` | `1` | `0` = faster, `2` = more accurate |

---

## 💡 Potential Improvements

- Alert sound or desktop notification on sustained bad posture
- Track right-side landmarks and average both sides for robustness
- Log posture history over a session and plot a timeline
- Package as a standalone `.py` script or desktop app

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).
