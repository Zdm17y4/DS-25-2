# 1. Introducción a DevOps: Qué es y qué no es
DevOps es una **cultura y conjunto de prácticas** para entregar software más rápido y confiable: del **código en el repo** hasta **producción**.  
No es solo automatización ni solo “usar Docker/Jenkins”.  
A diferencia de *Waterfall*, donde desarrollo y operaciones están separados y las entregas son raras y secuenciales, DevOps promueve iteraciones pequeñas, despliegues frecuentes y responsabilidad compartida.  
## "You build it, you run it" en el laboratorio:
  Si creas un servicio en tu Makefile, tú también defines los checks de salud (`curl /health`) y monitorizas en producción.  
  No esperas a “otro equipo de operaciones” para reaccionar.

## Mitos vs realidades

| Mito                         | Realidad                                                                      |
|------------------------------|----------------------------------------------------------------------------|
| DevOps = herramientas CI/CD  | DevOps = **CALMS**: Cultura, Automatización, Lean, Medición, Sharing.    |
| Solo importa la velocidad    | Calidad y feedback rápido (gates automáticos, métricas).                  |
| Ops ya no existen            | Roles cambian, pero la experiencia operativa sigue siendo esencial.         |


# 2. Marco CALMS en acción


# 3. Visión cultural de DevOps y paso a DevSecOps
- **Colaboración para evitar silos**: Dev y Ops revisan juntos el archivo nginx.conf y las unidades systemd. Si falla un certificado TLS en producción, el equipo no culpa: revisan logs, actualizan runbooks, y hacen una retrospectiva para fortalecer procesos.

- **Evolución a DevSecOps:** Seguridad integrada desde el principio:
    1. **Cabeceras TLS/HSTS** configuradas en `nginx.conf`.
    2. **Escaneo de dependencias** en el pipeline (`make quality-gate`).
    3. **Secret scanning o validación de permisos** antes de `deploy`.

| Control                       | Ubicación en CI/CD                              |
| ----------------------------- | ----------------------------------------------- |
| Escaneo de dependencias (SCA) | Etapa `lint/test` del Makefile                  |
| Validación TLS/HSTS           | Configuración de `nginx.conf` antes de `deploy` |
| Revisión de permisos systemd  | Antes de generar unidades `*.service`           |


# 4. Metodología 12-Factor App
