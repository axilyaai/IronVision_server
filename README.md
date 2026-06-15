# IronVision Server

Python WebSocket server that runs YOLOv8 object detection and streams results to the IronVision Android app.

**Android app:** [github.com/axilyaai/IronVision](https://github.com/axilyaai/IronVision)

---

## How it works

1. Android app connects via WebSocket
2. Each camera frame arrives as a JPEG byte stream
3. YOLOv8 runs inference on the frame
4. Results (labels, bounding boxes, confidence scores) go back as JSON
5. App draws overlays in real time

```json
{
  "detections": [
    {
      "label": "person",
      "en": "person",
      "conf": 0.91,
      "box": { "left": 0.1, "top": 0.2, "right": 0.4, "bottom": 0.9 }
    }
  ],
  "count": 1,
  "ms": 6.2,
  "fps": 24.1
}
```

Bounding box values are normalized `[0.0 – 1.0]` relative to frame size.

---

## Requirements

- Python 3.12.2
- NVIDIA GPU + CUDA 12.1 recommended
- Phone and PC on the same Wi-Fi network

---

## Installation

```bash
git clone https://github.com/axilyaai/IronVision_server.git
cd IronVision_server
pip install -r requirements.txt
```

**For GPU support (NVIDIA):**
```bash
pip uninstall torch torchvision -y
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu121
```

Verify:
```bash
python -c "import torch; print(torch.cuda.is_available())"
# True
```

---

## Running

```bash
# Default: yolov8s, CUDA, port 8765
python server.py

# Options
python server.py --model yolov8n.pt   # faster
python server.py --model yolov8m.pt   # more accurate
python server.py --device cpu         # no GPU
python server.py --conf 0.35          # lower detection threshold
python server.py --port 9000          # custom port
```

The model file downloads automatically on first run.

---

## Model comparison

| Model | Size | GPU inference | Notes |
|-------|------|---------------|-------|
| yolov8n | 6 MB | ~3ms | Best for weak hardware |
| yolov8s | 22 MB | ~5ms | Default — good balance |
| yolov8m | 50 MB | ~10ms | Higher accuracy |

---

## Files

```
IronVision_server/
├── server.py          # WebSocket server + YOLOv8 inference
└── requirements.txt   # Python dependencies
```

---

## Troubleshooting

**Port blocked by firewall (Windows)**
```
netsh advfirewall firewall add rule name="IronVision" dir=in action=allow protocol=TCP localport=8765
```

**CUDA not available**
```bash
python -c "import torch; print(torch.cuda.is_available())"
```
If `False`, reinstall PyTorch with the CUDA index URL above.

---

