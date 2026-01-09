### Install required packages

```bash
sudo pacman -Syu \
    ark \
    base base-devel \
    bmon \
    btop \
    chezmoi \
    cpufetch \
    cronie \
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

### Yet another Yogurt - An AUR Helper written in Go

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

### Cleaning the package cache

https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache

```bash
# Deletes all cached versions of installed and uninstalled packages, except for the most recent three. 
sudo systemctl enable --now paccache.timer
```

### A pyenv plugin to manage virtualenv (a.k.a. python-virtualenv)

```bash
git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
```

### Generating a new SSH key and adding it to the ssh-agent

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

```bash
ssh-keygen -t ed25519 -C "bull.ustin@gmail.com" -f ~/.ssh/id_ed25519_personal -N ""
ssh-keygen -t ed25519 -C "justin.bull@base1.com" -f ~/.ssh/id_ed25519_base1 -N ""
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_base1
```

### Adding a new SSH key to your account

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?tool=webui

```bash
cat ~/.ssh/id_ed25519_personal.pub
cat ~/.ssh/id_ed25519_base1.pub
```

### Generating a new GPG key

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

### Adding a GPG key to your GitHub account

https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account

```bash
gpg --armor --export "$(gpg --list-secret-keys --keyid-format=long --with-colons bull.justin@gmail.com | awk -F: '/^sec:/ {print $5}')"
gpg --armor --export "$(gpg --list-secret-keys --keyid-format=long --with-colons justin.bull@base1.com | awk -F: '/^sec:/ {print $5}')"
```

### Update git configuration with newly created credentials

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

### Locales are used by glibc and other locale-aware programs or libraries for rendering text, correctly displaying regional monetary values, time and date formats.

```bash
echo "en_US.UTF-8 UTF-8" | sudo tee /etc/locale.gen
sudo locale-gen
```

### A collection of LS_COLORS definitions

```bash
git clone https://github.com/trapd00r/LS_COLORS.git $HOME/.lscolors
```

### The Ultimate vimrc

```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

### Oh My Zsh is an open source, community-driven framework for managing your zsh configuration

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone --depth=1 https://github.com/zsh-users/zsh-completions.git $HOME/.oh-my-zsh/custom/plugins/zsh-completions 
git clone --depth=1 https://github.com/zsh-users/zsh-autosuggestions.git $HOME/.oh-my-zsh/custom/plugins/zsh-autosuggestions
git clone --depth=1 https://github.com/zsh-users/zsh-syntax-highlighting.git $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git $HOME/.oh-my-zsh/custom/themes/powerlevel10k
chsh -s /usr/bin/zsh
exec zsh
```

### Meslo Nerd Font patched for Powerlevel10k

```bash
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFRegular.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFBold.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFBoldItalic.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf
sudo wget -q -O /usr/share/fonts/TTF/MesloLGSNFItalic.ttf https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf
```

### Node version manager

```bash
nvm install --lts
nvm use --lts
npm install -g gtop
npm install -g npm-check-updates
```

### Update keyboard firmware

```bash
qmk setup
# This is just an example
# Go download the latest firmware and use that
# https://nuphy.com/pages/qmk-firmwares
qmk flash QMK_firmware_nuphy_gem80_trimode_ansi_v2.1.5.bin
```

### Give access to the hidraw device via udev rules

```bash
sudo cp $HOME/qmk_firmware/util/udev/50-qmk.rules /etc/udev/rules.d/
```

### **[Bug]** 50-qmk.rules Broken on systemd 258

- https://github.com/qmk/qmk_firmware/issues/25847
- https://github.com/systemd/systemd/issues/39056

```plain
Temporary fix
--------------------------------------------------
Replace: KERNEL=="hidraw*", MODE="0660", GROUP="plugdev", TAG+="uaccess", TAG+="udev-acl"
With:    KERNEL=="hidraw*", TAG+="uaccess"
File:    /etc/udev/rules.d/50-qmk.rules`
```

### Reload and reapply udev rules

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

### VIA Usage Guide for NuPhy Keyboards

Resources:

- https://nuphy.com/pages/qmk-firmwares
- https://nuphy.com/pages/update-instructions
- https://nuphy.com/pages/json-files-for-nuphy-keyboards
- https://nuphy.com/pages/instructions-on-flashing-the-rf-firmwares

Quick instructions:

1. Plug keyboard into device using USB
2. Open usevia.app with a Chromium-based browser
3. Download the correct JSON file for your keyboard
4. Go to settings tab and enable "Show Design Tab"
5. Go to design tab and load the JSON file
6. Go to configure tab and authorize device

### VIA is a simple and friendly app that lets you plug in your keyboard and customize it

- **Note:** VIA only supports browsers that have WebHID enabled.
- Mozilla has a negative opinion of the API: https://developer.mozilla.org/en-US/docs/Web/API/WebHID_API

```bash
chromium https://usevia.app
```
