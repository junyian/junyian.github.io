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
