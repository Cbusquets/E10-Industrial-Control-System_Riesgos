# 03 - Análisis de Amenazas - Metodología STRIDE
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Mateo Sparano, Christian Busquets  
**Fecha:** 18/05/2025  
**Versión:** 1.0  

---

## 4. ANÁLISIS DE AMENAZAS - METODOLOGÍA STRIDE

### 4.1 Aplicación de STRIDE

STRIDE es una metodología de modelado de amenazas desarrollada por Microsoft que clasifica las amenazas en seis categorías. En entornos OT/ICS, su aplicación es especialmente crítica dado que los impactos no son solo digitales sino físicos (parada de producción, daño a equipos, riesgo a personas).

| Categoría | Descripción | Propiedad violada |
|-----------|-------------|-------------------|
| **S** - Spoofing | Suplantación de identidad de usuarios, sistemas o dispositivos | Autenticación |
| **T** - Tampering | Manipulación de datos, configuraciones o firmware | Integridad |
| **R** - Repudiation | Negación de acciones realizadas, logs insuficientes | No repudio |
| **I** - Information Disclosure | Divulgación de información sensible o confidencial | Confidencialidad |
| **D** - Denial of Service | Interrupción de disponibilidad de sistemas críticos | Disponibilidad |
| **E** - Elevation of Privilege | Obtención de permisos superiores a los autorizados | Autorización |

---

### 4.2 Matriz de Amenazas por Componente

| ID   | Componente         | Categoría STRIDE | Descripción de la Amenaza                                              | CVE/CWE     |
|------|--------------------|------------------|------------------------------------------------------------------------|-------------|
| TH01 | VPN Acceso Remoto  | Spoofing         | Suplantación de credenciales de proveedor externo sin MFA              | CWE-287     |
| TH02 | HMI / SCADA        | Spoofing         | Suplantación de operador legítimo en sesión HMI no bloqueada           | CWE-306     |
| TH03 | PLCs (PROFINET)    | Tampering        | Modificación no autorizada de lógica de control en PLC Siemens         | CWE-345     |
| TH04 | Historian Server   | Tampering        | Alteración de datos históricos de producción para ocultar anomalías    | CWE-494     |
| TH05 | Servidor Ingeniería| Tampering        | Modificación de proyecto TIA Portal / Studio 5000 con lógica maliciosa | CWE-353     |
| TH06 | SCADA / HMI        | Repudiation      | Ausencia de logs de auditoría en acciones críticas del operador        | CWE-778     |
| TH07 | VPN Acceso Remoto  | Repudiation      | Sesiones de proveedores sin grabación ni trazabilidad de acciones      | CWE-778     |
| TH08 | Red PROFINET       | Information Disclosure | Captura de tráfico industrial sin cifrado (sniffing en red OT)   | CWE-319     |
| TH09 | Historian → ERP    | Information Disclosure | Exposición de datos de producción a través de API mal configurada  | CWE-200     |
| TH10 | Red OT (general)   | Information Disclosure | Reconocimiento de topología OT desde red corporativa comprometida  | CWE-200     |
| TH11 | Servidor SCADA     | Denial of Service | Ataque DoS sobre servidor SCADA principal causa pérdida de visibilidad | CWE-400     |
| TH12 | Red PROFINET/EtherNet/IP | Denial of Service | Flood de paquetes en red industrial interrumpe comunicación PLC-SCADA | CWE-400 |
| TH13 | PLCs               | Denial of Service | Explotación de vulnerabilidad en firmware PLC provoca parada de línea  | CVE-2019-13945 |
| TH14 | Firewall Industrial| Elevation of Privilege | Explotación de misconfiguration permite acceso de IT a OT sin restricción | CWE-732 |
| TH15 | Servidor Ingeniería| Elevation of Privilege | Escalada de privilegios desde cuenta de mantenimiento a admin OT   | CWE-269     |
| TH16 | HMI (Windows 10)   | Elevation of Privilege | Explotación de vulnerabilidad en SO desactualizado para obtener admin local | CWE-269 |

---

### 4.3 Detalle de Amenazas Principales

---

#### AMENAZA TH01: Suplantación de Credenciales de Acceso Remoto
**Categoría STRIDE:** Spoofing  
**Componente:** VPN de Acceso Remoto (T25)  
**Descripción:** Un atacante obtiene las credenciales de un proveedor externo (Siemens o Rockwell Automation) mediante phishing o fuerza bruta. Al no existir autenticación multifactor (MFA) en la VPN, el atacante accede directamente al servidor de ingeniería y desde allí puede modificar la configuración de los PLCs o moverse lateralmente hacia la red OT.  
**Activos afectados:** A04, T25, T13, T05, T06, T07, T08  
**Probabilidad:** Alta  
**Impacto:** Alto  
**Técnicas MITRE ATT&CK for ICS:**
- T0886 - Remote Services
- T0822 - External Remote Services
- T0812 - Default Credentials

---

#### AMENAZA TH02: Sesión HMI No Supervisada
**Categoría STRIDE:** Spoofing  
**Componente:** HMI Sala de Control Principal (T03)  
**Descripción:** Un operador deja una sesión HMI abierta y desbloqueada. Un actor malicioso interno (empleado, contratista, visita) aprovecha la sesión activa para ejecutar comandos de control sobre los PLCs sin autenticación adicional. Este escenario es especialmente crítico en entornos OT donde el bloqueo de pantalla suele desactivarse por comodidad operacional.  
**Activos afectados:** A04, T03, T05, T06, T07, T08  
**Probabilidad:** Media  
**Impacto:** Alto  
**Técnicas MITRE ATT&CK for ICS:**
- T0891 - Hardcoded Credentials
- T0859 - Valid Accounts

---

#### AMENAZA TH03: Modificación de Lógica de Control en PLC
**Categoría STRIDE:** Tampering  
**Componente:** PLCs Siemens S7-1500 (T05, T06)  
**Descripción:** Un atacante con acceso al servidor de ingeniería (TIA Portal) o directamente a la red PROFINET modifica la lógica de control (ladder logic / function blocks) de un PLC Siemens. La modificación puede ser sutil (cambiar un umbral de temperatura, modificar tiempos de ciclo) y difícil de detectar sin comparación de versiones. El impacto puede incluir daño físico a maquinaria, producción de piezas defectuosas o parada de línea.  
**Activos afectados:** A06, A01, T05, T06, T13, T20  
**Probabilidad:** Media  
**Impacto:** Crítico  
**Técnicas MITRE ATT&CK for ICS:**
- T0833 - Modify Control Logic
- T0821 - Modify Controller Tasking
- T0836 - Modify Parameter

---

#### AMENAZA TH04: Alteración de Datos en Historian
**Categoría STRIDE:** Tampering  
**Componente:** Historian Server - OSIsoft PI (T09)  
**Descripción:** Un atacante con acceso a la red OT modifica registros históricos en el Historian para ocultar una anomalía de proceso, un ataque previo o para falsificar métricas de producción reportadas al ERP. Esto puede afectar decisiones de negocio basadas en datos erróneos y dificultar la investigación forense posterior.  
**Activos afectados:** A02, T09, T17  
**Probabilidad:** Baja  
**Impacto:** Alto  
**Técnicas MITRE ATT&CK for ICS:**
- T0832 - Manipulation of View
- T0831 - Manipulation of Control

---

#### AMENAZA TH05: Inserción de Lógica Maliciosa en Proyecto de Ingeniería
**Categoría STRIDE:** Tampering  
**Componente:** Servidor de Ingeniería - TIA Portal / Studio 5000 (T13)  
**Descripción:** Un atacante compromete el servidor de ingeniería (mediante acceso remoto o movimiento lateral desde IT) e inserta código malicioso en los proyectos de PLC almacenados. La próxima vez que un ingeniero descargue el proyecto al PLC, el código malicioso se ejecutará en el entorno de control físico. Es análogo a un ataque de supply chain interno.  
**Activos afectados:** A06, A08, T13, T05, T06, T07, T08  
**Probabilidad:** Baja  
**Impacto:** Crítico  
**Técnicas MITRE ATT&CK for ICS:**
- T0839 - Module Firmware
- T0873 - Project File Infection

---

#### AMENAZA TH08: Sniffing de Tráfico en Red Industrial
**Categoría STRIDE:** Information Disclosure  
**Componente:** Red PROFINET (T20) / Red EtherNet/IP (T21)  
**Descripción:** Los protocolos PROFINET y EtherNet/IP no cifran el tráfico por defecto. Un atacante con acceso físico o lógico a la red industrial puede capturar el tráfico y obtener: parámetros de proceso, recetas de producción, topología de red, credenciales en texto plano (si las hay), y comandos de control para analizarlos o reproducirlos (replay attack).  
**Activos afectados:** A01, A06, A09, T20, T21  
**Probabilidad:** Media  
**Impacto:** Alto  
**Técnicas MITRE ATT&CK for ICS:**
- T0885 - Commonly Used Port
- T0842 - Network Sniffing

---

#### AMENAZA TH11: DoS sobre Servidor SCADA Principal
**Categoría STRIDE:** Denial of Service  
**Componente:** Servidor SCADA Principal (T01)  
**Descripción:** Un atacante lanza un ataque de denegación de servicio contra el servidor SCADA principal, sobrecargando su CPU o agotando recursos de red. Los operadores pierden visibilidad completa del proceso productivo. Si el servidor redundante (T02) no activa el failover correctamente, la planta queda operando a "ciegas", lo cual puede derivar en condiciones inseguras o parada de emergencia.  
**Activos afectados:** T01, T02, T03, T04  
**Probabilidad:** Media  
**Impacto:** Alto  
**Técnicas MITRE ATT&CK for ICS:**
- T0814 - Denial of Control
- T0816 - Device Restart/Shutdown

---

#### AMENAZA TH13: Explotación de Vulnerabilidad en Firmware de PLC
**Categoría STRIDE:** Denial of Service  
**Componente:** PLCs (T05–T08)  
**Descripción:** Los PLCs industriales presentan vulnerabilidades conocidas en sus implementaciones de protocolos de red (PROFINET, EtherNet/IP, Modbus). Un atacante envía paquetes malformados o explota una vulnerabilidad del firmware (como CVE-2019-13945 en Siemens S7) causando el reinicio o la parada del PLC. En un entorno de línea de producción continua, esto implica parada inmediata de la línea afectada.  
**Activos afectados:** T05, T06, T07, T08, T16  
**Probabilidad:** Media  
**Impacto:** Crítico  
**Técnicas MITRE ATT&CK for ICS:**
- T0816 - Device Restart/Shutdown
- T0835 - Manipulate I/O Image

---

#### AMENAZA TH14: Movimiento Lateral de IT a OT por Misconfiguration de Firewall
**Categoría STRIDE:** Elevation of Privilege  
**Componente:** Firewall Industrial (T12) - Trust Boundary TB-01  
**Descripción:** Una regla de firewall mal configurada (o demasiado permisiva) permite que un atacante que ya comprometió un equipo en la red corporativa (IT) acceda directamente a la red OT sin pasar por los controles de la DMZ. Esto representa el escenario más crítico para entornos IT/OT convergentes: una brecha en IT se convierte automáticamente en una brecha en OT.  
**Activos afectados:** T12, T01, T02, T05, T06, T07, T08, A09  
**Probabilidad:** Media  
**Impacto:** Crítico  
**Técnicas MITRE ATT&CK for ICS:**
- T0866 - Exploitation of Remote Services
- T0843 - Program Download

---

### 4.4 Resumen de Amenazas por Categoría STRIDE

| Categoría STRIDE     | Cantidad de Amenazas | Amenazas de mayor impacto |
|----------------------|----------------------|---------------------------|
| Spoofing             | 2                    | TH01, TH02                |
| Tampering            | 3                    | TH03, TH05                |
| Repudiation          | 2                    | TH06, TH07                |
| Information Disclosure | 3                  | TH08, TH09                |
| Denial of Service    | 3                    | TH11, TH13                |
| Elevation of Privilege | 3                  | TH14, TH15                |
| **TOTAL**            | **16**               |                           |

---

### 4.5 Enfoque IT/OT - Consideraciones Específicas

A diferencia de un análisis de riesgos IT tradicional, en entornos OT/ICS las amenazas tienen implicaciones adicionales:

| Aspecto          | Entorno IT                        | Entorno OT/ICS                              |
|------------------|-----------------------------------|---------------------------------------------|
| Prioridad máxima | Confidencialidad                  | **Disponibilidad** (la planta no puede parar) |
| Parcheo          | Frecuente y automatizable         | Restringido por ventanas de mantenimiento   |
| Impacto de DoS   | Pérdida de datos / servicio       | Parada de producción, daño físico, riesgo a personas |
| Protocolos       | TCP/IP estándar con TLS           | PROFINET, EtherNet/IP, Modbus (sin cifrado nativo) |
| Ciclo de vida    | 3-5 años                          | 15-25 años (sistemas legacy frecuentes)     |
| Actualizaciones  | Automáticas aceptadas             | Requieren validación y pruebas extensas     |
