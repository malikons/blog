# Installing the Nix Package Manager on Ubuntu 24.04

This guide will walk you through the steps to install the Nix Package Manager on your Ubuntu 24.04 system. The Nix Package Manager is a powerful tool that allows for reproducible, declarative package management.

## Prerequisites

* A running Ubuntu 24.04 system.
* `curl` installed on your system. If not, you can install it with:
    ```bash
    sudo apt update
    sudo apt install curl
    ```

## Installation Steps

1.  **Download the Nix installation script:**
    Open your terminal and run the following command:
    ```bash
    curl -L [https://nixos.org/nix/install](https://nixos.org/nix/install) | sh
    ```
    This command downloads the official Nix installation script and immediately executes it.

2.  **Follow the prompts:**
    The installation script will likely ask for confirmation before proceeding. Read the prompts carefully and typically press Enter to accept the defaults. The script will set up the Nix store and modify your shell configuration files.

3.  **Source your shell configuration:**
    After the installation script completes, you'll need to source your shell's configuration file to activate the Nix environment in your current terminal session. The script will usually tell you which file to source. For Bash, it's typically:
    ```bash
    . ~/.bashrc
    ```
    or
    ```bash
    source ~/.bashrc
    ```
    If you are using Zsh, it might be:
    ```bash
    . ~/.zshrc
    ```
    or
    ```bash
    source ~/.zshrc
    ```
    Close and reopen your terminal if sourcing doesn't seem to take effect.

4.  **Verify the installation:**
    To confirm that Nix is installed correctly, run the following command:
    ```bash
    nix-shell -p nix-info --run "nix-info -m"
    ```
    This should output information about your Nix installation. Alternatively, you can simply run:
    ```bash
    nix --version
    ```
    which should display the installed Nix version.

## Post-Installation Notes

* Nix installs packages into the `/nix` directory. This is intentional and allows for parallel installations and rollbacks.
* Your user environment will have access to Nix packages.
* You can now start using Nix to install software. For example, to install the `hello` package, you can run:
    ```bash
    nix-env -iA nixpkgs.hello
    ```
    and then run `hello` in your terminal.

Congratulations! You have successfully installed the Nix Package Manager on your Ubuntu 24.04 system. You can now explore the vast collection of packages available through Nixpkgs.
