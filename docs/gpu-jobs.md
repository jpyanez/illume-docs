# GPU jobs

## The GPUs

All GPUs are NVIDIA, Pascal generation (CUDA compute capability 6.1):

| Model | Memory |
|---|---|
| TITAN X (Pascal) | 12 GB |
| TITAN Xp | 12 GB |
| GeForce GTX 1080 Ti | 11 GB |

The driver supports CUDA up to 11.4: see [Software](software.md#gpu-software-limits).
`condor_status -compact -af Machine CUDADeviceName TotalGpus` shows which workers have which model.

## Requesting GPUs

```
request_gpus   = 1
request_cpus   = 2
request_memory = 16 GB
```

HTCondor tells your job which GPU(s) it got; frameworks such as PyTorch and TensorFlow see only those. Don't select GPUs by number yourself.

To ask for a particular model, or for 12 GB of GPU memory:

```
requirements = (CUDADeviceName == "NVIDIA TITAN Xp")
# or
requirements = (CUDAGlobalMemoryMb >= 12000)
```

Leave the model open unless you need it: your job starts sooner.

## Example: PyTorch training in a container

`train.sub`:

```
executable     = train.sh
transfer_input_files = train.py, pytorch.sif
output         = logs/train.$(Cluster).out
error          = logs/train.$(Cluster).err
log            = logs/train.$(Cluster).log
request_gpus   = 1
request_cpus   = 4
request_memory = 24 GB
request_disk   = 20 GB
queue
```

`train.sh`:

```bash
#!/bin/bash
nvidia-smi                                       # record which GPU the job got
singularity exec --nv pytorch.sif python3 train.py
```

Build `pytorch.sif` once on the login node from an image with a CUDA 11 build of PyTorch, e.g. `singularity pull pytorch.sif docker://pytorch/pytorch:<tag>` with a `cuda11.8` tag.
For large images, put the `.sif` file in `/data` and use its full path instead of transferring it.

<!-- TODO (admin): test a specific pytorch image tag on a worker and put it here -->
