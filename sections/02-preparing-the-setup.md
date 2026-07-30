---
layout: default
title: "Part 2 — Preparing the Setup"
nav_order: 2
---

# Part 2: Preparing the Setup

---

## 2.1 Docker Environment Overview 

### Architecture


### Dependencies

All the required dependencies shuld be satisfied automatically. 

However, the dependencies used by the complete framework are listed here: 

- GCC >= 4.9
- PAPI 
- GNU Linear Programming Kit
- MPI MPICH distribution > 4.0
- mercury 
- argobots
- json-c 
- margo 
- redis 
- hiredis 
- slurm > 24

### Why This Environment?

This tutorial uses Docker to provide a fully self-contained environment with MPI, FlexMPI, and all dependencies pre-configured. Docker eliminates the complexity of installing HPC libraries natively across different operating systems and guarantees that every participant runs identical software regardless of their platform. Furthermore, it eliminates the need for accounts or access to real HPC clusters, which could be a problem during the development of this tutorial, and would require a continuously stable internet connection.

It also makes cleanup simple: when the tutorial ends, simply remove the container and nothing remains on your machine. This reproducibility lets us focus on malleability concepts rather than troubleshooting environment inconsistencies.


---

## 2.2 Building the Docker Image 

### Step 1: Understanding the Dockerfile

The Dockerfile uses a multi-stage build pattern to separate the heavy build process from the final lightweight execution image.

- Stage 1: Build & Compilation
  - System Tools & Dev Libraries: Installs essential compilation tools.
  - Download dependencies
  - Extraction & Compilation: Unpacks, configures, compiles (make), and installs all downloaded tools into /usr/local.

- Stage 2: Final Runtime Image
  - Runtime Dependencies: Installs runtime tools and system dependencies.
  - Copying Compiled Libraries: Copies the pre-compiled binaries and libraries from stage1 (/usr/local) directly into the final image.
  - Service Configurations: munge, slurm and redis. 
  - User Provisioning & SSH Setup
  - Container Entrypoint: Copies docker-entrypoint.sh to the root directory and assigns it as the container's entrypoint to manage startup processes when the container runs.


### Step 2: Download and Build

Download the docker files from this link. 

Open a shell terminal, unzip the tarball and create the docker cluster using the script.

```bash
# Go to the Downloads folder (or the folder that contains the tarball)
cd ~/Downloads
# Unzip the tarball provided
tar zxvf docker_cluster.tar
# Go into the folder
cd docker_cluster
# Execute the build and launch script
./launch-slurm-cluster.sh -n 3 -c 3 -d
```


### Step 3: Verification

After step 2, the container should be running and it is accessible by terminal.

```bash
# Access the desired node: open a console to a cluster node <num_node> 
docker exec -it test-node-<num_node>-1 /bin/bash
```

Now, the user can use the system like a real machine with an Ubuntu OS installed.

---

## 2.3 User Interface Setup 

### Terminal Layout and Workspace


### Environment Variables and Aliases


### Testing the Connection


---

<nav style="display: flex; justify-content: space-between; margin-top: 3rem;">
  <a href="01-introduction-and-flexmpi">← Previous: Part 1</a>
  <a href="03-demo-execution">Next: Part 3 →</a>
</nav>
