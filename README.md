# RapidCFD
### CFD toolbox running on CUDA

OpenFOAM solvers ported to Nvidia CUDA. All the calculations are done on the GPU giving a huge speed-up.
Still in development stage, waiting for your contribution!

### Features:
* most incompressible and compressible solvers on static mesh are available
* all the calculations are done on the GPU
* no overhead for GPU-CPU memory copy
* can run in parallel on multiple GPUs


### Compilation instructions for Ubuntu 20.04

_Rewroted issue [#93](https://github.com/Atizar/RapidCFD-dev/issues/93)_

#### Install CUDA toolkit

* Download CUDA toolkit [here](https://developer.nvidia.com/cuda-downloads)
* Install CUDA toolkit per instructions provided at time of download
* Instructions for WSL [here](https://docs.nvidia.com/cuda/wsl-user-guide/index.html)

Also check and remember compute capability of your GPU [here](https://developer.nvidia.com/cuda-gpus)

*e.g. for GeForce 3070ti Compute capability 8.6*

Check CUDA:

```bash 
$nvcc --version

nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Tue_May__3_18:49:52_PDT_2022
Cuda compilation tools, release 11.7, V11.7.64
Build cuda_11.7.r11.7/compiler.31294372_0

```
If nvcc not found, you may need to set environment variables, e.g.,

```bash
export CUDA_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin
```

After executing these export statements try nvcc --version again. If it does not work, then you will need to find the location of CUDA on your system and edit the paths above as required for your individual setup. If it works, proceed to next step

#### Install RapidCFD [Under issues on my side]

```bash
#as dirpath is hardcoded and I don't want to fix it:
cd ~
mkdir RapidCFD
cd RapidCFD
git clone https://github.com/Atizar/RapidCFD-dev
cd RapidCFD-dev
```

Update compute capability in local files

```bash
vim wmake/rules/linux64Nvcc/c:10

cc = nvcc -m64 -arch=sm_86

## 86 means compute capability 8.6 Set your own 

vim wmake/rules/linux64Nvcc/c++:10

CC = nvcc -Xptxas -dlcm=cg -std=c++17 -m64 -arch=sm_86

```

Now, start compilation

```bash
#to run compilation in parallel:
export WM_NCOMPPROCS=10 # where 10 - is number of CPU's on your host
source etc/bashrc
./Allwmake
```

_If you have multiple GPU's then you will need to install ThirdParty-dev. I placed a copy of the ThirdParty-dev that I use here. To the best of my recollection (because I haven't rebuilt this ThirdParty-dev directory in years), you can place this material in /opt/ThirdParty-dev and compilation fo RapidCFD-dev should automatically find openmpi._



