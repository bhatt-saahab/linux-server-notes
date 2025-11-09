---

Below is a properly formatted, comprehensive note for **Debian**, covering all provided data about Debian 13 "Trixie" installation, system setup, networking, SSH configuration, and Zsh installation. Each command is separated with a copy-and-run format, ensuring no details are missed. The note is structured for clarity, with consistent formatting, and serves as a complete reference for setting up and configuring a Debian system.

---

# Debian: Comprehensive Setup Guide

## Overview

**Debian** is a free and open-source Linux distribution developed by the Debian Project, founded by Ian Murdock in 1993. It is one of the oldest and most influential Linux distributions, serving as the foundation for derivatives like Ubuntu, Kali Linux, and Raspbian. Known for its commitment to free software principles, Debian offers extensive software repositories (over 51,000 packages), stability, and security, making it ideal for personal desktops, enterprise servers, cloud platforms, Docker containers, and virtualization hosts.

- **Latest Stable Release**: Debian 13 "Trixie" (August 2025)
- **Key Features**:
    - Community-driven development
    - Long-term support and reliability
    - Flexible and robust for various use cases

This guide provides detailed steps for installing Debian 13 "Trixie," configuring networking, enabling SSH, and installing essential utilities like Zsh.

---

## Debian 13 "Trixie" Installation

This section outlines the steps to perform a clean installation of Debian 13 "Trixie" for desktop or server use.

### Prerequisites

- **USB Drive**: Minimum 8 GB
- **Disk Space**: Minimum 25 GB for the system
- **Internet Connection**: Recommended for netinst ISO
- **ISO Image**: Download from [Debian Official Downloads](https://www.debian.org/distrib/)
- **Official Release Notes**: [Debian 13 Release Notes](https://www.debian.org/releases/trixie/)

### Step 1: Download the Debian ISO

- **Netinst ISO**: Small image, downloads packages during installation (requires internet).
- **Full DVD ISO**: Includes all packages for offline installation.

### Step 2: Create Bootable USB

- **Windows**: Use [Rufus](https://rufus.ie/)
- **Linux/macOS/Windows**: Use [Etcher](https://etcher.balena.io/)
- **Linux (CLI)**:*Replace `/dev/sdX` with your USB device identifier (e.g., `/dev/sdb`).*
    
    ```bash
    dd if=debian-13.iso of=/dev/sdX bs=4M status=progress
    
    ```
    

### Step 3: Boot from USB

1. Insert the USB drive.
2. Enter BIOS/UEFI boot menu (usually F12, F2, or DEL).
3. Select USB as the boot device.
4. Choose **Graphical Install** (recommended) or **Install**.

### Step 4: Select Language, Location, and Keyboard

- Choose your preferred language.
- Select your country or region.
- Choose the correct keyboard layout.

### Step 5: Configure Network

- **DHCP**: Automatic IP assignment (default).
- **Manual**: Set static IP, gateway, and DNS if needed.

### Step 6: Set Hostname and Domain

- **Hostname**: Name your machine (e.g., `debian-pc`).
- **Domain**: Optional for home setups; required for servers in networks.

### Step 7: Create User Accounts

- **Root Password**: Set a root password or leave blank to use `sudo`.
- **Regular User**: Create a personal account for daily use.
- **Recommendation**: Use strong passwords for all accounts.

### Step 8: Partition the Disk

- **Guided Partitioning**: Uses entire disk, recommended for beginners.
- **Manual Partitioning**: For advanced setups (e.g., LVM, encryption, RAID).
- **Suggested Layout**:
    - `/` (root): 20–25 GB
    - `swap`: Equal to RAM (optional for modern systems)
    - `/home`: Remaining space
- **Note**: Double-check partitioning before formatting, as it is critical.

### Step 9: Install Base System

- Installs Linux kernel, core utilities, and essential packages.
- If using netinst ISO, missing packages are downloaded automatically (requires internet).

### Step 10: Select Software

- **Desktop Environment**: Choose from GNOME (default), KDE, XFCE, LXQt, Cinnamon, or MATE.
- **Optional Packages**:
    - SSH server
    - Print server
    - Standard system utilities
- Additional software can be installed later using `apt`.

### Step 11: Install GRUB Bootloader

- Install GRUB on the primary disk (usually `/dev/sda`).
- Required for system boot.
- For UEFI systems, ensure an EFI partition exists or is auto-created.

### Step 12: Finish Installation & Reboot

1. Remove the USB installation media.
2. Reboot the system.
3. Log in with the user account created.

---

## Debian System Setup

This section covers post-installation setup, including networking, SSH, APT sources, and Zsh configuration.

### Check System Architecture

Verify the system architecture (e.g., amd64, arm64).

```bash
dpkg --print-architecture

```

### Update and Upgrade the System

Ensure the system is up-to-date.

```bash
apt update

```

```bash
apt upgrade -y

```

### Install Basic Tools

Install essential utilities for system management.

```bash
apt install bash vim net-tools -y

```

### Install SSH Server and Client

Enable remote access via SSH.

```bash
apt install openssh-server openssh-client ssh -y

```

### Edit SSH Configuration (Optional)

Modify SSH settings, e.g., to allow root login.

```bash
vim /etc/ssh/sshd_config

```

**Example to allow root login**:

```
PermitRootLogin yes

```

### Enable and Start SSH

Enable SSH to start on boot and start the service.

```bash
systemctl enable ssh

```

```bash
systemctl start ssh

```

```bash
systemctl restart ssh

```

### Configure Network Interfaces

Edit the network interfaces file for static or dynamic IP configuration.

```bash
vim /etc/network/interfaces

```

**Example Configuration**:

```
# Loopback interface
auto lo
iface lo inet loopback

# Primary interface (enp0s3)
allow-hotplug enp0s3
iface enp0s3 inet static
    address 192.168.1.25
    netmask 255.255.255.0
    network 192.168.1.0
    broadcast 192.168.1.255
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8
iface enp0s3 inet6 auto

# Secondary interface (enp0s8)
allow-hotplug enp0s8
iface enp0s8 inet static
    address 192.168.2.10
    netmask 255.255.255.0
    network 192.168.2.0
    broadcast 192.168.2.255
    dns-nameservers 8.8.8.8
iface enp0s8 inet6 auto

```

### Configure DNS Resolver

Set DNS servers for name resolution.

```bash
vim /etc/resolv.conf

```

**Example**:

```
nameserver 8.8.8.8

```

### Restart Network Services

Apply network configuration changes.

```bash
systemctl restart networking

```

```bash
ifdown enp0s3 && ifup enp0s3

```

```bash
ifdown enp0s8 && ifup enp0s8

```

### Reboot the System

Ensure all changes take effect.

```bash
reboot

```

### SSH from a Different User

Connect to the system as a non-root user.

```bash
ssh sachin@192.168.1.21

```

### Switch to Root

Switch to the root user if needed.

```bash
su root

```

### Configure APT Sources

Edit the APT sources list to specify package repositories.

```bash
vim /etc/apt/sources.list

```

**Example for Debian 11 (Bullseye)**:

```
deb <http://deb.debian.org/debian/> bullseye main
# deb-src <http://deb.debian.org/debian/> bullseye-updates main

```

**Example for Debian 12 (Bookworm)**:

```
deb <https://deb.debian.org/debian/> bookworm main non-free-firmware
deb-src <http://deb.debian.org/debian/> bookworm main non-free-firmware

```

**Note for Debian 13 (Trixie)**: Update the sources list to use `trixie` instead of `bullseye` or `bookworm`.

```bash
deb <https://deb.debian.org/debian/> trixie main non-free-firmware
deb-src <https://deb.debian.org/debian/> trixie main non-free-firmware

```

### Install Useful Utilities

Install commonly used tools and fonts.

```bash
apt install vim fonts-lato fonts-open-sans fonts-roboto fonts-mononoki fonts-indic zsh net-tools curl wget unzip -y

```

### Reconnect via SSH

Test SSH connectivity after configuration.

```bash
ssh root@192.168.1.21

```

### Install Zsh and Plugins

Install and configure Zsh as an alternative shell for enhanced usability.

1. **Check Current Shell**
    
    ```bash
    echo $SHELL
    
    ```
    
2. **View Available Shells**
    
    ```bash
    cat /etc/shells
    
    ```
    
3. **Install Zsh and Plugins**
    
    ```bash
    apt install zsh zsh-autosuggestions zsh-syntax-highlighting -y
    
    ```
    
4. **Check Zsh Version and Location**
    
    ```bash
    zsh --version
    
    ```
    
    ```bash
    which zsh
    
    ```
    
5. **Set Zsh as Default Shell**
    
    Set Zsh for the current user.
    
    ```bash
    chsh -s $(which zsh)
    
    ```
    
6. **Verify Zsh Configuration**
    
    Check if Zsh is set as the default shell.
    
    ```bash
    grep zsh /etc/passwd
    
    ```
    
7. **Configure Zsh**
    
    Edit the Zsh configuration file for aliases, themes, or plugins.
    
    ```bash
    nano ~/.zshrc
    
    ```
    

---

## Notes on Usage

- **Root Privileges**: Some commands (e.g., `apt`, `systemctl`, network configuration) require root privileges. Use `sudo` or switch to the root user with `su root`.
- **Network Configuration**: Ensure correct interface names (e.g., `enp0s3`, `enp0s8`) using `ip link` before editing `/etc/network/interfaces`.
- **APT Sources**: Always verify the correct Debian release (e.g., `trixie` for Debian 13) in `/etc/apt/sources.list` to avoid package conflicts.
- **SSH Security**: Avoid enabling `PermitRootLogin` in production environments unless necessary; use a regular user with `sudo` instead.
- **Zsh Customization**: Add plugins like `zsh-autosuggestions` and `zsh-syntax-highlighting` to `~/.zshrc` for a better shell experience.

---

# ALL COMMAND

---

# Debian: Zsh and grc Setup Guide

## Overview

This guide extends the Debian system setup by detailing how to apply Zsh configuration changes, set Zsh as the default shell for another user, and install **grc (Generic Colouriser)** to enhance command output readability. These steps are designed for Debian 13 "Trixie" but are generally applicable to other Debian versions.

---

## Zsh Configuration

### Apply Zsh Configuration Changes

Reload the Zsh configuration file to apply changes made to `~/.zshrc`.

```bash
source ~/.zshrc

```

### Switch User and Set Zsh for Another User

Switch to another user (e.g., `armour`) and configure Zsh as their default shell.

1. **Switch to the `armour` user**
    
    ```bash
    su - armour
    
    ```
    
2. **Set Zsh as the default shell for `armour`**
    
    ```bash
    chsh -s $(which zsh)
    ```
    
3. **Edit the Zsh configuration for `armour`**
    
    Modify the user's Zsh configuration file to add aliases, themes, or plugins.
    
    ```jsx
    nano /home/armour/.zshrc
    
    ```
    
    ```bash
    #pate this in above file location 
    nano /home/armour/.zshrc
    # ~/.zshrc - Clean Zsh Configuration
    
    ### Zsh Options ###
    setopt autocd                      # Change directory by typing its name
    setopt interactive_comments        # Allow comments in interactive mode (corrected naming)
    setopt magic_equal_subst          # Enable filename expansion for arguments with = (corrected naming)
    setopt no_nomatch                 # Don't show error if pattern doesn't match (corrected naming)
    setopt notify                     # Report status of background jobs immediately
    setopt numeric_glob_sort          # Sort filenames numerically (corrected naming)
    setopt prompt_subst               # Enable command substitution in prompt
    WORDCHARS=${WORDCHARS//[\/]}      # Remove / from WORDCHARS for better word navigation
    
    # Hide end-of-line sign in prompt
    PROMPT_EOL_MARK=""
    
    ### Key Bindings ###
    bindkey -e                         # Use Emacs-style key bindings
    bindkey ' ' magic-space            # History expansion on space
    bindkey '^U' backward-kill-line    # Ctrl+U: delete line
    bindkey '^[[3;5~' kill-word        # Ctrl+Delete: delete word
    bindkey '^[[3~' delete-char        # Delete key
    bindkey '^[[1;5C' forward-word     # Ctrl+Right: move forward word
    bindkey '^[[1;5D' backward-word    # Ctrl+Left: move backward word
    bindkey '^[[5~' beginning-of-buffer-or-history  # Page Up
    bindkey '^[[6~' end-of-buffer-or-history        # Page Down
    bindkey '^[[H' beginning-of-line   # Home
    bindkey '^[[F' end-of-line         # End
    bindkey '^[[Z' undo                # Shift+Tab: undo
    zle -N toggle_oneline_prompt
    bindkey '^P' toggle_oneline_prompt # Ctrl+P: toggle prompt layout
    
    ### Completion System ###
    autoload -Uz compinit
    zstyle ':completion:*' cache-path ~/.cache/zcompdump # Use cache to speed up compinit
    compinit -d ~/.cache/zcompdump
    
    zstyle ':completion:*' menu select
    zstyle ':completion:*' auto-description 'specify: %d'
    zstyle ':completion:*' format 'Completing %d'
    zstyle ':completion:*' group-name ''
    zstyle ':completion:*' list-colors "${(s.:.)LS_COLORS}"
    zstyle ':completion:*' list-prompt '%SAt %p: Hit TAB for more, or the character to insert%s'
    zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'
    zstyle ':completion:*' rehash true
    zstyle ':completion:*' use-compctl false
    zstyle ':completion:*' verbose true
    zstyle ':completion:*:kill:*' command 'ps -u $USER -o pid,%cpu,tty,cputime,cmd'
    
    ### History ###
    HISTFILE=~/.zsh_history
    HISTSIZE=10000                     # Increased for better history retention
    SAVEHIST=20000                     # Increased for better history retention
    setopt append_history             # Append to history file (safer than share_history)
    setopt hist_expire_dups_first     # Expire duplicates first
    setopt hist_ignore_dups           # Ignore consecutive duplicates
    setopt hist_ignore_space          # Ignore commands starting with space
    setopt hist_verify                # Verify history expansion
    alias history="history 0"
    
    ### Timing Command Execution ###
    TIMEFMT=$'\nreal\t%E\nuser\t%U\nsys\t%S\ncpu\t%P'
    
    ### Directory Colors and Aliases ###
    if command -v dircolors >/dev/null 2>&1; then
        eval "$(dircolors -b ~/.dircolors 2>/dev/null || dircolors -b)"
        export LS_COLORS="$LS_COLORS:ow=30;44:"  # Highlight folders with 777 perms
        alias ls='ls --color=auto'
        alias dir='dir --color=auto'
        alias vdir='vdir --color=auto'
        alias grep='grep --color=auto'
        alias fgrep='fgrep --color=auto'
        alias egrep='egrep --color=auto'
        alias diff='diff --color=auto'
        alias ip='ip --color=auto'
    fi
    
    # Additional ls aliases
    alias ll='ls -l'
    alias la='ls -A'
    alias l='ls -CF'
    
    ### Colored `less` Output ###
    export LESS_TERMCAP_mb=$'\E[1;31m'  # Begin bold (red)
    export LESS_TERMCAP_md=$'\E[1;36m'  # Begin bold (cyan)
    export LESS_TERMCAP_me=$'\E[0m'     # End bold
    export LESS_TERMCAP_so=$'\E[01;33m' # Begin standout (yellow)
    export LESS_TERMCAP_se=$'\E[0m'     # End standout
    export LESS_TERMCAP_us=$'\E[1;32m'  # Begin underline (green)
    export LESS_TERMCAP_ue=$'\E[0m'     # End underline
    
    ### Syntax Highlighting ###
    if [ -f /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]; then
        source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
    fi
    
    # Example styles for syntax highlighting
    ZSH_HIGHLIGHT_HIGHLIGHTERS=(main brackets pattern)
    ZSH_HIGHLIGHT_STYLES[comment]='fg=black,bold'
    ZSH_HIGHLIGHT_STYLES[command]='fg=green'
    ZSH_HIGHLIGHT_STYLES[alias]='fg=cyan'
    ZSH_HIGHLIGHT_STYLES[globbing]='fg=blue,bold'
    ZSH_HIGHLIGHT_STYLES[single-quoted-argument]='fg=yellow'
    ZSH_HIGHLIGHT_STYLES[double-quoted-argument]='fg=yellow'
    ZSH_HIGHLIGHT_STYLES[back-quoted-argument]='fg=magenta,bold'
    ZSH_HIGHLIGHT_STYLES[redirection]='fg=blue,bold'
    ZSH_HIGHLIGHT_STYLES[path]='bold'
    ZSH_HIGHLIGHT_STYLES[history-expansion]='fg=blue,bold'
    
    ### Autosuggestions ###
    if [ -f /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh ]; then
        source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
        ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=#999'
    fi
    
    ### Command Not Found ###
    if [ -f /etc/zsh_command_not_found ]; then
        source /etc/zsh_command_not_found
    fi
    
    ### Terminal Title (for xterm, gnome-terminal, etc.) ###
    case "$TERM" in
    xterm*|rxvt*|Eterm|aterm|kterm|gnome*|alacritty)
        TERM_TITLE=$'\e]0;${debian_chroot:+($debian_chroot)}${VIRTUAL_ENV:+($(basename $VIRTUAL_ENV))} %n@%m: %~\a'
        ;;
    esac
    
    precmd() {
        print -Pn "$TERM_TITLE"
        if [[ "$NEWLINE_BEFORE_PROMPT" == "yes" ]]; then
            if [[ -z "$_NEW_LINE_BEFORE_PROMPT" ]]; then
                _NEW_LINE_BEFORE_PROMPT=1
            else
                print
            fi
        fi
    }
    
    ### Prompt Config ###
    PROMPT_ALTERNATIVE="twoline"
    NEWLINE_BEFORE_PROMPT="yes"
    
    configure_prompt() {
        case "$PROMPT_ALTERNATIVE" in
            twoline)
                PROMPT='%F{blue}%n@%m%f %F{green}%~%f
    %B%F{red}$ %f%b'
                RPROMPT='%F{yellow}[%?]%f'
                ;;
            oneline)
                PROMPT='%F{blue}%n@%m%f:%F{green}%~%f %# '
                RPROMPT='%F{yellow}[%?]%f'
                ;;
            *)
                PROMPT='%n@%m:%~ %# '
                ;;
        esac
    }
    
    toggle_oneline_prompt() {
        if [[ "$PROMPT_ALTERNATIVE" == "oneline" ]]; then
            PROMPT_ALTERNATIVE="twoline"
        else
            PROMPT_ALTERNATIVE="oneline"
        fi
        configure_prompt
        zle reset-prompt
    }
    
    configure_prompt
    
    ### End of ~/.zshrc ###
    ```
    

---

command to reload Zsh is:

```bash
source ~/.zshrc

```

or

```bash
. ~/.zshrc

```

## Install grc (Generic Colouriser) from Source

**grc** is a tool that colorizes command output to improve readability. The following steps download and install **grc v1.13** from its GitHub repository.

### Step 1: Download the grc v1.13 Archive

Download the source tarball from GitHub.

```bash
wget https://github.com/garabik/grc/archive/refs/tags/v1.13.tar.gz

```

### Step 2: Extract the Tarball

Extract the downloaded archive and move it to a standard directory.

```bash
tar xvf v1.13.tar.gz

```

```bash
mv -v grc-1.13 /opt/grc-1.13

```

### Step 3: Navigate to the Extracted Directory

Change to the grc source directory.

```bash
cd /opt/grc-1.13

```

### Step 4: Run the Installation Script

Execute the provided installation script to install grc.

```bash
./install.sh

```

### Step 5: Test grc Functionality

Test grc by prefixing a command to colorize its output.

```bash
grc ping 8.8.8.8

```

### Step 6: Compare with Original Command

Run the same command without grc to observe the difference in output.

```bash
ping 8.8.8.8

```

**Example Usage**:

```bash
grc ping google.com

```

---

## Notes on Usage

- **Zsh Configuration**: After editing `~/.zshrc`, always run `source ~/.zshrc` to apply changes without restarting the shell.
- **User Permissions**: Ensure the `armour` user exists before switching (`adduser armour` if needed). Root privileges are required for `chsh` and installing grc.
- **grc Installation**: The installation script may require root privileges (`sudo ./install.sh`). Ensure `/opt/grc-1.13` is accessible and writable.
- **grc Functionality**: grc enhances readability for commands like `ping`, `ls`, `ps`, etc., by adding colorized output. Configuration files in `/etc/grc.d/` or `~/.grc/` can customize which commands are colorized.
- **Verification**: After setting Zsh for a user, verify with `grep zsh /etc/passwd` to confirm the shell change.

---

If you need additional details, such as specific Zsh plugins, advanced grc configurations, or troubleshooting steps, please let me know! Would you like to include sample outputs for grc or further Zsh customization examples?
