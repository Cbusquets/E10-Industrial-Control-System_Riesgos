# 01 - Inventario de Activos
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Mateo Sparano, Christian Busquets  
**Fecha:** 18/05/2025  
**Versión:** 1.0  

---

## 2. IDENTIFICACIÓN DE ACTIVOS

### 2.1 Activos de Información

| ID  | Activo                              | Tipo de Dato         | Clasificación | Propietario              |
|-----|-------------------------------------|----------------------|---------------|--------------------------|
| A01 | Recetas de producción (parámetros de proceso) | Propiedad intelectual | Alta          | Ingeniería de Procesos   |
| A02 | Datos históricos de sensores (Historian) | Operacional          | Alta          | Operaciones              |
| A03 | Logs de alarmas y eventos           | Operacional          | Media         | Operaciones              |
| A04 | Credenciales de acceso SCADA/HMI    | Autenticación        | Alta          | Administración TI/OT     |
| A05 | Reportes de producción              | Operacional          | Media         | Gerencia de Planta       |
| A06 | Configuración de PLCs               | Configuración crítica | Alta          | Ingeniería de Control    |
| A07 | Datos de integración ERP            | Financiero/Operacional | Alta         | Sistemas Corporativos    |
| A08 | Firmware de dispositivos de campo   | Software crítico     | Alta          | Ingeniería de Control    |
| A09 | Topología de red industrial         | Infraestructura      | Alta          | Administración TI/OT     |
| A10 | Planes de mantenimiento predictivo  | Operacional          | Media         | Mantenimiento            |

---

### 2.2 Activos Tecnológicos

| ID  | Activo                              | Tipo       | Criticidad | Notas                                                    |
|-----|-------------------------------------|------------|------------|----------------------------------------------------------|
| T01 | Servidor SCADA principal            | Hardware   | Alta       | Sistema central de supervisión y control de procesos     |
| T02 | Servidor SCADA redundante (failover)| Hardware   | Alta       | Respaldo en caliente del servidor SCADA principal        |
| T03 | HMI - Sala de control principal     | Hardware   | Alta       | Interfaz operador, acceso directo a control de procesos  |
| T04 | HMI - Sala de control secundaria    | Hardware   | Media      | Interfaz de monitoreo en planta, acceso limitado         |
| T05 | PLC Siemens S7-1500 (Línea 1)       | Hardware   | Alta       | Control de línea de ensamblaje 1, protocolo PROFINET     |
| T06 | PLC Siemens S7-1500 (Línea 2)       | Hardware   | Alta       | Control de línea de ensamblaje 2, protocolo PROFINET     |
| T07 | PLC Allen Bradley ControlLogix (Línea 3) | Hardware | Alta    | Control de línea de ensamblaje 3, protocolo EtherNet/IP  |
| T08 | PLC Allen Bradley ControlLogix (Línea 4) | Hardware | Alta    | Control de línea de ensamblaje 4, protocolo EtherNet/IP  |
| T09 | Historian Server (OSIsoft PI)       | Hardware   | Alta       | Almacenamiento de datos históricos de proceso            |
| T10 | Switches industriales PROFINET      | Hardware   | Alta       | Red de campo Nivel 1, protocolo PROFINET                 |
| T11 | Switches industriales EtherNet/IP   | Hardware   | Alta       | Red de campo Nivel 1, protocolo EtherNet/IP              |
| T12 | Firewall industrial (DMZ OT)        | Hardware   | Alta       | Separación red corporativa / red OT                      |
| T13 | Servidor de ingeniería (TIA Portal / Studio 5000) | Hardware | Alta | Programación y mantenimiento de PLCs              |
| T14 | Estación de trabajo remota (acceso VPN) | Hardware | Alta    | Acceso remoto para mantenimiento de proveedores          |
| T15 | Sensores de temperatura y presión   | Hardware   | Media      | Instrumentación de campo, señales analógicas             |
| T16 | Actuadores y variadores de frecuencia | Hardware | Alta      | Control físico de motores y válvulas en planta           |
| T17 | Servidor ERP (SAP - red corporativa)| Software   | Alta       | Sistema de planificación empresarial, integración OT     |
| T18 | Software SCADA (WinCC)              | Software   | Alta       | Plataforma de supervisión y control                      |
| T19 | Sistema operativo HMI (Windows 10 LTSC) | Software | Alta   | SO base de las estaciones HMI                            |
| T20 | Red PROFINET (Nivel de campo)       | Red        | Alta       | Red industrial para comunicación con PLCs Siemens        |
| T21 | Red EtherNet/IP (Nivel de campo)    | Red        | Alta       | Red industrial para comunicación con PLCs Allen Bradley  |
| T22 | Red OT (Nivel de control)           | Red        | Alta       | Red de supervisión, conecta SCADA con PLCs               |
| T23 | DMZ Industrial                      | Red        | Alta       | Zona desmilitarizada entre red OT y red corporativa      |
| T24 | Red corporativa (IT)                | Red        | Media      | Red de oficinas, conecta ERP y sistemas administrativos  |
| T25 | Enlace VPN de acceso remoto         | Red        | Alta       | Acceso de proveedores y soporte técnico externo          |

---

### 2.3 Activos Intangibles

- **Reputación de la empresa:** Una interrupción de producción o incidente de seguridad OT puede impactar directamente contratos con clientes automotrices.
- **Continuidad operacional:** La disponibilidad del sistema es crítica; una parada no planificada implica pérdidas económicas significativas por hora de inactividad.
- **Propiedad intelectual:** Los parámetros de proceso y recetas de producción representan el know-how productivo de la empresa.
- **Cumplimiento normativo:** Cumplimiento con ISA/IEC 62443 y NIST SP 800-82 para sistemas de control industrial.

---

## Notas del Escenario

- **Planta:** Manufacturera de autopartes (escenario ficticio)
- **Modelo de red:** Purdue Model (niveles 0–4)
  - Nivel 0: Sensores y actuadores
  - Nivel 1: PLCs (Siemens S7-1500, Allen Bradley ControlLogix)
  - Nivel 2: SCADA/HMI, Historian
  - Nivel 3: Red OT / DMZ Industrial
  - Nivel 4: Red corporativa / ERP
- **Protocolos industriales:** PROFINET (Siemens), EtherNet/IP (Allen Bradley)
- **Acceso remoto:** VPN habilitada para soporte de proveedores (Siemens, Rockwell)
