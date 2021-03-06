# Docker Nvidia - Setup

Docker Nvidia is a container toolkit allows users to build and run GPU accelerated containers. The toolkit includes a container runtime library and utilities to auomatically configure containers to leverate Nvidia GPUs.  

![image](https://cloud.githubusercontent.com/assets/3028125/12213714/5b208976-b632-11e5-8406-38d379ec46aa.png)  

## Architecture Overview

The Nvidia container toolkit is comprised of the following components (from top to bottom in the hierarchy):  
- `nvidia-docker2`
- `nvidia-container-runtime`
- `nvidia-container-toolkit`
- `libnvidia-container`

![image](https://docs.nvidia.com/datacenter/cloud-native/_images/nvidia-docker-arch.png)  

The `nvidia-docker2` package dependencies can be seen below:  
```
├─ nvidia-docker2
│    ├─ docker-ce (>= 18.06.0~ce~3-0~ubuntu)
│    ├─ docker-ee (>= 18.06.0~ce~3-0~ubuntu)
│    ├─ docker.io (>= 18.06.0)
│    └─ nvidia-container-runtime (>= 3.3.0)

├─ nvidia-container-runtime
│    └─ nvidia-container-toolkit (<< 2.0.0)

├─ nvidia-container-toolkit
│    └─ libnvidia-container-tools (<< 2.0.0)

├─ libnvidia-container-tools
│    └─ libnvidia-container1 (>= 1.2.0~rc.3)
└─ libnvidia-container1
```

For more information, please visit the official [page](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/arch-overview.html#arch-overview).

## Pre-Requisites

1.GNU/Linux x86_64 with kernel version > `3.10  `
1.Docker >= `19.03` (recommended, but some distributions may include older versions of Docker. The minimum supported version is 1.12)  
1.NVIDIA GPU with Architecture > `Fermi` (or compute capability 2.1)  
1.NVIDIA drivers ~= `361.93` (untested on older versions)  

## Installation

The installation steps are tested and verified on the Ubuntu18.04.  

**Step 1: Install docker**  
Install docker, visit my [guide](https://github.com/BruceChanJianLe/docker-setup) or official [instructions](https://docs.docker.com/engine/install/).  

**Step 2: Setup the `stable` repository and the GPG key**  
```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```

**Step 3: Install `nvidia-docker2` package**
```bash
sudo apt-get update

sudo apt-get install -y nvidia-docker2
```

**Step 4: Restart the Docker daemon to complete the installation after setting the default runtime**  
```bash
sudo systemctl restart docker
```

**Step 5: Testing the installation**  
```bash
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi
# Output
# +-----------------------------------------------------------------------------+
# | NVIDIA-SMI 450.51.06    Driver Version: 450.51.06    CUDA Version: 11.0     |
# |-------------------------------+----------------------+----------------------+
# | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
# | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
# |                               |                      |               MIG M. |
# |===============================+======================+======================|
# |   0  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
# | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
# |                               |                      |                  N/A |
# +-------------------------------+----------------------+----------------------+

# +-----------------------------------------------------------------------------+
# | Processes:                                                                  |
# |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
# |        ID   ID                                                   Usage      |
# |=============================================================================|
# |  No running processes found                                                 |
# +-----------------------------------------------------------------------------+
```

For more detail installation steps please refer to this [page](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html).  

## Pre-Requisites Installation

**Nvidia Driver**  

Installing the latest Nvidia driver and cuda is quite important, therefore, I would like to take some time to point our to some useful resources.  

Starting from Ubuntu16 you will be able to install Nvidia drivers through `Software & Update` software on your Ubuntu. However, they may cause issue like greying out some options. Please follow this two links to remove and reinstalled your driver.  
- [link1](https://ubuntuforums.org/showthread.php?t=2413765)
- [link2](https://ubuntuforums.org/showthread.php?t=2397701&highlight=continue+manually+installed+driver)

You could also always download from their official site [link](https://www.nvidia.com/Download/index.aspx?lang=en-us).  

**Nvidia CUDA**  

Ubuntu 16.04 LTS and Ubuntu 18.04 LTS distributions using the package manager.  
```bash
# Step 1
# The NVIDIA driver requires that the kernel headers and development packages for the running version of the kernel be installed at the time of the driver installation, as well whenever the driver is rebuilt. For example, if your system is running kernel version 4.4.0, the 4.4.0 kernel headers and development packages must also be installed.
sudo apt-get install linux-headers-$(uname -r)

# Step 2
# Ensure packages on the CUDA network repository have priority over the Canonical repository.
distribution=$(. /etc/os-release;echo $ID$VERSION_ID | sed -e 's/\.//g')
wget https://developer.download.nvidia.com/compute/cuda/repos/$distribution/x86_64/cuda-$distribution.pin
sudo mv cuda-$distribution.pin /etc/apt/preferences.d/cuda-repository-pin-600

# Step 3
# Install the CUDA repository public GPG key. Note that on Ubuntu 16.04, replace https with http in the command below.
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/$distribution/x86_64/7fa2af80.pub

# Step 4
# Setup the CUDA network repository.
echo "deb http://developer.download.nvidia.com/compute/cuda/repos/$distribution/x86_64 /" | sudo tee /etc/apt/sources.list.d/cuda.list

# Step 5
# Update the APT repository cache and install the driver using the cuda-drivers meta-package. Use the --no-install-recommends option for a lean driver install without any dependencies on X packages. This is particularly useful for headless installations on cloud instances.
sudo apt-get update
sudo apt-get -y install cuda-drivers

# Step 6
# Follow the post-installation steps in the CUDA Installation Guide for Linux to setup environment variables, NVIDIA persistence daemon (recommended) and to verify the successful installation of the driver.
# https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions
# But we can skip this step since we are using a docker container:D
```

- [link](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)  

## References

- nvidia videos [link1](https://www.youtube.com/watch?v=r3LrCnou1K4) [link2](https://youtu.be/iAavYF-XqTA) [link3](https://youtu.be/uM3Ii79KQ20)
- video1 [link](https://www.youtube.com/watch?v=jdip_6vTw0s)