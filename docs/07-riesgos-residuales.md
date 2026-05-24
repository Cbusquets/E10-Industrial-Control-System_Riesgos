# 07 - Riesgos Residuales
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Christian Busquets  
**Fecha:** 21/05/2025  
**Versión:** 1.0  

---

## 9. RIESGOS RESIDUALES

### 9.1 Introducción

Los riesgos residuales son aquellos que persisten luego de aplicar los controles de seguridad definidos en el documento 06. En entornos OT/ICS, el riesgo cero no existe — siempre quedan riesgos que se aceptan conscientemente debido a limitaciones técnicas, operacionales o económicas propias del entorno industrial.

Las razones más comunes por las que persisten riesgos en entornos ICS son:

- **Compatibilidad industrial:** los protocolos PROFINET y EtherNet/IP no tienen cifrado nativo y reemplazarlos implicaría rediseñar toda la red de campo
- **Continuidad operacional:** aplicar parches o actualizar firmware requiere paradas de producción planificadas, lo que retrasa la remediación
- **Ciclo de vida largo:** los PLCs y equipos de campo tienen vida útil de 15-25 años, por lo que no siempre es posible actualizar a versiones más seguras
- **Dependencia de proveedores:** el acceso remoto de Siemens y Rockwell es necesario para soporte técnico especializado

---

### 9.2 Matriz de Riesgos Residuales

| ID  | Riesgo Residual | Amenaza origen | Prob. | Imp. | Justificación de aceptación |
|-----|-----------------|----------------|-------|------|------------------------------|
| R01 | Tráfico PROFINET sin cifrado en red de campo | TH08 | Media | Alto | PROFINET no soporta cifrado nativo en implementaciones actuales. Mitigado parcialmente con VLANs y segmentación. Requeriría reemplazo de infraestructura completa para eliminar. |
| R02 | Tráfico EtherNet/IP sin cifrado en red de campo | TH08 | Media | Alto | Igual que R01 para protocolo Allen Bradley. CIP Security (extensión de cifrado) existe pero requiere hardware compatible y ventana de actualización extensa. |
| R03 | Vulnerabilidades en firmware de PLCs sin parchear | TH13 | Baja | Alto | El parcheo de firmware requiere parada de producción planificada. Se aplica en ventanas de mantenimiento programadas, por lo que siempre existe un período de exposición entre el parche y su aplicación. |
| R04 | HMI con actualizaciones de seguridad retrasadas | TH16 | Media | Medio | Windows 10 LTSC en HMIs requiere validación de compatibilidad con software SCADA (WinCC) antes de cada actualización. El retraso es estructural al entorno OT. |
| R05 | Acceso remoto VPN de proveedores como vector de entrada | TH01 | Baja | Alto | Aunque se implementa MFA y acceso bajo demanda, el acceso remoto de proveedores es operacionalmente necesario. El riesgo se reduce pero no se elimina. |
| R06 | Insider threat — operador o contratista con acceso legítimo | TH02, TH15 | Baja | Alto | El mínimo privilegio y el logging reducen el riesgo, pero un actor interno con acceso autorizado siempre representa un riesgo residual difícil de eliminar sin impactar operaciones. |
| R07 | Ausencia de IDS/IPS en red OT | TH10, TH12 | Media | Medio | La implementación de IDS/IPS en redes OT requiere hardware especializado y puede generar falsos positivos que interrumpan procesos productivos. Es una mejora planificada a mediano plazo. |
| R08 | Dependencia de proveedor para soporte de PLCs legacy | TH05 | Baja | Medio | Los PLCs Siemens S7-1500 y Allen Bradley ControlLogix dependen de soporte del fabricante para actualizaciones críticas. Si el fabricante discontinúa soporte, el riesgo aumenta. |

---

### 9.3 Análisis de Riesgos Residuales Críticos

#### R01 / R02 — Protocolos industriales sin cifrado (PROFINET / EtherNet/IP)

Este es el riesgo residual más significativo del entorno. Los protocolos industriales utilizados no tienen cifrado nativo y son fundamentales para la operación de las 4 líneas de producción. 

**¿Por qué se acepta?**
Reemplazar la infraestructura de red de campo implicaría:
- Parada completa de producción por semanas
- Inversión en hardware compatible con PROFINET Security Class 3 o CIP Security
- Recertificación de toda la lógica de control

**Mitigaciones aplicadas que reducen el riesgo:**
- Segmentación por VLANs (limita el acceso a quien puede escuchar el tráfico)
- Firewall con reglas estrictas (limita quién puede llegar a la red de campo)
- Monitoreo de tráfico OT (detecta anomalías en patrones de comunicación)

**Riesgo residual aceptado:** exposición a sniffing y replay attacks desde dentro de la red OT.

---

#### R05 — Acceso remoto VPN de proveedores

El acceso remoto de Siemens y Rockwell Automation es operacionalmente necesario para soporte técnico especializado. Sin este acceso, la planta no podría recibir asistencia remota ante fallas críticas de PLCs.

**¿Por qué se acepta?**
Eliminar el acceso remoto implicaría depender exclusivamente de visitas presenciales de técnicos especializados, aumentando el tiempo de parada ante incidentes críticos.

**Mitigaciones aplicadas:**
- MFA obligatorio
- Acceso bajo demanda (no permanente)
- Grabación de sesión
- Doble aprobación (OT + TI)
- Timeout de sesión de 2 horas

**Riesgo residual aceptado:** el canal VPN existe y puede ser atacado si las credenciales del proveedor son comprometidas fuera del entorno de la planta.

---

#### R07 — Ausencia de IDS/IPS en red OT

Actualmente no existe una solución de detección de intrusos en la red OT. Esto significa que ataques de movimiento lateral o sniffing dentro de la red industrial podrían no ser detectados en tiempo real.

**¿Por qué se acepta temporalmente?**
Los IDS/IPS industriales (como Claroty, Dragos, Nozomi Networks) requieren:
- Inversión significativa en licenciamiento y hardware
- Período de calibración para evitar falsos positivos que puedan interrumpir procesos
- Personal capacitado para operar y responder a alertas OT

**Plan de mejora:** implementación de IDS/IPS OT planificada como mejora a mediano plazo (12-18 meses).

---

### 9.4 Conclusiones y Recomendaciones

#### 10.1 Resumen Ejecutivo

El análisis de riesgos del sistema SCADA/ICS de la planta manufacturera de autopartes identificó **16 amenazas** distribuidas en las 6 categorías STRIDE. La amenaza de mayor criticidad es el **movimiento lateral de IT a OT** por misconfiguration del firewall industrial (TH14, DREAD 40/50), seguida por la **suplantación de credenciales VPN** de proveedores (TH01, 39/50) y la **modificación de lógica de control en PLCs** (TH03, 38/50).

Se definieron **18 controles de seguridad** priorizados, cubriendo acceso remoto, segmentación de red, autenticación, logging y parcheo. Luego de aplicar los controles, quedan **8 riesgos residuales aceptados**, siendo los más significativos la falta de cifrado nativo en protocolos industriales (PROFINET, EtherNet/IP) y la necesidad operacional del acceso remoto de proveedores.

#### 10.2 Recomendaciones Prioritarias

1. **Implementar MFA en acceso VPN** — control de mayor impacto con menor complejidad técnica. Reduce TH01 de DREAD 39 a nivel MEDIO estimado.
2. **Revisar y endurecer reglas de firewall industrial** — elimina el riesgo de movimiento lateral IT→OT (TH14, el más crítico).
3. **Implementar logging de auditoría en SCADA/HMI** — sin logs no hay detección ni forense posible ante un incidente.
4. **Establecer control de versiones de proyectos PLC** — protege contra modificación de lógica de control (TH03, TH05).
5. **Planificar actualización de firmware PLCs** — en próxima ventana de mantenimiento programada.
6. **Iniciar evaluación de IDS/IPS OT** — mejora de mediano plazo para detectar ataques dentro de la red industrial.

#### 10.3 Próximos Pasos

- [ ] Implementar MFA en VPN — plazo inmediato (30 días)
- [ ] Revisar reglas de firewall industrial — plazo inmediato (30 días)
- [ ] Implementar logging en SCADA/HMI — plazo corto (60 días)
- [ ] Control de versiones PLC — plazo corto (60 días)
- [ ] Parcheo de firmware PLCs — próxima ventana de mantenimiento
- [ ] Evaluación IDS/IPS OT — plazo mediano (12-18 meses)
- [ ] Re-evaluar riesgos residuales en 6 meses

---

### Anexo A — Glosario

- **STRIDE:** Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege
- **DREAD:** Damage, Reproducibility, Exploitability, Affected Users, Discoverability
- **OT:** Operational Technology — tecnología de operación industrial
- **ICS:** Industrial Control System — sistema de control industrial
- **SCADA:** Supervisory Control and Data Acquisition
- **PLC:** Programmable Logic Controller
- **HMI:** Human-Machine Interface
- **PROFINET:** Protocolo industrial Ethernet de Siemens
- **EtherNet/IP:** Protocolo industrial Ethernet de Rockwell/Allen Bradley
- **DMZ:** Demilitarized Zone — zona desmilitarizada de red
- **MFA:** Multi-Factor Authentication — autenticación multifactor
- **IDS/IPS:** Intrusion Detection/Prevention System

### Anexo B — Referencias

- NIST SP 800-82 Rev. 3 — Guide to ICS Security
- ISA/IEC 62443 — Industrial Automation and Control Systems Security
- MITRE ATT&CK for ICS — https://collaborate.mitre.org/attackics/
- NIST SP 800-53 Rev. 5 — Security and Privacy Controls
- NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments

### Anexo C — Herramientas Utilizadas

- **Claude (claude.ai) - Claude Sonnet 4.6:** Generación de documentación, análisis STRIDE/DREAD, mapeo ATT&CK for ICS
- **draw.io (app.diagrams.net):** Elaboración del diagrama de arquitectura Purdue y flujo de ataque
