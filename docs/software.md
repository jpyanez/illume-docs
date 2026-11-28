# Software

The login node and all workers run the same operating system (Ubuntu 20.04) with the same basic tools.
For anything beyond that, use CVMFS, a container, or your own Python environment.

## CVMFS software repositories

CVMFS provides read-only software repositories under `/cvmfs`, on the login node and all workers.
A repository appears the first time you access it, so `ls /cvmfs` can look empty: `ls /cvmfs/sft.cern.ch` works.

| Repository | What's in it |
|---|---|
| `icecube.opensciencegrid.org` | IceCube software and containers |
| `software.pacific-neutrino.org` | P-ONE software |
| `sft.cern.ch` | CERN LCG software stacks (ROOT, Geant4, Python with scientific packages, ...) |
| `oasis.opensciencegrid.org`, `grid.cern.ch` | Open Science Grid and grid tools |
| `snoplus.egi.eu` | SNO+ software |

Example: a full LCG software stack (ROOT 6.34, Python 3.11 with NumPy and SciPy, ...) in one line:

```bash
source /cvmfs/sft.cern.ch/lcg/views/LCG_107/x86_64-ubuntu2004-gcc9-opt/setup.sh
```

LCG_107 is the newest stack built for Ubuntu 20.04.

## Containers

[Singularity](https://docs.sylabs.io/guides/3.7/user-guide/) 3.7 is installed on the login node and the workers.

```bash
singularity exec docker://python:3.11-slim python3 --version        # image from Docker Hub
singularity exec --nv my_image.sif python3 train.py                  # --nv gives the container the GPUs
```

- Build or pull the image once on the login node (`singularity pull my_image.sif docker://...`) and use the `.sif` file in your jobs; don't pull from Docker Hub in every job.
- Podman is also available on the login node for building images.

## Python

- The system Python is 3.8 (old). Prefer your own environment:
    - **conda**: Anaconda is installed in `/opt/anaconda3` on all nodes. Create your own environments in your home directory: `/opt/anaconda3/bin/conda create -n myenv python=3.11`.
    - **venv**: `python3 -m venv ~/venvs/myenv`.
    - or a **container** (above), which also pins the operating system.
- Keep environments in `/home`, so the workers see them too.

## GPU software limits

The GPUs are of the Pascal generation (CUDA compute capability 6.1), and the NVIDIA driver supports CUDA up to **11.4**.

- Use framework builds for **CUDA 11** (e.g. PyTorch `cu118` wheels), not CUDA 12.
- The newest framework releases are dropping Pascal support; if a recent version says "no kernel image is available for execution on the device", use an older version.

<!-- TODO (admin): test a PyTorch cu118 install on a worker and name a version known to work -->

## Asking for new software

Ask in **#illume-users**. Many requests are best solved with a container or conda environment in your home directory; system-wide installs are for things many users need.
