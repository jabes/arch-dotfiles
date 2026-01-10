## Install System Packages

```bash
# arch-audit              | security vulnerability scanner
# base                    | essential arch packages
# base-devel              | compilation/build tools
# bat                     | cat with syntax highlighting
# bat-extras              | additional bat utilities
# bmon                    | bandwidth monitor
# btop                    | resource monitor (better top)
# chezmoi                 | dotfile manager
# cpufetch                | CPU info display
# cronie                  | cron daemon
# ctop                    | container top
# curl                    | HTTP client
# diff-so-fancy           | prettier git diffs
# direnv                  | per-directory env vars
# docker                  | container platform
# docker-buildx           | docker build plugin
# docker-compose          | multi-container orchestration
# dog                     | DNS lookup tool
# duf                     | disk usage viewer
# dust                    | disk usage analyzer (better du)
# eza                     | modern ls replacement
# fastfetch               | system info display
# fd                      | find alternative
# ffmpeg                  | media encoder/converter
# fwupd                   | firmware updater
# fzf                     | fuzzy finder
# git                     | version control
# github-cli              | GitHub CLI tool
# glances                 | system monitor
# gnupg                   | encryption/signing
# htop                    | process viewer
# hwinfo                  | hardware info tool
# iftop                   | network bandwidth monitor
# inxi                    | system information
# iotop                   | disk I/O monitor
# jq                      | JSON processor
# lazydocker              | docker TUI
# lazygit                 | git TUI
# less                    | file pager
# man-db                  | manual page database
# man-pages               | linux manual pages
# mediainfo               | media file analyzer
# ncdu                    | ncurses disk usage
# nmap                    | network scanner
# nvm                     | node version manager
# nvtop                   | GPU monitor
# nyancat                 | nyan cat in terminal
# openssh                 | SSH client/server
# openvpn                 | VPN client
# pacman-contrib          | pacman helper scripts
# pipewire                | modern audio server
# power-profiles-daemon   | power management
# pyenv                   | python version manager
# qmk                     | keyboard firmware tools
# reflector               | mirrorlist updater
# ripgrep                 | faster grep alternative
# ripgrep-all             | ripgrep for PDFs/docs
# rust                    | rust compiler/tools
# smartmontools           | disk health monitoring
# solaar                  | logitech device manager
# sops                    | secrets encryption
# sysstat                 | system statistics
# tcpdump                 | packet analyzer
# tldr                    | simplified man pages
# ttf-ibm-plex            | IBM Plex font family
# ufw                     | firewall manager
# usbutils                | USB device utilities
# vim                     | text editor
# wget                    | file downloader
# wireplumber             | pipewire session manager
# xclip                   | clipboard manager
# zoxide                  | smart cd command
# zsh                     | shell

sudo pacman -Syu arch-audit base base-devel bat bat-extras bmon btop chezmoi cpufetch cronie ctop curl diff-so-fancy direnv docker docker-buildx docker-compose dog duf dust eza fastfetch fd ffmpeg fwupd fzf git github-cli glances gnupg htop hwinfo iftop inxi iotop jq lazydocker lazygit less man-db man-pages mediainfo ncdu nmap nvm nvtop nyancat openssh openvpn pacman-contrib pipewire power-profiles-daemon pyenv qmk reflector ripgrep ripgrep-all rust smartmontools solaar sops sysstat tcpdump tldr ttf-ibm-plex ufw usbutils vim wget wireplumber xclip zoxide zsh
```

## KDE Applications

```bash
# ark                  : Archive/compression file manager
# bluedevil            : Bluetooth device manager
# cameractrls          : Camera configuration utility
# celluloid            : GTK frontend for mpv media player
# chromium             : Open-source web browser
# dolphin              : KDE file manager
# elisa                : Music player
# filelight            : Disk usage visualization
# firefox              : Web browser
# gwenview             : Image viewer
# kalarm               : Alarm and reminder scheduler
# kate                 : Advanced text editor
# kcalc                : Calculator
# kclock               : Clock, timer, and stopwatch
# kdeconnect           : Device integration (phone sync)
# kompare              : File diff/comparison tool
# konsole              : Terminal emulator
# kweather             : Weather application
# meld                 : Visual diff and merge tool
# mpv                  : Minimalist media player
# okular               : Universal document viewer (PDF, etc.)
# partitionmanager     : Disk partition editor
# spectacle            : Screenshot capture tool

sudo pacman -Syu ark bluedevil cameractrls celluloid chromium dolphin elisa filelight firefox gwenview kalarm kate kcalc kclock kdeconnect kompare konsole kweather meld mpv okular partitionmanager spectacle
```

## Microcode

https://wiki.archlinux.org/title/Microcode

1. Install based on your CPU:

```bash
# Auto-detect and install correct microcode
grep -q "GenuineIntel" /proc/cpuinfo && sudo pacman -S intel-ucode
grep -q "AuthenticAMD" /proc/cpuinfo && sudo pacman -S amd-ucode
```

2. After installation:

```bash
# Regenerate bootloader config
sudo grub-mkconfig -o /boot/grub/grub.cfg
# Or if using systemd-boot
sudo bootctl update
# Then reboot and check dmesg
sudo dmesg | grep microcode
```

## Graphics Drivers / Hardware video acceleration

https://wiki.archlinux.org/title/Hardware_video_acceleration

1. First, you need to enable the multilib repository for 32-bit packages:

```bash
# Edit pacman.conf
sudo vim /etc/pacman.conf

# Uncomment these two lines:
[multilib]
Include = /etc/pacman.d/mirrorlist

# Save and update
sudo pacman -Syu
```

2. Now install the drivers:

```bash
# AMD
sudo pacman -S mesa lib32-mesa \
  vulkan-radeon lib32-vulkan-radeon \
  vulkan-icd-loader lib32-vulkan-icd-loader \
  radeontop \
  libva-mesa-driver lib32-libva-mesa-driver \
  libva-utils vdpauinfo vulkan-tools

# NVIDIA
sudo pacman -S nvidia-open nvidia-utils lib32-nvidia-utils \
  nvidia-settings \
  opencl-nvidia \
  libva-nvidia-driver \
  libva-utils vdpauinfo vulkan-tools \
  egl-wayland
```

3. Finally, verify after install:

```bash
# Check Vulkan
vulkaninfo --summary
# Check VA-API (video decode)
vainfo
# Check VDPAU (video decode, older API)
vdpauinfo
# Check OpenCL (for AMD, need rocm-opencl-runtime)
clinfo
```

## Yet another Yogurt - An AUR Helper written in Go

The initial installation of yay can be done by cloning the PKGBUILD and building with makepkg:

```bash
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

A few packages to get started:

```bash
yay -S jetbrains-toolbox
yay -S piavpn-bin
yay -S topgrade
yay -S sourcegit
```

## Reflector

Reflector is a Python script which can:

1. Retrieve the latest mirror list
2. Filter the most up-to-date mirrors
3. Sort them by speed

```bash
## Configure fastest mirrors
sudo reflector \
  --country Canada \
  --age 12 \
  --protocol https \
  --sort rate \
  --save /etc/pacman.d/mirrorlist
# Enable automatic mirror updates (weekly)
sudo systemctl enable reflector.timer
```

## Generating locales

Locales are used by glibc and other locale-aware programs or libraries for rendering text, correctly displaying regional monetary values, time and date formats.

```bash
CHARSET="UTF-8"
LANG="en_US.${CHARSET}"
echo "$LANG $CHARSET" | sudo tee /etc/locale.gen
sudo locale-gen
localectl set-locale "LANG=$LANG"
localectl list-locales
locale
```

## Firewall configuration

```bash
# Enable UFW
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 172.17.0.0/16 comment 'Docker'
sudo ufw allow from 192.168.0.0/16 to any port 11434 proto tcp comment 'Ollama'
sudo ufw allow from 192.168.0.0/16 to any port 8188 proto tcp comment 'ComfyUI'
# Enable firewall
sudo ufw enable
sudo systemctl enable ufw
# Check UFW rules
sudo ufw status numbered
```

## Clean package cache

https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache

```bash
# Deletes all cached versions of installed and uninstalled packages, except for the most recent three. 
sudo systemctl enable --now paccache.timer
```

## Python version management

To install additional Python versions, use pyenv install.

```bash
pyenv install 3.14
pyenv global 3.14
pyenv versions
```

To create a virtualenv for the Python version used with pyenv, run pyenv virtualenv, specifying the Python version you want and the name of the virtualenv directory.

```bash
git clone https://github.com/yyuu/pyenv-virtualenv.git $PYENV_ROOT/plugins/pyenv-virtualenv
pyenv virtualenv 3.11.14 my-virtual-env-3.11.14
pyenv virtualenvs
pyenv activate my-virtual-env-3.11.14
pyenv version
pyenv deactivate
pyenv uninstall my-virtual-env-3.11.14
```

## Generating a new SSH key and adding it to the ssh-agent

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

```bash
ssh-keygen -t ed25519 -C "bull.justin@gmail.com" -f ~/.ssh/id_ed25519_personal -N ""
ssh-keygen -t ed25519 -C "justin.bull@base1.com" -f ~/.ssh/id_ed25519_base1 -N ""
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_base1
```

## Add SSH keys to GitHub

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

```bash
cat ~/.ssh/id_ed25519_personal.pub
cat ~/.ssh/id_ed25519_base1.pub
```

## Generating GPG keys

https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key

```bash
gpg --batch --generate-key <<EOF
Key-Type: RSA
Key-Length: 4096
Subkey-Type: RSA
Subkey-Length: 4096
Name-Real: Justin Bull
Name-Email: bull.justin@gmail.com
Expire-Date: 0
%no-protection
%commit
EOF
```

```bash
gpg --batch --generate-key <<EOF
Key-Type: RSA
Key-Length: 4096
Subkey-Type: RSA
Subkey-Length: 4096
Name-Real: Justin Bull
Name-Email: justin.bull@base1.com
Expire-Date: 0
%no-protection
%commit
EOF
```

## Add GPG keys to GitHub

https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account

```bash
gpg --armor --export "$(gpg --list-secret-keys --keyid-format=long --with-colons bull.justin@gmail.com | awk -F: '/^sec:/ {print $5}')"
gpg --armor --export "$(gpg --list-secret-keys --keyid-format=long --with-colons justin.bull@base1.com | awk -F: '/^sec:/ {print $5}')"
```

## Update workspace specific git configurations

```bash
export GIT_AUTHOR_NAME="Justin Bull"
export GIT_AUTHOR_EMAIL="bull.justin@gmail.com"
git config -f ~/.gitconfig-personal user.name "$GIT_AUTHOR_NAME"
git config -f ~/.gitconfig-personal user.email "$GIT_AUTHOR_EMAIL"
git config -f ~/.gitconfig-personal user.signingkey "$(gpg --list-secret-keys --keyid-format=long $GIT_AUTHOR_EMAIL | grep sec | awk '{print $2}' | cut -d'/' -f2)"
git config -f ~/.gitconfig-personal core.sshCommand "ssh -i ~/.ssh/id_ed25519_personal"
```

```bash
export GIT_AUTHOR_NAME="Justin Bull"
export GIT_AUTHOR_EMAIL="justin.bull@base1.com"
git config -f ~/.gitconfig-work user.name "$GIT_AUTHOR_NAME"
git config -f ~/.gitconfig-work user.email "$GIT_AUTHOR_EMAIL"
git config -f ~/.gitconfig-work user.signingkey "$(gpg --list-secret-keys --keyid-format=long $GIT_AUTHOR_EMAIL | grep sec | awk '{print $2}' | cut -d'/' -f2)"
git config -f ~/.gitconfig-work core.sshCommand "ssh -i ~/.ssh/id_ed25519_base1"
```

## Chezmoi

Manage your dotfiles across multiple diverse machines, securely.

```bash
# Setup the source directory and update the destination directory to match the target state
chezmoi init git@github.com:jabes/arch-dotfiles.git
# Pull and apply any changes
chezmoi update
# Update the destination directory to match the target state
chezmoi apply
# Print the diff between the target state and the destination state
chezmoi diff
# Re-add modified files
chezmoi re-add
# Show the status of targets
chezmoi status
# Exit with success if the destination state matches the target state, fail otherwise
chezmoi verify
```

## Define LS_COLORS

This is a collection of extension:color mappings, suitable to use as your LS_COLORS environment variable.
Most of them use the extended color map (described in the ECMA-48); in other words—you'll need a terminal with capabilities of displaying 256 colors.

```bash
git clone https://github.com/trapd00r/LS_COLORS.git $HOME/.lscolors
```

## Ultimate vim configuration

```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

## Oh My Zsh

Oh My Zsh is an open source, community-driven framework for managing your zsh configuration.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/zsh-users/zsh-completions.git $HOME/.oh-my-zsh/custom/plugins/zsh-completions 
git clone --depth=1 https://github.com/zsh-users/zsh-autosuggestions.git $HOME/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone --depth=1 https://github.com/zsh-users/zsh-syntax-highlighting.git $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git $HOME/.oh-my-zsh/custom/themes/powerlevel10k
chsh -s /usr/bin/zsh
exec zsh
```

Install the recommended font: Meslo Nerd Font patched for Powerlevel10k

```bash
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFRegular.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFBold.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFBoldItalic.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFItalic.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf
```

## Node version manager

```bash
nvm install --lts
nvm use --lts
node --version
npm --version
npm install -g gtop
npm install -g npm-check-updates
```

## Docker

Linux post-installation steps for docker engine.

https://docs.docker.com/engine/install/linux-postinstall/

```bash
# Enable and start Docker service
sudo systemctl enable --now docker
# Add user to docker group
sudo usermod -aG docker $USER
# Apply group changes without logout
newgrp docker
# Version check
docker --version
docker compose version
# Test Docker installation
docker run hello-world
```

#### Docker daemon configuration

https://docs.docker.com/reference/cli/dockerd/#daemon-configuration-file

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<'EOF'
{
  "log-driver": "journald",
  "log-opts": {
    "tag": "{{.Name}}/{{.ID}}"
  },
  "storage-driver": "overlay2",
  "live-restore": true,
  "userland-proxy": false,
  "dns": ["1.1.1.1", "1.0.0.1"],
  "max-concurrent-downloads": 10,
  "default-ulimits": {
    "nofile": {
      "Name": "nofile",
      "Hard": 64000,
      "Soft": 64000
    }
  },
  "icc": false,
  "iptables": true,
  "features": {
    "buildkit": true
  }
}
EOF

# Validate configuration
sudo dockerd --validate

# Restart Docker
sudo systemctl restart docker

# Verify settings
docker info | grep -E "Logging Driver|Storage Driver"
```

#### Journal service configuration

https://www.freedesktop.org/software/systemd/man/latest/journald.conf.html

```bash
sudo tee /etc/systemd/journald.conf <<'EOF'
[Journal]
# Storage location
Storage=persistent
# Maximum disk space journald can use
SystemMaxUse=2G
# Keep free disk space
SystemKeepFree=1G
# Maximum size of individual journal files
SystemMaxFileSize=200M
# How long to keep logs
MaxRetentionSec=2week
# Compress logs
Compress=yes
# Forward to syslog (usually not needed)
ForwardToSyslog=no
# Rate limiting (prevent log flooding)
RateLimitIntervalSec=30s
RateLimitBurst=10000
EOF

# Apply changes
sudo systemctl restart systemd-journald
```

## Additional services to enable

```bash
sudo systemctl enable --now fstrim.timer
sudo systemctl enable --now cronie
sudo systemctl enable --now sysstat
sudo systemctl enable --now piavpn
```

## Private Internet Access

```bash
piactl background enable
piactl connect
piactl disconnect
piactl get connectionstate
```

## Linux Vendor Firmware Service

A system daemon to allow session software to update firmware.
 
```bash
fwupdmgr get-devices
fwupdmgr refresh
fwupdmgr get-updates
fwupdmgr update
```

## Update keyboard firmware

- https://docs.qmk.fm/newbs_getting_started
- https://docs.qmk.fm/newbs_flashing

```bash
qmk setup
# This is just an example
# Go download the latest firmware and use that
# https://nuphy.com/pages/qmk-firmwares
qmk flash QMK_firmware_nuphy_gem80_trimode_ansi_v2.1.5.bin
```

#### Give access to the hidraw device via udev rules

```bash
sudo cp $HOME/qmk_firmware/util/udev/50-qmk.rules /etc/udev/rules.d/
```

#### Linux device manager bug

A change to systemd with version 258 seems to have broken a lot of people's udev rules, and now the provided udev rule file no longer works.

- https://github.com/qmk/qmk_firmware/issues/25847
- https://github.com/systemd/systemd/issues/39056

#### Temporary fix

|              |                                                                                    |
|--------------|------------------------------------------------------------------------------------|
| replace      | `KERNEL=="hidraw*", MODE="0660", GROUP="plugdev", TAG+="uaccess", TAG+="udev-acl"` |
| with         | `KERNEL=="hidraw*", TAG+="uaccess"`                                                |
| file         | `/etc/udev/rules.d/50-qmk.rules`                                                   |

#### Reload and reapply udev rules

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

## VIA Usage Guide for NuPhy Keyboards

Configure, test and design in one place - VIA is the last application you'll need for your keyboard.

#### Resources:

- https://nuphy.com/pages/qmk-firmwares
- https://nuphy.com/pages/update-instructions
- https://nuphy.com/pages/json-files-for-nuphy-keyboards
- https://nuphy.com/pages/instructions-on-flashing-the-rf-firmwares

#### Quick instructions:

1. Plug keyboard into device using USB
2. Open usevia.app with a Chromium-based browser
3. Download the correct JSON file for your keyboard
4. Go to settings tab and enable "Show Design Tab"
5. Go to design tab and load the JSON file
6. Go to configure tab and authorize device

#### Note:

- VIA only supports browsers that have WebHID enabled...
- Mozilla has a [negative opinion](https://developer.mozilla.org/en-US/docs/Web/API/WebHID_API) of the api.

```bash
chromium https://usevia.app
```

## Security Audit

```bash
sudo lynis audit system
sudo lynis audit dockerfile $HOME/Repos/personal/storest/Dockerfile 
```

## Regular Maintenance Tasks

```bash
# Update everything, remove old packages
topgrade --cleanup --yes
# Check for security issues
arch-audit
# Review failed services
systemctl --failed
# Check disk health
sudo smartctl -H /dev/nvme0n1
# Review journal size
journalctl --disk-usage
# Remove unused packages (orphans)
pacman -Qdtq | pacman -Rns -
# Verify pacman database
sudo pacman -Dk
```
