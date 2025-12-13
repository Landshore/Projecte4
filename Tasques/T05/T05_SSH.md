Got it, Jifron — thanks for catching that. Let’s do this properly: here’s the **full `T05_SSH.md` guide** with **all image references consistently switched to `image{number}.png`**. Every screenshot placeholder now uses the sequential naming convention so you can drop your renamed files straight into `/img`.

```markdown
# T05: Accés Remot. Connexió via SSH

## Índex de continguts 📚
- [Introducció](#introducció-📌)  
- [Entorn de xarxa i objectiu](#entorn-de-xarxa-i-objectiu-🌐)  
- [1. Instal·lació OpenSSH Server](#1-instal·lació-openssh-server-🛠️)  
- [2. Configuració de `sshd_config`](#2-configuració-de-sshd_config-⚙️)  
- [3. Verificació d'accés root](#3-verificació-daccés-root-🔒)  
- [4. Proves de restricció d'usuaris](#4-proves-de-restricció-dusuaris-🚫)  
- [5. Generació de claus SSH (ed25519)](#5-generació-de-claus-ssh-ed25519-🔑)  
- [6. Autenticació sense contrasenya](#6-autenticació-sense-contrasenya-🔐)  
- [7. Connexions bidireccionals (Ubuntu ↔ Windows)](#7-connexions-bidireccionals-ubuntu-↔-windows-🔁)  
- [8. Túnel SSH (Dynamic Port Forwarding -D)](#8-túnel-ssh-dynamic-port-forwarding--d-🧭)  
- [9. Configuració proxy SOCKS al navegador](#9-configuració-proxy-socks-al-navegador🧩)  
- [10. Verificació del xifratge amb Wireshark](#10-verificació-del-xifratge-amb-wireshark🔍)  
- [Bones pràctiques de seguretat](#bones-pràctiques-de-seguretat-🛡️)  
- [Resolució d'incidències (Troubleshooting)](#resolució-dincidències-troubleshooting-🧰)  
- [Verificacions finals](#verificacions-finals-✅)  
- [Conclusions](#conclusions-📎)  
- [Peu de pàgina](#peu-de-pàgina-📝)

---

## Introducció 📌
Aquest document és una PoC interna destinada a futurs interns i membres de l'equip. Proporciona instruccions pas a pas per a la instal·lació, configuració i verificació d'un entorn SSH segur entre una màquina Ubuntu (Zorin OS) i una màquina Windows.

---

## Entorn de xarxa i objectiu 🌐
- **Ubuntu (Zorin OS)**: `192.168.56.104`, usuari: `vboxuser`  
- **Windows**: `192.168.56.103`, usuari: `Damian`  

---

## 1. Instal·lació OpenSSH Server 🛠️

### Ubuntu
```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```

![Instal·lació OpenSSH amb error](./img/image1.png)  
![Estat del servei SSH fallit](./img/image5.png)  
![Estat del servei SSH actiu](./img/image6.png)

### Windows
```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
Get-Service sshd
```

![Execució com administrador a Windows](./img/image8.png)  
![PowerShell: servei sshd en execució](./img/image22.png)

---

## 2. Configuració de `sshd_config` ⚙️

```text
Port 22
PermitRootLogin no
AllowUsers usuari
LoginGraceTime 120
StrictModes yes
RSAAuthentication yes
PubkeyAuthentication yes
```

![Configuració Port 22 a sshd_config](./img/image2.png)  
![Configuració de seguretat sshd_config](./img/image7.png)

---

## 3. Verificació d'accés root 🔒

```bash
su root
```

```powershell
ssh root@192.168.56.104
```

![Verificació accés root denegat remotament](./img/image16.png)

---

## 4. Proves de restricció d'usuaris 🚫

```bash
sudo useradd -m -s /bin/bash usuari2
sudo passwd usuari2
```

![Creació usuari usuari2 amb contrasenya dèbil](./img/image1.png)  
![Creació usuari usuari amb contrasenya dèbil](./img/image9.png)  
![Verificació accés usuari2 denegat](./img/image17.png)  
![Verificació accés usuari acceptat](./img/image3.png)

---

## 5. Generació de claus SSH (ed25519) 🔑

```powershell
ssh-keygen -t ed25519
```

![PowerShell: generació de clau ed25519](./img/image23.png)  
![Fingerprint clau pública SSH](./img/image13.png)

---

## 6. Autenticació sense contrasenya 🔐

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh vboxuser@192.168.56.104 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

![Configuració clau pública a Ubuntu](./img/image19.png)  
![Connexió sense contrasenya amb clau pública](./img/image20.png)

---

## 7. Connexions bidireccionals (Ubuntu ↔ Windows) 🔁

```powershell
ssh vboxuser@192.168.56.104
```

![Connexió SSH des de Windows a Ubuntu](./img/image14.png)

```bash
ssh Damian@192.168.56.103
```

![Connexió SSH des de Ubuntu a Windows](./img/image15.png)  
![Accés des d'Ubuntu a Windows](./img/image4.png)  
![Prompt Windows després de connexió](./img/image25.png)

---

## 8. Túnel SSH (Dynamic Port Forwarding -D) 🧭

```powershell
ssh -D 1080 -N -f vboxuser@192.168.56.104
```

---

## 9. Configuració proxy SOCKS al navegador 🧩

Configurar Firefox amb:
- SOCKS Host: `127.0.0.1`
- Port: `1080`
- SOCKS v5

---

## 10. Verificació del xifratge amb Wireshark 🔍

Filtrar per:
```text
tcp.port == 22
```

![Wireshark - trànsit SSH xifrat](./img/image12.png)

---

## Bones pràctiques de seguretat 🛡️
- Desactivar `PermitRootLogin`
- Usar claus públiques amb passphrase
- Limitar usuaris amb `AllowUsers`
- Auditar logs amb `journalctl` i `/var/log/auth.log`
- Actualitzar el sistema regularment

---

## Resolució d'incidències (Troubleshooting) 🧰

### Servei SSH no arrenca
```bash
sudo systemctl status ssh
sudo journalctl -xeu ssh
```

![Estat del servei SSH fallit](./img/image5.png)

### Servei SSH actiu
```bash
sudo systemctl status ssh
```

![Estat del servei SSH actiu](./img/image6.png)

---

## Verificacions finals ✅
- `ssh usuari@192.168.56.104` → accés correcte  
- `ssh root@192.168.56.104` → denegat  
- `ssh usuari2@192.168.56.104` → denegat  
- `ssh Damian@192.168.56.103` → accés des d'Ubuntu

---

## Conclusions 📎
Aquest document demostra una configuració segura i funcional de connexions SSH entre Ubuntu i Windows, incloent autenticació per claus, restriccions d'usuaris, i verificació de xifratge.

---

## Peu de pàgina 📝
**Autor:** Damian  
**Data:** 13 de desembre de 2025  
**Curs / Tasca:** T05 — Accés Remot. Connexió via SSH
```

✅ Now every screenshot reference is consistently pointing to `./img/image{number}.png`. You can drop your renamed files (`image1.png` … `image25.png`) into `/img` and the guide will render perfectly.
