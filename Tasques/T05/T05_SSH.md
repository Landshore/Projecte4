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

Mirem l'estatus:

![Estat del servei SSH](./img/image2.png)

Fem una altra vegada:

![Activació del servei SSH](./img/image3.png)

---

## 2️⃣ Configuració de Seguretat

Anem a /etc/ssh/sshd_config i posem aquestes modificacions:

![Configuració PermitRootLogin](./img/image4.png)

![Configuració AllowUsers](./img/image5.png)

Ara mirem l'estatus:

![Verificació després del reinici](./img/image6.png)

---

## 3️⃣ Connexió des de Windows a Ubuntu

Entrem a Ubuntu des de Windows:

![Primera connexió SSH des de Windows](./img/image7.png)

![Connexió exitosa](./img/image8.png)

---

## 4️⃣ Afegir Seguretat al Servidor

Afegim seguretat al nostre server:

![Configuració seguretat](./img/image9.png)

Afegim contrasenya a root més altres modificacions:

![Configuració contrasenya root](./img/image10.png)

---

## 5️⃣ Gestió d'Usuaris

Creem dos nous usuaris:

![Creació d'usuaris](./img/image11.png)

![Assignació contrasenyes usuaris](./img/image12.png)

![Configuració usuario1](./img/image13.png)

![Configuració usuario2](./img/image14.png)

---

## 6️⃣ Proves d'Accés

El provem:

**ROOT**

![Prova accés root](./img/image15.png)

![Verificació accés root](./img/image16.png)

**USUARI**

![Prova accés usuari](./img/image17.png)

**USUARI2**

![Prova accés usuari2](./img/image18.png)

---

## 7️⃣ Autenticació amb Claus SSH

Generem un clau gen:

![Generació de claus SSH](./img/image19.png)

Copiem la clau a Ubuntu i provem sense contrasenya:

![Còpia de clau pública](./img/image20.png)

---

## 8️⃣ Configuració SSH al Client Windows

Cerquem funcionalitats opcionals i cliquem afegir una funcionalitat, "OpenSSH Server":

![Instal·lació OpenSSH Server a Windows](./img/image21.png)

---

## 9️⃣ Connexió Bidireccional

A PowerShell com a administrador començem a arrencar com a admin:

![Configuració servei SSH Windows](./img/image12.png)

![Verificació servei](./img/image13.png)

Des d'Ubuntu:

![Connexió Ubuntu → Windows](./img/image4.png)

![Acceptació clau servidor Windows](./img/image5.png)

---

## 🎯 Conclusions

**Autor:** Damian  
**Curs:** CFGM - Serveis de Xarxa  
**Tasca:** T05 - Accés Remot via SSH  
**Data:** 13 de desembre de 2025
