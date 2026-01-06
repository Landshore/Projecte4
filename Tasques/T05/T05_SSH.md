# 🔐 T05: Accés Remot. Connexió via SSH

**Prova de Concepte (PoC) - Base de Coneixement Interna**

---

## 📑 Taula de Continguts

1. [Introducció](#introducció)
2. [Entorn de Treball](#entorn-de-treball)
3. [Part 1: Servidor SSH a Ubuntu](#part-1-servidor-ssh-a-ubuntu)
4. [Part 2: Configuració de Seguretat](#part-2-configuració-de-seguretat)
5. [Part 3: Autenticació amb Claus SSH](#part-3-autenticació-amb-claus-ssh)
6. [Part 4: Servidor SSH a Windows](#part-4-servidor-ssh-a-windows)
7. [Verificació i Proves](#verificació-i-proves)
8. [Troubleshooting](#troubleshooting)
9. [Conclusions](#conclusions)

---

## 🎯 Introducció

Aquesta guia documenta el procés complet de configuració d'accés remot segur mitjançant SSH (Secure Shell). Està dissenyada com a **material de formació** per als futurs becaris de la consultora, que hauran de gestionar servidors remots de clients.

### Objectius d'Aprenentatge

✅ Instal·lar i configurar servidors SSH en Linux i Windows  
✅ Aplicar configuracions de seguretat avançades  
✅ Implementar autenticació amb claus públiques/privades  
✅ Establir connexions SSH bidireccionals  
✅ Verificar la seguretat de les connexions  

---

## 🖥️ Entorn de Treball

### Configuració de les Màquines Virtuals

| Component | Sistema Operatiu | IP | Usuari Principal |
|-----------|-----------------|-----|------------------|
| **Servidor Linux** | Ubuntu (Zorin OS) | 192.168.56.104 | vboxuser |
| **Client Windows** | Windows 10/11 | 192.168.56.103 | Damian |

### Verificació de Connectivitat

Abans de començar, comprovem que les màquines es veuen:
```bash
# Des d'Ubuntu
ip addr show
ping 192.168.56.103
```

![Verificació IP Ubuntu](image1.png)

---

## 📦 Part 1: Servidor SSH a Ubuntu

### 1.1. Instal·lació d'OpenSSH Server

Primer, instal·lem el servidor SSH:
```bash
sudo apt install openssh-server
```

![Instal·lació OpenSSH Server](image2.png)

### 1.2. Verificació de l'Estat del Servei

Comprovem si el servei està actiu:
```bash
sudo systemctl status ssh
```

**Important:** Si el servei no està actiu (no mostra "active (running)" en verd), cal iniciar-lo.

![Estat inicial del servei SSH](image3.png)

### 1.3. Solucionar Errors de Configuració

Si el servei falla en arrencar, cal verificar errors de configuració:
```bash
sudo sshd -t
```

**Error comú trobat:** `ChallengeResponsiveAuthentication` (línia 68)

**Solució:** Editar `/etc/ssh/sshd_config` i corregir a `ChallengeResponseAuthentication`
```bash
sudo nano /etc/ssh/sshd_config
```

![Error de configuració detectat](image4.png)

### 1.4. Iniciar el Servei

Un cop corregits els errors:
```bash
sudo systemctl start ssh
sudo systemctl enable ssh
sudo systemctl status ssh
```

**Resultat esperat:** `active (running)` en verd ✅

![Servei SSH actiu](image5.png)

---

## 🔒 Part 2: Configuració de Seguretat

### 2.1. Editar la Configuració SSH
```bash
sudo nano /etc/ssh/sshd_config
```

### 2.2. Configuracions Aplicades

Modifiquem aquestes línies per millorar la seguretat:
```bash
# Temps de gràcia per login
LoginGraceTime 120

# Denegar accés root remot
PermitRootLogin no

# Modes estrictes
StrictModes yes

# Restringir usuaris autoritzats
AllowUsers vboxuser

# Autenticació RSA i amb clau pública
RSAAuthentication yes
PubkeyAuthentication yes
```

![Configuració sshd_config](image6.png)

### 2.3. Reiniciar el Servei

Després de modificar la configuració:
```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

![Reinici del servei SSH](image7.png)

---

## 🔐 Part 3: Primera Connexió i Proves de Seguretat

### 3.1. Connexió des de Windows

Des de **PowerShell** a Windows:
```powershell
ssh vboxuser@192.168.56.104
```

**Primera connexió - Verificació del Fingerprint:**
```
The authenticity of host '192.168.56.104' can't be established.
ECDSA key fingerprint is SHA256:...
Are you sure you want to continue connecting (yes/no)?
```

➡️ Escrivim **yes** i premem Enter

![Primera connexió SSH des de Windows](image8.png)

**Què passa aquí?**
- El servidor envia la seva clau pública
- Windows calcula el fingerprint (empremta digital)
- Ens demana confirmació per protegir contra Man-in-the-Middle
- La clau es guarda a `C:\Users\Damian\.ssh\known_hosts`

### 3.2. Afegir Seguretat al Servidor

#### Configurar Contrasenya de Root

A Ubuntu:
```bash
sudo passwd root
```

![Configuració contrasenya root](image9.png)

#### Crear Usuaris Addicionals

Creem dos usuaris per provar les restriccions:
```bash
sudo adduser usuario1
sudo adduser usuario2
```

![Creació d'usuaris](image10.png)

### 3.3. Provar Restriccions d'Accés

#### Prova 1: Accés Root Remot (ha de fallar ❌)

Des de Windows:
```powershell
ssh root@192.168.56.104
```

**Resultat esperat:** `Permission denied`

![Root denegat remotament](image11.png)

**Explicació:** `PermitRootLogin no` bloqueja l'accés root via SSH (seguretat)

#### Prova 2: Accés Root Local (ha de funcionar ✅)

A Ubuntu:
```bash
su root
```

![Root funciona localment](image12.png)

**Explicació:** Root pot fer login localment però NO remotament

#### Prova 3: Usuaris No Autoritzats (han de fallar ❌)

Des de Windows:
```powershell
ssh usuario1@192.168.56.104
ssh usuario2@192.168.56.104
```

**Resultat esperat:** `Permission denied` per ambdós

![Usuaris no autoritzats denegats](image13.png)

**Explicació:** `AllowUsers vboxuser` només permet aquest usuari

---

## 🔑 Part 4: Autenticació amb Claus SSH

### 4.1. Generar Parell de Claus a Windows

Des de **PowerShell**:
```powershell
ssh-keygen -t ed25519
```

Premem **Enter** 3 vegades (ubicació i passphrase per defecte)

![Generació de claus SSH](image14.png)

**Resultat:**
- Clau **privada**: `C:\Users\Damian\.ssh\id_ed25519` (SECRETA)
- Clau **pública**: `C:\Users\Damian\.ssh\id_ed25519.pub` (compartir)

### 4.2. Copiar Clau Pública al Servidor

#### Mètode: Copiar Manualment

**A Windows, mostrem la clau pública:**
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

**Copiem tot el text** (comença amb `ssh-ed25519 AAA...`)

![Clau pública generada](image15.png)

**A Ubuntu, creem el directori i fitxer:**
```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

Enganxem la clau pública, guardem (Ctrl+O, Enter, Ctrl+X)

**Permisos correctes:**
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### 4.3. Provar Connexió sense Contrasenya

Des de Windows:
```powershell
ssh vboxuser@192.168.56.104
```

**NO hauria de demanar contrasenya!** ✅

![Connexió sense contrasenya](image16.png)

**Explicació:** El servidor verifica la clau privada del client amb la clau pública guardada a `authorized_keys`

---

## 🪟 Part 5: Servidor SSH a Windows

### 5.1. Instal·lar OpenSSH Server

**Pas 1:** Obrir **Configuració** → **Aplicacions** → **Característiques opcionals**

**Pas 2:** Clicar **"Agregar una característica"**

**Pas 3:** Cercar i seleccionar **"Servidor OpenSSH"** (OpenSSH Server)

![Cercar característiques opcionals](image17.png)

**Pas 4:** Instal·lar

![Instal·lació OpenSSH Server a Windows](image18.png)

### 5.2. Iniciar el Servei SSH

Obrir **PowerShell com a Administrador**:
```powershell
Start-Service sshd
```

![Inici del servei SSH a Windows](image19.png)

**Configurar inici automàtic:**
```powershell
Set-Service -Name sshd -StartupType 'Automatic'
```

**Verificar estat:**
```powershell
Get-Service sshd
```

**Resultat esperat:** `Status: Running` ✅

### 5.3. Connectar des d'Ubuntu cap a Windows

A Ubuntu:
```bash
ssh Damian@192.168.56.103
```

**Primera connexió:** Acceptar fingerprint (`yes`)

![Connexió des d'Ubuntu cap a Windows](image20.png)

**Comprovar que estem a Windows:**
```cmd
hostname
```

![Terminal de Windows des d'Ubuntu](image21.png)

---

## ✅ Verificació i Proves

### Checklist de Tasques Completades

- [x] OpenSSH Server instal·lat a Ubuntu
- [x] Errors de configuració corregits (`ChallengeResponseAuthentication`)
- [x] Servei SSH actiu i funcionant
- [x] Configuració `/etc/ssh/sshd_config` aplicada:
  - [x] `PermitRootLogin no`
  - [x] `AllowUsers vboxuser`
- [x] Root denegat remotament, funciona localment ✅
- [x] Usuaris no autoritzats (usuario1, usuario2) denegats ✅
- [x] Claus SSH generades (ed25519)
- [x] Autenticació sense contrasenya funciona ✅
- [x] OpenSSH Server instal·lat a Windows
- [x] Servei SSH actiu a Windows
- [x] Connexió bidireccional Ubuntu ↔ Windows funciona ✅

### Comandes de Verificació Ràpida

**A Ubuntu:**
```bash
sudo systemctl status ssh
ip addr show
cat ~/.ssh/authorized_keys
```

**A Windows:**
```powershell
Get-Service sshd
ssh vboxuser@192.168.56.104
```

---

## 🛠️ Troubleshooting

### Problema 1: "Job for ssh.service failed"

**Causa:** Error de configuració a `/etc/ssh/sshd_config`

**Solució:**
```bash
sudo sshd -t  # Mostra errors específics
sudo nano /etc/ssh/sshd_config  # Corregir
sudo systemctl restart ssh
```

**Error comú trobat:** Línia 68 amb `ChallengeResponsiveAuthentication` en lloc de `ChallengeResponseAuthentication`

---

### Problema 2: "Permission denied" per root

**Causa:** `PermitRootLogin no` a la configuració (això és correcte per seguretat!)

**Solució:** NO modificar això. Root NO ha de poder connectar-se remotament. Utilitzar un usuari normal i després `sudo` o `su root`.

---

### Problema 3: Usuaris no poden connectar

**Causa:** No estan a la llista `AllowUsers`

**Solució:**
```bash
sudo nano /etc/ssh/sshd_config
# Afegir usuaris a la línia AllowUsers
AllowUsers vboxuser usuario1
sudo systemctl restart ssh
```

---

### Problema 4: Connexió sense clau no funciona

**Causa:** Clau pública no copiada correctament o permisos incorrectes

**Solució:**
```bash
# Verificar contingut
cat ~/.ssh/authorized_keys

# Corregir permisos
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### Problema 5: Windows no pot connectar a Ubuntu

**Causa:** Firewall bloquejant el port 22

**Solució:**
```bash
sudo ufw allow 22/tcp
sudo ufw reload
```

---

## 🎓 Conclusions

### Què Hem Après

En aquesta Prova de Concepte hem documentat:

✅ **Instal·lació i configuració** de servidors SSH en Linux i Windows  
✅ **Resolució d'errors** de configuració comuns  
✅ **Configuració de seguretat avançada:** denegar root, restringir usuaris  
✅ **Diferència entre accés local i remot** per usuaris privilegiats  
✅ **Autenticació amb claus públiques/privades** (més segur que contrasenyes)  
✅ **Connexions bidireccionals** Ubuntu ↔ Windows  
✅ **Verificació de restriccions** d'usuaris  

### Bones Pràctiques Aplicades

🔒 **Denegar accés root remot** (`PermitRootLogin no`)  
👥 **Restringir usuaris autoritzats** (`AllowUsers vboxuser`)  
🔑 **Utilitzar claus SSH** en lloc de contrasenyes  
✅ **Verificar fingerprints** en la primera connexió  
🛡️ **Corregir errors de configuració** abans d'activar el servei  

### Diferències Clau: Telnet vs SSH

| Característica | Telnet | SSH |
|----------------|--------|-----|
| **Xifrat** | ❌ NO | ✅ SÍ |
| **Port** | 23 | 22 |
| **Seguretat** | Nul·la | Alta |
| **Ús actual** | Obsolet | Estàndard |

**Conclusió:** SEMPRE utilitzar SSH, mai Telnet en producció.

### Properes Passes Recomanades

Per continuar millorant la seguretat:

- Implementar **Fail2Ban** (bloquejar IPs amb intents fallits)
- Canviar el **port SSH** (22 → altre port per evitar bots)
- Desactivar completament **PasswordAuthentication** (només claus)
- Configurar **autenticació de dos factors (2FA)**
- Implementar **logs centralitzats** per monitoratge

---

## 📚 Referències

### Documentació Oficial

- [OpenSSH Official Documentation](https://www.openssh.com/)
- [Ubuntu Server Guide - OpenSSH](https://ubuntu.com/server/docs/service-openssh)
- [Microsoft Docs - OpenSSH in Windows](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview)

