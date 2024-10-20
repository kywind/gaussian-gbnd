# GS-Dynamics

## Installation
Steps:
- Install GroundingDINO
- Install diff-gaussian-rasterization
- Download GroundingDINO and SegmentAnything weights
```
mkdir third-party
mkdir weights
cd third-party
git clone git@github.com:JonathonLuiten/diff-gaussian-rasterization-w-depth.git
cd diff-gaussian-rasterization-w-depth
python setup.py install
cd ..
git clone git@github.com:IDEA-Research/GroundingDINO.git
cd GroundingDINO
python setup.py install
cd ../../weights
gdown 1pc195KITtCCLteHkxD7mr1w-cG8XAvTs  # download DINO+SAM weights
gdown 1X-Et3-5TdSWuEUfq9gs_Ng-V-hypmLdB
gdown 1HR3O-rMv9qJoTrG6GNxi-4ZVREQzZ1Rf
gdown 1kN4trMTo5cavUqRSkYu0uzJq4mcL_ul_
```

## Data Preparation
Data should be stored in ```{base_path}/data``` and ```{base_path}/ckpts``` for the raw recording
data and the tracking result data separately.

**TODO:** describe data format more specifically.

## Training dynamics model
Steps:
- Preprocess data to parse unit actions, generating ```{base_path}/preprocessed```
- Train the model
```
cd src
python preprocess.py --config config/rope.yaml  # preprocesses training data; rope as an example
python train.py --config config/rope.yaml  # trains
```

## Evaluating dynamics prediction
```
cd src
python predict.py --config config/rope.yaml  # predicts on the provided validation data split
```

## Running real-world rollouts (requires a realsense camera setup)
Steps:
- Calibrate the cameras using the chArUcO calibration board
- Put object in workspace, run an interactive demo to simulate interaction with the object with GS-Dynamics
```
cd src/real_world
python calibrate.py --calibrate  # calibrate realsense cameras
python gs_sim_real_gradio.py --config config/rope.py
```

