### README.md

```markdown
# T05: Accés Remot. Connexió via SSH

**Breu introducció**  
Aquest repositori conté la prova de concepte (PoC) T05 per a la documentació interna sobre gestió remota segura de servidors mitjançant SSH. L'objectiu és crear una base de coneixement per a futurs interns i membres de l'equip sobre instal·lació, configuració, autenticació i bones pràctiques.

**Guia principal**  
Consulteu la guia completa: [T05_SSH.md](./T05_SSH.md)

## Estructura del projecte
- `T05_SSH.md` — Guia completa i pas a pas (català).  
- `img/` — Captures de pantalla i imatges de suport referenciades a la guia (01.png, 02.png, ...).  
- `README.md` — Resum del projecte i enllaç a la guia principal.

## Resum de continguts
- Instal·lació d'OpenSSH (Ubuntu i Windows)  
- Configuració segura de `sshd_config`  
- Gestió d'usuaris i restriccions d'accés  
- Autenticació amb claus (ed25519) i connexions sense contrasenya  
- Túnels SSH i proxy SOCKS per a navegació segura  
- Verificació de xifratge amb Wireshark  
- Bones pràctiques i resolució d'incidències

**Autor:** Damian  
**Data:** 13 de desembre de 2025
```

---

### T05_SSH.md

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
Aquest document és una PoC interna destinada a futurs interns i membres de l'equip. Proporciona instruccions pas a pas per a la instal·lació, configuració i verificació d'un entorn SSH segur entre una màquina Ubuntu (Zorin OS) i una màquina Windows. Inclou exemples de configuració, captures de pantalla de referència i procediments de comprovació.

---

## Entorn de xarxa i objectiu 🌐
- **Ubuntu (Zorin OS)**: `192.168.56.104`, usuari: `vboxuser`  
- **Windows**: `192.168.56.103`, usuari: `Damian`  

**Objectiu:** Configurar connexions SSH segures, habilitar autenticació per claus, restringir accés d'usuaris i demostrar túnels i proxy SOCKS per a navegació xifrada.

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
**Explicació:** `openssh-server` proporciona el servei `sshd`. `systemctl status ssh` mostra l'estat i missatges d'error si n'hi ha.

**Captura:**  
![Instal·lació OpenSSH a Ubuntu](./img/01.png)

### Windows (OpenSSH Server opcional)
- A Windows, afegir la funcionalitat opcional **OpenSSH Server** des de "Optional features" o instal·lar via PowerShell.
```powershell
# Executar PowerShell com a administrador
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
Get-Service sshd
```
**Captura:**  
![Instal·lació OpenSSH a Windows](./img/02.png)

---

## 2. Configuració de `sshd_config` ⚙️

Editar `/etc/ssh/sshd_config` a Ubuntu (i l'equivalent a Windows si cal):

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo nano /etc/ssh/sshd_config
```

**Canvis recomanats (exemples):**
```text
# Port per defecte
Port 22

# No permetre login directe de root per password
PermitRootLogin no

# Permetre només usuaris específics
AllowUsers vboxuser

# Temps per fer login
LoginGraceTime 120

# Autenticació per claus
RSAAuthentication yes
PubkeyAuthentication yes

# Altres opcions de seguretat
StrictModes yes
#MaxAuthTries 6
#MaxSessions 10
```

Després de modificar:
```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

**Explicació:** `PermitRootLogin no` evita que l'usuari `root` s'hi autentiqui remotament amb contrasenya. `AllowUsers` limita l'accés només als usuaris indicats.

**Captura:**  
![Configuració sshd_config](./img/03.png)

---

## 3. Verificació d'accés root 🔒

- **Local (a la màquina Ubuntu):**
```bash
su root
# o
sudo -i
```
- **Remot (des de Windows):**
```powershell
ssh root@192.168.56.104
```
**Resultat esperat:** L'accés remot com a `root` ha de ser denegat (si `PermitRootLogin no` està activat), mentre que l'accés local amb `su` o `sudo` ha de funcionar.

**Captura:**  
![Intent d'accés root remot denegat](./img/04.png)

---

## 4. Proves de restricció d'usuaris 🚫

Afegir un usuari de prova i comprovar que no pot accedir si no està a `AllowUsers`.

```bash
sudo useradd -m -s /bin/bash usuari2
sudo passwd usuari2
```

Prova de connexió:
```powershell
ssh usuari2@192.168.56.104
# Esperat: Permission denied (si no està a AllowUsers)
```

**Captura:**  
![Usuari denegat per AllowUsers](./img/05.png)

---

## 5. Generació de claus SSH (ed25519) 🔑

A Windows (usuari `Damian`) generar parell de claus ed25519:

```powershell
ssh-keygen -t ed25519
# Acceptar ruta per defecte: C:\Users\Damian\.ssh\id_ed25519
# Opcional: afegir passphrase
```

**Captura:**  
![Generació de claus ed25519 a Windows](./img/06.png)

---

## 6. Autenticació sense contrasenya 🔐

Copiar la clau pública al servidor Ubuntu (usuari `vboxuser`):

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh vboxuser@192.168.56.104 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
```

Prova de connexió sense contrasenya:
```powershell
ssh vboxuser@192.168.56.104
# Ha de permetre l'accés sense demanar contrasenya si la clau és correcta
```

**Captura:**  
![Connexió sense contrasenya](./img/07.png)

**Notes de seguretat:** Protegir la clau privada (`~/.ssh/id_ed25519`) i, si és possible, utilitzar passphrase i `ssh-agent`.

---

## 7. Connexions bidireccionals (Ubuntu ↔ Windows) 🔁

- Des de Windows a Ubuntu:
```powershell
ssh vboxuser@192.168.56.104
```
- Des de Ubuntu a Windows (si Windows té OpenSSH Server en execució):
```bash
ssh Damian@192.168.56.103
```

Assegureu-vos que el servei `sshd` a Windows està en estat `Running` i que el tallafocs permet connexions al port 22.

**Captura:**  
![Connexió Ubuntu a Windows](./img/08.png)

---

## 8. Túnel SSH (Dynamic Port Forwarding -D) 🧭

Crear un túnel dinàmic (proxy SOCKS) des de Windows cap a Ubuntu o viceversa. Exemple des de Windows per navegar a través d'Ubuntu:

```powershell
ssh -D 1080 -N -f vboxuser@192.168.56.104
```

- `-D 1080` crea un proxy SOCKS a `localhost:1080`.  
- `-N` no executa cap comanda remota (només túnel).  
- `-f` envia el procés al fons.

**Captura:**  
![Túnel SSH -D creat](./img/09.png)

---

## 9. Configuració proxy SOCKS al navegador 🧩

Configurar el navegador per utilitzar el proxy SOCKS5 a `localhost:1080`.

**Firefox (exemple):**
1. Preferències → Xarxa → Configuració de connexió.  
2. Seleccionar "Configuració manual del proxy".  
3. Proxy SOCKS Host: `127.0.0.1`, Port: `1080`.  
4. Seleccionar SOCKS v5 i activar "Proxy DNS when using SOCKS v5" si cal.

**Captura:**  
![Configuració proxy al navegador](./img/10.png)

---

## 10. Verificació del xifratge amb Wireshark 🔍

Per verificar que el trànsit SSH està xifrat:
1. Iniciar captura a la interfície de xarxa amb Wireshark.  
2. Filtrar per `tcp.port == 22` o `ssh`.  
3. Observar que el contingut de la sessió no és llegible (paquets xifrats).  

**Comprovació bàsica:**
- No veureu dades HTTP/cleartext dins la sessió SSH.  
- Si s'utilitza túnel SOCKS, el trànsit cap a destinacions externes estarà encapsulat dins SSH.

**Captura:**  
![Wireshark - trànsit SSH xifrat](./img/11.png)

---

## Bones pràctiques de seguretat 🛡️
- **Desactivar `PermitRootLogin`** i utilitzar `sudo` per a tasques administratives.  
- **Limitar usuaris** amb `AllowUsers` o `AllowGroups`.  
- **Usar claus públiques** (ed25519 o rsa 4096) en lloc de contrasenyes.  
- **Protegir claus privades** amb passphrase i `ssh-agent`.  
- **Canviar el port per defecte** només si és necessari (no substitueix altres controls).  
- **Habilitar autenticació de dos factors** si és possible (e.g., `google-authenticator` + SSH).  
- **Auditar logs** (`/var/log/auth.log`) i configurar alertes per intents fallits.  
- **Actualitzar el sistema** i OpenSSH regularment.

---

## Resolució d'incidències (Troubleshooting) 🧰

### `ssh.service` falla a iniciar
```bash
sudo systemctl status ssh
sudo journalctl -xeu ssh
sudo /usr/sbin/sshd -t
```
- Comproveu errors de sintaxi a `/etc/ssh/sshd_config`.  
- Restaurar còpia de seguretat: `sudo cp /etc/ssh/sshd_config.bak /etc/ssh/sshd_config`.

### `Permission denied (publickey, password)`
- Assegureu-vos que la clau pública està a `~/.ssh/authorized_keys` i permisos correctes:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
- Comproveu que el fitxer `sshd_config` permet `PubkeyAuthentication yes`.

### Accés root remot encara possible
- Verifiqueu que no hi ha regles específiques a `sshd_config` que sobreescriguin `PermitRootLogin`.  
- Reinicieu el servei després de canvis: `sudo systemctl restart ssh`.

### Problemes amb túnel SOCKS
- Assegureu-vos que el túnel està actiu (`ps aux | grep ssh`) i que el port local (1080) està escoltant (`netstat -tulpen | grep 1080`).

---

## Verificacions finals ✅
- `ssh vboxuser@192.168.56.104` → connexió amb clau (sense contrasenya).  
- `ssh root@192.168.56.104` → ha de ser denegat remotament.  
- `ssh usuari2@192.168.56.104` → denegat si no està a `AllowUsers`.  
- Navegació a través del proxy SOCKS i comprovació amb Wireshark que el trànsit està xifrat.

---

## Conclusions 📎
Aquest PoC documenta un flux segur i repetible per a la gestió remota mitjançant SSH: instal·lació, configuració segura, autenticació per claus, restricció d'usuaris i ús de túnels per a navegació xifrada. És una base per a la formació d'interns i per a la creació d'un procediment operatiu estàndard (SOP) intern.

---

## Peu de pàgina 📝
**Autor:** Damian  
**Data:** 13 de desembre de 2025  
**Curs / Tasca:** T05 — Accés Remot. Connexió via SSH
```

