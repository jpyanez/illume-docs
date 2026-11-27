# Troubleshooting

!!! tip "First, check #illume-users"
    Outages are posted there automatically. If nothing is posted and something looks wrong, report it there with:
    what you ran, the job ID, the exact error message, and when it happened.

## My job stays idle

```bash
condor_q -better-analyze <job id>
```

Common reasons:

- **Asking for more than any worker has**: e.g. more than ~116 GB of memory, more than 4 GPUs, or more than 16 CPUs.
- **A `requirements` line nobody matches**, such as a misspelled GPU name.
- **All matching workers are busy**: the job will start when one is free. GPU jobs wait longest.

## My job is held

```bash
condor_q -hold <job id>
```

The hold reason says what went wrong. Common ones:

- A file in `transfer_input_files` or the `executable` does not exist, or the `logs/` directory is missing.
- The job used more memory or disk than requested.

Fix the problem, then `condor_release <job id>` (or `condor_rm` it and submit again).

## My job ran twice / started again

The worker it was running on failed, and HTCondor restarted the job elsewhere. See [Long jobs and restarts](jobs.md#long-jobs-and-restarts).

## "Illegal instruction"

The program was built for a newer CPU. See [The machines](machines.md#worker-nodes).

## "no kernel image is available for execution on the device"

Your GPU software no longer supports Pascal GPUs, or was built for CUDA 12. See [Software](software.md#gpu-software-limits).

## "Permission denied (publickey)" when logging in

- Check that you use the right username and key: `ssh -v <username>@login.illume.systems` shows which keys are tried.
- New computer? Send your new public key to the owner ([Getting access](access.md#new-computer-or-lost-key)).

## "No space left on device"

Check `df -h /home /data /data2`. If a shared file system is full, report it in #illume-users. If it is the job's own directory, raise `request_disk`.
