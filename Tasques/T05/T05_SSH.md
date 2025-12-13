# 🔐 T05: Accés Remot - Connexió via SSH

**Prova de Concepte (PoC) - Base de Coneixement Interna**

---

## 📋 Taula de Continguts

1. [Introducció](#introducció)
2. [Entorn de Treball](#entorn-de-treball)
3. [Instal·lació i Configuració SSH al Servidor Ubuntu](#1-installació-i-configuració-ssh-al-servidor-ubuntu)
4. [Configuració de Seguretat](#2-configuració-de-seguretat)
5. [Gestió d'Usuaris i Restriccions](#3-gestió-dusuaris-i-restriccions)
6. [Autenticació amb Claus SSH](#4-autenticació-amb-claus-ssh)
7. [Configuració SSH al Client Windows](#5-configuració-ssh-al-client-windows)
8. [Connexió Bidireccional](#6-connexió-bidireccional)
9. [Verificació i Bones Pràctiques](#7-verificació-i-bones-pràctiques)
10. [Resolució de Problemes](#8-resolució-de-problemes)
11. [Conclusions](#conclusions)

---

## 📖 Introducció

Aquest document serveix com a **guia oficial interna** per a la gestió remota segura de servidors mitjançant SSH (Secure Shell). Com a consultora tecnològica, l'accés remot als servidors dels nostres clients és essencial per a les nostres operacions diàries.

**Objectius del document:**
- Proporcionar una base de coneixement sòlida per a nous becaris
- Documentar procediments estàndard de connexió SSH
- Establir bones pràctiques de seguretat
- Facilitar la integració ràpida de nous membres a l'equip tècnic

**⚠️ Important:** Aquest és un document viu que s'actualitzarà amb noves millores i procediments.

---

## 🖥️ Entorn de Treball

### Configuració de Xarxa

| Màquina | Sistema Operatiu | IP | Usuari Principal |
|---------|------------------|-----|------------------|
| Servidor | Ubuntu (Zorin OS) | 192.168.56.104 | vboxuser |
| Client | Windows 11 | 192.168.56.103 | Damian |

### Requisits Previs
- Màquines virtuals configurades a la mateixa xarxa
- Connectivitat de xarxa verificada (ping)
- Permisos d'administrador/sudo

---

## 1️⃣ Instal·lació i Configuració SSH al Servidor Ubuntu

### 1.1 Instal·lació d'OpenSSH Server

Primer, instal·lem el servidor SSH a Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server
```

![Instal·lació OpenSSH Server](./img/image1.png)

### 1.2 Verificació de l'Estat del Servei

Comprovem que el servei SSH s'està executant correctament:

```bash
sudo systemctl status ssh
```

![Estat del servei SSH](./img/image2.png)

Si el servei no està actiu, l'iniciem i l'habilitem per a l'arrencada automàtica:

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

![Activació del servei SSH](./img/image3.png)

---

## 2️⃣ Configuració de Seguretat

### 2.1 Modificació del Fitxer de Configuració

El fitxer principal de configuració d'SSH es troba a `/etc/ssh/sshd_config`. Editarem aquest fitxer per millorar la seguretat:

```bash
sudo nano /etc/ssh/sshd_config
```

### 2.2 Paràmetres de Seguretat Crítics

**Deshabilitar accés root remot:**

Localitzem i modifiquem la línia:

```bash
PermitRootLogin no
```

![Configuració PermitRootLogin](./img/image4.png)

**Restringir usuaris permesos:**

Afegim la directiva per permetre només usuaris específics:

```bash
AllowUsers vboxuser
```

![Configuració AllowUsers](./img/image5.png)

### 2.3 Aplicar els Canvis

Després de modificar la configuració, reiniciem el servei SSH:

```bash
sudo systemctl restart ssh
```

Verifiquem que el servei s'ha reiniciat correctament:

```bash
sudo systemctl status ssh
```

![Verificació després del reinici](./img/image6.png)

---

## 3️⃣ Gestió d'Usuaris i Restriccions

### 3.1 Configuració de l'Usuari Root

Assignem una contrasenya segura a l'usuari root:

```bash
sudo passwd root
```

![Configuració contrasenya root](./img/image9.png)

### 3.2 Connexió Inicial des de Windows

Ara provem la connexió SSH des del client Windows cap a Ubuntu:

```powershell
ssh vboxuser@192.168.56.104
```

![Primera connexió SSH des de Windows](./img/image7.png)

Acceptem la clau del servidor en la primera connexió i introduïm la contrasenya:

![Connexió exitosa](./img/image8.png)

### 3.3 Creació d'Usuaris de Prova

Per verificar les restriccions d'accés, creem dos usuaris addicionals:

```bash
sudo adduser usuario1
sudo adduser usuario2
```

![Creació d'usuaris](./img/image10.png)

Assignem contrasenyes als nous usuaris:

![Assignació contrasenyes](./img/image11.png)

### 3.4 Proves de Restricció d'Accés

**Prova 1: Accés Root (Ha de fallar remotament)**

Des de Windows, intentem connectar-nos com a root:

```powershell
ssh root@192.168.56.104
```

![Accés root denegat remotament](./img/image14.png)

✅ **Resultat esperat:** Accés denegat per la configuració `PermitRootLogin no`

**Verificació local:** Root pot accedir localment al sistema

![Accés root local permès](./img/image15.png)

**Prova 2: Usuari Permès (vboxuser)**

```powershell
ssh vboxuser@192.168.56.104
```

![Accés vboxuser permès](./img/image16.png)

✅ **Resultat esperat:** Connexió exitosa

**Prova 3: Usuari No Permès (usuario2)**

```powershell
ssh usuario2@192.168.56.104
```

![Accés usuario2 denegat](./img/image17.png)

✅ **Resultat esperat:** Accés denegat per la configuració `AllowUsers vboxuser`

---

## 4️⃣ Autenticació amb Claus SSH

### 4.1 Generació de Parella de Claus

L'autenticació amb claus públiques/privades és més segura que les contrasenyes. Generem una parella de claus al client Windows:

```powershell
ssh-keygen -t ed25519
```

**Paràmetres:**
- `-t ed25519`: Utilitza l'algoritme Ed25519 (modern i segur)
- Ubicació per defecte: `C:\Users\Damian\.ssh\id_ed25519`
- Passphrase (opcional): Protecció addicional de la clau privada

![Generació de claus SSH](./img/image18.png)

### 4.2 Còpia de la Clau Pública al Servidor

Copiem la clau pública al servidor Ubuntu:

```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh vboxuser@192.168.56.104 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

O utilitzant la comanda específica:

```powershell
ssh-copy-id vboxuser@192.168.56.104
```

![Còpia de clau pública](./img/image19.png)

### 4.3 Verificació d'Autenticació sense Contrasenya

Ara podem connectar-nos sense introduir contrasenya:

```powershell
ssh vboxuser@192.168.56.104
```

![Connexió sense contrasenya](./img/image20.png)

✅ **Resultat:** Accés directe sense sol·licitar contrasenya

---

## 5️⃣ Configuració SSH al Client Windows

### 5.1 Instal·lació d'OpenSSH Server

Per permetre connexions SSH des d'Ubuntu cap a Windows, instal·lem OpenSSH Server:

1. **Configuració de Windows** → **Aplicacions** → **Funcionalitats opcionals**
2. **Afegir una funcionalitat**
3. Cercar i instal·lar **"OpenSSH Server"**

![Instal·lació OpenSSH Server a Windows](./img/image21.png)

### 5.2 Iniciar i Configurar el Servei

Obrim PowerShell com a administrador:

```powershell
# Iniciar el servei
Start-Service sshd

# Habilitar per a l'arrencada automàtica
Set-Service -Name sshd -StartupType 'Automatic'

# Verificar estat
Get-Service sshd
```

![Configuració servei SSH Windows](./img/image12.png)

### 5.3 Verificació de Regles de Tallafoc

Comprovem que el tallafoc permet connexions SSH:

```powershell
Get-NetFirewallRule -Name *ssh*
```

![Verificació regles tallafoc](./img/image13.png)

---

## 6️⃣ Connexió Bidireccional

### 6.1 Connexió des d'Ubuntu cap a Windows

Ara provem la connexió en sentit invers, des del servidor Ubuntu cap al client Windows:

```bash
ssh Damian@192.168.56.103
```

![Connexió Ubuntu → Windows](./img/image4.png)

En la primera connexió, acceptem la clau del servidor:

![Acceptació clau servidor Windows](./img/image5.png)

✅ **Resultat:** Connexió bidireccional funcionant correctament

---

## 7️⃣ Verificació i Bones Pràctiques

### ✅ Checklist de Seguretat

- [x] Accés root denegat remotament
- [x] Restriccions d'usuaris funcionals (AllowUsers)
- [x] Autenticació amb claus SSH configurada
- [x] Contrasenyes segures assignades
- [x] Tallafoc configurat adequadament
- [x] Servei SSH actiu i en arrencada automàtica

### 🔒 Bones Pràctiques Recomanades

1. **Canviar el port per defecte (22):** Redueix atacs automatitzats
2. **Deshabilitar autenticació per contrasenya:** Forçar ús de claus
3. **Implementar fail2ban:** Protecció contra atacs de força bruta
4. **Mantenir el sistema actualitzat:** `sudo apt update && sudo apt upgrade`
5. **Revisar logs regularment:** `/var/log/auth.log` per activitat SSH
6. **Utilitzar timeout de sessió:** Desconnexió automàtica per inactivitat

### 📊 Comandes Útils de Diagnòstic

```bash
# Veure connexions SSH actives
who

# Revisar últims accessos
last

# Logs d'autenticació SSH
sudo tail -f /var/log/auth.log

# Verificar configuració SSH
sudo sshd -T
```

---

## 8️⃣ Resolució de Problemes

### ❌ Problema: "Connection refused"

**Causa:** Servei SSH no actiu

**Solució:**
```bash
sudo systemctl start ssh
sudo systemctl status ssh
```

### ❌ Problema: "Permission denied (publickey)"

**Causa:** Clau pública no configurada correctament

**Solució:**
```bash
# Verificar permisos al servidor
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# Verificar contingut
cat ~/.ssh/authorized_keys
```

### ❌ Problema: "Network unreachable"

**Causa:** Problema de connectivitat

**Solució:**
```bash
# Verificar IP
ip addr show

# Test de connectivitat
ping 192.168.56.103
```

### ❌ Problema: Usuari no pot connectar-se

**Causa:** Restricció AllowUsers

**Solució:** Afegir l'usuari a `/etc/ssh/sshd_config`:
```bash
AllowUsers vboxuser usuari_nou
sudo systemctl restart ssh
```

---

## 🎯 Conclusions

Aquest document ha cobert els aspectes fonamentals de SSH per a la nostra consultora:

✅ **Configuració segura** d'OpenSSH Server en entorns Linux i Windows  
✅ **Hardening** del servidor amb restriccions d'accés  
✅ **Autenticació avançada** mitjançant claus públiques/privades  
✅ **Connexions bidireccionals** entre diferents sistemes operatius  
✅ **Bones pràctiques** de seguretat i manteniment  

### 📚 Pròxims Passos

Per a futurs becaris o ampliacions d'aquest document:
- Configuració de túnels SSH (Port Forwarding)
- Integració amb eines de monitoratge
- Automatització amb scripts
- Configuració d'autenticació de dos factors (2FA)

### 👤 Informació del Document

**Autor:** Damian  
**Curs:** CFGM - Serveis de Xarxa  
**Tasca:** T05 - Accés Remot via SSH  
**Data:** 13 de desembre de 2025  
**Versió:** 1.0

---

**📝 Nota Final:** Aquest document és una eina viva. Si detecteu millores o actualitzacions necessàries, contacteu amb l'equip tècnic per incorporar-les en futures versions.

**🔐 Recordatori de Seguretat:** Mai compartiu les vostres claus privades. Manteniu sempre actualitzats els vostres sistemes i seguiu les polítiques de seguretat de l'empresa.
