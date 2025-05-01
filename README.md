

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
