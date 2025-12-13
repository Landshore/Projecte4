```markdown
# 🔐 T05: Accés Remot - Connexió via SSH

---

## 🖥️ Entorn de Treball

### Configuració de Xarxa

| Màquina | Sistema Operatiu | IP | Usuari Principal |
|---------|------------------|-----|------------------|
| Servidor | Ubuntu (Zorin OS) | 192.168.56.104 | vboxuser |
| Client | Windows 11 | 192.168.56.103 | Damian |

---

## 1️⃣ Instal·lació OpenSSH Server a Ubuntu

Instal·lem el servidor SSH a Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server
```

![Instal·lació OpenSSH Server](./img/image1.png)

Comprovem que el servei SSH s'està executant:

```bash
sudo systemctl status ssh
```

![Estat del servei SSH](./img/image2.png)

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

![Activació del servei SSH](./img/image3.png)

---

## 2️⃣ Configuració de Seguretat

Editem el fitxer de configuració d'SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

Deshabilitem accés root remot:

```bash
PermitRootLogin no
```

![Configuració PermitRootLogin](./img/image4.png)

Restringim usuaris permesos:

```bash
AllowUsers vboxuser
```

![Configuració AllowUsers](./img/image5.png)

Reiniciem el servei SSH:

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

![Verificació després del reinici](./img/image6.png)

---

## 3️⃣ Gestió d'Usuaris i Restriccions

Assignem contrasenya a root:

```bash
sudo passwd root
```

![Configuració contrasenya root](./img/image9.png)

Connexió SSH des de Windows cap a Ubuntu:

```powershell
ssh vboxuser@192.168.56.104
```

![Primera connexió SSH des de Windows](./img/image7.png)

![Connexió exitosa](./img/image8.png)

Creem usuaris de prova:

```bash
sudo adduser usuario1
sudo adduser usuario2
```

![Creació d'usuaris](./img/image10.png)

![Assignació contrasenyes](./img/image11.png)

### Proves de Restricció d'Accés

**Prova 1: Accés Root (denegat remotament)**

```powershell
ssh root@192.168.56.104
```

![Accés root denegat remotament](./img/image14.png)

![Accés root local permès](./img/image15.png)

**Prova 2: Usuari Permès (vboxuser)**

```powershell
ssh vboxuser@192.168.56.104
```

![Accés vboxuser permès](./img/image16.png)

**Prova 3: Usuari No Permès (usuario2)**

```powershell
ssh usuario2@192.168.56.104
```

![Accés usuario2 denegat](./img/image17.png)

---

## 4️⃣ Autenticació amb Claus SSH

Generem parella de claus al client Windows:

```powershell
ssh-keygen -t ed25519
```

![Generació de claus SSH](./img/image18.png)

Copiem la clau pública al servidor:

```powershell
ssh-copy-id vboxuser@192.168.56.104
```

![Còpia de clau pública](./img/image19.png)

Connexió sense contrasenya:

```powershell
ssh vboxuser@192.168.56.104
```

![Connexió sense contrasenya](./img/image20.png)

---

## 5️⃣ Configuració SSH al Client Windows

Instal·lem OpenSSH Server:

**Configuració de Windows** → **Aplicacions** → **Funcionalitats opcionals** → **OpenSSH Server**

![Instal·lació OpenSSH Server a Windows](./img/image21.png)

PowerShell com a administrador:

```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
Get-Service sshd
```

![Configuració servei SSH Windows](./img/image12.png)

![Verificació regles tallafoc](./img/image13.png)

---

## 6️⃣ Connexió Bidireccional

Connexió des d'Ubuntu cap a Windows:

```bash
ssh Damian@192.168.56.103
```

![Connexió Ubuntu → Windows](./img/image4.png)

![Acceptació clau servidor Windows](./img/image5.png)

---

## 🎯 Conclusions

**Autor:** Damian  
**Curs:** CFGM - Serveis de Xarxa  
**Tasca:** T05 - Accés Remot via SSH  
**Data:** 13 de desembre de 2025
```
