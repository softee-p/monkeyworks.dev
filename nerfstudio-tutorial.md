---
title: "Installing WSL, CUDA Toolkit, and Nerfstudio: A Comprehensive Guide"
description: "Learn how to set up WSL2, install CUDA Toolkit, and get Nerfstudio running on your Windows machine."
date: "2024-09-03"
---

# Installing WSL, CUDA Toolkit, and Nerfstudio: A Comprehensive Guide

In this tutorial, we'll walk through the process of setting up Windows Subsystem for Linux (WSL2), installing CUDA Toolkit, and getting Nerfstudio up and running. This guide has been tested successfully on Ubuntu 22.04.

## 1. Installing WSL

First, let's install WSL:

1. Enable WSL from Windows features.
2. Open PowerShell as administrator and run:

```powershell
wsl --install
wsl --update
wsl
```

For more detailed information, check out the [official Microsoft WSL documentation](https://learn.microsoft.com/en-us/windows/wsl/install).

### Moving WSL (Optional)

If you want to move your WSL disk to another Windows drive, follow [this guide](https://github.com/LpCodes/Moving-WSL-Distribution-to-Another-Drive) and don't forget to set the default user to avoid bugs.

## 2. Setting Up the Environment

### Update WSL

Once inside WSL, update your system:

```bash
sudo apt update && sudo apt upgrade -y
```

### Install Anaconda

Download and install Anaconda:

```bash
# Replace <INSTALLER_VERSION> with the latest version
curl -O https://repo.anaconda.com/archive/Anaconda3-<INSTALLER_VERSION>-Linux-x86_64.sh
shasum -a 256 ./Anaconda3-<INSTALLER_VERSION>-Linux-x86_64.sh
bash ./Anaconda3-<INSTALLER_VERSION>-Linux-x86_64.sh
```

To prevent conda base environment from auto-activating:

```bash
conda config --set auto_activate_base False
```

## 3. Installing CUDA Toolkit 11.8

Follow the [official NVIDIA CUDA WSL-Ubuntu installation guide](https://docs.nvidia.com/cuda/wsl-user-guide/index.html) for the most up-to-date instructions.

Important notes:
- Do not install any drivers inside WSL, as this will overwrite the shared driver that already exists.
- Follow the deb(local) installation method.

Before installing, remove any old apt keys:

```bash
sudo apt-key del 7fa2af80
```

After installation, you may need to edit your `~/.bashrc` file. Add these lines at the end:

```bash
export PATH=/usr/local/cuda-11.8/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Then run:

```bash
source ~/.bashrc
```

## 4. Installing Nerfstudio

Now, let's install Nerfstudio following the [official instructions](https://docs.nerf.studio/quickstart/installation.html):

1. Create a conda environment:

```bash
conda create --name nerfstudio -y python=3.8
conda activate nerfstudio
python -m pip install --upgrade pip
```

2. Install PyTorch for CUDA 11.8:

```bash
pip install torch==2.1.2+cu118 torchvision==0.16.2+cu118 --extra-index-url https://download.pytorch.org/whl/cu118
```

3. Verify CUDA availability:

```python
python
>>> import torch
>>> torch.cuda.is_available()
True
```

If it returns `False`, try reinstalling NVIDIA Toolkit or run:

```bash
conda install cuda -c nvidia/label/cuda-11.8.0
```

4. Install tiny-cuda-nn:

```bash
pip install ninja git+https://github.com/NVlabs/tiny-cuda-nn/#subdirectory=bindings/torch
```

5. Install Nerfstudio:

```bash
git clone https://github.com/nerfstudio-project/nerfstudio.git
cd nerfstudio
pip install --upgrade pip setuptools
pip install -e .
ns-install-cli
```

Restart your terminal after installation.

## 5. Installing COLMAP

To install COLMAP:

```bash
git clone https://github.com/colmap/colmap.git
cd colmap
mkdir build
cd build
cmake .. -GNinja -DCMAKE_CUDA_ARCHITECTURES=89
ninja
sudo ninja install
ns-install-cli
```

Relaunch your terminal after installation.

## Tips and Tricks

- To convert PLY to a standalone static HTML webpage powered by gsplat.js, check out [this GitHub repository](https://github.com/SpectacularAI/point-cloud-tools).
- When converting data with sai-cli, don't use the `--preview` option to avoid OpenGL errors.
- Consider using RealityCapture Desktop as an alternative to COLMAP. It's now free, and you can find instructions on converting data to Nerfstudio format [here](https://docs.nerf.studio/quickstart/custom_dataset.html#realitycapture).

With these steps, you should have WSL, CUDA Toolkit, and Nerfstudio up and running on your Windows machine. Happy experimenting!
