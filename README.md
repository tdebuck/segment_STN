# Segment-STN  
## Automated Subthalamic Nucleus Segmentation from T2-weighted MRI

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
================================================================================
```

---

## Overview

**Segment-STN** is a research tool for **fully automated segmentation of the subthalamic nucleus (STN)** from **T2-weighted (T2w) MRI scans**.

It is designed to be:
- Easy to use (Docker / Singularity)
- Scalable (HPC / SLURM compatible)
- Clinically interpretable (left/right outputs)

---

## Example Output (MNI space)

> 📌 *TBD: Insert example figure here (e.g., STN segmentation overlaid on MNI T2 template)*

```
[ Placeholder for figure: STN segmentation on MNI template ]
```

---

## What does this tool do?

Segment-STN:
- Detects the **subthalamic nucleus (STN)** automatically
- Produces **left and right hemisphere segmentations**
- Optionally outputs a **probability map (model confidence)**

This is particularly useful in **DBS research workflows** and anatomical studies.

---

## Training & Generalization

- Trained on **>400 manually annotated STN masks**
- Based on **7 Tesla (7T) high-resolution MRI data**
- Found to generalize to:
  - Other field strengths (including 3T MRI)
  - Different vendors (Siemens, Philips, GE)
- Works best when:
  - Image quality is high in the deep nuclei
  - Input data is *not* brain-extracted (de-faced is fine)

---

## Input Requirements

- **Modality:** T2-weighted MRI (T2w)
- **Format:** `.nii` or `.nii.gz`
- Place all scans in a single folder

---

## Output

For each scan:

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

### Docker (local machine)
*Run the tool locally using a containerized environment.*

```bash
docker run --rm -v /path/to/data:/data thijsdebuck/segment-stn:1.105
```

---

### Singularity (HPC / clusters)
*Run the same container without Docker (common on HPC systems).*

```bash
singularity run --nv \
  --bind /path/to/data:/data \
  docker://thijsdebuck/segment-stn:1.105
```

---

### SLURM (scheduled jobs)
*Submit a batch job on a compute cluster.*

```bash
#!/bin/bash
#SBATCH --job-name=STN_segment
#SBATCH --time=6:00:00
#SBATCH --cpus-per-task=10
#SBATCH --mem=128GB
#SBATCH --partition=cuda

DATA_DIR=/path/to/data

singularity run --nv \
  --bind ${DATA_DIR}:/data \
  docker://thijsdebuck/segment-stn:1.105
```

---

### Directly loading nnUNet-v2 weights
*Feel free to reach out for the shared model weights (1.14GB). These are to large to include on GitHub, but can be shared directly. In that case, I can also share the model trained on 1.5 mm data (instead of 1.5 mm). Inference is quicker using this model, but it is less robust to differnces in contrast/resolution - so it's not included in the "main" release of Segment-STN.*

---

## Modes

Run with:

```bash
docker run ... segment-stn [mode]
```

| Mode | Description |
|------|------------|
| fast | Quick single-pass inference (low accuracy) |
| full | More robust (ensemble) |
| prob | Default; includes voxel-wise probability map |

---

## Hardware Support

- ✅ NVIDIA GPU (CUDA) — recommended  
- ✅ Apple Silicon GPU (MPS)  
- ⚠️ CPU — slower and may fail (not recommended)

---

## Disclaimer

This tool is intended for **research purposes only**.  
Not approved for clinical use.

---

## Contact

Thijs de Buck  
Amsterdam UMC & Spinoza Centre for Neuroimaging
m.h.s.debuck@amsterdamumc.nl & t.de.buck@spinozacentre.nl
