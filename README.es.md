*Este proyecto ha sido creado como parte del plan de estudios de 42 por vhedo-ga.*

<div align="right">

[English](README.md) | [Español](README.es.md)

</div>

# Born2beRoot

Born2beRoot es un proyecto de administración de sistemas de 42 que consiste en crear y reforzar un servidor Linux virtual. Enseña virtualización, particionado, LVM, cifrado, gestión de usuarios y grupos, SSH, configuración de firewall, políticas de contraseñas, control de privilegios y monitorización del sistema.

## Descripción

El objetivo es configurar un servidor seguro y mínimo que arranque sin entorno gráfico y pueda auditarse con las herramientas propias del sistema. La implementación separa sistemas de archivos mediante particiones y volúmenes lógicos, restringe el acceso privilegiado, limita la exposición de red y muestra automáticamente el estado del servidor.

Las decisiones principales de diseño son:

- Utilizar una cuenta administrativa para el trabajo normal y `sudo` para elevar privilegios.
- Deshabilitar el acceso root mediante SSH y restringir el puerto SSH.
- Permitir únicamente los servicios necesarios en el firewall.
- Aplicar reglas de expiración y complejidad de contraseñas.
- Restringir y registrar el uso de `sudo`.
- Ejecutar un script Bash que recopila y muestra la información de monitorización requerida.

### Elección del sistema operativo

Esta configuración utiliza **Debian**. Es una opción práctica para un primer servidor reforzado: dispone de documentación extensa, actualizaciones conservadoras, un gran repositorio de paquetes y herramientas como APT y UFW fáciles de administrar.

Sus principales desventajas son que algunas versiones de paquetes pueden ser más antiguas que en distribuciones con ciclos rápidos y que ciertas herramientas empresariales o repositorios pueden requerir configuración adicional.

#### Debian vs Rocky Linux

| Debian | Rocky Linux |
| --- | --- |
| Utiliza APT y paquetes DEB. | Utiliza DNF y paquetes RPM. |
| Amplia documentación comunitaria y repositorios generales. | Compatible a nivel binario con Red Hat Enterprise Linux y orientado a entornos empresariales. |
| UFW es una opción habitual. | SELinux y firewalld son opciones nativas habituales. |
| Versiones centradas en estabilidad. | Compatible con infraestructura Red Hat y soporte empresarial. |
| Adecuado para una configuración ligera y sencilla. | Adecuado para entornos orientados a RHEL. |

### Elecciones obligatorias de seguridad

**AppArmor vs SELinux**

- **AppArmor** usa perfiles de control obligatorio de acceso, es relativamente fácil de leer y modificar y resulta habitual en Debian/Ubuntu.
- **SELinux** usa etiquetas y políticas detalladas, está muy integrado en distribuciones Red Hat y puede ser más complejo de depurar.
- Esta implementación combina Debian con AppArmor porque encaja mejor con su modelo de paquetes y administración.

**UFW vs firewalld**

- **UFW** es una interfaz sencilla para nftables/iptables con reglas claras de permitir y denegar.
- **firewalld** gestiona zonas dinámicas y reglas temporales o permanentes, y es común en Rocky Linux.
- UFW es adecuado para Debian cuando se necesita un conjunto pequeño y predecible de reglas.

**VirtualBox vs UTM**

- **VirtualBox** está disponible en varias plataformas y facilita el flujo gráfico habitual de los proyectos de 42.
- **UTM** es especialmente práctico en Apple Silicon y ofrece integración específica con macOS.
- Esta guía utiliza VirtualBox por su compatibilidad, gestión de instantáneas y herramientas de red ampliamente conocidas.

## Diseño de particiones y seguridad

La configuración del disco sigue los requisitos del subject y utiliza un volumen físico cifrado, LVM y volúmenes lógicos separados cuando sea necesario. LVM facilita modificar el tamaño de los volúmenes sin modificar el sistema anfitrión.

El servidor aplica además:

- Política de contraseñas fuertes y expiración de cuentas.
- Restricciones y registro de acciones mediante `sudo`.
- Autenticación SSH mediante contraseña o clave, según el subject.
- Acceso SSH de root deshabilitado.
- Reglas de firewall limitadas a SSH y servicios autorizados.
- Paquetes mínimos y actualizaciones de seguridad periódicas.

## Usuarios y privilegios

La cuenta administrativa utiliza el login de 42 `vhedo-ga`. El hostname recomendado es `vhedo-ga42`, siguiendo la convención login más `42`.

Las tareas administrativas deben realizarse mediante `sudo`, no mediante una sesión root. La configuración de `sudo` debe limitar los comandos, exigir autenticación cuando corresponda y registrar su uso.

## SSH

SSH escucha en el puerto requerido por el subject de Born2beRoot. Antes de modificar SSH, conviene mantener abierta una sesión y probar una nueva conexión desde el host.

```bash
ssh vhedo-ga@<ip-del-servidor> -p <puerto-ssh>
sudo sshd -t
sudo systemctl status ssh
```

## Firewall

El firewall permite el puerto SSH y rechaza por defecto el tráfico entrante no solicitado.

```bash
# Debian / UFW
sudo ufw status verbose

# Rocky Linux / firewalld
sudo firewall-cmd --list-all
```

## Monitorización

Un script Bash recopila la información requerida: arquitectura y kernel, CPU físicas y virtuales, uso de RAM y disco, carga de CPU, último arranque, estado de LVM, información de red, usuarios activos y número de comandos ejecutados con `sudo`.

El script se programa mediante `cron` o se muestra mediante `wall` con el intervalo indicado por el subject.

```bash
ls /var/spool/cron
sudo crontab -l
df -h
free -m
lsblk
```

## Perspectiva de red teaming

Born2beRoot proporciona un entorno controlado para comprender vectores de ataque y controles defensivos. Un ejercicio defensivo de red teaming puede incluir:

- Auditar servicios expuestos y la configuración SSH.
- Comprobar contraseñas débiles y políticas de expiración.
- Revisar reglas `sudo` con privilegios excesivos.
- Verificar el bloqueo por defecto del firewall.
- Enumerar puertos abiertos y procesos innecesarios.
- Comprobar cifrado, registros, actualizaciones y políticas MAC.

Estas comprobaciones solo deben realizarse en esta máquina virtual propia o en sistemas con autorización explícita. El objetivo es validar las defensas, no explotar servicios externos.

## Instrucciones

### Requisitos previos

- VirtualBox o UTM
- ISO de Debian o Rocky Linux
- Archivo `en.subject.pdf`
- Acceso al host para redirección de puertos y pruebas SSH

### Instalación

1. Crear la máquina virtual y desactivar la instalación gráfica.
2. Crear las particiones, volúmenes LVM y cifrado requeridos.
3. Instalar el sistema base y configurar la red.
4. Crear `vhedo-ga`, los grupos necesarios y una política de contraseñas segura.
5. Configurar `sudo`, SSH y el firewall.
6. Añadir y programar el script de monitorización.
7. Reiniciar y verificar todos los requisitos.

### Verificación

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

### Lista de comprobación

- [ ] La máquina virtual arranca correctamente.
- [ ] El hostname `vhedo-ga42` está configurado.
- [ ] Se cumplen los requisitos de particiones, LVM y cifrado.
- [ ] La política de contraseñas y expiración está activa.
- [ ] `sudo` está restringido y registra las acciones.
- [ ] SSH usa el puerto requerido y el acceso root está deshabilitado.
- [ ] El firewall rechaza tráfico no solicitado.
- [ ] La monitorización se ejecuta con el intervalo requerido.
- [ ] La implementación puede explicarse durante la defensa.

> **Nota de seguridad:** Nunca subas contraseñas, contraseñas de cifrado, claves privadas ni información sensible.

## Recursos

- [Subject de Born2beRoot](en.subject.pdf)
- [Documentación de Debian](https://www.debian.org/doc/)
- [Documentación de Rocky Linux](https://docs.rockylinux.org/)
- [Documentación de OpenSSH](https://www.openssh.com/manual.html)
- [Documentación de AppArmor](https://documentation.ubuntu.com/server/how-to/security/apparmor/)
- [Proyecto SELinux](https://selinuxproject.org/page/Main_Page)
- [Documentación de UFW](https://help.ubuntu.com/community/UFW)
- [Documentación de firewalld](https://firewalld.org/documentation/)
- [Manual de VirtualBox](https://www.virtualbox.org/manual/)
- [Documentación de UTM](https://docs.getutm.app/)

### Uso de IA

La IA se utilizó para estructurar y mejorar este README, organizar los requisitos del subject en secciones claras y revisar la redacción. La IA no configuró la máquina virtual, modificó archivos del sistema, eligió ni proporcionó credenciales, ni verificó el comportamiento real del servidor. La configuración técnica final debe comprobarse y documentarse localmente por el autor.
