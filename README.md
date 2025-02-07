# Debian Dev PC Bootstrap Installation

This guide walks you through setting up a Debian-based personal PC with various tools and applications. It covers everything from configuring `vi` to installing development and productivity apps.

---

## Table of Contents

1. [Enable INSERT in vi](#1-enable-insert-in-vi)
2. [Configure Sudo Access](#2-configure-sudo-access)
3. [System Update](#3-system-update)
4. [Disable IPv6](#4-disable-ipv6)
5. [Setup SSH Keys](#5-setup-ssh-keys)
6. [Install Applications](#6-install-applications)
    - [Sublime Text](#sublime-text)
    - [PHP](#php)
    - [Heroku CLI](#heroku-cli)
    - [Additional Tools](#additional-tools)
    - [Visual Studio Code](#visual-studio-code)
    - [Other Applications](#other-applications)
7. [Clean Up](#7-clean-up)

---

## 1. Enable INSERT in vi

To improve your `vi` experience, switch to the root user and install `vim-gui-common` which provides better support for input modes.

```bash
su
sudo apt-get install vim-gui-common -y
```

---

## 2. Configure Sudo Access

Add your user to the sudoers file to allow administrative commands without switching users. Open `/etc/sudoers` with your favorite editor (use `visudo` if possible) and add:

```text
my_username ALL=(ALL:ALL) ALL
```

> **Note:** Replace `my_username` with your actual username.

---

## 3. System Update

Update the package lists and upgrade installed packages:

```bash
sudo apt update -y && sudo apt full-upgrade -y
```

---

## 4. Disable IPv6

*Instructions for disabling IPv6 have not been provided in detail here. Please consult your system documentation or online guides for the proper steps.*

---

## 5. Setup SSH Keys

If you need to manage SSH keys:

1. **Edit/Create your private key (if necessary):**

    ```bash
    vi ~/.ssh/id_rsa
    ```

2. **Set the correct permissions:**

    ```bash
    chmod 600 ~/.ssh/id_rsa
    ```

3. **Generate the public key from your private key:**

    ```bash
    ssh-keygen -y -f ~/.ssh/id_rsa > ~/.ssh/id_rsa.pub
    ```

---

## 6. Install Applications

### Sublime Text

Add the Sublime Text repository and install the editor:

```bash
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/sublimehq-archive.gpg > /dev/null
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt update -y && sudo apt install sublime-text -y
```

### PHP

Install PHP without the recommended extra packages:

```bash
sudo apt install php --no-install-recommends -y
```

### Heroku CLI

Install the Heroku Command Line Interface:

```bash
curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
```

### Additional Tools

Install a collection of useful tools and applications:

```bash
sudo apt install curl vlc gimp ffmpeg nodejs npm apt-transport-https filezilla python3-pip -y
```

### Visual Studio Code

Follow these steps to install Visual Studio Code:

1. **Install prerequisites:**

    ```bash
    sudo apt-get install wget gpg -y
    ```

2. **Add Microsoft's GPG key:**

    ```bash
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
    sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
    ```

3. **Add the VSCode repository:**

    ```bash
    echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
    ```

4. **Clean up the temporary key file:**

    ```bash
    rm -f packages.microsoft.gpg
    ```

5. **Update and install VSCode:**

    ```bash
    sudo apt update -y && sudo apt install code -y
    ```

### Other Applications

For additional software, refer to the following resources or their official installation guides:

- **1Password:** [Install 1Password for Linux](https://support.1password.com/install-linux/)
- **Docker**
- **Studio 3T**
- **Android Studio**

> **Note:** Installation steps for Docker, Studio 3T, and Android Studio are not included in this guide. Please consult their official documentation for setup instructions.

---

## 7. Clean Up

Remove any unnecessary packages to keep your system tidy:

```bash
sudo apt autoremove -y
```

---
