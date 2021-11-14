layout: post
title: "Nix as Docker/VM alternative"
date: 2021-11-14


In this post, I'm going to show an alternative to Docker/VMs for creating isolated development environment using Nix.

# 1. What is Nix

Nix could mean one of these three things:
- The Nix package manager (nixpkgs).
- The NixOS operating system, a Linux distribution that uses nixpkgs as its package manager.
- The Nix expression language, which is used for developing nixpkgs and NixOS themselves.

See more at [nixos.org](https://nixos.org/)

In this post, it refers to the package manager (nixpkgs).

# 2. Installing Nix

See [https://nixos.org/download.html#nix-quick-install](https://nixos.org/download.html#nix-quick-install)

# 3. Using Nix (as I do)

I'll be honest, I'm a total newbie at Nix, so everything I wrote here is totally my personal experience. The right way to use Nix may be different.

In this post, we'll use Nix as a virtual environment.

The official guide to this is [here](https://nixos.org/guides/dev-environment.html), but at the time of this post, it only shows how to install Python dependencies. Part of the reason I wrote this is to share my experience in figuring out how to install arbitrary dependencies.

## 3.1 Init script

In your project root, create a file named `default.nix` or `shell.nix`:

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {}
```

Then in your terminal, run:

```shell
nix-shell
```

Nix will:
1. Create a new shell environment.
2. Install some basic dependencies that present in every operating systems (C compiler and build utilities) SEPARATELY in sub directories under `/nix/store`.
3. Create symlinks to these dependencies in this new shell environment.

## 3.2 Adding dependencies

Search the dependencies you need on [Nix's package repository](https://search.nixos.org/packages) (you can go there from the lookup icon on Nix's homepage).

Edit your `pkgs.mkShell` function into, for example:

```nix
pkgs.mkShell {
  buildInputs = [ pkgs.postgresql pkgs.python39Packages.django ];
}
```

You can avoid repeating `pkgs` in their names:

```nix
pkgs.mkShell {
  buildInputs = with pkgs; [ postgresql python39Packages.django ];
}
```

What if you have more than one Python dependencies and you don't want to repeat `python39Packages`? Well, I'm also trying to figure this out ¯\_(ツ)_/¯

Now exit your current Nix shell and run `nix-shell` again. Then check your dependencies, e.g.:

```
which psql
which python
```

They should show up under `/nix/store`, for example `/nix/store/<some-hash>/psql`.
