# Cambios

Aquí se documentan todos los cambios relevantes de este proyecto.

El formato sigue la guía de [Keep a Changelog](https://keepachangelog.com/es-ES/1.0.0/).

## [No publicado]

- Trabajo en curso para futuras versiones.

### Mejoras planificadas / Próximamente

- **Parametrización avanzada**
  Permitir definir los valores de rotación (frecuencia, rotaciones, maxage, minsize, etc.) mediante variables y plantillas Jinja2 (no más archivos fijos)

- **CI/CD & Molecule**
  Integrar Molecule y GitHub Actions para pruebas automáticas en múltiples sistemas

- **Soporte multi-distro**
  Probar y extender soporte a RHEL 8, Rocky/AlmaLinux, CentOS Stream y, opcionalmente, Debian/Ubuntu

- **Políticas de logs personalizadas**
  Facilitar que los usuarios puedan añadir sus propios archivos y políticas de logs sin modificar el código

- **Handlers opcionales**
  Añadir handlers para reiniciar/recargar servicios (como rsyslog) tras cambios de configuración, controlado por variable

- **Tareas de verificación**
  Añadir comprobaciones post-despliegue para asegurar que todos los archivos y permisos se han aplicado correctamente

- **Documentación y ejemplos**
  Ampliar la documentación con más ejemplos, casos de troubleshooting e integración con herramientas de monitorización y alertas

## [0.1.1] - 2025-07-15

### Añadido

- Traducciones al inglés del README y descripción en meta/main.yml para soporte de internacionalización y publicación en Galaxy.

- Documentación actualizada con comentarios bilingües (inglés y español) para mejor accesibilidad.

## [0.1.0] - 2025-06-14

### Añadido

- Primera publicación del rol `rotatrix`.

- Políticas para rotación segura y automatizada de logs en sistemas RHEL 9.

- Archivo logrotate.conf y políticas por defecto para btmp, wtmp, rsyslog y dnf.

- Documentación inicial (README) en español.

- Pruebas básicas (smoke tests) e inventario para localhost.
