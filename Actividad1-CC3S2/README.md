# Actividad 1 — Introducción DevOps / DevSecOps
Alumno: Miguel Angel Méndez Gonzalo  
Fecha: 2025-08-31  
Tiempo acumulado invertido hasta ahora: 01:51
Contexto del entorno: Trabajo local en Windows 10 con WSL2 y navegador Chrome (DevTools).

## 4.1 DevOps vs Cascada tradicional
En la siguiente imagen se muestra la diferencia visual entre el modelo de cascada y el enfoque DevOps.

**Imagen:** ![DevOps vs Cascada](imagenes/devops-vs-cascada.jpg)

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

**Imagen:** ![Silos y handoffs](imagenes/silos-equipos.jpg)

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
