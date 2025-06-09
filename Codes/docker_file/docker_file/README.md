# EuroSAT Classifier Deployment on GPU Droplet (DigitalOcean)

INFO: Follow this guide to run all models described in the thesis. This README explains how to use this folder to prepare, transfer, and run a Docker-based training setup on a GPU-powered Virtual Machine.
IMPORTANT: Choose which proportion of data (i.e 10%, 50%) should be used as baseline for the classifier. Correspondingly, also choose which diffusion model should be used (i.e based on 10% or 50% of the data). The parts to be adjusted in the code are marked ######.
The code can be found in the file `05_claudia10_metrics.py`.
---

This repository includes all code, data, and configuration files needed to build the Docker image. The Dockerfile is configured to copy everything inside the current directory into the image under /workspace/.


In this example, we will create a docker image called `eurosat-classifier_50
### 1. Build the Docker Image

```bash
docker build -t eurosat-classifier_50 .
```

### 2. Make sure all files got copied correctly into the Docker image

To ensure all necessary files (e.g., training scripts, datasets, requirements.txt) are available inside the container:

```bash

docker run --rm -it eurosat-classifier_50 ls /workspace
```

### 3. Save and Compress for later use:

The following makes the image ready to use if we want to deploy it on a virtual machine.

```bash

docker save eurosat-classifier_50 | gzip > eurosat-classifier_50.tar.gz
```



### 4. Upload the Image to the GPU Droplet

Use scp (secure copy) to transfer the compressed Docker image to your remote VM:

```bash
scp -i ~/KEY eurosat-classifier_50.tar.gz root@<DROPLET_IP>:~
```

---


### 5. SSH into the Droplet

Access your droplet via SSH:

```bash
ssh -i ~/KEY root@<DROPLET_IP>
```

### 6. Install Docker

Install Docker to enable container execution:

```bash
apt update && apt install -y docker.io
```

### 7. Install NVIDIA GPU Drivers & Toolkit


Install GPU drivers and the container toolkit so Docker can access the GPU:


```bash
apt install -y ubuntu-drivers-common
ubuntu-drivers autoinstall
reboot
```

 After reboot, reconnect:

```bash
ssh -i ~/KEY root@<DROPLET_IP>
```

Verify GPU access:

```bash
nvidia-smi
```

Install NVIDIA Container Toolkit:

```bash
distribution=ubuntu22.04
curl -s -L https://nvidia.github.io/libnvidia-container/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
  sudo tee /etc/apt/sources.list.d/libnvidia-container.list

apt update
apt install -y nvidia-container-toolkit
nvidia-ctk runtime configure --runtime=docker
systemctl restart docker
```

---

##  Load and Verify Docker Image

###  8. Decompress and Load the Image

```bash
gunzip eurosat-classifier_50.tar.gz
docker load < eurosat-classifier_50.tar
```

List available images:

```bash
docker images
```

---

## Run Training in a GPU-Powered Container

Now we're ready to run the training process inside a GPU-enabled container.

### 9. Run the Docker Container

```bash
docker run --rm --gpus all \
  --shm-size=16g \
  --ulimit memlock=-1 \
  --ulimit stack=67108864 \
  -v ~/outputs:/workspace/output \
  -v ~/results:/workspace/results \
  -v ~/data:/workspace/data \
  -it eurosat-classifier_50
```

 Make sure your script saves files into `/workspace/output` or `/workspace/results`.

---

Additionally, to avoid loosing progress when getting disconnected, the following can be done:


##  Keep Training Running While Disconnected

### Use `tmux`:

```bash
apt install -y tmux
tmux new -s eurosat
```

Inside the session, run the Docker command above. Then detach safely:

```
Ctrl + b, then d
```

Reattach later with:

```bash
tmux attach -t eurosat
```

---

##  Download Trained Models & Results

Finally, we can securely copy the results and put it into our local environment!

```bash
scp -i ~/KEY root@<DROPLET_IP>:~/outputs/* ./local_outputs/
scp -i ~/KEY root@<DROPLET_IP>:~/results/* ./local_results/
```


