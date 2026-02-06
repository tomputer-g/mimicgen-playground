# mimicgen-playground
Kinova setup + MimicGen + RoboSuite + Mac Mujoco setup

# Installation

Note: Tested on Apple Silicon Mac.

```bash
# Clone this repository (make sure the submodules are cloned as well)
git clone --recursive git@github.com:tomputer-g/mimicgen-playground.git

# Create a conda environment 
conda create -n mimicgen python=3.10
conda activate mimicgen

# Install!
cd egl_probe/
pip install -e .
cd ..

cd mimicgen/
pip install -e .
cd ..

cd robosuite/
pip install -e .
cd ..

cd robomimic/
pip install -e .
cd ..

# Remove some duplicate(?) Mujoco executables. Should only have mujoco==2.3.2
pip install mujoco==2.3.2
pip uninstall free-mujoco-py
pip uninstall mujoco-py

# Install pygame (for annotation)
pip install pygame

# You may have to change env_robosuite.py (in robomimic) potentially due to a mismatch in where exceptions are defined
# If you get an error at robomimic/robomimic/envs/env_robosuite.py around the import for mujoco,
# Make the MUJOCO_EXCEPTIONS list equal empty list.
```

## Test installation
```bash
# Should be able to have an arbitrary manipulator twitch in an arbitrary environment.
cd mimicgen/scripts
python demo_random_action.py

# Should be able to use their source dataset locally
cd mimicgen/
python mimicgen/scripts/download_datasets.py --dataset_type source --tasks square

python mimicgen/scripts/prepare_src_dataset.py \
    --dataset datasets/source/square.hdf5 \
    --env_interface MG_Square \
    --env_interface_type robosuite

python mimicgen/scripts/generate_core_configs.py # NOTE: set NUM_TRAJ = 10 and GUARANTEE = False for debug runs

python mimicgen/scripts/generate_dataset.py \
    --config /tmp/core_configs/demo_src_square_task_D1.json \
    --auto-remove-exp

# You should see generated trajectories under /tmp/core_datasets/square/demo_src/square_task_D1...

# View each individual hdf5:
python robomimic/robomimic/scripts/playback_dataset.py --dataset /tmp/core_datasets/square/demo_src_square_task_D1/tmp/<filename>.hdf5 --render
```


# Known issues
robosuite -> PyObjC requires at least Python 3.9... Used py 3.10 when setting up instead of specified py3.8 under MimicGen.

Made a fork of egl_probe just to change the CMake version, since otherwise it's a long winding road for setup

Have to install EXACTLY b9d8d3de5e3dfd1724f4a0e6555246c460407daa for robosuite otherwise it will have an error when loading coffee machine (Addressed in .gitmodules)

