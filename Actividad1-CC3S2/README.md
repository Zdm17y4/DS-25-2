# Actividad 1 — Introducción DevOps / DevSecOps
Alumno: Miguel Angel Méndez Gonzalo  
Fecha: 2025-08-31  
Tiempo acumulado invertido hasta ahora: 04:32
Contexto del entorno: Trabajo local en Windows 11 con Powershell y navegador Chrome (DevTools).

## 4.1 DevOps vs Cascada tradicional
En la siguiente imagen se muestra la diferencia visual entre el modelo de cascada y el enfoque DevOps.

**Imagen ubicada en:** `imagenes/devops-vs-cascada.jpg`

DevOps acelera y reduce el riesgo del software en la nube al adoptar el feedback continuo, lo que permite detectar problemas tempranamente. Utiliza pequeños lotes de cambio para limitar el impacto de los fallos y facilitar los rollbacks. Además, la automatización de tareas repetitivas elimina errores humanos y acelera la entrega de valor.

**Contexto donde cascada aún es razonable:** sistemas con certificaciones regulatorias muy estrictas (p. ej., aviación o dispositivos médicos).  
- **Criterio verificable 1:** requerimiento de auditoría oficial y firma de pruebas antes de despliegue.  
- **Criterio verificable 2:** existencia de procesos de certificación que impiden releases iterativos.  
**Trade-offs:** cumplimiento y trazabilidad vs velocidad e iteración.
---

## 4.2 Dos pasos y silos — limitaciones
Aquí se muestra la separación clásica entre construcción y operación. Esta práctica genera limitaciones:

1. Se acumulan grandes lotes antes de probar en producción.  
2. Los defectos se agrupan en colas largas, lo que aumenta tiempos de recuperación.

**Imagen ubicada en:** `imagenes/silos-equipos.jpg`

**Anti-patrones identificados:**  
- "Throw over the wall": desarrollo entrega sin contexto → retrabajo.  
- Seguridad como auditoría final: vulnerabilidades se descubren demasiado tarde.  

Ambos elevan MTTR y costo de integración tardía, por handoffs y asimetrías de información.

---

## 4.3 Principios y beneficios DevOps
- **CI:** integrar cambios pequeños con pruebas automáticas inmediatas.  
- **CD:** generar artefactos listos para despliegue en cualquier momento.  
- **Práctica Agile que aporta:** las dailys y retrospectivas ayudan a decidir qué se promueve o bloquea en el pipeline.  

**Indicador observable definido:** tiempo desde que un PR queda listo hasta que se despliega en entorno de pruebas.  
Método de recolección: usar metadatos de PR (hora de merge) y registros de despliegue para calcular la mediana.

## 4.4 DevSecOps (SAST/DAST y gates)
**Ubicación en el pipeline (según BlackDuck):**  
- **SAST (Static Application Security Testing):** analiza código fuente/binaries en reposo. Se ejecuta tras el **Build**, antes de notificar el resultado, para encontrar vulnerabilidades temprano.  
- **DAST (Dynamic Application Security Testing):** analiza la aplicación en ejecución desde el exterior. Se ejecuta en el entorno de **Staging**, después de desplegar el build.  

**Gate mínimo:**  
- **SAST:** 0 vulnerabilidades críticas/altas (CVSS ≥ 7).  
- **DAST:** Cobertura ≥ 80% en rutas críticas (ejemplo: login, pagos).  

**Política de excepción:**  
- Duración máxima: 7 días.  
- Dueño asignado: líder técnico responsable del servicio.  
- Mitigación temporal documentada (ejemplo: WAF, reglas de firewall).  
- Excepción caduca automáticamente al 7.º día.  

**Señales para evitar “teatro de seguridad”:**  
1. Vulnerabilidades repetidas ≤ 5% entre despliegues consecutivos.  
2. Tiempo de remediación (TTR) de vulnerabilidades críticas ≤ 48 horas.  

## 4.5 Estrategia de despliegue: Pipeline CI/CD
**Estrategia elegida:** Sandbox → Pipeline CI/CD.  
Aplicada al microservicio de **autenticación**, esta estrategia permite validar cambios en un entorno seguro (*sandbox*) antes de pasar a producción. La ejecución controlada reduce el riesgo de introducir errores en un servicio crítico.  

**Imagen ubicada en:** `imagenes/pipeline_canary.png`

**Riesgos y mitigaciones:**
| Riesgo | Mitigación |
|---|---|
| Regresión funcional | Validación de contrato y pruebas automáticas antes de promover |
| Costo operativo del doble despliegue | Limitar la convivencia de entornos a menos de 1 hora |
| Manejo de sesiones | Draining de conexiones activas y compatibilidad de esquemas de base de datos |

**KPI primario técnico:**  
- Métrica: tasa de errores HTTP 5xx.  
- Umbral: ≤ 0.1% en una ventana de observación de 1 hora tras el despliegue.  
- Regla: si se supera, se aborta/promueve según resultado.  

**Pregunta retadora:**  
Aunque el KPI técnico (errores 5xx) se mantenga dentro del umbral, puede caer una métrica de producto como la conversión. Esto ocurre porque el sistema puede estar respondiendo “correctamente” desde un punto de vista técnico, pero generando una mala experiencia de usuario (por ejemplo, cambios en flujo de login). Por eso, los **KPIs técnicos y métricas de producto deben coexistir en el gate**: uno garantiza confiabilidad operativa y el otro asegura que la experiencia y valor de negocio no se degraden.

## 4.6 Fundamentos prácticos sin comandos
### 1. **HTTP – contrato observable**  
Evidencia en `imagenes/http-evidencia.png`.

**Cabeceras observadas:**
- `Cache-Control: max-age=3600` (impacto: mejora rendimiento vía caché).  
- `X-Request-ID: abc123` (impacto: facilita trazabilidad en logs).

### 2. **DNS – nombres y TTL**  
Evidencia en `imagenes/dns-ttl.png`.
- **Registros observados:**  
  - example.com → A → 23.215.0.138 (TTL=284s)  
  - example.com → A → 23.220.75.232 (TTL=284s)  
  - example.com → A → 23.192.228.80 (TTL=284s)  
  - example.com → A → 23.192.228.84 (TTL=284s)  
  - example.com → A → 23.220.75.245 (TTL=284s)  
  - example.com → A → 23.215.0.136 (TTL=284s)  

**Interpretación:**  
El dominio *example.com* resuelve a múltiples direcciones IP (balanceo de carga vía DNS) con un **TTL=284 segundos (~5 minutos)**.  
- Un **TTL bajo** facilita rollbacks y cambios de IP rápidos, porque los resolvers de clientes revalidan con mayor frecuencia.  
- Un **TTL alto** puede retrasar propagación de cambios y generar ventanas de inconsistencia (usuarios en distintas regiones resolviendo a IPs distintas durante más tiempo).  

**Impacto:** este TTL medio (≈5 min) permite cierto equilibrio: no satura los resolvers, pero aún da margen para que un cambio se propague sin esperar horas.

### 3. **TLS – seguridad en tránsito**  
Evidencia en `imagenes/tls-cert.png`.  
- **CN (Nombre común):** *.example.com  
- **Organización (O):** Internet Corporation for Assigned Names and Numbers  
- **Emisor:** DigiCert Global G3 TLS ECC SHA384 2020 CA1 (DigiCert Inc)  
- **Vigencia:** del 14 de enero de 2025 al 15 de enero de 2026  

**Interpretación:**  
El certificado cubre todos los subdominios de *example.com* mediante un comodín (*.example.com).  
Está emitido por DigiCert, una CA de confianza, y tiene una vigencia de un año.  
Si la **cadena de confianza no valida** (ej. certificado caducado o emisor no confiable), los navegadores mostrarán advertencias de seguridad, afectando la experiencia de usuario y exponiendo a ataques de tipo MITM.  

### 4. **Puertos – estado de runtime**  
Evidencia en `imagenes/puertos.png`.  

**Puertos observados en PowerShell (estado LISTENING):**  
- 135 → Servicio de **RPC (Remote Procedure Call)** usado por Windows para comunicación entre procesos distribuidos.  
- 445 → Servicio de **SMB (Server Message Block)**, protocolo para compartir archivos e impresoras en red.  

**Interpretación:**  
Estos puertos en escucha indican que el sistema tiene servicios de red expuestos:  
- El puerto 135 (RPC) es crítico para comunicación interna, pero si queda expuesto a Internet puede representar un riesgo de seguridad.  
- El puerto 445 (SMB) es sensible, pues está asociado a ataques conocidos (ej. WannaCry).

### 5. 12-Factor — port binding, configuración, logs
- **Port binding:** el servicio no debe tener el puerto hardcodeado en el código. En su lugar, debe leerlo de una **variable de entorno** (`PORT`) o un archivo de configuración externo. Esto permite desplegar la misma build en distintos entornos sin cambios de código.  
- **Logs:** deben enviarse al **flujo estándar (stdout/stderr)**, de modo que el sistema operativo o la plataforma de orquestación los capture y los rote automáticamente. Es un error escribir logs en archivos locales, porque requieren rotación manual y dificultan la centralización.  
- **Anti-patrón:** almacenar **credenciales en el código fuente**. Esto rompe la reproducibilidad (builds no se pueden portar entre entornos) y aumenta riesgo de fuga de secretos. La configuración sensible debe inyectarse como variables de entorno seguras.  

---

### 6. Checklist de diagnóstico (incidente simulado)
**Escenario:** usuarios reportan intermitencia en el servicio.  

1. **Contrato HTTP roto**  
   - Objetivo: validar respuestas y cabeceras.  
   - Evidencia esperada: status 200 y cabeceras de contrato (`Cache-Control`, `X-Request-ID`).  
   - Interpretación: si hay 5xx o faltan cabeceras → contrato roto.  
   - Acción: detener promoción y revisar logs de aplicación.  

2. **Resolución DNS inconsistente**  
   - Objetivo: confirmar registros y TTL.  
   - Evidencia esperada: registros A/CNAME correctos con TTL esperado.  
   - Interpretación: si el registro apunta a IP incorrecta o TTL alto retrasa cambios → inconsistencia.  
   - Acción: revertir cambios DNS o esperar propagación según criticidad.  

3. **Certificado TLS inválido**  
   - Objetivo: verificar vigencia y cadena de confianza.  
   - Evidencia esperada: CN/SAN correctos, certificado dentro de fechas, emisor confiable.  
   - Interpretación: si caducado o no confiable → riesgo de MITM.  
   - Acción: renovar certificado y bloquear temporalmente acceso externo.  

4. **Puerto no expuesto o mal configurado**  
   - Objetivo: comprobar servicios en escucha.  
   - Evidencia esperada: puertos esperados (ej. 80/443) en LISTENING.  
   - Interpretación: si falta puerto esperado → despliegue incompleto; si ocupado por otro proceso → conflicto.  
   - Acción: redeploy del servicio o rollback.  

5. **KPI técnico degradado**  
   - Objetivo: revisar métricas post-deploy.  
   - Evidencia esperada: errores 5xx ≤ 0.1% en ventana de 1 hora.  
   - Interpretación: si se supera → degradación técnica.  
   - Acción: rollback inmediato del despliegue.  

6. **Métrica de producto caída**  
   - Objetivo: validar experiencia de usuario.  
   - Evidencia esperada: conversión ≥ 95% del valor previo al despliegue.  
   - Interpretación: si cae aunque KPIs técnicos se mantengan → problema funcional/UX.  
   - Acción: revertir despliegue y abrir análisis con equipo de producto.  

## 4.7 Desafíos de DevOps y mitigaciones

**Diagrama de apoyo:** `imagenes/desafios_devops.png` (tres desafíos: cultural, técnico, gobernanza).

**Tres riesgos y mitigaciones concretas:**
| Riesgo | Mitigación concreta |
|---|---|
| Regresión masiva por despliegue | Rollback automático y deployment por etapas (gradual) para limitar blast radius |
| Deuda técnica y tests frágiles | Forzar PRs pequeños + coverage mínimo (≥80%) y test contract antes de promover |
| Falta de controles / permisos débiles | Implementar roles y gates; excepciones con caducidad y responsable asignado |

**Experimento controlado: validar que despliegue gradual reduce riesgo vs big-bang**  
- **Métrica primaria:** tasa de incidentes críticos (p.ej., solicitudes bloqueadas / errores 5xx) medida en la ventana de 24 horas post-despliegue.  
- **Grupo control:** despliegue big-bang (100% tráfico al nuevo release).  
- **Grupo experimental:** despliegue gradual (10% → 50% → 100% en ventanas de 30 min si KPIs OK).  
- **Tamaño / duración:** ejecutar 20 despliegues por grupo en condiciones equivalentes o durante 4 semanas de actividad típica.  
- **Criterio de éxito:** reducción ≥ 30% en la métrica primaria en el grupo experimental respecto al control, con significancia práctica (menor MTTR y menor número de rollbacks).  
- **Plan de reversión:** si durante la ventana de observación la tasa de errores 5xx supera 0.1% o la métrica de producto cae >5% respecto al baseline, detener promoción y ejecutar rollback automático al 0% (grupo experimental) o restaurar snapshot (control). Registrar RCA y bloquear la misma técnica hasta mitigación.

---

## 4.8 Arquitectura mínima para DevSecOps (HTTP / DNS / TLS + 12-Factor)

**Diagrama de apoyo:** `imagenes/arquitectura-minima.png` (Cliente → DNS → LB/Proxy → Servicio HTTP (+ TLS)).

**Dónde aplicar controles y por qué:**
- **DNS:** aplicar políticas de TTL y records verificados. TTL bajo (p.ej., 300s) acorta ventanas de inconsistencia y facilita rollbacks; TTL alto puede retrasar correcciones.  
- **LB / Proxy (L7):** aplicar WAF, cache-control, rate-limiting y terminación TLS (si aplica). Aquí se filtra tráfico malicioso y se reduce carga al backend.  
- **Servicio (HTTP):** validar contratos API (schema validation), usar port binding via variable de entorno `PORT`, y emitir logs a stdout para recolección centralizada.  
- **TLS:** validar cadena de certificados, automatizar renovación (ACME/Let’s Encrypt o CA corporativa) y comprobar CN/SAN y vigencia en pipeline de despliegue.

**Cómo cada capa contribuye a despliegues seguros y reproducibles:**
- **DNS:** control de resolución y TTL permite gestionar cambios de IP y rollbacks sin alterar el código.  
- **LB/Proxy:** centraliza políticas de seguridad y permite degradación controlada (circuit breakers, rate-limit).  
- **Servicio:** 12-Factor (config por entorno y port binding) garantiza la misma build entre entornos; contratos API y tests aseguran compatibilidad.  
- **TLS:** asegura confidencialidad e integridad en tránsito; la automatización de certificados evita interrupciones humanas.

**12-Factor — evidencias operativas que el docente puede revisar:**
- **Config por entorno (ENV):** evidencia: archivo de despliegue env.example + variables en CI/CD; diffs mínimos entre entornos en control de versiones (solo valores/secretos en vault).  
- **Logs a stdout:** evidencia: logs centralizados (p. ej., visualización en Kibana/LogCLI) y correlación por `X-Request-ID` que demuestra trazabilidad entre frontend y backend.  

