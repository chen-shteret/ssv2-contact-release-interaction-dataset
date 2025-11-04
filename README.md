# video-interaction-recognition

A **metadata and annotation repository** for video interaction recognition,  
structured according to the **Something-Something V2** dataset format.

---

## Dataset Label Structure

This repository follows the **Something-Something V2** interaction schema,  
where each video in the original dataset is associated with a **template** and **placeholders**  
representing the objects involved in the interaction.

> Note: This repository contains only metadata and annotations — not the original videos.

---

## License

This repository (code + metadata) is released under the  
**Data License Agreement – Research Use Only**  
from Qualcomm Technologies, Inc.  

[View the official license (PDF)](https://www.qualcomm.com/content/dam/qcomm-martech/dm-assets/documents/jester_something_something_exercise_research_license_final_qti_28jul2022.pdf)

The dataset and any derivatives may be used **only for non-commercial research and educational purposes**.

---

## Metadata Folder

All dataset-related metadata files are stored under the `metadata/` directory.  
These files contain structured information about labeled video events, dataset splits,  
and mappings between templates and video IDs.

---

### 1. `metadata/video_events_labels.json`

Contains detailed labels for each annotated event, including the type of interaction and its frame-level attributes.  
Each key represents a video ID, and the value is a list of labeled events.

**Format example:**
```json
{
  "20": [
    {
      "action": "release",
      "agent": "hand-object",
      "frameNumber": 9,
      "pointX": 113,
      "pointY": 169
    }
  ]
}
```

**Description:**
- `action` – Type of event (e.g., `contact`, `release`)  
- `agent` – Interacting entities (e.g., `hand-object`, `object-surface`)  
- `frameNumber` – The frame in which the event occurs  
- `pointX`, `pointY` – Pixel coordinates of the annotated event in the frame  

---

### 2. `metadata/template_to_video_ids_map.json`

Maps each action template to all video IDs that were manually labeled and contain interaction events (contact/release).  
Only videos with valid annotations are included.

**Format example:**
```json
{
  "Putting something on a surface": ["5845", "8627", "19469"],
  "Lifting something": ["7001", "7154", "7320"]
}
```

---

### 3. `metadata/train_videos_ids_labeled.json`,  
`metadata/validation_videos_ids_labeled.json`,  
`metadata/test_videos_ids_labeled.json`

These three files list the video IDs from the corresponding dataset split (train/validation/test)  
that were selected for labeling and contain at least one interaction event.

**Format example:**
```json
[
  "5845",
  "8627",
  "19469",
  "20251"
]
```

---

## Frame Extraction

We extracted frames from all videos using **OpenCV 4.7.0** at their original FPS.  
Each frame was saved as a `.jpg` image (default quality = 95) in **BGR color format** —  
the default channel order used internally by OpenCV.

**Example:**
```python
import os
import cv2

def video_to_frames(video_path, output_dir):
    os.makedirs(output_dir, exist_ok=True)  # ensure output folder exists

    cap = cv2.VideoCapture(video_path)
    fps = cap.get(cv2.CAP_PROP_FPS)
    count = 0

    while cap.isOpened():
        ret, frame = cap.read()

        if not ret:
            break

        cv2.imwrite(os.path.join(output_dir, f"frame_{count:05d}.jpg"), frame)
        count += 1

    cap.release()
    print(f"Extracted {count} frames at {fps:.2f} FPS.")
```

The same procedure was applied to all videos in the dataset on a computing cluster,
using individual jobs per video.

---

### Summary Table

| File | Description |
|------|--------------|
| `metadata/video_events_labels.json`                 | Frame-level annotations for each video, including action type, coordinates, and frame number.           |
| `metadata/template_to_video_ids_map.json`           | Mapping of each action template to all labeled video IDs with interactions.                          |
| `metadata/train_videos_ids_labeled.json`            | IDs of labeled videos in the training set.                                                  |
| `metadata/validation_videos_ids_labeled.json`       | IDs of labeled videos in the validation set.                                                  |
| `metadata/test_videos_ids_labeled.json`             | IDs of labeled videos in the test set.                                                  |

