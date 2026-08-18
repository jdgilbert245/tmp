# VS Code Remote Tunnel on Myriad (via Apptainer + AlmaLinux 9)

Works around Myriad's old kernel/glibc by running the `code` CLI inside a
modern container, then tunnelling out through Microsoft's relay so you can
connect from vscode.dev or the desktop app with no VPN/SSH gateway needed
after the tunnel is up.

## 1\. SSH into Myriad as normal

You still need VPN/gateway access for this one-time setup step:

```bash
ssh <ucl-id>@myriad.rc.ucl.ac.uk
```

## 2\. Start a persistent session

Login node processes get killed on logout unless detached. Use `tmux` (or
`screen`):

```bash
tmux new -s vscode-tunnel
```

## 3\. Pull an AlmaLinux 9 container

```bash
mkdir -p \\\~/apptainer \\\&\\\& cd \\\~/apptainer
apptainer pull almalinux9.sif docker://almalinux:9
```

## 4\. Shell into the container, mounting your home dir

```bash
apptainer shell --bind /home/<ucl-id>:/home/<ucl-id> almalinux9.sif
```

## 5\. Download the VS Code CLI (glibc build, not musl/alpine)

Inside the container:

```bash
mkdir -p \\\~/vscode-cli \\\&\\\& cd \\\~/vscode-cli
curl -Lk 'https://update.code.visualstudio.com/latest/cli-linux-x64/stable' -o vscode\\\_cli.tar.gz
tar -xf vscode\\\_cli.tar.gz
```

This gives you a `code` binary built against a recent glibc, which will run
fine inside the AlmaLinux 9 userspace.

## 6\. Start the tunnel

```bash
./code tunnel --accept-server-license-terms --name myriad
```

* `--name myriad` gives it a stable, memorable identifier.
* You'll get a device-code login prompt: a URL (`https://github.com/login/device`)
and a short code. Open that URL on any device, enter the code, sign in with
GitHub (or Microsoft).
* Once authenticated, it'll say the tunnel is running and named `myriad`.

## 7\. Detach, don't close

```
Ctrl+b, then d
```

This detaches the tmux session but leaves the tunnel running. You can safely
close your SSH connection now — the container process and tunnel keep going
on the login node under tmux.

## 8\. Connect from anywhere, no VPN needed

**Browser:** go to https://vscode.dev, sign in with the same GitHub/Microsoft
account, open the Remote Explorer / Tunnels view, select `myriad`.

**Desktop app:** requires the **Remote - Tunnels** extension
(`ms-vscode.remote-server`) installed first. Once installed: Command Palette
→ "Remote Tunnels: Connect to Tunnel" → sign in with the same account →
select `myriad`. (Not needed for vscode.dev in the browser — tunnel support
is built in there.)

## Etiquette: idle tunnels on login nodes

Login nodes are a shared, limited resource used by everyone on the cluster
for editing, submitting jobs, and light interactive work — they're not meant
to host long-running background processes on your behalf indefinitely.

* **Kill the tunnel when you're done for the day**, rather than leaving it
running for days/weeks "just in case." Reattach with
`tmux attach -t vscode-tunnel`, then `Ctrl+C` the `code tunnel` process,
and `exit` the container shell.
* **The tunnel itself is lightweight** (idle CPU/memory footprint), so the
concern isn't load — it's accumulation. If many users each leave tunnels
running indefinitely, that's a lot of stray long-lived processes and open
connections for RC staff to reason about, and one more thing that can go
stale/orphaned if you change machines, lose your GitHub session, etc.
* **Don't use the login node shell (tunnelled or not) for actual compute.**
The tunnel just gets you an editor session on the login node — real
workloads still belong in SGE jobs (`qsub`), same as always.
* If you know you'll want tunnel access regularly, it's worth checking with
UCL RC whether there's a preferred/supported way to do this rather than
freelancing it via Apptainer — some HPC centres have opinions (or outright
policies) about persistent outbound processes on login nodes.
* **Re-attaching after disconnect:** `tmux attach -t vscode-tunnel` if you
need to check on it or restart it.
* **Tunnel dies if the login node reboots/is drained** — you'll need to
redo steps 4–7 (the container pull in step 3 only needs doing once, since
`almalinux9.sif` persists in `\\\~/apptainer`).
* **Idle timeout:** if unused for a while, `code tunnel` may need restarting;
it's not a systemd service here since containers typically lack systemd,
so tmux is doing the persistence job.

