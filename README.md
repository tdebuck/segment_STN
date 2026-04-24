# Segment-STN  
## Automated Subthalamic Nucleus Segmentation from T2-weighted MRI  
This tool is intended for **research purposes only**, not approved for clinical use.

```
================================================================================
|      _____                                 _         _____ _______ _   _     |
|     / ____|                               | |       / ____|__   __| \ | |    |
|    | (___   ___  __ _ _ __ ___   ___ _ __ | |_     | (___    | |  |  \| |    |
|     \___ \ / _ \/ _` | '_ ` _ \ / _ \ '_ \| __| --  \___ \   | |  | . ` |    |
|     ____) |  __/ (_| | | | | | |  __/ | | | |_      ____) |  | |  | |\  |    |
|    |_____/ \___|\__, |_| |_| |_|\___|_| |_|\__|    |_____/   |_|  |_| \_|    |
|                  __/ |                                                       |
|                 |___/                v1.105, April 2026                      |
|                                                                              |
|        Thijs de Buck, Maarten Bot, et al.                                    |
|        Department of Neurosurgery, Amsterdam UMC, The Netherlands            |
================================================================================
```
## Table of Contents

- [Overview & Example](#overview)
- [Training & Generalization](#training--generalization)
- [Input & Output](#input--output)
- [How to Use](#how-to-use)
  - [Option 1: Docker (local machine)](#option-1-docker-local-machine)
  - [Option 2: Singularity (HPC / clusters)](#option-2-singularity-hpc--clusters)
  - [Option 3: SLURM (scheduled jobs)](#option-3-slurm-scheduled-jobs)
  - [Option 4: Directly loading nnUNet-v2 weights](#option-4-directly-loading-nnunet-v2-weights)
- [Settings](#settings)
- [Contact](#contact)

---

## Overview

**Segment-STN** is a research tool for **fully automated segmentation of the subthalamic nucleus (STN)** from **T2-weighted (T2w) MRI scans**.

It is designed to be:
- Easy to use (Docker / Singularity)
- Scalable (HPC / SLURM compatible)
- Clinically interpretable (left/right outputs and voxel-wise probabilities)

**Example**  
The figure below shows Segment-STN output for T2-weighted scans acquired at 3T and at 7T in the same subject. Example data were taken from the _UNC Paired 3T and 7T dataset_ (https://doi.org/10.6084/m9.figshare.c.6485272), so independent from the training data.

<p align="center">
  <img src="https://raw.githubusercontent.com/tdebuck/segment_STN/main/Segment-STNexample.png" width="80%">
</p>
<p align="center">
  <em>Probabilistic STN segmentation on 3T and 7T T2-weighted MRI data.</em>
</p>

---

## Training & Generalization

Segment-STN ...  
... was trained on **400 manually annotated STN masks** on **7 Tesla (7T) high-resolution MRI data**  
... was found to generalize to other field strengths (including 3T MRI) and different vendors (Siemens, Philips, GE)  
... works best when image quality is high in the deep nuclei and input data is *not* brain-extracted (de-faced is fine)

---

## Input & Output

**Input**  
- **Modality:** T2-weighted MRI (T2w)
- **Format:** `.nii` or `.nii.gz`
- Place all scans in a single folder

**Output**  
For each scan (i.e., each nifti file):
- `<basename>_STN.nii.gz` → Full segmentation  
- `<basename>_LSTN.nii.gz` → Left STN  
- `<basename>_RSTN.nii.gz` → Right STN  

Optional (prob mode):
- `<basename>_STN_probabilistic.nii.gz`

Outputs are saved in:
```
/input_folder/Segment-STN/
```

---

## How to Use

For the most recent Docker tags for Segment-STN, see https://hub.docker.com/repository/docker/thijsdebuck/segment-stn/tags

### Option 1: Docker (local machine)
*Run the tool locally using a containerized environment.*

```bash
docker run --rm -v /path/to/data:/data thijsdebuck/segment-stn:1.105
```

---

### Option 2: Singularity (HPC / clusters)
*Run the same container without Docker (common on HPC systems).*

```bash
singularity run --nv \
  --bind /path/to/data:/data \
  docker://thijsdebuck/segment-stn:1.105
```

---

### Option 3: SLURM (scheduled jobs)
*Submit a batch job on a compute cluster.*

```bash
#!/bin/bash
#SBATCH --job-name=STN_segment
#SBATCH --time=1:00:00
#SBATCH --cpus-per-task=10
#SBATCH --mem=64GB
#SBATCH --partition=cuda

DATA_DIR=/path/to/data

singularity run --nv \
  --bind ${DATA_DIR}:/data \
  docker://thijsdebuck/segment-stn:1.105
```

---

### Option 4: Directly loading nnUNet-v2 weights
Feel free to reach out to receive the raw model weights (1.14GB). These are too large to include on GitHub, but can be shared directly. In that case, I can also share the model trained on 1.5 mm data (instead of 1.5 mm). Inference is quicker using this model, but it is less robust to differences in contrast/resolution - so it's not included in this "main" release of Segment-STN.

---

## Settings

For simplicity, the dockerized version of Segment-STN only takes one single input argument: _mode_ (default = _prob_). Below is an overview of the three possible settings.

| Mode | Description |
|------|------------|
| fast | Quick single-pass inference (low accuracy) |
| full | More robust (ensemble) |
| prob | Default & recommended; includes voxel-wise probability map |

---

## Hardware Support

- ✅ NVIDIA GPU (CUDA) — recommended  
- ✅ Apple Silicon GPU (MPS)  
- ⚠️ CPU — slower and may fail (not recommended)

---

## Contact

Thijs de Buck & Maarten Bot  
Amsterdam UMC  
m.bot@amsterdamumc.nl & m.h.s.debuck@amsterdamumc.nl  

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTo2t2zGWE90lTRjLz6FX-dd5aBEuJn07ys5A&s" width="400"/>

This tool is intended for **research purposes only**.  
Not approved for clinical use.
