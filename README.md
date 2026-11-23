<div align="center">

# Born2beroot

**A secure Linux server administration project**

[![42 Project](https://img.shields.io/badge/42-Born2beroot-black?logo=42)](https://www.42network.org/)
[![Linux](https://img.shields.io/badge/Linux-Debian%20%7C%20Rocky%20Linux-FCC624?logo=linux&logoColor=black)](https://www.linux.org/)

[English](README.md) · [Español](README.es.md)

</div>

## About the project

Born2beroot is a system-administration project from the 42 curriculum. Its goal is to create and secure a virtual Linux server while learning virtualization, partitioning, users and groups, SSH, firewall configuration, password policies, privilege management, and system monitoring.

The project is not only about installing an operating system: it is about understanding why each security measure exists and being able to explain the server configuration.

## Learning objectives

- Install and configure a minimal Linux server in a virtual machine.
- Use LVM and encrypted partitions where required.
- Manage users, groups, ownership, permissions, and `sudo` access.
- Configure SSH securely without exposing the root account.
- Apply password and account-expiration policies.
- Configure a firewall with only the required services exposed.
- Automate system monitoring with a Bash script.
- Understand basic virtualization and server-hardening principles.

## Main requirements

### Operating system

The virtual machine uses one of the distributions allowed by the subject:

- Debian
- Rocky Linux

The installation is performed without a graphical interface so the server remains lightweight and easier to audit.

### Storage and encryption

The disk is partitioned according to the subject requirements. LVM is used to manage logical volumes, and the required encrypted volumes are protected with a passphrase.

### Users, groups, and privileges

The system includes a regular administrative user, a dedicated administrative group, a strong password policy, restricted `sudo` permissions, and no direct root login through SSH. Administrative actions are performed through `sudo` for traceability.

### SSH and firewall

SSH is configured on the port required by the subject. Direct root access is disabled and only intended users can authenticate. The firewall allows only required services and denies unsolicited incoming traffic by default.

```bash
ssh <username>@<server-ip> -p <ssh-port>
sudo ufw status numbered                 # Debian
sudo firewall-cmd --list-all             # Rocky Linux
```

### Monitoring script

A Bash monitoring script periodically displays relevant information about the server, including OS and kernel version, CPU and RAM usage, disk space, last boot time, LVM status, active connections, logged-in users, network addresses, and the number of commands executed with `sudo`.

The script is scheduled with `cron` or `wall` so its output is automatically broadcast to logged-in users at the required interval.

## Virtual machine checks

```bash
uname -a
cat /etc/os-release
lsblk
df -h
getent passwd
getent group
sudo systemctl status ssh
sudo journalctl _COMM=sudo
```

## Installation overview

1. Create a virtual machine in VirtualBox or another supported hypervisor.
2. Install the selected operating system without a graphical environment.
3. Configure the required partitions, LVM volumes, and encryption.
4. Create the administrative user and required groups.
5. Apply the password, `sudo`, and SSH policies.
6. Configure the firewall and verify that only the required port is open.
7. Install and schedule the monitoring script.
8. Reboot and verify every requirement before evaluation.

> **Security note:** Never commit passwords, encryption passphrases, private keys, or other sensitive information to this repository.

## Evaluation checklist

- [ ] The virtual machine boots correctly.
- [ ] Hostname and username follow the required format.
- [ ] The disk uses the required LVM/encryption setup.
- [ ] The password policy is active and enforced.
- [ ] The `sudo` policy is restricted and logged.
- [ ] SSH runs on the required port and root login is disabled.
- [ ] The firewall is enabled with the correct rules.
- [ ] The monitoring script runs automatically at the required interval.
- [ ] The configuration can be explained during the project defense.

## Resources

- [42 Born2beroot subject](en.subject.pdf)
- [Debian Documentation](https://www.debian.org/doc/)
- [Rocky Linux Documentation](https://docs.rockylinux.org/)
- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [UFW Documentation](https://help.ubuntu.com/community/UFW)
- [Firewalld Documentation](https://firewalld.org/documentation/)

## Author

**wolfredothewise**

- GitHub: [@wolfredothewise](https://github.com/wolfredothewise)

