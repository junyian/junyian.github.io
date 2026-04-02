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
