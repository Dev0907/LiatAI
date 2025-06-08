# LiatAI
# Player ID Mapping Between Broadcast and Tacticam Feeds

## Overview
This project maps player IDs between two video feeds: a broadcast feed (dynamic camera angle) and a tacticam feed (top-down view). The goal is to achieve 100% accuracy in matching players across the feeds, even when they wear similar uniforms (e.g., same team jerseys). The approach uses:

- **YOLOv11** for player detection.
- **MediaPipe** for pose estimation to extract unique keypoints (e.g., shoulders, hips) for each player.
- **Relative Distances** to other players as a perspective-invariant spatial feature.
- **Hungarian Algorithm** for one-to-one matching of players.

## Features
- Detects players in both video feeds using YOLOv11.
- Extracts pose features to differentiate players with similar appearances.
- Uses relative spatial distances to handle perspective differences between feeds.
- Outputs a one-to-one mapping of player IDs (Broadcast ID -> Tacticam ID).

## Requirements
To run this project, you need the following dependencies:
- Python 3.8+
- Libraries:
  - `ultralytics` (for YOLOv11)
  - `mediapipe` (for pose estimation)
  - `opencv-python` (for video processing)
  - `numpy` (for numerical computations)
  - `scipy` (for Hungarian algorithm)
  - `gdown` (for downloading models)

Install the dependencies using:
```bash
pip install gdown ultralytics mediapipe opencv-python numpy scipy
```

## Setup
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/player-id-mapping.git
   cd player-id-mapping
   ```

2. **Prepare the Videos**:
   - Place your `broadcast.mp4` and `tacticam.mp4` videos in a directory (e.g., `data/`).
   - Update the paths in the script (`broadcast_path` and `tacticam_path`) to point to your video files.

3. **Download the YOLO Model** (Optional):
   - The script attempts to download a custom YOLO model from Google Drive (`model_url` in the code).
   - If the download fails, it falls back to the pre-trained `YOLOv11n.pt` model.
   - To use a custom model, ensure the Google Drive link is accessible and the file is a valid `.pt` file.

## Usage
1. **Run the Script**:
   Execute the main script to process the videos and generate player ID mappings:
   ```bash
   python player_id_mapping.py
   ```

2. **Output**:
   - The script prints the player ID mappings to the console.
   - It also saves the mappings to `player_mappings.txt` in the working directory.
   - Example output:
     ```
     Player ID Mappings (Broadcast ID -> Tacticam ID):
     Player 1 in broadcast feed maps to Player 0 in tacticam feed
     Player 3 in broadcast feed maps to Player 2 in tacticam feed
     ...
     ```

## Code Structure
- `player_id_mapping.py`: Main script containing the player detection, tracking, and matching logic.
- `data/`: Directory to store input videos (`broadcast.mp4` and `tacticam.mp4`).

## How It Works
1. **Player Detection**:
   - Uses YOLOv11 to detect players in both video feeds.
   - Falls back to any detected object if the expected class ID (0 for "person") isn’t found.

2. **Feature Extraction**:
   - Extracts pose features (keypoints like shoulders and hips) using MediaPipe to differentiate players.
   - Computes relative distances to other players as a spatial feature.

3. **Player Tracking**:
   - Tracks players within each feed using Intersection over Union (IoU) to maintain consistent IDs across frames.

4. **Player Matching**:
   - Aggregates features across frames to create a stable player profile.
   - Uses the Hungarian algorithm to perform one-to-one matching based on a cost function combining pose and spatial features.

## Troubleshooting
- **Model Loading Error**:
  - If you see `TypeError: model='/kaggle/working/model.pt' is not a supported model format`, ensure the model file exists and is a valid `.pt` file.
  - Verify the Google Drive link (`model_url`) is accessible.
  - The script will fall back to `YOLOv11n.pt` if the custom model fails to load.

- **No Players Detected**:
  - Check the debug logs for `Broadcast detected classes` and `Tacticam detected classes`.
  - If empty, the YOLO model isn’t detecting players. Ensure the videos are valid and the model weights are compatible.

- **No Mappings Generated**:
  - If `player_mappings` is empty, check the player counts (`Broadcast players` and `Tacticam players`) in the debug logs.
  - Ensure both videos contain detectable players and frames are being read successfully.

## Contributing
Contributions are welcome! To contribute:
1. Fork the repository.
2. Create a new branch (`git checkout -b feature/your-feature`).
3. Make your changes and commit (`git commit -m "Add your feature"`).
4. Push to your branch (`git push origin feature/your-feature`).
5. Open a pull request.

## License
This project is licensed under the MIT License. See the `LICENSE` file for details.

## Acknowledgments
- Built with [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) for object detection.
- Uses [MediaPipe](https://mediapipe.dev/) for pose estimation.
- Developed as part of a solution to achieve 100% accuracy in player ID mapping.
