# ChessVision+

ChessVision+ is an end-to-end computer vision system that analyzes a single RGB image of a chessboard, reconstructs the board state symbolically, and produces a best-move recommendation using a chess engine.

The project combines deep learning–based perception with geometric reasoning and classical chess analysis to enable robust chessboard understanding under real-world conditions.

---

## 🔍 Problem Overview

Understanding a chess position from an image is a challenging task due to:
- Perspective distortion and varying camera viewpoints  
- Occlusions and visually similar chess pieces  
- The need to convert pixel-level information into a symbolic board representation  

ChessVision+ addresses these challenges by designing a modular pipeline that decomposes the problem into interpretable stages, enabling both robustness and extensibility.

---

## 🧠 System Pipeline

The system follows a multi-stage pipeline:

1. **Board Segmentation (UNet)**  
   Localizes the chessboard in the input image using semantic segmentation.

2. **Perspective Warping & Normalization**  
   Applies a homography transformation to obtain a canonical top-down board view.

3. **Piece Recognition**  
   - **Initial approach:** Square-based classification with ResNet18 (abandoned)  
   - **Final approach:** YOLO-based object detection on the original image

4. **Grid Mapping**  
   Projects detected piece locations onto an 8×8 board grid.

5. **FEN Generation**  
   Converts the grid representation into Forsyth–Edwards Notation (FEN).

6. **Chess Engine Integration**  
   Uses Stockfish to compute and visualize the best move.

---

## 🚧 Why Square-Based Classification Failed

An early design classified each board square independently using a ResNet18 model.  
This approach proved unreliable due to:
- Loss of 3D cues after perspective warping reminds only piece bases  
- High visual similarity between different pieces  
- No explicit *empty square* class, leading to forced misclassifications  

These limitations motivated a shift to an object detection formulation.

---

## 🚀 Key Results

### Board Segmentation (UNet)
- **IoU:** 0.9848  
- **Dice (F1):** 0.9923  
- **Success rate:** 100% (43/43 test images)  
- **Avg. runtime:** ~101 ms per image  

### Piece Detection (YOLO)
- **Precision:** 0.9757  
- **Recall:** 0.9874  
- **mAP@0.5:** 0.9848  
- **mAP@0.5:0.95:** 0.7707  

### End-to-End Pipeline
- **Success rate:** 100%  
- **Average runtime:** ~807 ms  
- **Median runtime:** ~477 ms  

These results demonstrate that object detection significantly improves robustness and accuracy compared to square-based classification.

---

## 📂 Repository Structure
