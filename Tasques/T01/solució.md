# T01 — DRP: Còpies de Seguretat *(Treball individual)*

## **Fase 1: Treball individual**

### **1. Què copiar? (Priorització)**

Segons l’anàlisi del cas de l’empresa **Muntatges i Serveis Tècnics SL**, les dades crítiques del servidor són:

- **Bases de Dades (Comptabilitat i Clients, 20 GB)**  
  Dades d’ús diari i essencials per al funcionament de l’empresa. La seva pèrdua implicaria una interrupció immediata de les operacions.

- **Documents de Projectes (300 GB)**  
  Contenen plànols i especificacions tècniques. Tot i que tenen un creixement moderat, són crucials per als projectes en curs.

- **Carpetes Personals dels Usuaris (100 GB)**  
  Inclouen treball diari i informes temporals. Tot i no ser crítics com les bases de dades, convé tenir còpies periòdiques per evitar pèrdues accidentals.

Pel que fa als **10 equips clients**, no és necessari realitzar còpia completa, només arxius locals crítics no sincronitzats amb el servidor.

**Justificació:** Prioritzant BD i documents de projectes, s’assegura la continuïtat del negoci i el compliment dels requisits **RPO/RTO (pèrdua màxima de 4 hores)**.

---

### **2. Periodicitat i Tipus de Còpia**

| Dia       | Dades                                | Tipus de còpia   |
|-----------|--------------------------------------|-------------------|
| Dilluns   | Base de dades                       | Incremental       |
| Dimarts   | Base de dades                       | Diferencial       |
| Dimecres  | BD + Documents de Projectes         | Incremental       |
| Dijous    | Base de dades                       | Incremental       |
| Divendres | BD + Documents de Projectes         | Diferencial       |
| Dissabte  | Base de dades                       | Incremental       |
| Diumenge  | BD + Documents de Projectes         | Completa          |

**Justificació:**
- **Incremental:** ràpid i estalvia espai.
- **Diferencial:** combina estalvi amb seguretat.
- **Completa:** assegura còpia completa setmanal.

---

### **3. Mitjans i Ubicació (Regla 3-2-1)**

- **Mitjà 1 (Local):** NAS empresarial de 8 TB.
- **Mitjà 2 (Extern):** Servidor Cloud (Google Cloud/Azure).
- **Ubicació fora de lloc:** Cloud + còpia física mensual en cinta/disc dur.

**Justificació:** Compliment de la regla **3-2-1** (3 còpies, 2 mitjans, 1 fora de lloc).

---

## **Fase 2 – Proposta Unificada**

| Element                | Proposta de la Parella                                | Justificació                                      |
|------------------------|--------------------------------------------------------|---------------------------------------------------|
| **Dades Crítiques**    | BD, documents de projectes, carpetes personals        | Centralitzar evita copiar els 10 PCs             |
| **Periodicitat (BD)**  | Incremental cada 4h, completa cada nit i setmanal     | BD canvia sovint                                 |
| **Tipus de Còpia (BD)**| Incrementals + completes                              | Incrementals ràpides, completes per restauració  |
| **Periodicitat Docs**  | Incremental diària, completa setmanal i mensual       | Canvis moderats                                  |
| **Mitjà 1 (Local)**    | NAS                                                   | Restauracions immediates                         |
| **Mitjà 2 (Extern)**   | Cloud                                                 | Compliment regla 3-2-1                           |

---

## **Fase 3 – Política Final**

### **1. Dades Objecte de Còpia**

| Tipus                      | Objecte de Còpia                  | Criticitat | Comentari                      |
|---------------------------|-----------------------------------|-----------|--------------------------------|
| Servidor de Fitxers       | Bases de Dades (Comptabilitat)   | Alta      | Canvi constant, RPO/RTO 4h    |
|                           | Documents de Projectes           | Mitjana   | Gran volum, canvis moderats   |
|                           | Carpetes Personals               | Mitjana   | Canvis diaris                 |
| Equips Clients (Windows)  | Carpeta Documents               | Mitjana   | Informes i arxius temporals   |

---

### **2. Cronograma Setmanal Detallat**

| Dia       | Dades                | Tipus de Còpia                  | Mitjà              |
|-----------|----------------------|---------------------------------|--------------------|
| Dilluns   | Bases de Dades       | Incremental cada 4h            | NAS                |
|           | Documents/Personals  | Incremental diari              | NAS                |
| Dimarts   | Bases de Dades       | Incremental cada 4h            | NAS                |
| Divendres | BD                   | Diferencial + Completa setmanal| NAS + Disc extern  |
| Diumenge  | BD/Docs/Personals    | Completa mensual               | Cloud (AWS)        |

---

### **3. Mitjans i Ubicació (Regla 3-2-1)**

| Categoria          | Mitjà           | Ubicació                  | Responsabilitat       |
|--------------------|-----------------|---------------------------|------------------------|
| Mitjà 1 (Local)    | NAS intern      | Sala de Servidors         | Administrador TI       |
| Mitjà 2 (Extern)   | Disc dur extern | Armaris segurs            | Administrador TI       |
| Ubicació Fora Lloc | Cloud (AWS)     | Servei Cloud, dades xifrades| TI + Responsable Seguretat |

---

### **4. Estratègia de Recuperació (RTO/RPO)**

- **Bases de Dades:**  
  - RPO: 4h (incrementals cada 4h)  
  - RTO: 4h (restauració des del NAS, disc extern o Cloud)

- **Documents/Personals:**  
  - RPO: 24h  
  - RTO: ≤24h  

**Procediment:**  
1. Restaurar BD des del NAS.  
2. Si NAS no disponible → disc extern.  
3. Desastre total → còpia mensual del Cloud.

