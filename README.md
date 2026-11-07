# PFP-SegC

**Penn-Fudan Pedestrian Segmentation Competition**

PFP-SegC evaluates pedestrian instance segmentation on images from the Penn-Fudan Pedestrian Database, capturing campus and urban street scenes from the University of Pennsylvania and Fudan University (170 images, 345 pedestrian instances). Participants submit individual instance polygon masks, category labels, and confidence scores in COCO results JSON format. Submissions are evaluated automatically against private test annotations using COCO instance segmentation evaluation (`segm`).

<p align="center">
  <img src="/images/ped_seg.png" alt="Penn-Fudan Pedestrian Segmentation Example" width="800">
</p>

## Public data

The public dataset is organized as follows:

```text
data/public/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
└── annotations/
    ├── instances_train.json
    ├── instances_val.json
    └── images_test.json
```

- `images/train/`: training images.
- `images/val/`: validation images.
- `images/test/`: test images used to generate predictions.
- `instances_train.json`: COCO-format training annotations, including instance polygon masks.
- `instances_val.json`: COCO-format validation annotations, including instance polygon masks.
- `images_test.json`: test-image metadata and category definitions.

**The test annotations are hidden.** `images_test.json` intentionally contains image metadata and categories (`pedestrian` with `category_id=1`) but has an empty `annotations` array. Use its image IDs and category IDs when creating your submission. Do not add or infer ground-truth annotations.

## Task: instance segmentation

Submit one prediction record for each detected pedestrian instance. Each record must contain:

| Field | Description |
|---|---|
| `image_id` | Integer image ID from `images_test.json`. |
| `category_id` | Integer category ID from the `categories` section of `images_test.json` (`1` for `pedestrian`). |
| `segmentation` | COCO polygon segmentation: a list containing one or more flat polygon coordinate arrays. |
| `score` | Confidence score between `0` and `1`. |

A polygon array is a sequence of pixel coordinates in this order:

`[x1, y1, x2, y2, x3, y3, ...]`

Each polygon must have at least three coordinate pairs (at least six numbers), with an even number of finite numeric coordinates. Coordinates must refer to the provided test image at its published resolution. The outer `segmentation` list is required, even when an instance has only one polygon.

**This is an instance-segmentation task, not a bounding-box detection task.** Do not submit a detection-only record containing `bbox` instead of `segmentation`. A `bbox` field is not required for the segmentation submission format.

## Submission format

Upload a `.json` file containing a JSON array of COCO instance-segmentation prediction records. For example:

```json
[
  {
    "image_id": 136,
    "category_id": 1,
    "segmentation": [
      [
        185.0, 120.0,
        192.0, 115.0,
        205.0, 118.0,
        210.0, 135.0,
        208.0, 175.0,
        198.0, 240.0,
        192.0, 310.0,
        180.0, 310.0,
        175.0, 240.0,
        178.0, 160.0
      ]
    ],
    "score": 0.94
  }
]
```

The example demonstrates the required structure. Replace the example coordinates, image ID, category ID, and score with values generated for your predictions. The image and category IDs must exist in the public test metadata.

### Submission requirements

1. The file must be valid JSON and its top-level value must be an array (`[]` is valid JSON, but an empty submission may be rejected).
2. Every prediction must include `image_id`, `category_id`, `segmentation`, and `score`.
3. Use only image IDs and category IDs listed in `images_test.json`.
4. `segmentation` must be a non-empty list of polygon arrays. Each polygon must contain at least three `(x, y)` coordinate pairs (`[x1, y1, x2, y2, x3, y3, ...]`).
5. Polygon coordinates must be finite pixel values within the corresponding image boundaries. Do not submit normalized coordinates or YOLO segmentation text files.
6. `score` must be a finite number in the range `[0, 1]`.
7. Submit one record per predicted instance. Multiple pedestrian instances in the same image should have separate records.
8. Submit predictions only. Do not include ground-truth annotations, image files, training labels, or explanatory text in the JSON file.
9. An image with no predicted instances does not need a prediction record.

The public example uses COCO polygon masks. Do not assume that binary mask arrays, YOLO segmentation text files, or arbitrary RLE encodings are accepted unless the repository's current `submission_format.json`, validator, and evaluator explicitly document and support them.

See `data/public/annotations/submission_format.json` and `sample_submission.json` if they are provided in the repository; use the repository files as the final authority for the accepted schema.

## How to submit

Submit your prediction JSON file through the official Google Form:

[Open the PFP-SegC submission form](YOUR_GOOGLE_FORM_URL_HERE)

Complete the form's required team and model information and upload or provide the prediction file as requested. Please do not submit private ground-truth data or share private organizer links.

## Evaluation and leaderboard

Submissions are checked for format and valid IDs, then evaluated against the private test annotations using COCO instance-segmentation evaluation (`segm`). The leaderboard reports segmentation metrics such as AP, AP50, and AP75. A successful format validation does not guarantee a high segmentation score; the score measures agreement between predicted instance masks and the hidden annotations.

[View the PFP-SegC leaderboard](https://faranbutt.github.io/PFP-SegC/)

## Reproducibility and fair use

- Use the publicly released training and validation data for model development.
- Use the published test images and metadata to prepare predictions.
- Do not attempt to obtain, reconstruct, publish, or otherwise use hidden test annotations.
- Report the model and inference settings accurately in the submission form.
- Do not include Google Drive file IDs, private download URLs, credentials, or organizer-only materials in public issues, pull requests, or leaderboard metadata.

## Questions and troubleshooting

Before submitting, verify that your JSON parses, every ID exists in `images_test.json`, every instance has a polygon mask, all polygon coordinates use pixel units, and every confidence score is between `0` and `1`.
