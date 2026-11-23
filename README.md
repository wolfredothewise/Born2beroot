*This project has been created as part of the 42 curriculum by vhedo-ga.*

<div align="right">

[English](README.md) | [Español](README.es.md)

</div>

# Born2beRoot

Born2beRoot is a 42 system-administration project that builds and hardens a virtual Linux server. The project teaches virtualization, disk partitioning, LVM, encryption, user and group management, SSH, firewall configuration, password policies, privilege escalation controls, and system monitoring.

## Description

The goal of this project is to configure a secure, minimal server that boots with no graphical interface and can be audited with built-in system tools. The implementation separates filesystems through partitions and logical volumes, restricts privileged access, limits network exposure, and reports server status automatically.

The server configuration can be documented with the following design choices:

- A dedicated administrative login is used for normal work and `sudo` is used for elevated commands.
- Root SSH login is disabled and SSH is restricted to an authorized port.
- A firewall allows only required services.
- Password expiration and complexity rules are applied.
- `sudo` use is restricted and logged.
- A Bash script gathers and broadcasts server monitoring information at the required interval.

### Operating system choice

This setup uses **Debian**. Debian is a practical choice for a first hardened server: it has extensive documentation, conservative package updates, a large package repository, and tools such as APT and UFW that are straightforward to manage.

Its main disadvantages are that package versions can be older than those of faster-moving distributions and that enterprise-oriented tools or repositories may require additional configuration.

#### Debian vs Rocky Linux

| Debian | Rocky Linux |
| --- | --- |
| Uses APT and DEB packages. | Uses DNF and RPM packages. |
| Broad community documentation and a large general repository. | Binary-compatible with Red Hat Enterprise Linux and oriented toward enterprise environments. |
| UFW is commonly used on the system. | SELinux and firewalld are common native choices. |
| Package versions are usually focused on stability. | Emphasizes enterprise compatibility and predictable long-term support. |
| Easier for a lightweight and beginner-friendly server setup. | Better fit for Red Hat-oriented infrastructure and administration. |

### Mandatory security choices

**AppArmor vs SELinux**

- **AppArmor** loads profile-based mandatory access controls, is relatively easy to read and modify, and is the traditional MAC implementation in Debian/Ubuntu environments.
- **SELinux** provides fine-grained labels and policies, is closely integrated with Red Hat-family distributions, and can be more complex to troubleshoot.
- Debian AppArmor is the preferred AppArmor/SELinux pairing for this implementation because it aligns with Debian's package and administration model.

**UFW vs firewalld**

- **UFW** is a user-friendly front end for nftables/iptables and provides simple allow and deny rules.
- **firewalld** manages dynamic zones and runtime/permanent rules, and is commonly integrated with Rocky Linux.
- UFW is a suitable choice for a Debian server where a small, predictable rule set is preferred.

**VirtualBox vs UTM**

- **VirtualBox** is available across major platforms and is convenient for the graphical 42 project workflow.
- **UTM** is especially popular on Apple Silicon and supports macOS-specific virtualization.
- VirtualBox is the original 42-style choice for this project and is selected here because of its cross-platform tooling and widely available networking and snapshot features.

## Partitioning and security design

The disk layout follows the subject requirements, using an encrypted physical volume, LVM, and separate logical volumes where required. LVM makes volume sizes easier to manage without affecting the host filesystem.

The server also applies:

- Strong password policy and account expiration.
- `sudo` restrictions and command logging.
- SSH key or password authentication as required by the subject.
- Root SSH login disabled.
- Firewall rules limited to SSH and explicitly approved services.
- Minimal installed packages and regular security updates.

## Users and privileges

The administrative account is named according to 42 login `vhedo-ga`. Hostname `vhedo-ga42` follows the required login-plus-`42` convention.

Administrative actions should use `sudo` rather than a root shell. `sudo` configuration should define allowed commands, authentication behavior, and logging so that privilege use remains auditable.

## SSH

SSH listens on the port required by the Born2beRoot subject. Before changing the SSH configuration, keep an existing session open and test a new connection from the host.

```bash
ssh vhedo-ga@<server-ip> -p <ssh-port>
sudo sshd -t
sudo systemctl status ssh
```

## Firewall

The firewall allows the SSH port and rejects unsolicited incoming traffic by default.

```bash
# Debian / UFW
sudo ufw status verbose

# Rocky Linux / firewalld
sudo firewall-cmd --list-all
```

## Monitoring

A Bash script collects the required system information: architecture and kernel, physical and virtual CPUs, RAM and disk usage, CPU load, boot time, LVM state, network information, active users, and `sudo` command count.

The script is scheduled with `cron` or broadcast with `wall` at the interval required by the subject.

```bash
ls /var/spool/cron
sudo crontab -l
df -h
free -m
lsblk
```

## Red teaming perspective

Born2beRoot provides a controlled environment for understanding attack paths and defensive controls. Defensive red-team exercises can include:

- Auditing exposed services and SSH configuration.
- Testing weak password and account-expiration settings.
- Reviewing `sudo` rules for excessive privileges.
- Verifying firewall default-deny behavior.
- Enumerating listening ports and unnecessary processes.
- Checking that encryption, logs, updates, and MAC policies are effective.

These checks should be performed only against this owned virtual machine or systems with explicit authorization. The purpose is to validate defenses, not to exploit external services.

## Instructions

### Prerequisites

- VirtualBox or UTM
- Debian or Rocky Linux ISO
- The `en.subject.pdf` file
- Host access for port forwarding and SSH testing

### Installation

1. Create a virtual machine and disable graphical installation.
2. Create the required partitions, LVM logical volumes, and encryption.
3. Install the base system and configure the network.
4. Create `vhedo-ga`, the required groups, and a secure password policy.
5. Configure `sudo`, SSH, and the firewall.
6. Add and schedule the monitoring script.
7. Reboot and verify every requirement.

### Verification

```bash
uname -a
cat /etc/os-release
lsblk
df -h
getent passwd
getent group
sudo systemctl status ssh
sudo ufw status verbose
sudo journalctl _COMM=sudo
```

### Evaluation checklist

- [ ] The virtual machine starts correctly.
- [ ] Hostname `vhedo-ga42` is configured.
- [ ] Partition, LVM, and encryption requirements are met.
- [ ] Password policy and account expiration are active.
- [ ] `sudo` is restricted and logged.
- [ ] SSH uses the required port and root login is disabled.
- [ ] The firewall denies unsolicited traffic.
- [ ] Monitoring output runs at the required interval.
- [ ] The implementation can be explained during the defense.

> **Security note:** Never commit passwords, encryption passphrases, private keys, or other sensitive information.

## Resources

- [Born2beRoot subject](en.subject.pdf)
- [Debian documentation](https://www.debian.org/doc/)
- [Rocky Linux documentation](https://docs.rockylinux.org/)
- [OpenSSH documentation](https://www.openssh.com/manual.html)
- [Debian AppArmor documentation](https://documentation.ubuntu.com/server/how-to/security/apparmor/)
- [SELinux Project](https://selinuxproject.org/page/Main_Page)
- [UFW documentation](https://help.ubuntu.com/community/UFW)
- [Firewalld documentation](https://firewalld.org/documentation/)
- [VirtualBox manual](https://www.virtualbox.org/manual/)
- [UTM documentation](https://docs.getutm.app/)

### AI usage

AI assistance was used to structure and improve this README, organize the subject requirements into clear sections, and review wording. AI did not configure the virtual machine, modify system files, choose or supply credentials, or verify server behavior. The final technical configuration must be checked locally and documented by the author.
