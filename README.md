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

## References

- nvidia videos [link1](https://www.youtube.com/watch?v=r3LrCnou1K4) [link2](https://youtu.be/iAavYF-XqTA) [link3](https://youtu.be/uM3Ii79KQ20)
- video1 [link](https://www.youtube.com/watch?v=jdip_6vTw0s)