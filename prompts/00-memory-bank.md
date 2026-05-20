# Memory Bank - Contexto para IA
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS  
**Última actualización:** 20/05/2025  

---

## Descripción del Proyecto

Trabajo académico de análisis de riesgos y modelado de amenazas para la materia de Seguridad Informática (UTU). Se aplica la metodología STRIDE + DREAD sobre un sistema SCADA/ICS ficticio, siguiendo la plantilla `templates/Plantilla_Análisis_de_Riesgo.md`.

**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Mateo Sparano, Christian Busquets  
**Entrega:** 26 o 28 de mayo de 2025  
**Herramienta IA utilizada:** Claude (claude.ai) - Claude Sonnet 4.6

---

## Escenario Ficticio Definido

**Planta:** Manufacturera de autopartes (ficticia)  
**Sistema:** SCADA/ICS para control de 4 líneas de producción  
**Modelo de red:** Purdue Model (niveles 0–4)  
**PLCs:**
- Siemens S7-1500 → Líneas 1 y 2 → protocolo PROFINET
- Allen Bradley ControlLogix → Líneas 3 y 4 → protocolo EtherNet/IP

**Software SCADA:** WinCC (Siemens)  
**Historian:** OSIsoft PI  
**ERP:** SAP (red corporativa)  
**HMI:** Windows 10 LTSC  
**Acceso remoto:** VPN para proveedores (Siemens, Rockwell Automation)  
**Firewall industrial:** Presente, con DMZ industrial  

---

## Archivos Generados

### `docs/01-inventario-activos.md`
Inventario completo de activos del sistema. Contiene:
- 10 activos de información (A01–A10): recetas de producción, datos Historian, credenciales SCADA, configuración PLCs, firmware, topología de red, datos ERP, reportes de producción, logs de alarmas, planes de mantenimiento
- 25 activos tecnológicos (T01–T25): servidores SCADA principal y redundante, 2 HMIs, 4 PLCs, Historian, switches industriales PROFINET y EtherNet/IP, firewall, DMZ, servidor ingeniería, estación VPN remota, sensores, actuadores, ERP SAP, red OT, red corporativa
- Activos intangibles: reputación, continuidad operacional, propiedad intelectual, cumplimiento normativo (ISA/IEC 62443, NIST SP 800-82)

### `docs/02-arquitectura-trust-boundaries.md`
Arquitectura del sistema y límites de confianza. Contiene:
- Diagrama ASCII actualizado del Modelo Purdue con los 4 trust boundaries (TB-01 a TB-04)
- Separación de Cell/Area Zone Siemens (PROFINET) y Allen Bradley (EtherNet/IP) en nivel 1, cada una con Local HMI, PLCs y switch
- DB Server en Level 3 (Manufacturing Zone)
- AV/Patch Server en DMZ Industrial
- Tabla de flujos de datos con protocolos (F01–F08)
- Tabla de actores del sistema (operador, ingeniero, admin, proveedor externo, ERP, atacante)
- 4 Trust Boundaries definidos:
  - TB-01: Red corporativa IT ↔ DMZ Industrial (firewall) — criticidad ALTA
  - TB-02: DMZ Industrial ↔ Red SCADA/HMI (switches VLAN) — criticidad ALTA
  - TB-03: Red SCADA ↔ PLCs (PROFINET/EtherNet/IP sin cifrado nativo) — criticidad CRÍTICA
  - TB-04: Acceso remoto externo VPN proveedores ↔ DMZ — criticidad ALTA
- Referencia al diagrama: `diagrams/arquitectura.png` y `diagrams/arquitectura.drawio`
- Supuestos del análisis (VPN permanente, sin IDS/IPS, HMI con parches retrasados, red de campo plana)

### `docs/03-analisis-stride.md`
Análisis de amenazas con metodología STRIDE. Contiene:
- 16 amenazas identificadas (TH01–TH16) distribuidas en 6 categorías STRIDE
- Matriz de amenazas por componente con CVE/CWE referenciados
- Detalle de 8 amenazas principales con activos afectados, probabilidad, impacto y técnicas MITRE ATT&CK for ICS
- Amenazas más críticas:
  - TH01: Suplantación credenciales VPN proveedor (sin MFA) — Spoofing
  - TH03: Modificación lógica PLC Siemens (TIA Portal) — Tampering
  - TH05: Inserción código malicioso en proyecto ingeniería — Tampering
  - TH08: Sniffing tráfico PROFINET sin cifrado — Information Disclosure
  - TH11: DoS sobre servidor SCADA principal — Denial of Service
  - TH13: Explotación CVE-2019-13945 en firmware PLC Siemens — DoS
  - TH14: Movimiento lateral IT→OT por misconfiguration firewall — Elevation of Privilege
- Tabla comparativa enfoque IT vs OT (prioridad disponibilidad en OT, protocolos sin cifrado, ciclo de vida 15-25 años)

### `docs/04-priorizacion-dread.md`
Priorización de las 16 amenazas STRIDE con metodología DREAD. Contiene:
- Tabla de criterios DREAD con escala detallada (1–10 por criterio, total sobre 50)
- Matriz DREAD completa para las 16 amenazas con puntaje por criterio y nivel resultante
- Ranking de prioridad de 1° a 16°
- Resultados: 1 amenaza CRÍTICA (TH14 - movimiento lateral IT→OT, 40/50), 14 amenazas ALTO, 1 amenaza MEDIO (TH04 - alteración Historian, 26/50)
- Análisis de resultados con observación OT vs IT (disponibilidad e integridad dominan sobre confidencialidad)

---

## Archivos Pendientes de Generar

| Archivo | Descripción | Estado |
|---------|-------------|--------|
| `docs/05-mapa-attack.md` | Técnicas MITRE ATT&CK for ICS mapeadas, flujo de ataque | ⏳ Pendiente |
| `docs/06-plan-mitigacion.md` | Controles de seguridad, plan de segmentación, controles acceso remoto | ⏳ Pendiente |
| `docs/07-riesgos-residuales.md` | Riesgos que persisten luego de aplicar controles | ⏳ Pendiente |
| `diagrams/arquitectura.png` | Diagrama visual de arquitectura Purdue con trust boundaries | ✅ Generado (subir al repo) |
| `diagrams/arquitectura.drawio` | Archivo fuente editable del diagrama | ✅ Generado (subir al repo) |
| `diagrams/attack-flow.png` | Diagrama visual de flujo de ataque | ⏳ Pendiente |
| `README.md` | README del repositorio con descripción, herramientas IA, equipo | ⏳ Pendiente |

---

## Decisiones Clave Tomadas

- Se usa **Modelo Purdue** como framework de arquitectura (ISA-95)
- El **alcance del análisis es Nivel 1 a Nivel 4** — el Nivel 0 (sensores/actuadores) aparece en el diagrama como referencia visual pero no se analiza como zona de red (comunicación analógica/digital, sin IP)
- Las amenazas se numeran **TH01–TH16** en el STRIDE
- Los activos de información se numeran **A01–A10**
- Los activos tecnológicos se numeran **T01–T25**
- Se referencia **MITRE ATT&CK for ICS** (no el ATT&CK Enterprise) para técnicas
- El escenario de mayor criticidad es **TB-03** (red SCADA ↔ PLCs sin cifrado nativo)
- La amenaza de mayor impacto combinado es **TH14** (movimiento lateral IT→OT)
- Se asume que **no hay IDS/IPS** en la red OT actualmente
- Se asume que la **VPN está habilitada de forma permanente** para proveedores

---

## Normativas de Referencia

- **NIST SP 800-82 Rev. 3** — Guide to ICS Security (referencia principal OT)
- **ISA/IEC 62443** — Industrial Automation and Control Systems Security
- **MITRE ATT&CK for ICS** — https://collaborate.mitre.org/attackics/
- **NIST SP 800-53 Rev. 5** — Controles de seguridad para plan de mitigación

---

## Instrucciones para Continuar

Al iniciar una nueva sesión, adjuntar este archivo junto con los documentos `docs/` relevantes según la tarea a realizar. Por ejemplo:

- Para generar `05-mapa-attack.md` → adjuntar `03-analisis-stride.md`
- Para generar `06-plan-mitigacion.md` → adjuntar `03-analisis-stride.md` y `04-priorizacion-dread.md`
- Para generar `07-riesgos-residuales.md` → adjuntar `06-plan-mitigacion.md`
- Para el README final → adjuntar todos los docs generados
