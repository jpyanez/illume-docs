# illume user guide

illume is a batch computing cluster at the University of Alberta for physics research.
It runs on the Cirrus research cloud and uses the **HTCondor** scheduler.
It is built for many independent jobs: simulations, fits, data processing, and machine-learning training on one or a few GPUs.

!!! tip "Slack channel #illume-users: the main way to reach us"
    - **Outages and recoveries** are posted there automatically. If something seems broken, look there first.
    - **Announcements** (maintenance, changes) are posted there.
    - **Report problems and ask questions** there. Other users may already know the answer.

    To join, send a private Slack message to **juanpablo** on the IceCube or P-ONE Slack. More in [Policies and help](policies.md#contact).

## Quick start

Once you have an account ([Getting access](access.md)):

```bash
ssh <username>@login.illume.systems     # 1. log in
cat > hello.sub <<'END'                  # 2. write a submit file
executable     = /bin/hostname
output         = hello.out
error          = hello.err
log            = hello.log
request_cpus   = 1
request_memory = 1 GB
queue
END
condor_submit hello.sub                  # 3. submit it
condor_q                                 # 4. watch it (it may sit "idle" for a minute)
cat hello.out                            # 5. the name of the machine that ran it
```

Then read [Running jobs](jobs.md) and, for GPUs, [GPU jobs](gpu-jobs.md).

## Two things to know from day one

- **Nothing on illume is backed up.** Keep a copy of anything important elsewhere. See [Storage](storage.md).
- **The login node is shared.** Edit, compile and submit there; run the real work as jobs.
