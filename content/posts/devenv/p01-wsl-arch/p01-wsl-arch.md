+++
title = 'Dev Environment Part 1: WSL + Arch Linux'
date = 2026-03-29T00:00:00+08:00
categories = ["Dev Environment"]
tags = ["wsl", "arch", "linux"]
series = ["Dev Environment"]
+++

## Introduction

Your Ubuntu WSL environment grew to 80GB, and a fresh start makes sense. Arch Linux offers a lean, minimal foundation—you install only what you need. This guide walks you through setting up Arch on WSL from scratch, without assuming Linux expertise.

By the end, you'll have a clean, working Arch WSL environment ready for the rest of this series. You'll understand how to maintain it—keeping it lean, updating safely, and backing it up when needed.

This isn't a deep dive into Linux internals. Think of it as the practical setup phase that clears the way for the tools that actually matter—Neovim, Nix, and your dotfiles.

## Installation Overview

The installation process takes 10–15 minutes and follows a straightforward path: verify your system meets basic requirements, download a minimal Arch filesystem, bootstrap it into WSL, configure a few essentials, and verify everything works. No magic—just clear steps in logical order.

Here's what you'll do:

1. Verify WSL2 is installed and set as default
2. Download the Arch Linux filesystem tarball
3. Import and bootstrap Arch into your WSL environment
4. Configure basic system settings (pacman, locale, hostname)
5. Verify Arch is working and ready for the next steps

If you get stuck, the troubleshooting section at the end covers common hiccups.

## Installation Details

### Prerequisites and Setup

Before starting the Arch installation, verify your WSL2 setup is correct. This takes just a few minutes and ensures everything is ready.

**Step 1: Check your WSL version**

First, verify that WSL2 is installed on your system:

```
wsl --version
```

Expected output (exact format may vary slightly):
```
WSL version: 2.x.x
Kernel version: 6.x.x.x
```

If you see an error like "wsl: command not found", WSL isn't installed yet. Visit the [Windows Subsystem for Linux documentation](https://learn.microsoft.com/en-us/windows/wsl/install) for installation instructions specific to your Windows version.

**Step 2: Check your WSL default version**

Next, list your installed distributions and verify WSL2 is set as the default:

```
wsl -l -v
```

Expected output (example with Ubuntu):
```
  NAME                   STATE           VERSION
* Ubuntu                 Running         2
  docker-desktop         Stopped         2
```

The `VERSION` column shows which WSL version each distribution uses. The `*` marks your default distribution. All versions should show `2`—if any show `1`, you'll need to convert them in the next step.

**Step 3: Set WSL2 as default (if needed)**

If the previous command showed `VERSION 1` for any distribution, set WSL2 as the default:

```
wsl --set-default-version 2
```

Expected output:
```
For information on key differences with WSL 2 please visit https://aka.ms/wsl2
The operation completed successfully.
```

This only sets the default for *future* distributions; since Arch will be a fresh WSL2 installation, no additional conversion steps are needed.

**You're ready to proceed.** Once these checks pass, you have everything needed to download and install Arch. The next section covers downloading the Arch filesystem.

### Downloading the Arch Filesystem

Now that your WSL2 environment is verified and ready, the next step is downloading the minimal Arch Linux filesystem. This is a small tarball—usually 160–180 MB—that contains just the essentials. You'll extract it into WSL in the next section.

**Step 1: Navigate to the Arch bootstrap images**

Open your web browser and navigate to the Arch bootstrap images mirror:

[https://mirror.archlinux.org/iso/latest/](https://mirror.archlinux.org/iso/latest/)

You'll see a list of filesystems. Look for the file named `archlinux-bootstrap-YYYY.MM.DD-x86_64.tar.zst` (version dates change monthly; download the latest).

**Step 2: Download the correct tarball**

The file you want is the **x86_64 tarball**—this is the standard for modern Windows systems (both 32-bit and 64-bit Windows run x86_64 applications). Do not download the `aarch64` version unless you're on ARM-based Windows (rare).

Right-click the link and select **"Save link as..."**, or click to download directly. Save the file to an easy-to-find location:

```
C:\Users\<YourUsername>\Downloads\
```

The download will take 1–2 minutes on a typical internet connection.

**Step 3: Verify the download (recommended)**

Verifying your download ensures the file wasn't corrupted during transfer. This step is optional but good practice.

On the Arch bootstrap images page, you'll find SHA256 checksums listed next to each file. The checksum looks like:

```
a1b2c3d4e5f6... (a very long string of letters and numbers)
```

To verify on Windows, open PowerShell and run:

```powershell
Get-FileHash C:\Users\<YourUsername>\Downloads\archlinux-bootstrap-YYYY.MM.DD-x86_64.tar.zst
```

The output will show something like:

```
Algorithm       : SHA256
Hash            : A1B2C3D4E5F6... (a long string)
Path            : C:\Users\YourUsername\Downloads\archlinux-bootstrap-YYYY.MM.DD-x86_64.tar.zst
```

Compare the **Hash** value with the checksum listed on the Arch download page. If they match exactly, your file is good.

**You're ready to proceed.** Once your download is complete (and verified, if you chose to do so), you have the Arch filesystem ready to import into WSL. The next section covers bootstrapping it into your WSL environment.
