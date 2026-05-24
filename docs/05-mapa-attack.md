# 05 - Mapa de Ataque (MITRE ATT&CK for ICS)
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Christian Busquets  
**Fecha:** 21/05/2025  
**Versión:** 1.0  

---

## 6. MAPA DE ATAQUE (ATT&CK for ICS)

### 6.1 Introducción

MITRE ATT&CK for ICS es un framework que describe las tácticas y técnicas utilizadas por atacantes en entornos de sistemas de control industrial. A diferencia del ATT&CK Enterprise (orientado a IT), ATT&CK for ICS contempla el impacto físico sobre procesos productivos, equipos y seguridad de personas.

Las técnicas identificadas a continuación se derivan directamente de las amenazas TH01–TH16 definidas en el análisis STRIDE (documento 03).

---

### 6.2 Técnicas Identificadas

| ID Técnica | Nombre | Táctica | Amenaza STRIDE | Mitigación Principal |
|------------|--------|---------|----------------|----------------------|
| T0822 | External Remote Services | Initial Access | TH01 | MFA, acceso VPN bajo demanda |
| T0812 | Default Credentials | Initial Access | TH01, TH02 | Política de contraseñas, gestión de cuentas |
| T0886 | Remote Services | Lateral Movement | TH01, TH15 | Segmentación de red, privilegio mínimo |
| T0859 | Valid Accounts | Persistence / Lateral Movement | TH02, TH15 | MFA, revisión periódica de cuentas |
| T0891 | Hardcoded Credentials | Initial Access | TH02 | Auditoría de credenciales en dispositivos |
| T0833 | Modify Control Logic | Impair Process Control | TH03, TH05 | Control de versiones PLC, firma de código |
| T0821 | Modify Controller Tasking | Impair Process Control | TH03 | Whitelisting de aplicaciones, auditoría |
| T0836 | Modify Parameter | Impair Process Control | TH03 | Alertas de cambio de parámetros en SCADA |
| T0839 | Module Firmware | Impair Process Control | TH05, TH13 | Verificación de integridad de firmware |
| T0873 | Project File Infection | Execution | TH05 | Control de acceso al servidor de ingeniería |
| T0858 | Change Credential | Persistence | TH06 | Logging de cambios de credenciales |
| T0845 | Program Upload | Collection | TH07, TH08 | Restricción de acceso a herramientas de programación |
| T0842 | Network Sniffing | Discovery | TH08, TH10 | Cifrado de tráfico, VLANs, IDS/IPS |
| T0885 | Commonly Used Port | Command and Control | TH08 | Firewall con whitelist de puertos |
| T0801 | Monitor Process State | Collection | TH09, TH10 | Control de acceso al Historian, segmentación |
| T0814 | Denial of Control | Inhibit Response Function | TH11, TH12 | Redundancia SCADA, monitoreo de red |
| T0816 | Device Restart/Shutdown | Inhibit Response Function | TH13, TH11 | Protección de firmware, actualizaciones |
| T0835 | Manipulate I/O Image | Impair Process Control | TH13 | Validación de señales en PLCs |
| T0866 | Exploitation of Remote Services | Initial Access / Lateral Movement | TH14, TH16 | Parcheo de sistemas, firewall estricto |
| T0843 | Program Download | Execution | TH14, TH03 | Autenticación en descarga de programas PLC |
| T0832 | Manipulation of View | Impair Process Control | TH04 | Integridad de datos en Historian, alertas |
| T0831 | Manipulation of Control | Impair Process Control | TH04 | Control de acceso al Historian |

---

### 6.3 Tácticas ATT&CK for ICS Cubiertas

| Táctica | Descripción | Técnicas identificadas |
|---------|-------------|------------------------|
| **Initial Access** | Cómo el atacante entra al entorno ICS | T0822, T0812, T0891, T0866 |
| **Execution** | Cómo ejecuta código malicioso | T0873, T0843 |
| **Persistence** | Cómo mantiene acceso | T0859, T0858 |
| **Lateral Movement** | Cómo se mueve entre sistemas | T0886, T0859 |
| **Collection** | Cómo recopila información | T0845, T0801, T0842 |
| **Discovery** | Cómo reconoce el entorno | T0842, T0801 |
| **Command and Control** | Cómo mantiene comunicación con el atacante | T0885 |
| **Impair Process Control** | Cómo afecta el proceso productivo | T0833, T0821, T0836, T0839, T0832, T0831, T0835 |
| **Inhibit Response Function** | Cómo impide la respuesta al incidente | T0814, T0816 |

---

### 6.4 Flujo de Ataque Principal (Escenario TH01 → TH14 → TH03)

Este es el escenario de ataque más crítico identificado, que combina las tres amenazas de mayor puntaje DREAD:

```
[ATACANTE EXTERNO]
        │
        │ 1. Phishing / fuerza bruta sobre credenciales VPN
        │    Técnica: T0822 - External Remote Services
        │    Técnica: T0812 - Default Credentials
        ▼
[VPN GATEWAY - TB-04]
        │
        │ 2. Acceso al Servidor de Ingeniería (TIA Portal)
        │    Técnica: T0886 - Remote Services
        ▼
[SERVIDOR INGENIERÍA - DMZ]
        │
        │ 3. Reconocimiento de red OT
        │    Técnica: T0842 - Network Sniffing
        │    Técnica: T0801 - Monitor Process State
        ▼
[RED OT - TB-02 / TB-03]
        │
        │ 4. Movimiento lateral hacia SCADA / PLCs
        │    Técnica: T0866 - Exploitation of Remote Services
        │    Técnica: T0843 - Program Download
        ▼
[PLCs SIEMENS S7-1500]
        │
        │ 5. Modificación de lógica de control
        │    Técnica: T0833 - Modify Control Logic
        │    Técnica: T0836 - Modify Parameter
        ▼
[IMPACTO FÍSICO]
   - Parada de línea de producción
   - Daño a maquinaria
   - Producción de piezas defectuosas
   - Riesgo a seguridad del personal
```

---

### 6.5 Flujo de Ataque Secundario (Escenario TH08 → TH03)

Escenario de ataque interno o desde red OT ya comprometida:

```
[ATACANTE INTERNO / RED OT COMPROMETIDA]
        │
        │ 1. Captura de tráfico PROFINET sin cifrado
        │    Técnica: T0842 - Network Sniffing
        │    Técnica: T0885 - Commonly Used Port
        ▼
[RED PROFINET - TB-03]
        │
        │ 2. Análisis de comandos y parámetros de proceso
        │    Técnica: T0801 - Monitor Process State
        ▼
[DATOS DE PROCESO]
        │
        │ 3. Replay attack o modificación de parámetros
        │    Técnica: T0836 - Modify Parameter
        │    Técnica: T0835 - Manipulate I/O Image
        ▼
[IMPACTO FÍSICO]
   - Modificación encubierta de parámetros
   - Difícil detección sin IDS/IPS en red OT
```

---

### 6.6 Matriz de Impacto por Táctica

| Táctica | Impacto IT | Impacto OT/Físico | Probabilidad |
|---------|------------|-------------------|--------------|
| Initial Access | Acceso no autorizado a sistemas | Puerta de entrada a red OT | Alta |
| Lateral Movement | Propagación en red corporativa | Cruce de trust boundaries IT→OT | Media |
| Impair Process Control | N/A | Parada de producción, daño físico, riesgo a personas | Media |
| Inhibit Response Function | Pérdida de disponibilidad de servicios | Pérdida de visibilidad del proceso, operación a ciegas | Media |
| Collection | Robo de datos | Robo de recetas de producción, topología de red | Alta |
| Persistence | Backdoor en sistemas IT | Backdoor en PLCs o servidor de ingeniería | Baja |

---

### 6.7 Referencias

- MITRE ATT&CK for ICS Matrix: https://collaborate.mitre.org/attackics/index.php/Main_Page
- NIST SP 800-82 Rev. 3 — Guide to ICS Security
- ISA/IEC 62443 — Industrial Automation and Control Systems Security
