
Instal·lació i configuració SSH a Ubuntu
![image1](./img/image1.png)


Mirem el estatus:
![comanda per mirar l'estatus d'un servei](./img/image2.png)


Fem una altre vegada
![Comanda per iniciar un servei](./img/image3.png)


Anem a /etc/sshsshd_config i posem aquestes modificacions

![imatge de d'un port concret al fitxer](./img/image4.png)
![modificacions afegides al fitxer](./img/image5.png)

Ara mirem el estatus
![Un servei actiu](./img/image6.png)

Entrem a Ubuntu desde Windows:
![un connexió amb éxit establert](./img/image7.png)
![Una serie de comprovacions](./img/image8.png)

Afegim seguretat a la nostre màquina:

![Afageix una línea al fitxer](./img/image9.png)

Afegim contrasenya a root més altres modificacions:
![usuari afegint una contrasenya per root](./img/image10.png)

Creem dos nous usuaris:
![creant un usuari i afegint contrasenya](./img/image14.png)
![creant un segon usuari](./img/image11.png)
![afegint una contrasenya per el segon usuar](./img/image12.png)
![comprovació de dos usuaris creats](./img/image13.png)

El provem:

**ROOT**

![iniciant a root](./img/image15.png)
![intent per accés a la màquina amb root denegada](./img/image16.png)


**USUARI**

![intent amb èxit per iniciar a la màquina com a usuari](./img/image17.png)

**USUARI2**

![intent per iniciar a la màquina com a usuari denegada](./img/image18.png)

Generem un claud gen:
![generació d'una clau gen.](./img/image19.png)

Copiem la clau a Ubuntu i provem sense contrasenya:
![copia la clau a l'altre màquina](./img/image20.png)

Cerquem funcionalitats opcionals i cliquem afegir una funcionalitat, “OpenSSH Server”:
![un icona per afegir una funcionalitat](./img/image21.png)

Posem:
![Afageix la funcionalitat OpenSSH Server](./img/image22.png)

A PowerShell com a administrador començem a arrencar com a admin:
![Opció per exucutar com a adminstrador](./img/image23.png)

![Inicia el servei OpenSHH Server](./img/image24.png)

Desde Ubuntu:

![Iniciant a la màquina Windows des de Ubuntu](./img/image25.png)

![Intent amb èxit a la màquina Windows](./img/image26.png)





