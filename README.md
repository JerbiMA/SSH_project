# Linux Server & SSH Setup

Project URL : https://roadmap.sh/projects/ssh-remote-server-setup

## 1. Server setup

I created an Ubuntu 24.04 LTS server using AWS EC2.

I connected to the server for the first time using the SSH key provided by AWS:

```bash
ssh -i <MyPath/key.pem> ubuntu@<server-ip>
```

## 2. SSH keys

For the project, I created two new SSH key pairs using Ed25519:

```bash
ssh-keygen -t ed25519 -C "linux-project-key-1" -f "$HOME\.ssh\linux-project-1"

ssh-keygen -t ed25519 -C "linux-project-key-2" -f "$HOME\.ssh\linux-project-2"
```

This created:

```text
linux-project-1
linux-project-1.pub

linux-project-2
linux-project-2.pub
```

I added both public keys to the server in:

```text
~/.ssh/authorized_keys
```

I tested both keys to make sure I could connect to the server with each one.

## 3. SSH config

I configured the SSH config file so I could connect using aliases instead of writing the full SSH command every time.

Example:

```text
Host devops-vm-key1
    HostName <server-ip>
    User ubuntu
    IdentityFile ~/.ssh/linux-project-1

Host devops-vm-key2
    HostName <server-ip>
    User ubuntu
    IdentityFile ~/.ssh/linux-project-2
```

I tested both aliases:

```bash
ssh devops-vm-key1
ssh devops-vm-key2
```

Both connections worked.

## 4. Fail2Ban

As the stretch goal, I installed Fail2Ban to protect SSH from repeated failed login attempts.

Installation:

```bash
sudo apt update
sudo apt install fail2ban -y
```

I created `/etc/fail2ban/jail.local` with:

```ini
[DEFAULT]
bantime = 1h
findtime = 10m
maxretry = 5

[sshd]
enabled = true
```

Then I enabled Fail2Ban:

```bash
sudo systemctl enable --now fail2ban
```

I checked the SSH jail with:

```bash
sudo fail2ban-client status sshd
```

Fail2Ban is now monitoring SSH login attempts and temporarily banning IP addresses after repeated failed authentication attempts.

## Result

The project is now set up with:

* Ubuntu 24.04 LTS server on AWS EC2
* Two SSH key pairs
* Both public keys added to the server
* SSH access tested with both keys
* Two SSH aliases configured
* Fail2Ban installed and configured

The private SSH keys were kept on my local machine and were not added to the repository.
