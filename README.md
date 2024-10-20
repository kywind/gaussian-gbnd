<div align="center">

# Dynamic 3D Gaussian Tracking for Graph-Based Neural Dynamics Modeling

[Project Page](https://gs-dynamics.github.io/) | [Video](https://gs-dynamics.github.io/static/website_gaussian_gbnd-4bfc95648bcf3b3feedd0535c3187e7e.mp4)

</div>

<div align="center">
  <img src="assets/teaser.png" style="width:80%" />
</div>

---

## Installation

1. Setup conda environment
```
conda create -n gs-dynamics python=3.9
conda activate gs-dynamics
pip install -r requirements.txt
```

2. Install Gaussian Rasterization
```
mkdir third-party
cd third-party
git clone git@github.com:JonathonLuiten/diff-gaussian-rasterization-w-depth.git
cd diff-gaussian-rasterization-w-depth
python setup.py install
cd ..
```

3. Install [Grounded SAM](https://github.com/IDEA-Research/Grounded-Segment-Anything)
```
python -m pip install -e segment_anything
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
Data should be stored in ```{base_path}/data``` and ```{base_path}/ckpts``` for the raw recording data and the tracking result data separately.

## Tracking Optimization

1. Prepare data to obtain the mask, initial point cloud, and metadata for the object
```
cd src/tracking
python utils/obtain_mask.py --text_prompt "nylon rope" --data_path $data_path # obtain object mask
python utils/merge_masks.py --data_path $data_path # obtain the foreground images of the object
python utils/init_pcd.py --data_path $data_path # obtain the initial point cloud
python utils/metadata.py --data_path $data_path # obtain metadata for training
```

2. Run the optimization
```
python train_gs.py --sequence $episode --exp_name $exp_name --weight_im $weight_im --weight_rigid $weight_rigid --weight_seg $weight_seg --weight_soft_col_cons $weight_soft_col_cons --weight_bg $weight_bg --weight_iso $weight_iso --weight_rot $weight_rot --num_knn $num_knn --metadata_path $metadata_path --init_pt_cld_path=$init_pt_cld_path --scale_scene_radius=$scale_scene_radius
```

We provide a short description of the data captured from real world and the different configurations for various objects in [assets/datasets.md](assets/datasets.md).


## Training dynamics model

1. Prepare the data to parse unit actions,saved in ```{base_path}/preprocessed```
```
cd src
python preprocess.py --config config/rope.yaml  # preprocesses training data; rope as an example
```

2. Train the dynamics model
```
python train.py --config config/rope.yaml
```

## Evaluating dynamics prediction
```
cd src
python predict.py --config config/rope.yaml  # predicts on the provided validation data split
```

## Running real-world rollouts (requires a realsense camera setup)

1. Calibrate the cameras using the chArUcO calibration board
```
cd src/real_world
python calibrate.py --calibrate  # calibrate realsense cameras
```

2. Put object in workspace, run an interactive demo to simulate interaction with the object with GS-Dynamics
```
python gs_sim_real_gradio.py --config config/rope.py
```