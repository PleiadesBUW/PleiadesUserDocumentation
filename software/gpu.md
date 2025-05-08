---
title: "GPU Modules"
layout: default
parent: Software on PLEIADES
nav_order: 6
---

## Software: GPU Modules
There are several software [modules](../software/modules) intended for GPU nodes.
This includes certain versions of ("CUDA-enabled") TensorFlow, and others.

You can also install software in your home directory, e.g. setting up a python virtual environment containing TensorFlow:

```bash
#!/bin/sh

#SBATCH --job-name=setup_tensorflow_venv
#SBATCH --partition=gpu
#SBATCH --account=<yourgroup>_gpu
#SBATCH --time=0-0:30:0
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --gpus-per-task=1
#SBATCH --gpus=1
#SBATCH --cpus-per-task=16
#SBATCH --mem-per-cpu=16000

module purge
module load 2023a  GCC/12.3.0  OpenMPI/4.1.5
module load Python/3.11.3

echo "SETTING UP VENV"

mkdir /beegfs/${USER}/venv
python -m venv /beegfs/${USER}/venv/tf_cuda
source /beegfs/${USER}/venv/tf_cuda/bin/activate

echo "INSTALLING VENV PACKAGES"
python -m pip install tensorflow[and-cuda] scipy tqdm numpy tf_keras h5py matplotlib tensorflow-probability # more packages you need go here
echo "CHECKING TENSORFLOW"
srun python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
decativate
```
