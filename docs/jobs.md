# Running jobs

illume uses [HTCondor](https://htcondor.readthedocs.io/en/23.0/users-manual/index.html).
You describe a job in a **submit file**, hand it to HTCondor with `condor_submit`, and HTCondor runs it on a free worker when one matches what you asked for.

## A first job

`myjob.sh`:

```bash
#!/bin/bash
echo "Running on $(hostname) with argument $1"
python3 analyse.py --seed "$1" --out result_$1.txt
```

`myjob.sub`:

```
executable     = myjob.sh
arguments      = 42
transfer_input_files = analyse.py
output         = logs/myjob.$(Cluster).$(Process).out
error          = logs/myjob.$(Cluster).$(Process).err
log            = logs/myjob.$(Cluster).log

request_cpus   = 1
request_memory = 2 GB
request_disk   = 5 GB

queue
```

```bash
mkdir -p logs
chmod +x myjob.sh
condor_submit myjob.sub
```

- `output` / `error`: what your program prints (stdout / stderr).
- `log`: HTCondor's own record of the job (where it ran, when, how much memory it used).
- `request_*`: what your job needs. A job only starts on a worker that has that much free.

## Input and output files

Each job starts in its own empty directory on the worker.

- **File transfer (recommended):** HTCondor copies the executable and the files in `transfer_input_files` into that directory, and copies new files the job creates there back to the directory you submitted from when it ends. This keeps the load on the shared file systems low.
- **Shared file systems:** `/home`, `/data` and `/data2` are mounted on every worker, so a job can also read and write them directly with full paths (e.g. `/data/p-one/$USER/input.root`). Fine for a moderate number of jobs reading large files; avoid thousands of jobs writing small files there at the same time.

## Many jobs

```
arguments = $(Process)
queue 100              # 100 jobs, $(Process) = 0 ... 99
```

or one job per line of a file:

```
arguments = $(infile)
queue infile from inputs.txt
```

## Watching and managing jobs

| Command | What it does |
|---|---|
| `condor_q` | your jobs in the queue |
| `condor_q -better-analyze <job id>` | why a job is not starting |
| `condor_q -hold` | held jobs and the reason |
| `condor_history <job id>` | finished jobs |
| `condor_rm <job id>` | remove a job (`condor_rm $USER` removes all yours) |
| `condor_release <job id>` | release a held job after fixing the problem |
| `condor_status -compact` | the workers and what's free |

## Ask for what you need, not more

Requested CPUs, memory and GPUs are reserved for your job while it runs, even if it doesn't use them.
After a test job, check its real usage at the end of the `log` file (or `condor_history -af MemoryUsage RequestMemory <job id>`), and set your requests a bit above that.
If a job uses more memory than requested, it may be held or killed.

## Long jobs and restarts

Jobs are not stopped to make room for other users, but a job can be restarted from the beginning if a worker fails.

- Split long work into shorter jobs (a few hours each is ideal).
- Write partial results or checkpoints, and let the job continue from them.
- `max_retries = 3` in the submit file re-runs a job automatically if it exits with an error.

## Interactive jobs

To get a shell on a worker (for testing, or debugging on a GPU):

```bash
condor_submit -interactive request_gpus=1 request_memory=4GB
```

Exit the shell to end the job. Don't leave interactive jobs idle: they hold the resources.

<!-- TODO (admin): test condor_submit -interactive on illume before publishing -->
