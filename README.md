# Análisis de Riesgos - Escenario 10: Industrial Control System (SCADA/ICS)

**Materia:** Seguridad Informática  
**Institución:** UTU  
**Equipo:** Christian Busquets  
**Entrega:** Mayo 2025  

---

## Descripción del grupo y escenario elegido

### Escenario 10 — Industrial Control System (SCADA/ICS)

El escenario consiste en un sistema de control industrial para una **planta manufacturera de autopartes** (ficticia). El sistema gestiona 4 líneas de producción a través de PLCs Siemens y Allen Bradley, supervisados por un servidor SCADA central, con integración hacia la red corporativa mediante un servidor Historian y un ERP SAP.

**Componentes principales del sistema:**
- **SCADA/HMI:** Servidor WinCC (principal y redundante), HMIs en sala de control
- **PLCs:** Siemens S7-1500 (Líneas 1 y 2, protocolo PROFINET) y Allen Bradley ControlLogix (Líneas 3 y 4, protocolo EtherNet/IP)
- **Historian:** OSIsoft PI Server (almacenamiento de datos históricos de proceso)
- **Red industrial:** PROFINET y EtherNet/IP con DMZ industrial
- **Integración corporativa:** ERP SAP conectado al Historian vía OPC-UA
- **Acceso remoto:** VPN para proveedores Siemens y Rockwell Automation

**Metodologías aplicadas:**
- Modelado de amenazas: **STRIDE**
- Priorización de riesgos: **DREAD**
- Mapeo de técnicas de ataque: **MITRE ATT&CK for ICS**
- Marco de referencia de arquitectura: **Modelo Purdue (ISA-95)**
- Normativas: **NIST SP 800-82**, **ISA/IEC 62443**, **NIST SP 800-53**

---

## Estructura del repositorio

```
E10-Industrial-Control-System_Riesgos/
├── README.md
├── prompts/
│   ├── 00-memory-bank.md
│   ├── 01-inventario-activos.txt
│   ├── 02-arquitectura-trust-boundaries.txt
│   ├── 03-stride-analysis.txt
│   ├── 04-priorizacion-dread.txt
│   ├── 05-mapa-attack.txt
│   ├── 06-plan-mitigacion.txt
│   └── 07-riesgos-residuales.txt
├── docs/
│   ├── 01-inventario-activos.md
│   ├── 02-arquitectura-trust-boundaries.md
│   ├── 03-analisis-stride.md
│   ├── 04-priorizacion-dread.md
│   ├── 05-mapa-attack.md
│   ├── 06-plan-mitigacion.md
│   └── 07-riesgos-residuales.md
├── diagrams/
│   ├── arquitectura.drawio
│   ├── arquitectura.png
│   └── attack-flow.png
└── templates/
    └── Plantilla_Análisis_de_Riesgo.pdf
```

---

## Resumen de hallazgos

- **16 amenazas identificadas** mediante análisis STRIDE
- **Amenaza más crítica:** Movimiento lateral IT→OT por misconfiguration de firewall (TH14, DREAD 40/50)
- **18 controles de seguridad** definidos y priorizados
- **8 riesgos residuales aceptados**, siendo el más significativo la falta de cifrado nativo en protocolos PROFINET y EtherNet/IP

---

## Herramientas de IA utilizadas

| Herramienta | Uso |
|-------------|-----|
| **Claude (claude.ai) - Claude Sonnet 4.6** | Generación de todos los documentos de análisis: inventario de activos, arquitectura, análisis STRIDE, priorización DREAD, mapa ATT&CK for ICS, plan de mitigación y riesgos residuales |

> Todos los prompts utilizados se encuentran documentados en la carpeta `prompts/`.

---

## Herramientas adicionales utilizadas

| Herramienta | Uso |
|-------------|-----|
| **draw.io (app.diagrams.net)** | Elaboración del diagrama de arquitectura Purdue con trust boundaries y del diagrama de flujo de ataque |

---

## Referencias

- [NIST SP 800-82 Rev. 3 — Guide to ICS Security](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-82r3.pdf)
- [MITRE ATT&CK for ICS](https://collaborate.mitre.org/attackics/index.php/Main_Page)
- [ISA/IEC 62443 — Industrial Automation Security](https://www.isa.org/standards-and-publications/isa-standards/isa-iec-62443-series)
- [NIST SP 800-53 Rev. 5 — Security Controls](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)
