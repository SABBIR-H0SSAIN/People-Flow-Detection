# People Flow Detection

Uses YOLO11n and ByteTrack to find people, follow their movement, and count them
as they cross two horizontal lines. Saves a video with labels and a heatmap
showing where people spend the most time.

## Quick Start

1. Open `people-flow-detection.ipynb` in Jupyter.
2. Run all cells — dependencies, model weights, and the sample video download automatically when needed.
3. Check `output/` for the video with labels and the heatmap image.

## Detection and Tracking

- **Finding people:** A pretrained YOLO11n model detects people and draws blue
  boxes around them.
- **Following people:** ByteTrack gives each person an ID to follow them across
  frames. The `previous_positions` dictionary remembers their recent positions.
- **Showing results:** The video displays IDs, two colored lines, and IN/OUT
  totals that update as people cross.

## Line Coordinates

The sample video is **1920 × 1080** pixels. Positions are written as `(x, y)`,
measured from the top-left corner. Larger `y` values are farther down the image.

| Line | Color | Start | End | Setting |
|------|-------|-------|-----|---------|
| Upper / IN | Green | `(0, 388)` | `(1919, 388)` | `UPPER = 0.36` |
| Lower / OUT | Red | `(0, 702)` | `(1919, 702)` | `LOWER = 0.65` |

Both lines span the video width. The upper line sits 36% down the image;
the lower line sits 65% down.

To move the lines, change `UPPER` and `LOWER` in the notebook and rerun.
Values range from `0` (top) to `1` (bottom). Keep `UPPER` smaller than `LOWER`.

## IN/OUT Logic

- **IN:** A person's box center moves from above the green upper line to below it.
- **OUT:** A person's box center moves from below the red lower line to above it.
- Moving in the opposite direction does not count. Crossing one qualifying line
  is enough; a person does not need to cross both.

The code smooths recent positions to reduce small box movements. It ignores a
6-pixel strip on each side of a line and confirms the new side twice before
accepting a crossing. First seeing a person does not count as a crossing.
Each ID can add to IN once and OUT once during the video.

## Heatmap

The code records each person's box center in every frame, then smooths and
colors the recorded points. Warm colors show places where people were seen
more often, including people standing still. The heatmap appears over the first
video frame and is saved as a separate image.

## Final Output

| File | Description |
|------|-------------|
| [people-flow-detection.ipynb](people-flow-detection.ipynb) | Code and steps to run the project |
| [output/people-walking-detection.mp4](output/people-walking-detection.mp4) | Video with boxes, IDs, lines, and counters |
| [output/people-walking-heatmap.png](output/people-walking-heatmap.png) | Final heatmap |
| [output/demo.gif](output/demo.gif) | Supplied 1-second preview |

## Demo

[![Tracking preview](output/demo.gif)](output/people-walking-detection.mp4)

![Presence heatmap](output/people-walking-heatmap.png)
