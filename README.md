## Install required packages

```bash
sudo pacman -Syu \
    ark \
    base base-devel \
    bmon \
    btop \
    chezmoi \
    cpufetch \
    cronie \
    ctop \
    curl \
    diff-so-fancy \
    docker \
    docker-buildx \
    docker-compose \
    fastfetch \
    fzf \
    git \
    github-cli \
    glances \
    gnupg \
    htop \
    hwinfo \
    iftop \
    inxi \
    jq \
    lazydocker \
    lazygit \
    less \
    mediainfo \
    mpv \
    nmap \
    nvm \
    nvtop \
    nyancat \
    openssh \
    openvpn \
    pacman-contrib \
    partitionmanager \
    pyenv \
    qmk \
    reflector \
    rust \
    solaar \
    sops \
    spectacle \
    ttf-ibm-plex \
    ufw \
    usbutils \
    vim \
    wget \
    xclip \
    zsh
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
sudo ufw allow ssh
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
# Initialize
chezmoi init git@github.com:jabes/arch-dotfiles.git
# Apply dotfiles
chezmoi apply
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

### VIA is a simple and friendly app that lets you plug in your keyboard and customize it

```bash
# VIA only supports browsers that have WebHID enabled
# Mozilla has a negative opinion of the api
# https://developer.mozilla.org/en-US/docs/Web/API/WebHID_API
chromium https://usevia.app
```
