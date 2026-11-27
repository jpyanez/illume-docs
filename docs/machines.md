# The machines

## Login node

`login.illume.systems` is where you log in, edit files, compile code, and submit jobs.
It is shared by everyone: **do not run heavy computations on it.** Use jobs instead.

## Worker nodes

Jobs run on the worker nodes. A typical worker has:

| | |
|---|---|
| CPU | 16 threads, Intel Xeon E5-2623 v4 (2.6 GHz, Broadwell). Supports AVX2, **not AVX-512** |
| Memory | about 116 GB |
| GPUs | up to 4, NVIDIA Pascal generation (see [GPU jobs](gpu-jobs.md)) |
| Local scratch | about 3 TB on spinning disks, per worker |

A few workers have fewer CPUs or less memory. To see what is available right now:

```bash
condor_status -compact
```

!!! note "AVX-512"
    Some prebuilt software, or code compiled with `-march=native` on a newer computer, needs AVX-512 and crashes on the workers with "Illegal instruction".
    The login node has a different CPU (AMD EPYC) from the workers, so **don't use `-march=native` there either**. Build for the workers with `-march=broadwell` (or `-march=x86-64-v3`).

## Hardware age

The hardware dates from 2017. Expect an occasional failed node or GPU, and write jobs that can be restarted (see [Running jobs](jobs.md#long-jobs-and-restarts)).
