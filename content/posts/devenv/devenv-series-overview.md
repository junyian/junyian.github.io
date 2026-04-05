+++
title = 'Building My DevEnv: Arch, Nvim and Nix'
date = 2026-03-29T00:00:00+08:00
categories = ["Dev Environment"]
tags = ["wsl", "arch", "neovim", "nix", "stow", "dotfiles"]
+++

## Background

I was not a full-time developer. I spent over 20 years in technical sales
and consulting in the mobile telecoms industry. Now, I work at a broadband telco
wearing multiple hats: technical specialist, IT manager, product manager. My scope
includes overseeing the company's IT assets, cybersecurity, and software development
(where I lead a small team of fresh graduates building internal tools). Most of
my dev environment knowledge has been self-taught and pieced together over time.

I mention this because most Arch + Nix content out there is written by and for
Linux power users. This series is written from the perspective of someone who needs
a working environment that works the way I want, and maybe my learnings will help
someone that's going through a similar path.

## The Problem

My primary machine is Windows (with WSL, of course). For cybersecurity work,
I use Kali WSL and FlareVM. For software development, I was using Ubuntu WSL,
with Docker Desktop on Windows. My primary IDE is Neovim in WSL.

I'm also tasked with maintaining legacy projects started by my predecessor, which
are mix of Python, Node, and PHP codebases with varying versions, frameworks,
and server environments.

Ubuntu WSL handled this for a while, but it wasn't pretty. The image was
growing fast, eating nearly 80GB of storage on my 256GB work machine. Some
packages were severely outdated or non-existent, which pushed me to rely on
brew. That added yet another maintenance burden. Meanwhile, Kali WSL sat there using
about 25GB for tools I only touched occasionally. I didn't want to install
random security tools on my Ubuntu setup either.

## Where I Landed

Articles about Arch and NixOS kept popping up in my feeds. I got curious, set up
VMs to try things out, and kept trashing them. Some things worked, some created
new friction.

What I settled on is a setup that suits my situation and workflows. Arch keeps
the base system lean. Nix with flakes handles per-project dependencies in isolation
(so I'm not polluting one global environment with five different Python and NodeJS
versions). GNU Stow syncs my configuration dotfiles across my machines at work
and home. And Neovim with LazyVim gives me a proper editor that lives in the
terminal.

The WSL image is much smaller now, I can tear down and recreate project
environments without worrying about breaking anything else, and I get a
consistent setup wherever I sit down to work.

## What This Series Covers

This series walks through how I built this environment from scratch. Each part
covers a distinct layer of the stack, and I've tried to document the actual
process.

- **Part 1 — WSL + Arch**: Getting Arch Linux running on WSL2
- **Part 2 — Neovim + LazyVim**: Setting up Neovim as a full IDE with LazyVim
  and Lazygit
- **Part 3 — Nix + Flakes**: Reproducible, per-project dev shells with Nix
  (without letting it take over your whole system)
- **Part 4 — Stow + Dotfiles**: Managing and syncing dotfiles across machines
  with GNU Stow
