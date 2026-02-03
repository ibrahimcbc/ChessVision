# Demo Instructions: Chess Vision Pipeline

## YOLO Training Metrics Summary

**Location:** `models/yolov8_trained/chess_yolo_run/`

**Key Metrics (Final Epoch 20):**
- **Precision:** 98.74%
- **Recall:** 98.74%
- **mAP50:** 98.48%
- **mAP50-95:** 77.07%

**Training Artifacts:**
- `results.csv` - Full training history
- `results.png` - Training curves
- `confusion_matrix.png` - Class confusion matrix
- `BoxPR_curve.png` - Precision-Recall curve
- `BoxF1_curve.png` - F1 score curve

## How to Run Batch Demo

### Step 1: Open Notebook
Open `notebooks/pipeline_&_FEN_generation.ipynb`

### Step 2: Configure (Cell 2 - CONFIG section)
```python
BATCH_MODE = True  # Set to True for batch processing
INPUT_FOLDER = BASE_DIR / 'data' / 'raw' / 'Chess Pieces Detection Image Dataset' / 'Board_coordinates' / 'test' / 'images'
OUTPUT_FOLDER = BASE_DIR / 'demo_outputs'
```

**To use a different folder:**
- Update `INPUT_FOLDER` to point to your folder with 20-30 test images
- The folder should contain `.jpg`, `.jpeg`, or `.png` files

### Step 3: Run All Cells
Execute cells 0, 1, and 2 in order:
- **Cell 0:** Colab setup (if in Colab) or skip if local
- **Cell 1:** Install dependencies
- **Cell 2:** Main pipeline code + execution

### Step 4: Check Results
After completion, check:
- **Overlay images:** `demo_outputs/*_overlay.jpg` - One per input image with annotations
- **Results CSV:** `demo_outputs/demo_results.csv` - Contains:
  - `image_name` - Original filename
  - `fen` - Generated FEN string
  - `rotation_deg` - Detected board rotation (0, 90, 180, 270)
  - `num_detections` - Number of pieces detected
  - `runtime_ms` - Processing time per image
  - `status` - 'success' or 'error'
  - `error_message` - Error details if failed

### Step 5: Verify Output
The notebook will print:
- Progress for each image
- Summary statistics (success rate, average runtime, average detections)
- File paths where outputs were saved

## Quick Test (3 Images)

To test with a small subset:
1. Create a test folder with 3 images
2. Update `INPUT_FOLDER` in CONFIG to point to this folder
3. Run cells
4. Check `demo_outputs/` for results

## Troubleshooting

**"Input folder not found":**
- Check that `INPUT_FOLDER` path is correct
- Ensure the folder contains image files (.jpg, .png, etc.)

**"YOLO model not found":**
- Ensure `models/best_yolov8.pt` exists
- Check `BASE_DIR` is correctly detected

**"UNet model not found":**
- Ensure `models/best_unet.pth` exists
- Note: The notebook looks for `best_unet.pth` (not `best_unet_model.pth`)

**No images processed:**
- Check that image files have valid extensions (.jpg, .jpeg, .png)
- Ensure files are not corrupted

## Output Format

**demo_results.csv columns:**
- `image_name` - Original image filename
- `fen` - Full FEN notation string
- `rotation_deg` - Board orientation (0, 90, 180, or 270 degrees)
- `num_detections` - Count of pieces detected by YOLO
- `runtime_ms` - Processing time in milliseconds
- `status` - 'success' or 'error'
- `error_message` - Empty if success, error details if failed

**Overlay images:**
- Green contour: Detected board boundaries
- Yellow arrow: Best move suggestion (if API call succeeds)
- Text overlay: FEN (truncated) and rotation/detection info
