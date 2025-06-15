# rotatrix

**Rotación segura y automatizada de logs en sistemas Linux**

Rol de Ansible para la gestión avanzada y segura de la rotación de logs en servidores Linux empresariales.

---

## Introducción

Este rol implementa políticas de rotación y retención de logs recomendadas para entornos Linux empresariales. Permite adaptar la configuración según requerimientos regulatorios, capacidad de almacenamiento o criticidad del servicio. Se recomienda revisar las políticas anualmente o ante cambios significativos en la infraestructura.

---

## Requisitos

* Ansible >= 2.9

* Sistema operativo:
  - Red Hat Enterprise Linux 9 (RHEL 9) o compatibles

* Privilegios de root en los nodos destino

---

## Variables configurables

Las siguientes variables permiten adaptar el despliegue de las políticas de rotación de logs según las necesidades de cada entorno.

Se pueden sobrescribir fácilmente desde un playbook o mediante `group_vars` o `host_vars`.

| Variable | Valor por defecto | Descripción |
| :---: | :---: | :---: |
| `logrotate_conf_src` | logrotate.conf | Archivo fuente con la configuración principal de logrotate |
| `logrotate_conf_dest` | /etc/logrotate.conf | Ruta de destino donde se instalará la configuración principal de logrotate |
| `logrotate_d_dir_src` | logrotate.d | Carpeta que contiene los archivos de políticas individuales a desplegar en `/etc/logrotate.d/` |
| `logrotate_d_dir_dest` | /etc/logrotate.d | Carpeta destino donde se copiarán las políticas individuales |
| `logrotate_d_files` | [btmp, dnf, rsyslog, wtmp] | Lista de archivos de políticas específicas que se copiarán a la carpeta de destino |

### Ejemplo de personalización en un playbook

Si se necesita desplegar solo algunas políticas o cambiar las rutas por defecto, se puede redefinir las variables en el playbook así:

```yaml
- hosts: servidores
  become: true
  roles:
    - role: rotatrix
      vars:
        logrotate_d_files:
          - rsyslog
          - dnf
```

O para cambiar la ubicación de los archivos:

```yaml
- hosts: servidores
  become: true
  roles:
    - role: rotatrix
      vars:
        logrotate_conf_dest: /etc/custom-logrotate.conf
        logrotate_d_dir_dest: /etc/custom-logrotate.d
```

---

## Estructura de los archivos adjuntos

Todos los archivos de configuración listados en esta documentación se encuentran en la carpeta `files/etc`, listos para ser desplegados en el sistema correspondiente.

La estructura es la siguiente:

```plaintext
files/
└── etc/
    ├── logrotate.conf
    └── logrotate.d/
        ├── btmp
        ├── dnf
        ├── rsyslog
        └── wtmp
```

### Descripción de los archivos:

* `logrotate.conf`
  Archivo de configuración principal de logrotate. Define las políticas globales de rotación y carga las políticas específicas de cada servicio

* `logrotate.d/btmp`
  Política específica de rotación para el log de autenticación fallida (/var/log/btmp)

* `logrotate.d/dnf`
  Política de rotación para los logs de gestión de paquetes generados por DNF

* `logrotate.d/rsyslog`
  Políticas de rotación para los logs gestionados por rsyslog, incluyendo logs del sistema y de servicios clave

**Nota**: Antes de aplicar estos archivos en un entorno de producción, se debe revisar y ajustar las rutas, permisos y parámetros según las particularidades y necesidades del sistema.

---

## Ejemplo de uso

Se debe incluir este rol en un playbook de Ansible:

```yaml
- hosts: all
  become: true
  roles:
    - role: rotatrix
```

---

## Pruebas

Para ejecutar los tests básicos incluidos:

```sh
ansible-playbook -i tests/inventory tests/test.yml
```

---

## Implementación de la configuración

### Configuración general (`/etc/logrotate.conf`)

La configuración general de `logrotate` define los valores por defecto que se aplican a todos los archivos de log, salvo que sean sobrescritos por archivos específicos en `/etc/logrotate.d`.

Esta política global aplica criterios seguros y racionales para entornos de servidor:

- **Frecuencia**: rotación semanal
- **Retención**: 5 archivos rotados
- **Límite de antigüedad**: 60 días
- **Tamaño mínimo para rotar**: 1 MB
- **Compresión**: habilitada, con retraso en el último log
- **Nombres de archivo con fecha**: para facilitar trazabilidad
- **Evita errores por archivos inexistentes o vacíos**
- **Carga las políticas específicas desde `/etc/logrotate.d`**

Ubicación del archivo: `/etc/logrotate.conf`

#### Parámetros aplicados por defecto

| Directiva | Valor | Descripción |
| :---: | :---: | :---: |
| `missingok` | — | No falla si el log no existe |
| `notifempty` | — | No rota archivos vacíos |
| `weekly` | — | Frecuencia de rotación por defecto |
| `rotate` | `5` | Archivos históricos conservados |
| `maxage` | `60` días | Antigüedad máxima de los logs |
| `minsize` | `1M` | Solo rota si el archivo supera 1 MB |
| `dateext` | — | Añade la fecha al nombre del archivo rotado |
| `compress` | — | Comprime los archivos rotados |
| `delaycompress` | — | Retrasa la compresión del archivo más reciente |
| `include` | `/etc/logrotate.d` | Carga políticas por servicio o aplicación |

Estas opciones aseguran que la rotación de logs sea eficiente, evite pérdida de datos, y mantenga una base histórica razonable sin sobrecargar el sistema.

### Rotación del log de autenticación fallida

Este log binario registra todos los intentos de autenticación fallidos en el sistema. Es especialmente útil en tareas de auditoría, análisis forense o revisión de accesos sospechosos. Dado su carácter sensible y poco frecuente, se rota de forma más conservadora que otros logs.

- Formato binario: debe consultarse con el comando lastb
- No se comprime: para conservar su legibilidad con herramientas estándar
- Retención larga: permite trazar accesos indebidos durante un año completo
- Permisos estrictos: accesible únicamente para procesos autorizados del sistema

Ubicación del archivo de configuración: `/etc/logrotate.d/btmp`

#### Política de rotación de log (`/var/log/btmp`)

| Fichero | Frecuencia de rotación | Nº de copias | Retención máxima | Tamaño mínimo | Permisos | Comentario |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `/var/log/btmp` | Mensual | 12 | 366 días | 1 MB | `0660` | Intentos fallidos de login (consultable con lastb) |

### Rotación del log de sesiones

Esta configuración de logrotate gestiona la rotación de `/var/log/wtmp`, un log binario que almacena inicios y cierres de sesión, así como reinicios del sistema. Es consultado por herramientas como `last` o `who` y requiere una política conservadora de rotación para mantener trazabilidad histórica sin comprometer su integridad.

- Archivo binario, no debe comprimirse
- Conserva 12 meses de histórico (1 año)
- Permisos adecuados para su lectura por herramientas del sistema
- Se rota mensualmente si supera 1 MB

Ubicación del archivo: `/etc/logrotate.d/wtmp`

#### Política de rotación de log de sesiones (`/etc/logrotate.d/wtmp`)

| Fichero | Frecuencia de rotación | Nº de copias | Retención máxima | Tamaño mínimo | Permisos | Comentario |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `/var/log/wtmp` | Mensual | 12 | 366 días | 1 MB | `0664` | Inicios/cierres de sesión y reinicios; consultado por last, who. No se comprime al ser binario |

### Rotación de logs del sistema

Esta configuración de `logrotate` para `rsyslog` implementa una política de rotación segura y eficiente:

- Retención adaptada a la criticidad de cada log
- Compresión automática de logs antiguos
- Permisos restrictivos en archivos sensibles (`secure`, `maillog`, etcétera)
- Evita rotaciones innecesarias usando `minsize` para logs de bajo volumen
- Recarga automática de `rsyslog` tras cada rotación para evitar pérdida de eventos

Ubicación del archivo: `/etc/logrotate.d/rsyslog`

#### Política de rotación de logs (`/etc/logrotate.d/rsyslog`)

A continuación se detallan los parámetros aplicados a cada uno de los logs gestionados por `rsyslog`:

| Fichero | Frecuencia de rotación | Nº de copias | Retención máxima | Tamaño mínimo | Permisos | Comentario |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `/var/log/boot.log` | Semanal | 5 | 60 días | 1 MB | `0664` | Mensajes del arranque |
| `/var/log/cron` | Semanal | 14 | 180 días | 1 MB | `0664` | Actividad de tareas programadas |
| `/var/log/maillog` | Semanal | 14 | 180 días | 1 MB | `0600` | Servicios de correo (Postfix, etcétera) |
| `/var/log/messages` | Semanal | 14 | 180 días | 1 MB | `0600` | Mensajes generales del sistema |
| `/var/log/secure` | Mensual | 12 | 366 días | 1 MB | `0600` | Accesos, autenticación, sudo, SSH |
| `/var/log/spooler` | Semanal | 5 | 60 días | 1 MB | `0600` | Servicios de impresión (CUPS, etcétera) |

### Rotación de logs de gestión de paquetes (DNF)

Esta configuración de `logrotate` gestiona la rotación de los logs generados por DNF (`dnf`, `hawkey`, `librepo`, `rpm`) para asegurar una retención razonable, eficiencia de espacio y trazabilidad de operaciones relacionadas con la gestión de paquetes del sistema.

- Retención ajustada al uso real del sistema
- Compresión automática de logs antiguos
- Evita rotaciones inútiles usando `minsize` para logs pequeños
- No requiere recarga de servicios tras la rotación

Ubicación del archivo: `/etc/logrotate.d/dnf`

#### Política de rotación de logs de DNF (`/etc/logrotate.d/dnf`)

A continuación se detallan los parámetros aplicados a cada uno de los logs relacionados con la gestión de paquetes:

| Fichero | Frecuencia de rotación | Nº de copias | Retención máxima | Tamaño mínimo | Permisos | Comentario |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `/var/log/dnf.log` | Semanal | 14 | 180 días | 1 MB | `0644` | Operaciones de alto nivel (`dnf install`, etcétera)  |
| `/var/log/hawkey.log` | Semanal | 14 | 180 días | 1 MB | `0644` | Resolución de dependencias (motor de `libsolv`) |
| `/var/log/dnf.rpm.log` | Semanal | 14 | 180 días | 1 MB | `0644` | Información de instalación/actualización de paquetes RPM |
| `/var/log/dnf.librepo.log` | Semanal | 14 | 180 días | 1 MB | `0644` | Descargas y acceso a repositorios remotos |

---

## Notas y advertencias importantes

### Nota importante sobre logs binarios (wtmp, btmp)

Los archivos de logs binarios, como `/var/log/btmp` y `/var/log/wtmp`, no deben comprimirse ni editarse manualmente. Alterarlos puede corromper la información y volverlos inutilizables para herramientas estándar del sistema como `lastb`, `last`o `who`. Para su consulta, se deben utilizar exclusivamente las utilidades específicas del sistema, y nunca se debe editar, borrar ni modificar su contenido fuera del proceso de rotación automatizada.

---

## Parámetros avanzados y recomendaciones

### Control de logs de tamaño excesivo

En casos donde los logs puedan crecer de forma inusual o imprevisible (por ejemplo, tras un incidente o fallo de servicio), es posible utilizar la directiva `maxsize` en la configuración de `logrotate`. Esta directiva permite forzar la rotación de logs que alcancen un tamaño máximo especificado, independientemente de la frecuencia de rotación definida.

Ejemplo de uso:

```shell
maxsize 100M
```

Esto asegurará que el log se rote automáticamente al superar los 100 MB, ayudando a evitar problemas de espacio en disco o pérdida de información por archivos demasiado grandes.

**Nota**: En los archivos de configuración adjuntos no se incluye esta directiva por defecto, pero puede añadirse fácilmente en entornos críticos o bajo requerimientos específicos.

---

## Referencias adicionales

* [Página de manual de logrotate](https://linux.die.net/man/8/logrotate)

* [Documentación oficial de logrotate](https://man7.org/linux/man-pages/man8/logrotate.8.html)

Se recomienda consultar estos recursos para detalles avanzados de configuración y resolución de dudas específicas.

---

## Contribuir

¿Sugerencias, mejoras o errores? ¡No hay nada como un Merge Request (MR) en este repositorio y se revisa!

---

## Licencia

Este repositorio se distribuye bajo la licencia MIT. Se puede consultar el archivo [LICENSE](LICENSE) para más detalles.

