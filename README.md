

# NixOS-home

Managing dotfiles with Nix

Also, after installing Nix be sure to create file $HOME/.config/nix/nix.conf with the following content to be comfortable while using Nix’s CLI:

experimental-features = nix-command flakes
Managing dotfiles with Nix


configuration that looks like this:

$ tree $HOME/.dotfiles -a -I .git
/home/seroperson/.dotfiles
├── .config/     # My actual dotfiles (zsh, nvim, git etc confs)
├── flake.lock
├── flake.nix
└── home.nix
In this example, we’re storing all our dotfiles in the $HOME/.dotfiles/.config


----------

Generate an initial configuration:

nixos-generate-config --root /mnt/nixos
# /etc/nixos/configuration.nix
# /etc/nixos/hardware-configuration.nix


nixos-rebuild switch


man configuration.nix


wing as root:

nix-channel --list
# nixos https://nixos.org/channels/nixos-19.03
If you want to live on the bleeding edge:

nix-channel --add https://nixos.org/channels/nixos-unstable nixos
To upgrade NixOS:

nixos-rebuild switch --upgrade
The command is equivalent to the more verbose:

nix-channel --update
nixos-rebuild switch
7. Cleaning the Nix Store
nix-collect-garbage [--delete-old -d]
