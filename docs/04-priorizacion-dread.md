# 04 - Priorización de Riesgos - Metodología DREAD
**Proyecto:** Análisis de Riesgos - Sistema SCADA/ICS Planta Manufacturera  
**Escenario:** 10 - Industrial Control System (SCADA/ICS)  
**Equipo:** Mateo Sparano, Christian Busquets  
**Fecha:** 18/05/2025  
**Versión:** 1.0  

---

## 5. ANÁLISIS DE RIESGOS - METODOLOGÍA DREAD

### 5.1 Criterios DREAD

DREAD es una metodología de cuantificación de riesgos que asigna un puntaje numérico a cada amenaza según cinco criterios. Permite priorizar qué amenazas atender primero de forma objetiva.

| Criterio           | Descripción                                              | Escala |
|--------------------|----------------------------------------------------------|--------|
| **D** - Damage     | Daño potencial si la amenaza se explota con éxito        | 1–10   |
| **R** - Reproducibility | Facilidad para reproducir el ataque repetidamente   | 1–10   |
| **E** - Exploitability | Dificultad técnica para explotar la vulnerabilidad   | 1–10   |
| **A** - Affected Users | Cantidad de usuarios/sistemas/procesos afectados    | 1–10   |
| **D** - Discoverability | Facilidad para descubrir la vulnerabilidad          | 1–10   |

**Escala de severidad (sobre 50 puntos):**

| Rango  | Nivel    | Acción requerida                        |
|--------|----------|-----------------------------------------|
| 40–50  | CRÍTICO  | Remediar inmediatamente                 |
| 30–39  | ALTO     | Remediar a la brevedad posible          |
| 20–29  | MEDIO    | Remediar en siguiente ciclo de mejora   |
| 10–19  | BAJO     | Monitorear                              |
| 1–9    | MÍNIMO   | Aceptar riesgo                          |

---

### 5.2 Escala DREAD Detallada

| Puntaje | Daño (D)          | Reproducibilidad (R) | Explotabilidad (E) | Afectados (A)   | Descubribilidad (D) |
|---------|-------------------|----------------------|--------------------|-----------------|----------------------|
| 10      | Sistema completo  | Siempre              | Fácil (tool disponible) | Todos        | Muy fácil (pública)  |
| 7–9     | Significativo     | Frecuente            | Moderado           | Muchos          | Fácil                |
| 4–6     | Moderado          | A veces              | Difícil            | Algunos         | Promedio             |
| 1–3     | Mínimo            | Rara vez             | Muy difícil        | Pocos           | Difícil              |

---

### 5.3 Matriz de Riesgos DREAD

| ID   | Amenaza                                              | D  | R  | E  | A  | D  | TOTAL | Nivel    |
|------|------------------------------------------------------|----|----|----|----|----|-------|----------|
| TH14 | Movimiento lateral IT→OT por misconfiguration firewall | 10 | 7  | 7  | 10 | 6  | 40/50 | **CRÍTICO** |
| TH03 | Modificación de lógica de control en PLC Siemens     | 10 | 6  | 7  | 9  | 6  | 38/50 | **ALTO** |
| TH05 | Inserción de lógica maliciosa en proyecto ingeniería | 10 | 5  | 6  | 9  | 5  | 35/50 | **ALTO** |
| TH13 | Explotación CVE en firmware PLC (parada de línea)    | 9  | 7  | 6  | 8  | 7  | 37/50 | **ALTO** |
| TH01 | Suplantación credenciales VPN proveedor (sin MFA)    | 8  | 8  | 8  | 8  | 7  | 39/50 | **ALTO** |
| TH11 | DoS sobre servidor SCADA principal                   | 9  | 7  | 6  | 9  | 6  | 37/50 | **ALTO** |
| TH08 | Sniffing de tráfico PROFINET sin cifrado             | 7  | 8  | 7  | 7  | 8  | 37/50 | **ALTO** |
| TH15 | Escalada de privilegios desde cuenta mantenimiento   | 8  | 5  | 6  | 8  | 5  | 32/50 | **ALTO** |
| TH16 | Explotación SO desactualizado en HMI (Windows 10)    | 8  | 7  | 7  | 7  | 7  | 36/50 | **ALTO** |
| TH02 | Sesión HMI no supervisada (operador ausente)         | 8  | 7  | 9  | 7  | 5  | 36/50 | **ALTO** |
| TH12 | Flood de paquetes en red industrial (DoS PLC-SCADA)  | 8  | 6  | 6  | 8  | 5  | 33/50 | **ALTO** |
| TH09 | Exposición datos producción vía API mal configurada  | 6  | 6  | 6  | 6  | 7  | 31/50 | **ALTO** |
| TH10 | Reconocimiento topología OT desde red corporativa    | 5  | 7  | 7  | 6  | 8  | 33/50 | **ALTO** |
| TH04 | Alteración de datos históricos en Historian          | 7  | 4  | 5  | 6  | 4  | 26/50 | **MEDIO** |
| TH06 | Ausencia de logs de auditoría en SCADA/HMI           | 5  | 9  | 9  | 7  | 7  | 37/50 | **ALTO** |
| TH07 | Sesiones VPN proveedor sin trazabilidad              | 5  | 8  | 8  | 6  | 6  | 33/50 | **ALTO** |

---

### 5.4 Ranking de Amenazas por Prioridad

| Prioridad | ID   | Amenaza                                              | Total | Nivel    |
|-----------|------|------------------------------------------------------|-------|----------|
| 1°        | TH14 | Movimiento lateral IT→OT por misconfiguration firewall | 40  | CRÍTICO  |
| 2°        | TH01 | Suplantación credenciales VPN proveedor (sin MFA)    | 39   | ALTO     |
| 3°        | TH03 | Modificación de lógica de control en PLC Siemens     | 38   | ALTO     |
| 4°        | TH13 | Explotación CVE en firmware PLC (parada de línea)    | 37   | ALTO     |
| 5°        | TH11 | DoS sobre servidor SCADA principal                   | 37   | ALTO     |
| 6°        | TH08 | Sniffing de tráfico PROFINET sin cifrado             | 37   | ALTO     |
| 7°        | TH06 | Ausencia de logs de auditoría en SCADA/HMI           | 37   | ALTO     |
| 8°        | TH16 | Explotación SO desactualizado en HMI (Windows 10)    | 36   | ALTO     |
| 9°        | TH02 | Sesión HMI no supervisada (operador ausente)         | 36   | ALTO     |
| 10°       | TH05 | Inserción de lógica maliciosa en proyecto ingeniería | 35   | ALTO     |
| 11°       | TH15 | Escalada de privilegios desde cuenta mantenimiento   | 32   | ALTO     |
| 12°       | TH12 | Flood de paquetes en red industrial                  | 33   | ALTO     |
| 13°       | TH10 | Reconocimiento topología OT desde red corporativa    | 33   | ALTO     |
| 14°       | TH07 | Sesiones VPN proveedor sin trazabilidad              | 33   | ALTO     |
| 15°       | TH09 | Exposición datos producción vía API mal configurada  | 31   | ALTO     |
| 16°       | TH04 | Alteración de datos históricos en Historian          | 26   | MEDIO    |

---

### 5.5 Análisis de Resultados

**Amenaza crítica (TH14):**  
El movimiento lateral desde IT hacia OT por mala configuración del firewall es la amenaza de mayor riesgo total. Combina daño máximo (10) con alto alcance (10) porque un atacante que cruza ese límite tiene acceso potencial a todos los sistemas de control de la planta simultáneamente.

**Cluster de amenazas ALTO (TH01, TH03, TH13, TH11, TH08, TH06):**  
Seis amenazas comparten puntaje 37. Son heterogéneas en tipo pero todas representan vectores reales y frecuentemente explotados en entornos ICS:
- TH01 y TH08 son amenazas de **acceso inicial** (entrada al sistema)
- TH03 y TH13 son amenazas de **impacto físico** (afectan la producción directamente)
- TH11 es amenaza de **pérdida de visibilidad** operacional
- TH06 es amenaza de **cobertura del atacante** (sin logs, no hay detección ni forense)

**Única amenaza MEDIO (TH04):**  
La alteración del Historian tiene menor puntaje porque requiere acceso previo al entorno OT y su reproducibilidad y descubribilidad son bajas. Sin embargo, su impacto en integridad de datos es significativo a largo plazo.

**Observación OT vs IT:**  
En un análisis IT clásico, las amenazas de Information Disclosure suelen puntuar más alto. En OT, las amenazas de Denial of Service y Tampering dominan el ranking porque la **disponibilidad y la integridad del proceso físico** son prioritarias sobre la confidencialidad.
