+++
title = 'Building My DevEnv Part 1: WSL + Arch'
date = 2026-04-06T00:00:00+08:00
categories = ["Dev Environment"]
series = ["Dev Environment"]
tags = ["wsl", "arch", "linux", "windows", "cli"]
+++

_This is Part 1 of the "Dev Environment" series, where I document how I
rebuilt my dev environment from scratch using Arch Linux, Neovim, Nix, and GNU
Stow. The [series overview](/posts/devenv/devenv-series-overview) covers the full picture._

---

## Why Not Just Stick with Ubuntu?

Ubuntu is the default WSL distro for good reason. It works out of the box,
Microsoft officially supports it, and most tutorials assume you're running it.

But Ubuntu comes with a lot of bloat that I do not need. My Ubuntu WSL image had
grown to nearly 80GB on a 256GB work machine. Some of that was Docker layers and
project dependencies. But a lot of it was accumulated junk, i.e. outdated packages,
things installed via `apt`, `brew` (because `apt` didn't have recent
enough versions), and even `snap`.

Arch on WSL starts with basically nothing. No snap daemon, no background
services, no pre-installed applications. You get `pacman` and a shell. Everything
else is a deliberate choice. My current Arch WSL image sits at about 7GB with
a full development setup (Neovim, language servers, build tools, the works).

The rolling release model also means I don't deal with distro version upgrades.
`pacman -Syu` keeps everything current. And the AUR (Arch User Repository) has
pretty much every package you'd ever need, so I haven't touched Homebrew since
the switch.

## Getting Arch on WSL

The Arch Wiki has a dedicated page for this:
[Install Arch Linux on WSL](https://wiki.archlinux.org/title/Install_Arch_Linux_on_WSL).
I'll summarize the key steps here, but check the wiki for the latest details.

Download the latest Arch Linux `.wsl` image from an Arch mirror. It'll be named
something like `archlinux-<version>.wsl`.

Then from PowerShell:

```powershell
# Create a directory for the Arch virtual disk
mkdir C:\wsl\arch

# Import into WSL
wsl --import archlinux C:\wsl\arch C:\path\to\archlinux-<version>.wsl
```

Launch it with `wsl -d archlinux` and you'll be dropped into a root shell.

## System updates

Before doing anything, let's update the system first. This is via a simple command.

```bash
pacman -Syu
```

Memorize this. This is the single, most useful command to update the entire system.
I run this command about once or twice a week, depending on how badly I needed an
update for a specific package.

## The bare login

Remember, Arch logs you in as root. And you do not want to stay as root. So the
first step is to add a new user. Also, the base image does not come with sudo. That
should be installed here too.

### Creating your user

```bash
# Install sudo
pacman -S sudo
useradd -m -G wheel -s /bin/bash yourusername
passwd yourusername
# Enable the wheel group in /etc/sudoers
# This line simply uncomments the line in the file
sed -i 's/^# %wheel ALL=(ALL:ALL) ALL/%wheel ALL=(ALL:ALL) ALL/' /etc/sudoers
```

### Setting the Default User

By default, WSL will log you in as root every time. You need to tell it to use
your new user account.

This file controls how your Arch instance integrates with Windows. Create or
edit `/etc/wsl.conf`:

```ini
[boot]
systemd=true

[interop]
enabled = true
appendWindowsPath = true

[user]
default = yourusername
```

A few things worth explaining here:

**`systemd=true`** — This enables systemd as the init system inside WSL. It
used to be that WSL didn't support systemd at all, but Microsoft added support
for it in late 2022. You want this on because several tools expect it (Nix's
daemon, Docker, various services). Without it, you'll run into weird issues
later.

**`interop`** — This is what allows your Linux environment to execute Windows
binaries. That sounds abstract until you need it. When you run `gh auth login`
and it tries to open a browser, it's executing `cmd.exe` through WSL interop.
When you use `win32yank` for Neovim clipboard integration, that's interop too.
If this is disabled, you'll get "Exec format error" when trying to run any
`.exe` from inside WSL.

**`appendWindowsPath`** — Adds your Windows PATH to the Linux PATH. This is
what lets you type `code .` from WSL to open VS Code, or run `explorer.exe .`
to open the current directory in File Explorer.

After editing `wsl.conf`, you need to fully restart WSL from PowerShell:

```powershell
wsl --shutdown
```

Then relaunch. You should now be logged in as your user, not root.

### The Locale Warning

Every fresh Arch install hits this. You'll see warnings like:

```bash
# When installing packages that uses perl
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings
```

Arch doesn't generate any locales by default. To fix it:

```bash
# Uncomment en_US.UTF-8 in locale.gen
sudo sed -i 's/^#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen

# Generate the locale
sudo locale-gen

# Set it as default
echo 'LANG=en_US.UTF-8' | sudo tee /etc/locale.conf
```

If you skip this, things mostly work but you'll get those warnings constantly,
and some tools (Starship, Neovim with certain plugins) might render special
characters incorrectly.

And that's it! The basic barebones of Arch is now ready for use and further
customizations.

## The Modern CLI Toolkit

One of the things I enjoyed about setting up Arch was choosing every tool
deliberately. On Ubuntu, I'd accumulated a mix of `apt` packages, Homebrew
formulae, and manually installed binaries. On Arch, everything comes through
`pacman` or the AUR.

Here's what I installed as my baseline:

```bash
sudo pacman -S git base-devel neovim starship zoxide eza bat fzf ripgrep fd unzip
```

Quick rundown on the less obvious ones:

**`eza`** replaces `ls`. It has better defaults (color output, git integration,
tree view). I alias it in `.bashrc`:

```bash
alias ls='eza'
alias ll='eza -l --git'
alias la='eza -la --git'
alias lt='eza --tree --level=2'
```

**`zoxide`** replaces `cd`. It learns which directories you visit frequently and
lets you jump to them with partial names. After using it for a week you'll
wonder how you ever typed full paths. Add `eval "$(zoxide init bash)"` to your
`.bashrc` and then `z project` takes you to wherever your project directory is.

**`bat`** replaces `cat`. Syntax highlighting, line numbers, git integration.
Useful when you're quickly checking a config file.

**`starship`** is a cross-shell prompt. It shows your current git branch, the
language version for whatever project you're in, and other context-aware info.
Add `eval "$(starship init bash)"` to your `.bashrc`.

**`ripgrep`** (`rg`) and **`fd`** are faster replacements for `grep` and `find`.
They also happen to be what LazyVim (and most modern Neovim setups) use
internally for fuzzy finding and live grep. You'll need them in Part 2.

**`base-devel`** is a package group that includes `gcc`, `make`, and other build
essentials. You need this for compiling AUR packages and for Mason (Neovim's LSP
installer) to build language servers.

## Resource Constraints

On a machine with limited resources (like a Surface Pro), you can cap
how much memory and CPU WSL is allowed to use. Create a `.wslconfig` file in
your Windows user directory (`C:\Users\yourusername\.wslconfig`):

```ini
[wsl2]
memory=4GB
processors=2
```

This is a global setting that applies to all WSL distributions.

## Part 1 Wrap Up

At this point, you have a working Arch Linux environment on WSL2 that:

- Takes up a fraction of what Ubuntu did
- Has a modern CLI toolkit installed through a single package manager
- Integrates with Windows (clipboard, browser, file system)
- Has systemd enabled for tools that need it later

The image is small, the installed tools are there because I chose them, and
nothing is running in the background that I didn't ask for.

Next up is getting Neovim configured as a full development environment with
LazyVim and dealing with the various ways a minimal Arch install makes that
process more interesting than expected.

---

\_Next: Part 2 — Neovim + LazyVim
