---
layout: default
title: "Part 1 — Introduction & FlexMPI"
nav_order: 1
---

# Part 1: Introduction and Content Access

## 1.1 Introduction to HPC and Dynamic Systems

### HPC Area and Dynamic Systems

- Traditional resource management in HPC is static (inefficient?). 
  - Reservations are static, with fixed resources for each job. 
  - Current deployment models demand varying resources at runtime (by phases, workflow stages, etc.).

- Opportunities:
  - Adapt resources to actual demand
  - Fill gaps between tasks
  - Start tasks earlier with fewer resources
  - Reduce work group completion time
  - Potential energy savings through consolidation

- Related works

| Alternative | Mechanism | Slurm support | 
|---|---|---|
| FlexMPI | Runtime + Monitor + library | Yes |
| Elastic MPI | MPI extension + slurm coordinator | Yes |
| DMR | Resource manager + MPI library | 4+ |


### Job Types

- **Rigid** — Fixed resource allocation, cannot change at runtime
- **Moldable** — Resource count decided at launch, fixed thereafter
- **Evolving** — Resources can change at well-defined stages
- **Malleable** — Resources can be added/removed dynamically during execution

### Dynamic Resource Management Policies

- **Proactive** — Resources are adjusted predictively based on forecasts
- **Reactive** — Resources are adjusted in response to actual system events

### Current Challenges

The main challenges involve dynamically managing resources in cooperation with existing management systems (SLURM being the primary one). These systems are complex and are present in most production HPC systems, so integration must be facilitated without requiring customization.

Therefore, the main challenges can be defined as follows:
- Slurm is designed for static reservations.
- Applications require support for runtime reconfiguration.
- Real-time monitoring is required for decision-making.
- Security: Slurm restricts operations between user jobs.
- Avoid modifying Slurm to facilitate its use in real-world environments.

---

## 1.2 FlexMPI 

Runtime that provides dynamic load balancing and performance-aware malleability capabilities to MPI applications

### Architecture and Component Description

- Used with iterative SPMD parallel applications
- Consists of: 
  - A multithreaded library executed within each MPI application.
  - An external controller that coordinates the execution of multiple applications (optional).
  - Interfaces with third-party applications.

- Source code released: https://gitlab.arcos.inf.uc3m.es/desingh/FlexMPI

### Programming example: Original Code vs. FlexMPI Code

- Differences between a pure MPI application and the same application integrated with FlexMPI. 

### FlexMPI Application Co-design: EpiGraph

- Description of a real-world application that uses FlexMPI to exploit malleability and save energy and costs.

---

## 1.3 Environment Setup 

See the [full prerequisites page](sections/00-prerequisites) for platform-specific setup instructions.

### Docker files

Add URL to the docker folder.

### Launch a docker cluster with 3 nodes and 3 "slurm" CPUs

- Open a shell terminal and create the docker cluster using the script.

```bash
# Uncompress the tarball provided
tar zxvf docker_cluster.tar
# Go into the folder
cd docker_cluster
# Execute the build and launch script
./launch-slurm-cluster.sh -n 3 -c 3 -d
```

After this step, the container should be running and it is accessible by terminal.

```bash
# Access the desired node: open a console to a cluster node <num_node> 
docker exec -it test-node-<num_node>-1 /bin/bash
```

---

<nav style="display: flex; justify-content: space-between; margin-top: 3rem;">
  <a href="../index">← Home</a>
  <a href="02-preparing-the-setup">Next: Part 2 →</a>
</nav>
