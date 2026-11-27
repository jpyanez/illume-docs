# Storage

!!! danger "Nothing on illume is backed up"
    There are no backups, snapshots or second copies of any file on illume, including your home directory.
    A hardware failure or a mistaken `rm` loses data for good. **Keep a copy of anything important somewhere else.**

## Where to put files

| Path | Use it for | Shared by | Notes |
|---|---|---|---|
| `/home/<username>` | code, configuration, small results | all nodes | no backup |
| `/data`, `/data2` | large datasets and job outputs | all nodes | no backup; organised by group (e.g. `/data/icecube`, `/data/p-one`) |
| `/scratch` on the login node | temporary files while working interactively | login node only | may be cleaned without notice |
| job sandbox on a worker | files a running job reads and writes | that job only | deleted when the job ends |

- `/home`, `/data` and `/data2` are network file systems mounted on the login node and on every worker, at the same paths.
- In `/data` and `/data2`, make your own directory inside your group's directory, e.g. `mkdir /data/p-one/$USER`. Ask the owner if your group has no directory yet.
- Check how much space is left with `df -h /home /data /data2`.

## Many small files are slow

The shared file systems handle a few large files well, and thousands of small files badly, both for you and for everyone else.

- Pack small files into an archive: `tar czf results.tar.gz results/`.
- In jobs, write intermediate files to the job's own directory on the worker and copy back only the final output (see [Running jobs](jobs.md#input-and-output-files)).

## Copying files to and from illume

From your own computer:

```bash
scp results.tar.gz <username>@login.illume.systems:/data/p-one/<username>/      # one file
rsync -av --progress mydir/ <username>@login.illume.systems:mydir/             # a directory, resumable
```

Graphical clients work too: FileZilla, WinSCP (Windows), Cyberduck (macOS). Use SFTP, host `login.illume.systems`, and your SSH key.
