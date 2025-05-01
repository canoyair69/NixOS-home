
# NixOS-home: Managing Dotfiles with Nix

This document outlines how to manage your dotfiles using Nix and Home Manager.

> Install XCode CLI tools
```
```
xcode-select --install

Install Nix (a reboot could be necessary)

```
sh <(curl -L https://nixos.org/nix/install) --darwin-use-unencrypted-nix-store-volume

```

Add home-manager and unstable channels

```

nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
nix-channel --add https://nixos.org/channels/nixpkgs-unstable nixpkgs-unstable
nix-channel --update
export NIX_PATH=$HOME/.nix-defexpr/channels${NIX_PATH:+:}$NIX_PATH

```
Install home-manager
```
nix-shell '<home-manager>' -A install

```

Clone this repo inside ~/.config/nixpkgs (must remove default nixpkgs before cloning)
```

rm -r ~/.config/nixpkgs
git clone https://github.com/biosan/dotfiles ~/.config/nixpkgs
home-manager switch

```


nano flake.nix

```
{
    description = "My system configuration";
    inputs = {
        nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-unstable";
        nix-darwin = {
            url = "github:LnL7/nix-darwin";
            inputs.nixpkgs.follows = "nixpkgs";
        };
        home-manager = {
            url = "github:nix-community/home-manager";
            inputs.nixpkgs.follows = "nixpkgs";
        };
    };
    # ...
```


```
experimental-features = nix-command flakes
```

## Dotfiles Structure

A common and organized approach is to store your dotfiles in a dedicated directory. This example demonstrates a structure where all configuration files are located within `$HOME/.dotfiles/.config`:

```
$ tree $HOME/.dotfiles -a -I .git
/home/seroperson/.dotfiles
├── .config/        # Your actual dotfiles (zsh, nvim, git, etc. configurations)
├── flake.lock
├── flake.nix
└── home.nix
```

## Initial NixOS Configuration (If Applicable)

If you are starting with a new NixOS installation, you can generate an initial configuration using the following command:

```bash
sudo nixos-generate-config --root /mnt/nixos
```

This command will typically create two configuration files in `/etc/nixos/`:

- `/etc/nixos/configuration.nix`: The main NixOS configuration file.
- `/etc/nixos/hardware-configuration.nix`: Hardware-specific configurations.

After making changes to these files, you can apply them using:

```bash
sudo nixos-rebuild switch
```

For detailed information on the NixOS configuration, refer to the manual:

```bash
man configuration.nix
```

## Managing Nix Channels

Nix channels are used to track different versions of Nix packages. You can list your currently subscribed channels using:

```bash
nix-channel --list
# Example output:
# nixos [https://nixos.org/channels/nixos-19.03](https://nixos.org/channels/nixos-19.03)
```

To use the latest, potentially unstable, packages, you can add the `nixos-unstable` channel:

```bash
sudo nix-channel --add [https://nixos.org/channels/nixos-unstable](https://nixos.org/channels/nixos-unstable) nixos
```

To upgrade your NixOS system to the latest versions available in your subscribed channels, run:

```bash
sudo nixos-rebuild switch --upgrade
```

This is equivalent to first updating the channels and then rebuilding:

```bash
sudo nix-channel --update
sudo nixos-rebuild switch
```

## Cleaning the Nix Store

Over time, the Nix store (`/nix/store`) can accumulate old and unused packages. To reclaim disk space, you can run the garbage collector:

```bash
nix-collect-garbage [--delete-old -d]
```

The `--delete-old` or `-d` flags will aggressively remove older generations. Use with caution.

## Setting up Home Manager for Dotfile Management

Home Manager is a tool for managing user environment configurations using Nix.

### Install Xcode Command Line Tools (macOS)

If you are on macOS, you might need to install the Xcode Command Line Tools:

```bash
xcode-select --install
```

A reboot might be necessary after this installation.

### Install Nix (macOS)

If you haven't already installed Nix on macOS, you can do so with the following command:

```bash
sh <(curl -L [https://nixos.org/nix/install](https://nixos.org/nix/install)) --darwin-use-unencrypted-nix-store-volume
```

### Add Home Manager and Unstable Channels

Add the Home Manager and Nixpkgs Unstable channels to your Nix configuration:

```bash
nix-channel --add [https://github.com/nix-community/home-manager/archive/master.tar.gz](https://github.com/nix-community/home-manager/archive/master.tar.gz) home-manager
nix-channel --add [https://nixos.org/channels/nixpkgs-unstable](https://nixos.org/channels/nixpkgs-unstable) nixpkgs-unstable
nix-channel --update
```

Update your `NIX_PATH` environment variable:

```bash
export NIX_PATH=$HOME/.nix-defexpr/channels${NIX_PATH:+:}$NIX_PATH
```

You might want to add this `export` command to your shell's configuration file (e.g., `.zshrc`, `.bashrc`).

### Install Home Manager

Install Home Manager using `nix-shell`:

```bash
nix-shell '<home-manager>' -A install
```

### Clone Dotfiles Repository

Clone your dotfiles repository (in this example, `https://github.com/biosan/dotfiles`) into the `~/.config/nixpkgs` directory. **Important:** If a `nixpkgs` directory already exists, you need to remove it first:

```bash
rm -r ~/.config/nixpkgs
git clone [https://github.com/biosan/dotfiles](https://github.com/biosan/dotfiles) ~/.config/nixpkgs
```

### Apply Home Manager Configuration

Apply the Home Manager configuration to install and configure the programs defined in your `home.nix` (or similar configuration file within your dotfiles repository). **Note:** This process can take a significant amount of time depending on the complexity of your configuration.

```bash
home-manager switch
```

### Install Homebrew (Optional, macOS)

If you still need Homebrew for certain packages not yet available or easily configured with Nix, you can install it using:

```bash
bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
```
```
