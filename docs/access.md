# Getting access

## Requesting an account

Email the cluster owner, Juan Pablo Yáñez ([j.p.yanez@ualberta.ca](mailto:j.p.yanez@ualberta.ca)), with:

- your name and institutional email address;
- your supervisor or research group;
- your position (student, postdoc, staff, ...) and how long you expect to stay in the program;
- the username you would like;
- your **public** SSH key (see below).

## SSH keys

illume accepts SSH keys only, no passwords.

=== "Linux / macOS"

    ```bash
    ssh-keygen -t ed25519 -C "your.email@ualberta.ca"
    ```

=== "Windows"

    In PowerShell (OpenSSH is built into Windows 10 and 11):

    ```powershell
    ssh-keygen -t ed25519 -C "your.email@ualberta.ca"
    ```

- Choose a **passphrase** when asked.
- This creates two files: `id_ed25519` (private) and `id_ed25519.pub` (public).
- Send only the **`.pub`** file. **Never share the private key**, not even with the admins.

## Logging in

```bash
ssh <username>@login.illume.systems
```

To type less, add this to `~/.ssh/config` on your computer:

```
Host illume
    HostName login.illume.systems
    User <username>
    IdentityFile ~/.ssh/id_ed25519
```

Then `ssh illume` is enough. Editors such as VS Code (Remote-SSH extension) use the same entry.

## New computer or lost key

Make a new key and send the new `.pub` file to the owner. Tell them if the old key may have been lost or copied, so it can be removed.

## Leaving the group

Your account and your data are **kept for 6 months after you leave, then deleted completely**. Before you leave:

1. Delete what is no longer needed.
2. Copy what you want to keep to your own storage.
3. Tell the owner which data may still be useful to the group, or needs to be archived long-term.
4. Hand over shared data to your supervisor or group.
