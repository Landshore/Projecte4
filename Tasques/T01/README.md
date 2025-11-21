# T01 - DRP: Còpies de Seguretat - Estudi Cas Client

## 📋 Informació General

**Tipus de tasca**: Treball cooperatiu (1-2-4)  
**Mòdul**: 0226 Seguretat Informàtica  
**Durada estimada**: 3 hores  
**Data inici**: [Omplir]  
**Data lliurament**: [Omplir]

---

## 🎯 Objectius

L'objectiu d'aquesta activitat és treballar els aspectes relatius a la **política de còpies de seguretat** mitjançant una estructura de treball cooperatiu 1-2-4.

### Competències treballades
- Determinar la logística associada a les operacions de manteniment de sistemes microinformàtics
- Elaborar documentació tècnica i administrativa del sistema
- Seleccionar estratègies per a la realització de còpies de seguretat

---

## 📖 Context del Cas

### Client: Muntatges i Serveis Tècnics SL

Petita empresa dedicada a la instal·lació i manteniment d'equips industrials.

#### Infraestructura Tècnica

**Servidor de Fitxers (Ubuntu Server)**:
- Documents de Projectes: Plànols, especificacions tècniques (300 GB, creixement moderat)
- Bases de Dades (Comptabilitat i Clients): Crítiques i d'ús diari (20 GB, canvi constant)
- Carpetes Personals dels Usuaris: Per a la feina diària (100 GB)

**10 Equips Clients (Windows 10/11)**:
- Els usuaris treballen majoritàriament amb fitxers del servidor
- Alguns tècnics guarden temporalment informes i arxius importants a la carpeta Documents

**Connexió a Internet**: Fibra òptica de 600 Mbps (simètrica)

#### Requisits de Recuperació

- **RTO (Recovery Time Objective)**: Les dades de Comptabilitat/Clients han d'estar disponibles en menys de 4 hores
- **RPO (Recovery Point Objective)**: 
  - Pèrdua màxima admesa: 24 hores per a la majoria de dades
  - Dades de Comptabilitat/Clients: no poden perdre més de 4 hores de treball
- **Retenció**: Historial d'almenys un mes

---

## 📝 Desenvolupament de l'Activitat

### Fase 1: Treball Individual (30 min)

De forma individual, respon a les següents preguntes basant-te en el cas pràctic:

1. **Què copiar? (Priorització)**
   - Quines són les dades més crítiques del servidor?
   - Cal fer còpia dels 10 equips clients? Justifica-ho.

2. **Periodicitat i Tipus de Còpia**
   - Proposa un calendari bàsic per a la setmana (Diari/Setmanal/Mensual)
   - Quin tipus de còpia aplicaràs (Completa, Diferencial, Incremental) per a les dades crítiques?

3. **Mitjans i Ubicació**
   - Quin tipus de mitjà de còpia utilitzaries? (Discs durs externs, NAS, Cloud, Cintes)
   - On s'hauria de guardar físicament la còpia més recent? (Regla 3-2-1)

**Documenta les teves respostes a**: `fase1_individual.md`

---

### Fase 2: Treball per Parelles (45 min)

Treballant per parelles:

1. **Discussió i Consens**: Compareu les vostres respostes individuals
2. **Elaboració d'una Proposta Unificada**: Consensueu i dissenyeu el vostre propi **Esquema 3-2-1 de Còpies**

Completeu la següent taula:

| Element | Proposta de la Parella | Justificació |
|---------|------------------------|--------------|
| **Dades Crítiques** | | |
| **Periodicitat (BD)** | | |
| **Tipus de Còpia (BD)** | | |
| **Mitjà 1 (Local)** | | |
| **Mitjà 2 (Extern)** | | |

**Documenta la vostra proposta a**: `fase2_parelles.md`

---

### Fase 3: Treball en Grup (90 min)

1. **Debat i Selecció**: 
   - Cada parella presenta el seu esquema
   - El grup debat els pros i contres de cada proposta (cost, temps de recuperació, seguretat, simplicitat)

2. **Disseny de la Política Final**: 
   - El grup redacta la **Política de Còpies de Seguretat Definitiva** per presentar a "Muntatges i Serveis Tècnics SL"

---

## 📄 Document Final (Fase 3)

El grup ha de generar un document amb els següents punts resolts:

### 1) Dades Objecte de Còpia
Quines dades es copien i amb quina freqüència (separant Servidor/Clients i crítiques/no crítiques).

### 2) Cronograma Setmanal Detallat

| Dia | Dades (Ex: BD) | Tipus de còpia | Mitjà |
|-----|----------------|----------------|-------|
| Dilluns | | | |
| Dimarts | | | |
| Dimecres | | | |
| Dijous | | | |
| Divendres | | | |
| Dissabte | | | |
| Diumenge | | | |

### 3) Elecció de Mitjans i Ubicació (Regla 3-2-1)

- **Mitjà 1 (Local)**: Quin mitjà concret (p. ex., Disc dur USB, NAS) s'utilitza
- **Mitjà 2 (Extern)**: Quin mitjà (p. ex., Cloud, LTO) i el proveïdor proposat (p. ex., Azure, Google Cloud, servei local)
- **Ubicació Fora de Lloc**: On es guarda la còpia externa (física o lògica) i qui és el responsable de la seva gestió

### 4) Estratègia de Recuperació (RTO/RPO)

Com es garanteix que les dades de Comptabilitat/Clients compleixen amb el requisit de:
- **RPO**: 4 hores
- **RTO**: 4 hores

**Document final**: `SOLUCIO.md`

---

## 📦 Estructura de Carpetes
```
T01_DRP_Cas_Client/
│
├── README.md                    # Aquest document
├── fase1_individual.md          # Respostes individuals
├── fase2_parelles.md            # Proposta per parelles
├── SOLUCIO.md                   # Document final del grup
└── img/                         # Captures o diagrames (opcional)
    ├── esquema_backup.png
    └── cronograma.png
```

---

## 📚 Materials i Recursos

### Documentació oficial
- [INCIBE - Copias de seguridad: Guía de aproximación para el empresario](https://www.incibe.es/)
- Moodle 0226 Seguretat Informàtica: RA2.AA3 Còpies

### Vídeos recomanats
- [Xataka - Backup 3-2-1: el método definitivo para mantener a salvo tus datos](https://youtu.be/PM_M4Iz6I4o?si=F7DRyDDTZE3hjWn8)

---

## ✅ Criteris d'Avaluació

### Fase 1 (Individual)
- [ ] Identificació correcta de dades crítiques
- [ ] Proposta de periodicitat justificada
- [ ] Selecció de mitjans adequada
- [ ] Aplicació de la regla 3-2-1

### Fase 2 (Parelles)
- [ ] Consens entre ambdós membres
- [ ] Taula completada amb justificacions
- [ ] Coherència de la proposta

### Fase 3 (Grup)
- [ ] Document final complet amb els 4 punts
- [ ] Cronograma detallat
- [ ] Justificació de l'estratègia RTO/RPO
- [ ] Aplicació correcta de la regla 3-2-1
- [ ] Qualitat de la documentació

---

## 🚀 Lliurament

**Termini**: [Data indicada al Moodle]

**Format**: Enllaç a la carpeta del repositori a la tasca corresponent del Moodle

**Contingut a lliurar**:
- README.md amb l'enunciat
- SOLUCIO.md que incorpori:
  - Part individual (fase 1)
  - Fase 2 per parelles
  - Document final de grup (fase 3)

---

## 💡 Consells

- Penseu en l'equilibri entre **seguretat** i **cost**
- Considereu la **facilitat d'ús** per al personal de l'empresa
- Tingueu en compte el **temps de recuperació** en cas d'emergència
- La regla **3-2-1** és imprescindible: 3 còpies, 2 mitjans diferents, 1 fora del lloc

---

## 📞 Dubtes i Consultes

Consulteu amb el vostre professor/a de:
- **Mòdul**: 0226 Seguretat Informàtica
- **Horari de tutories**: [Omplir]

---

**Última actualització**: [Data]  
**Autor**: [Nom del grup]  
**Versió**: 1.0
