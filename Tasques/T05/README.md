📌 T05 — Accés Remot: Connexió via SSH
Projecte 04 – EverPia III: El Desafiament Final
SMiX2 – CFGM Sistemes Microinformàtics i Xarxes
📝 Descripció de la tasca

En aquesta tasca s’implementa i es documenta el servei SSH (Secure Shell) en un entorn Linux, amb l’objectiu de permetre l’administració remota segura dels servidors d’EverPia.

La tasca forma part del bloc Accés Remot i té com a objectiu demostrar competències en configuració, seguretat i validació de sistemes remots.

🎯 Objectius específics

Instal·lar i activar el servei OpenSSH Server.

Configurar paràmetres bàsics i segurs al fitxer sshd_config.

Establir connexions remotes des de Windows i Linux.

Implementar autenticació per clau pública.

Documentar el procés amb qualitat professional.

📦 1. Instal·lació del servidor SSH
Actualització de repositoris i instal·lació:
sudo apt update
sudo apt install openssh-server -y

Comprovació de l’estat:
systemctl status ssh


Sortida esperada:

active (running)

🔧 2. Configuració bàsica i segura

El fitxer principal és:

/etc/ssh/sshd_config

Paràmetres configurats:
Port 22
Protocol 2
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication yes   # o no, depenent de la política
AllowUsers usuari1 usuari2

Reiniciar el servei després de modificar:
sudo systemctl restart ssh

🔑 3. Configuració d'autenticació per clau pública
Generar claus al client:
ssh-keygen -t rsa -b 4096

Copiar la clau al servidor:
ssh-copy-id usuari@IP_DEL_SERVIDOR

Connexió:
ssh usuari@IP_DEL_SERVIDOR

🧱 4. Configuració del tallafocs (UFW)
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status

💻 5. Proves realitzades
✔ Connexió des de Linux:
ssh usuari@IP

✔ Connexió des de Windows (PuTTY):

Hostname: IP del servidor

Port: 22

Tipus: SSH

Funciona correctament

✔ Validació de claus:

Autenticació sense contrasenya → OK

❌ Accés root bloquejat:

Intent → Denegat correctament

📁 6. Estructura recomanada de la carpeta
T05-SSH/
│── README.md
│── sshd_config (opcional)
│── captures/ (opcional)
└── proves/ (opcional)

📄 Conclusions

El servei SSH ha estat instal·lat i configurat correctament, permetent un accés remot segur i fiable.
Aquesta configuració és essencial per al treball administratiu en entorns reals i per a la gestió de sistemes en una empresa IT com EverPia.

📚 Competències treballades

0227 Serveis de Xarxa

RA 6 — Mètodes d'accés remot

Configuració de serveis, seguretat, proves i documentació.
