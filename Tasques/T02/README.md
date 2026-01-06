# T02 – DPR: Còpies de seguretat (Cas pràctic)

## Descripció general
Aquesta tasca té com a objectiu implementar i documentar una política de còpies de seguretat per al client **Muntatges i Serveis Tècnics SL**, aplicant l’esquema **3-2-1** i utilitzant eines reals tant en entorns **Windows** com **Linux**.

La tasca es divideix en dues parts:
- Còpia de seguretat d’un equip client Windows (director de l’empresa)
- Còpia de seguretat d’un servidor Linux

---

## Part 1: Còpia de seguretat equip Windows

### Objectiu
Realitzar còpies de seguretat del perfil d’usuari del director, emmagatzemant:
- Una còpia local en un disc secundari
- Una còpia al cloud (Google Drive)

### Entorn utilitzat
- Màquina virtual **Windows 11**
- 2 discos:
  - Disc principal: sistema operatiu
  - Disc secundari: 10 GB (destinat a backups)
- Eina de còpies: **Duplicati**
- Cloud: **Google Drive**

### Configuració del disc secundari
1. Afegit un segon disc virtual de 10 GB.
2. Inicialitzat amb **GPT**.
3. Creat volum NTFS i assignada lletra de unitat.
4. Disc destinat exclusivament a còpies de seguretat locals.

### Configuració de Duplicati
- Origen: carpeta del perfil d’usuari (Documents principalment).
- Destinació local: disc secundari.
- Destinació cloud: Google Drive.
- Programació:
  - Còpia local: **cada hora**
  - Còpia al cloud: **diàriament a les 18:00**
- Opcions:
  - Compressió activada
  - Volums remots amb mida per defecte
  - Retenció limitada per evitar saturació del disc

### Proves realitzades
- Execució manual del backup.
- Verificació de fitxers de còpia al disc secundari.
- Eliminació d’arxius de Documents.
- Restauració:
  - Des de còpia local
  - Des de còpia al cloud
- Comprovació de recuperació correcta dels arxius.

---

## Part 2: Còpia de seguretat servidor Linux

### Objectiu
Implementar un sistema de còpies de seguretat automatitzat per a un servidor Linux utilitzant **Duplicity** i **cron**.

### Eines utilitzades
- Sistema operatiu: Linux
- Eina de còpia: **Duplicity**
- Programació: **cron**

### Configuració
- Instal·lació de Duplicity mitjançant gestor de paquets.
- Còpies de seguretat:
  - Local: cap a un directori del sistema
  - Remota: mitjançant SFTP
- Automatització:
  - Còpia local horària
  - Còpia remota diària a les 18:00

### Restauració
- Restauració completa o parcial de fitxers.
- Verificació de la integritat de les dades restaurades.

---

## Conclusions
- S’ha implementat correctament una política de còpies de seguretat segons el model **3-2-1**.
- Les eines utilitzades permeten automatització, seguretat i restauració fiable.
- La solució és escalable i aplicable a entorns reals d’empresa.

---
