<div align="center">

# Born2beroot

**Un proyecto de administración segura de servidores Linux**

[![Proyecto 42](https://img.shields.io/badge/42-Born2beroot-black?logo=42)](https://www.42network.org/)
[![Linux](https://img.shields.io/badge/Linux-Debian%20%7C%20Rocky%20Linux-FCC624?logo=linux&logoColor=black)](https://www.linux.org/)

[English](README.md) · [Español](README.es.md)

</div>

## Sobre el proyecto

Born2beroot es un proyecto de administración de sistemas del programa de 42. Su objetivo es crear y proteger un servidor Linux virtual, aprendiendo virtualización, particionado, usuarios y grupos, SSH, configuración del firewall, políticas de contraseñas, gestión de privilegios y monitorización del sistema.

El proyecto no consiste únicamente en instalar un sistema operativo: también requiere comprender por qué existe cada medida de seguridad y poder explicar la configuración del servidor.

## Objetivos de aprendizaje

- Instalar y configurar un servidor Linux mínimo en una máquina virtual.
- Utilizar LVM y particiones cifradas cuando sea necesario.
- Gestionar usuarios, grupos, propietarios, permisos y acceso mediante `sudo`.
- Configurar SSH de forma segura sin exponer la cuenta root.
- Aplicar políticas de contraseñas y expiración de cuentas.
- Configurar un firewall exponiendo únicamente los servicios necesarios.
- Automatizar la monitorización mediante un script Bash.
- Comprender los fundamentos de virtualización y hardening de servidores.

## Requisitos principales

### Sistema operativo

La máquina virtual utiliza una de las distribuciones permitidas por el subject:

- Debian
- Rocky Linux

La instalación se realiza sin interfaz gráfica para mantener el servidor ligero y facilitar su auditoría.

### Almacenamiento y cifrado

El disco se particiona de acuerdo con los requisitos del subject. Se utiliza LVM para gestionar los volúmenes lógicos y los volúmenes cifrados requeridos se protegen mediante una contraseña.

### Usuarios, grupos y privilegios

El sistema incluye un usuario administrativo normal, un grupo administrativo, una política de contraseñas robustas, permisos `sudo` restringidos y acceso root directo deshabilitado mediante SSH. Las acciones administrativas se realizan mediante `sudo` para aportar trazabilidad.

### SSH y firewall

SSH está configurado en el puerto requerido por el subject. El acceso directo de root está deshabilitado y solo los usuarios previstos pueden autenticarse. El firewall permite únicamente los servicios necesarios y bloquea por defecto el tráfico entrante no solicitado.

```bash
ssh <usuario>@<ip-del-servidor> -p <puerto-ssh>
sudo ufw status numbered                 # Debian
sudo firewall-cmd --list-all             # Rocky Linux
```

### Script de monitorización

Un script Bash muestra periódicamente información relevante del servidor, incluyendo la versión del sistema y del kernel, uso de CPU y RAM, espacio de disco, último arranque, estado de LVM, conexiones activas, usuarios conectados, direcciones de red y número de comandos ejecutados con `sudo`.

El script se programa mediante `cron` o `wall` para mostrar automáticamente su salida a los usuarios conectados con el intervalo requerido.

## Comprobaciones de la máquina virtual

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

## Resumen de instalación

1. Crear una máquina virtual en VirtualBox u otro hipervisor compatible.
2. Instalar el sistema operativo seleccionado sin entorno gráfico.
3. Configurar las particiones, los volúmenes LVM y el cifrado requeridos.
4. Crear el usuario administrativo y los grupos necesarios.
5. Aplicar las políticas de contraseñas, `sudo` y SSH.
6. Configurar el firewall y verificar que solo el puerto requerido esté abierto.
7. Instalar y programar el script de monitorización.
8. Reiniciar y verificar todos los requisitos antes de la evaluación.

> **Nota de seguridad:** Nunca subas contraseñas, contraseñas de cifrado, claves privadas u otra información sensible a este repositorio.

## Lista de comprobación para la evaluación

- [ ] La máquina virtual arranca correctamente.
- [ ] El hostname y el nombre de usuario siguen el formato requerido.
- [ ] El disco utiliza la configuración LVM/cifrado requerida.
- [ ] La política de contraseñas está activa y se aplica correctamente.
- [ ] La política de `sudo` está restringida y registra las acciones.
- [ ] SSH utiliza el puerto requerido y el acceso root está deshabilitado.
- [ ] El firewall está activo con las reglas correctas.
- [ ] El script de monitorización se ejecuta automáticamente con el intervalo requerido.
- [ ] La configuración puede explicarse durante la defensa del proyecto.

## Recursos

- [Subject de Born2beroot de 42](en.subject.pdf)
- [Documentación de Debian](https://www.debian.org/doc/)
- [Documentación de Rocky Linux](https://docs.rockylinux.org/)
- [Documentación de OpenSSH](https://www.openssh.com/manual.html)
- [Documentación de UFW](https://help.ubuntu.com/community/UFW)
- [Documentación de Firewalld](https://firewalld.org/documentation/)

## Autor

**wolfredothewise**

- GitHub: [@wolfredothewise](https://github.com/wolfredothewise)

