# 02 - Arquitectura y Trust Boundaries
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Christian Busquets  
**Fecha:** 20/05/2025  
**Versión:** 1.1  

---

## 3. ARQUITECTURA DEL SISTEMA

### 3.1 Descripción General

El sistema sigue el **Modelo Purdue (ISA-95)**, que divide la arquitectura en niveles jerárquicos que separan los sistemas de control industrial (OT) de los sistemas corporativos (IT). Esta separación es fundamental para la seguridad del entorno industrial.

```
                                          ── TRUST BOUNDARY TB-04 ──
  [Proveedor Siemens / Rockwell]              VPN / TLS
           ↓ VPN/TLS                              ↓
╔══════════════════════════════════════════════════════════════╗
║                    NIVEL 4 - RED CORPORATIVA (IT)            ║
║   [ERP SAP]  [Correo]  [PCs Corporativas]  [Internet/Ext.]  ║
╠══════════════════════════════════════════════════════════════╣
║                ── TRUST BOUNDARY TB-01 ──                    ║
║              FIREWALL INDUSTRIAL + DMZ                       ║
╠══════════════════════════════════════════════════════════════╣
║              DMZ INDUSTRIAL                                  ║
║   [Historian Server]  [Servidor Ingeniería]  [VPN Gateway]  ║
║                       [AV / Patch Server]                    ║
╠══════════════════════════════════════════════════════════════╣
║                ── TRUST BOUNDARY TB-02 ──                    ║
║                  SWITCH INDUSTRIAL (VLANs)                   ║
╠══════════════════════════════════════════════════════════════╣
║         NIVEL 3 - MANUFACTURING ZONE (SCADA / HMI)          ║
║   [Servidor SCADA Principal]  [Servidor SCADA Redundante]   ║
║   [HMI Sala Control Principal]  [HMI Sala Control Sec.]     ║
║                      [DB Server]                             ║
╠══════════════════════════════════════════════════════════════╣
║         ── TRUST BOUNDARY TB-03 (CRÍTICO) ──                 ║
║         PROFINET / EtherNet/IP (sin cifrado nativo)          ║
╠══════════════════════════════════════════════════════════════╣
║    CELL/AREA ZONE SIEMENS    ║  CELL/AREA ZONE ALLEN BRADLEY ║
║  [Local HMI]                 ║  [Local HMI]                  ║
║  [PLC S7-1500 L1]            ║  [PLC ControlLogix L3]        ║
║  [PLC S7-1500 L2]            ║  [PLC ControlLogix L4]        ║
║  [Switch PROFINET]           ║  [Switch EtherNet/IP]         ║
╠══════════════════════════════════════════════════════════════╣
║                     NIVEL 0 - CAMPO                          ║
║  [Sensores L1] [Actuadores L1] [Sensores L2] [Actuadores L2]║
║  [Sensores L3] [Actuadores L3] [Sensores L4] [Actuadores L4]║
╚══════════════════════════════════════════════════════════════╝
```

> **Ver diagrama completo:** `diagrams/arquitectura.png` / `diagrams/arquitectura.drawio`

---

### 3.2 Flujo de Datos

```
[Sensores/Actuadores]
        ↓ señales analógicas/digitales
[PLCs Siemens S7-1500 / Allen Bradley ControlLogix]
        ↓ PROFINET / EtherNet/IP
[Servidor SCADA (WinCC)] ←→ [HMI Operador]
        ↓ OPC-DA / OPC-UA
[Historian Server (OSIsoft PI)]
        ↓ API / Conector ERP (DMZ)
[ERP SAP - Red Corporativa]

[VPN Externa] → [Servidor Ingeniería] → [PLCs] (mantenimiento remoto)
```

**Flujos críticos identificados:**

| ID Flujo | Origen              | Destino             | Protocolo        | Dirección  |
|----------|---------------------|---------------------|------------------|------------|
| F01      | PLCs Siemens        | Servidor SCADA      | PROFINET         | ↑          |
| F02      | PLCs Allen Bradley  | Servidor SCADA      | EtherNet/IP      | ↑          |
| F03      | Servidor SCADA      | PLCs (comandos)     | PROFINET/EtherNet/IP | ↓      |
| F04      | Servidor SCADA      | Historian Server    | OPC-DA / OPC-UA  | ↑          |
| F05      | Historian Server    | ERP SAP             | API REST (DMZ)   | ↑          |
| F06      | HMI Operador        | Servidor SCADA      | TCP/IP interno   | ↔          |
| F07      | VPN Externa         | Servidor Ingeniería | VPN/TLS          | →          |
| F08      | Servidor Ingeniería | PLCs                | PROFINET/EtherNet/IP | ↓      |

---

### 3.3 Actores del Sistema

| Actor                    | Descripción                                              | Privilegios                        |
|--------------------------|----------------------------------------------------------|------------------------------------|
| Operador de planta       | Controla procesos desde HMI en sala de control           | Lectura/escritura en SCADA/HMI     |
| Ingeniero de control     | Programa y configura PLCs y SCADA                        | Acceso total a nivel OT            |
| Administrador TI/OT      | Gestiona redes, usuarios y seguridad del entorno         | Acceso total a infraestructura     |
| Técnico de mantenimiento | Acceso puntual a equipos físicos y PLCs                  | Limitado, supervisado              |
| Proveedor externo (Siemens/Rockwell) | Soporte remoto y actualizaciones de firmware | Acceso remoto acotado vía VPN      |
| Sistema ERP (SAP)        | Consume datos de producción del Historian                | Solo lectura vía API en DMZ        |
| Atacante externo         | Actor malicioso desde internet o red corporativa         | Ninguno (evaluar vectores)         |
| Atacante interno         | Empleado o contratista con acceso legítimo comprometido  | Variable según rol (evaluar)       |

---

### 3.4 Trust Boundaries (Límites de Confianza)

Los trust boundaries son los puntos donde el nivel de confianza cambia entre zonas. Son los lugares más críticos a analizar porque es donde los atacantes intentan moverse lateralmente.

#### TB-01: Red Corporativa (IT) ↔ DMZ Industrial

| Atributo       | Detalle                                                                 |
|----------------|-------------------------------------------------------------------------|
| Ubicación      | Entre red corporativa (Nivel 4) y DMZ industrial (Nivel 3)             |
| Control        | Firewall industrial + reglas de filtrado estrictas                      |
| Flujos permitidos | Solo API REST del Historian hacia ERP (puerto específico, unidireccional) |
| Riesgo principal | Movimiento lateral desde IT hacia OT si el firewall está mal configurado |
| Criticidad     | **ALTA**                                                                |

#### TB-02: DMZ Industrial ↔ Red de Supervisión SCADA

| Atributo       | Detalle                                                                 |
|----------------|-------------------------------------------------------------------------|
| Ubicación      | Entre DMZ (Nivel 3) y red SCADA/HMI (Nivel 2)                         |
| Control        | Switch industrial con VLANs segmentadas                                 |
| Flujos permitidos | OPC-UA desde SCADA hacia Historian; VPN hacia servidor de ingeniería |
| Riesgo principal | Acceso remoto vía VPN sin MFA puede ser explotado por atacantes       |
| Criticidad     | **ALTA**                                                                |

#### TB-03: Red SCADA ↔ Red de Control (PLCs)

| Atributo       | Detalle                                                                 |
|----------------|-------------------------------------------------------------------------|
| Ubicación      | Entre servidor SCADA (Nivel 2) y PLCs (Nivel 1)                        |
| Control        | Switches industriales PROFINET / EtherNet/IP                            |
| Flujos permitidos | Comandos SCADA→PLC y telemetría PLC→SCADA                            |
| Riesgo principal | Protocolos industriales sin autenticación nativa (PROFINET, EtherNet/IP no cifran por defecto) |
| Criticidad     | **CRÍTICA**                                                             |

#### TB-04: Acceso Remoto Externo ↔ Red OT

| Atributo       | Detalle                                                                 |
|----------------|-------------------------------------------------------------------------|
| Ubicación      | Entre internet/red de proveedores y DMZ industrial                     |
| Control        | VPN con autenticación de usuario                                        |
| Flujos permitidos | Sesiones de mantenimiento autorizadas y auditadas                    |
| Riesgo principal | Credenciales de proveedor comprometidas, sin MFA, sin grabación de sesión |
| Criticidad     | **ALTA**                                                                |

---

### 3.5 Supuestos del Análisis

- El firewall industrial existe pero su configuración puede no ser óptima.
- El acceso VPN para proveedores está habilitado de forma permanente, no solo bajo demanda.
- Los PLCs corren firmware actualizado al momento del análisis.
- No existe segmentación adicional dentro de la red de nivel 1 (red de campo plana).
- Los sistemas HMI corren Windows 10 LTSC con actualizaciones de seguridad con retraso por compatibilidad industrial.
- No hay solución de detección de intrusos (IDS/IPS) desplegada en la red OT actualmente.
