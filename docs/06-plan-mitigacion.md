# 06 - Plan de Mitigación
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Christian Busquets  
**Fecha:** 21/05/2025  
**Versión:** 1.0  

---

## 7. PLAN DE MITIGACIÓN

### 7.1 Controles de Seguridad por Amenaza

Los controles se priorizan según el ranking DREAD del documento 04, atendiendo primero las amenazas de mayor puntaje.

| ID  | Amenaza                                              | Control de Seguridad                              | Prioridad | Estado  |
|-----|------------------------------------------------------|---------------------------------------------------|-----------|---------|
| C01 | TH14 - Movimiento lateral IT→OT (firewall)           | Revisión y endurecimiento de reglas de firewall   | 1         | Pendiente |
| C02 | TH01 - Suplantación credenciales VPN                 | Implementar MFA en acceso VPN                     | 2         | Pendiente |
| C03 | TH01 - Suplantación credenciales VPN                 | VPN bajo demanda (no permanente)                  | 2         | Pendiente |
| C04 | TH03 - Modificación lógica PLC Siemens               | Control de versiones de proyectos PLC             | 3         | Pendiente |
| C05 | TH03 - Modificación lógica PLC Siemens               | Autenticación para descarga de programas PLC      | 3         | Pendiente |
| C06 | TH13 - Explotación firmware PLC                      | Actualización de firmware PLCs                    | 4         | Pendiente |
| C07 | TH11 - DoS sobre servidor SCADA                      | Redundancia activa servidor SCADA                 | 5         | Pendiente |
| C08 | TH08 - Sniffing tráfico PROFINET                     | Segmentación con VLANs en red de campo            | 6         | Pendiente |
| C09 | TH06 - Ausencia de logs en SCADA/HMI                 | Implementar logging de auditoría en SCADA         | 7         | Pendiente |
| C10 | TH16 - SO desactualizado en HMI                      | Plan de parcheo para HMIs (Windows 10 LTSC)       | 8         | Pendiente |
| C11 | TH02 - Sesión HMI no supervisada                     | Bloqueo automático de sesión HMI por inactividad  | 9         | Pendiente |
| C12 | TH05 - Lógica maliciosa en proyecto ingeniería       | Restricción de acceso al servidor de ingeniería   | 10        | Pendiente |
| C13 | TH15 - Escalada de privilegios mantenimiento         | Principio de mínimo privilegio en cuentas OT      | 11        | Pendiente |
| C14 | TH12 - Flood de paquetes en red industrial           | Rate limiting en switches industriales            | 12        | Pendiente |
| C15 | TH10 - Reconocimiento topología OT                   | Ocultar topología OT desde red corporativa        | 13        | Pendiente |
| C16 | TH09 - Exposición datos vía API Historian            | Hardening de API REST del Historian               | 14        | Pendiente |
| C17 | TH07 - Sesiones VPN sin trazabilidad                 | Grabación y auditoría de sesiones VPN proveedor   | 15        | Pendiente |
| C18 | TH04 - Alteración datos Historian                    | Control de integridad de datos en Historian       | 16        | Pendiente |

---

### 7.2 Controles por Categoría

#### Controles Preventivos

- [x] **Autenticación multifactor (MFA)** en acceso VPN para proveedores externos
- [x] **Acceso VPN bajo demanda** — habilitar solo durante ventanas de mantenimiento autorizadas
- [x] **Principio de mínimo privilegio** — cuentas de operador, ingeniero y mantenimiento con permisos diferenciados
- [x] **Control de versiones de proyectos PLC** — repositorio versionado de lógica de control (TIA Portal, Studio 5000)
- [x] **Autenticación para descarga de programas PLC** — requerir credenciales al descargar lógica al PLC
- [x] **Segmentación de red con VLANs** — separar tráfico PROFINET, EtherNet/IP y red de supervisión
- [x] **Endurecimiento del firewall industrial** — reglas estrictas, whitelist de flujos permitidos, revisión periódica
- [x] **Bloqueo automático de sesión HMI** — timeout de inactividad en todas las estaciones HMI
- [x] **Restricción de acceso al servidor de ingeniería** — solo ingenieros autorizados, con registro de acceso

#### Controles Detectivos

- [x] **Logging de auditoría en SCADA/HMI** — registro de todas las acciones de operadores e ingenieros
- [x] **Monitoreo de cambios en configuración PLC** — alertas automáticas ante modificaciones no programadas
- [x] **Grabación de sesiones VPN de proveedores** — registro completo de actividad durante mantenimiento remoto
- [x] **Monitoreo de tráfico en red OT** — detección de anomalías en patrones de comunicación PROFINET/EtherNet/IP
- [x] **Alertas de autenticación fallida** — detección de intentos de fuerza bruta en VPN y SCADA

#### Controles Correctivos

- [x] **Plan de respuesta a incidentes OT** — procedimiento específico para incidentes en entorno industrial
- [x] **Procedimientos de backup de configuración PLC** — respaldo regular de lógica de control y parámetros
- [x] **Plan de recuperación SCADA** — failover automático al servidor redundante ante falla del principal
- [x] **Procedimiento de restauración de firmware PLC** — rollback a versión anterior ante compromiso detectado

---

### 7.3 Plan de Segmentación de Red

La segmentación de red es uno de los controles más críticos en entornos OT/ICS. Reduce drásticamente la superficie de ataque al limitar el movimiento lateral entre zonas.

#### Segmentación propuesta por zonas

| Zona | Red | Dispositivos | Comunicación permitida |
|------|-----|--------------|------------------------|
| Zona 1 - Corporativa | 192.168.1.0/24 | ERP SAP, PCs, Servidor email | Solo hacia DMZ vía API REST |
| Zona 2 - DMZ Industrial | 192.168.10.0/24 | Historian, Srv. Ingeniería, VPN GW, AV/Patch | Mediadora entre IT y OT |
| Zona 3 - SCADA/HMI | 192.168.20.0/24 | Servidor SCADA, HMIs, DB Server | Solo hacia PLCs vía PROFINET/EtherNet/IP |
| Zona 4A - PLCs Siemens | 192.168.30.0/24 | PLC S7-1500 L1, PLC S7-1500 L2, Switch PROFINET | Solo hacia SCADA y campo |
| Zona 4B - PLCs Allen Bradley | 192.168.40.0/24 | PLC ControlLogix L3, PLC ControlLogix L4, Switch EtherNet/IP | Solo hacia SCADA y campo |

#### Reglas de firewall propuestas (TB-01)

| Regla | Origen | Destino | Puerto/Protocolo | Acción |
|-------|--------|---------|------------------|--------|
| R01 | Zona Corporativa | Historian (DMZ) | TCP 443 (HTTPS/API) | Permitir |
| R02 | Historian (DMZ) | ERP SAP | TCP 443 (OPC-UA) | Permitir |
| R03 | VPN Gateway | Srv. Ingeniería | TCP 102 (S7) | Permitir (bajo demanda) |
| R04 | Cualquier | Zona SCADA | Cualquiera | Denegar |
| R05 | Cualquier | Zona PLCs | Cualquiera | Denegar |
| R06 | Zona SCADA | Historian | UDP 102 (OPC-UA) | Permitir |
| R07 | * | * | Cualquiera | Denegar (default deny) |

---

### 7.4 Controles de Acceso Remoto

El acceso remoto de proveedores (Siemens, Rockwell Automation) es uno de los vectores de ataque más críticos identificados (TH01, DREAD 39/50).

#### Controles implementados

| Control | Descripción | Normativa de referencia |
|---------|-------------|------------------------|
| MFA obligatorio | Segundo factor requerido para toda sesión VPN | NIST SP 800-82 §6.2 |
| VPN bajo demanda | Acceso habilitado solo durante ventana de mantenimiento autorizada | ISA/IEC 62443-2-1 |
| Grabación de sesión | Registro completo de acciones durante sesión remota | NIST SP 800-53 AU-14 |
| Autenticación de doble aprobación | Solicitud de acceso aprobada por responsable OT + TI | ISA/IEC 62443-3-3 |
| Tiempo máximo de sesión | Sesión VPN expira automáticamente tras 2 horas | NIST SP 800-53 AC-12 |
| Acceso con mínimo privilegio | Proveedor solo accede al dispositivo específico que requiere | NIST SP 800-53 AC-6 |
| Auditoría post-sesión | Revisión de log de sesión dentro de las 24h posteriores | NIST SP 800-53 AU-6 |

---

### 7.5 Matriz de Controles NIST SP 800-53 / ISA-IEC 62443

| ID Control | Control | Descripción | Referencia | Amenazas cubiertas |
|------------|---------|-------------|------------|--------------------|
| AC-1 | Access Control Policy | Política de control de acceso OT | NIST AC-1 | TH01, TH02, TH15 |
| AC-2 | Account Management | Gestión de cuentas de usuario | NIST AC-2 | TH01, TH02 |
| AC-6 | Least Privilege | Mínimo privilegio en cuentas OT | NIST AC-6 | TH15, TH14 |
| AC-12 | Session Termination | Cierre automático de sesión | NIST AC-12 | TH02, TH07 |
| AU-2 | Audit Events | Eventos de auditoría en SCADA | NIST AU-2 | TH06, TH07 |
| AU-6 | Audit Review | Revisión periódica de logs | NIST AU-6 | TH06, TH07 |
| AU-14 | Session Audit | Grabación de sesiones remotas | NIST AU-14 | TH07, TH01 |
| CM-2 | Baseline Configuration | Configuración base de PLCs y SCADA | NIST CM-2 | TH03, TH05 |
| CM-6 | Configuration Settings | Control de cambios de configuración | NIST CM-6 | TH03, TH04 |
| IA-2 | Identification Auth. | Autenticación de usuarios con MFA | NIST IA-2 | TH01, TH02 |
| IA-3 | Device Identification | Autenticación de dispositivos OT | NIST IA-3 | TH03, TH13 |
| IR-4 | Incident Handling | Plan de respuesta a incidentes OT | NIST IR-4 | Todas |
| SC-7 | Boundary Protection | Firewall y segmentación de red | NIST SC-7 | TH14, TH10 |
| SC-8 | Transmission Conf. | Confidencialidad en tránsito | NIST SC-8 | TH08, TH09 |
| SI-2 | Flaw Remediation | Parcheo de sistemas y firmware | NIST SI-2 | TH13, TH16 |
| SI-3 | Malicious Code Prot. | Protección contra código malicioso | NIST SI-3 | TH05, TH16 |
