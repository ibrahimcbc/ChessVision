# ChessVision+

ChessVision+ is an end-to-end computer vision system that analyzes a single RGB image of a chessboard, reconstructs the board state symbolically, and produces a best-move recommendation using a chess engine.

The project combines deep learning–based perception with geometric reasoning and classical chess analysis to enable robust chessboard understanding under real-world conditions.

---

## 🔍 Problem Overview

Understanding a chess position from an image is challenging because of:

- Perspective distortion and varying camera viewpoints
- Occlusions and visually similar chess pieces
- The need to convert pixel-level information into a symbolic board representation

ChessVision+ addresses these challenges with a modular pipeline that decomposes the problem into interpretable stages, enabling both robustness and extensibility.

---

## 🧠 System Pipeline

1. **Board segmentation (UNet)**
   Localizes the chessboard in the input image using semantic segmentation.

2. **Perspective warping & normalization**
   Applies a homography transformation to obtain a canonical top-down board view.

3. **Piece recognition**
   - *Initial approach:* square-based classification with ResNet18 (abandoned — see below)
   - *Final approach:* YOLO-based object detection on the original image

4. **Grid mapping**
   Projects detected piece locations onto an 8×8 board grid.

5. **FEN generation**
   Converts the grid representation into Forsyth–Edwards Notation (FEN).

6. **Chess engine integration**
   Uses Stockfish to compute and visualize the best move.

---

## 🚧 Why Square-Based Classification Failed

An early design classified each board square independently using a ResNet18 model. This approach proved unreliable for three reasons:

- Perspective warping removes 3D height cues, leaving mostly the piece bases visible
- Different pieces look highly similar at the base level
- There was no explicit *empty square* class, forcing the model into misclassifications

These limitations motivated a shift to an object detection formulation, which operates on the original image and preserves the 3D cues that distinguish pieces.

---

## 🧰 Tech Stack

| Area | Tools |
|---|---|
| Deep learning | PyTorch, torchvision |
| Segmentation | segmentation-models-pytorch (UNet) |
| Object detection | YOLOv8 (Ultralytics) |
| Image processing | OpenCV, Pillow |
| Data & evaluation | NumPy, pandas, scikit-learn |
| Visualization | Matplotlib, seaborn |
| Chess logic | Stockfish |

---

## 🚀 Key Results

### Board segmentation (UNet)

| Metric | Value |
|---|---|
| IoU | 0.9848 |
| Dice (F1) | 0.9923 |
| Success rate | 100% (43/43 test images) |
| Avg. runtime | ~101 ms per image |

### Piece detection (YOLO)

| Metric | Value |
|---|---|
| Precision | 0.9757 |
| Recall | 0.9874 |
| mAP@0.5 | 0.9848 |
| mAP@0.5:0.95 | 0.7707 |

### End-to-end pipeline

| Metric | Value |
|---|---|
| Success rate | 100% |
| Avg. runtime | ~807 ms |
| Median runtime | ~477 ms |

Object detection significantly improves robustness and accuracy over square-based classification.

---

## 🧪 Experiments & Evaluation

All models are evaluated on a custom dataset of real chessboard images covering:

- Varying orientations and viewpoints
- Different lighting conditions
- Multiple game states and piece densities

Quantitative metrics (IoU, Dice, precision, recall, mAP) and qualitative visualizations are reported in the accompanying project report.

---

## 🛠️ Installation

```bash
pip install -r requirements.txt
```

Stockfish must be installed separately and available on your `PATH`.

---

## ▶️ Demo

- [`DEMO_INSTRUCTIONS.md`](DEMO_INSTRUCTIONS.md) — how to run the full pipeline
- [`pipeline_&_FEN_generation.ipynb`](pipeline_&_FEN_generation.ipynb) — end-to-end demo notebook

The output includes:

- Detected board and pieces
- Generated FEN string
- Best-move visualization overlaid on the original image

---

## 📁 Repository Structure

| File | Purpose |
|---|---|
| `unet_training.ipynb` | Trains the UNet board segmentation model |
| `unet_inference_demo.ipynb` | Runs segmentation on new images |
| `batch_warp_from_unet.ipynb` | Applies homography warping in batch |
| `square_extraction.ipynb` | Extracts individual squares from a warped board |
| `square_labeling_from_bboxes.ipynb` | Derives square labels from bounding boxes |
| `resnet_piece_classifier_training.ipynb` | Square-based classifier (abandoned approach) |
| `yolov8-piece_detection/` | YOLO training and inference for piece detection |
| `classify_squares/` | Square classification experiments |
| `csv_bbox_visualization.ipynb` | Visualizes annotated bounding boxes |
| `pipeline_&_FEN_generation.ipynb` | Full pipeline: image → FEN → best move |

---

## ⚠️ Limitations

- Assumes a standard 8×8 chessboard
- Performance may degrade under heavy occlusion or extreme blur
- Non-standard boards and piece sets are not supported

---

## 📄 Report

Full methodology, ablations and qualitative results: [`report.pdf`](report.pdf)
