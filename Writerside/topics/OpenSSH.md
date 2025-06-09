# OpenSSH

Reference: [ssh.com/academy/ssh](https://www.ssh.com/academy/ssh)

OpenSSH is a suite of secure networking tools based on the SSH protocol. It includes both client and server components,
and supports secure remote login, file transfer, and tunneling.

## Key OpenSSH Tools

- `sshd`: OpenSSH server daemon.
- `ssh`: Establishes a secure shell session on a remote system.
- `scp`: Secure file transfer between local and remote systems.
- `ssh-copy-id`: Installs your public key on a remote server's `authorized_keys` file.
- `ssh-keyscan`: Collects public host keys from remote hosts.
- `ssh-keygen`: Creates and manages authentication keys.
- `ssh-agent`: Caches private key passphrases to enable automatic authentication.
- `ssh-add`: Adds private keys to the `ssh-agent`.
- `sshfs`: Mounts a remote filesystem via SSH.

## Basic Usage

```bash
ssh username@host -p port
````

---

## SSH Server Configuration

### Check if SSH Server is Running

```bash
systemctl status sshd
```

### Allow SSH Port Through Firewall

```bash
sudo ufw allow 22/tcp
```

### Modify SSH Daemon Configuration

Edit the file:

```bash
sudo nano /etc/ssh/sshd_config
```

To reload the configuration:

```bash
sudo systemctl reload sshd.service
```

Enable strict mode to check file and directory permissions:

```bash
StrictModes yes
```

### Validate SSH Configuration

```bash
sudo sshd -t
```

(Silent if no errors)

### Regenerate Host Keys

```bash
sudo rm /etc/ssh/ssh_host*
sudo ssh-keygen -A
sudo chmod 400 /etc/ssh/ssh_host*
sudo chmod 644 /etc/ssh/ssh_host*.pub
```

> SSH host keys should **not** be password-protected.

---

## SSH Client Configuration

Create or edit the file `~/.ssh/config`:

```text
Host qwerty
  Hostname 10.0.0.1
  User root
  Port 2222
```

Then connect using:

```bash
ssh qwerty
```

### Generate a New Key

```bash
ssh-keygen -t ed25519
```

> The `ed25519` algorithm is preferred over `rsa` for better security.

### Send Public Key to Server

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
```

This adds the public key to the server's `~/.ssh/authorized_keys`.

---

## Useful Commands

### Check Failed Login Attempts

```bash
cat /var/log/secure | grep "Failed password for"
```

### Send File Over SSH

```bash
scp ~/test.txt user@host:documents
```

### Run GUI Applications Remotely

1. On the server: ensure `X11Forwarding yes` in `/etc/ssh/sshd_config`.
2. Then run:

```bash
ssh -XC user@remotehost "eclipse"
```

### Run Local Script on Remote Host

```bash
ssh user@host 'bash -s' < script.sh
```
