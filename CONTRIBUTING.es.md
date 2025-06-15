# Guía de contribución a rotatrix

Toda contribución es bienvenida para mejorar la calidad, funcionalidad y alcance de este rol de Ansible.

Por favor, se solicita seguir las siguientes directrices para facilitar la revisión y la integración de las propuestas.

---

## ¿Cómo contribuir?

1. Hacer un fork del repositorio y crear una rama basada en `main`.

2. Realizar los cambios necesarios y describir claramente las modificaciones tanto en el mensaje del commit como en la Pull Request.

3. Comprobar que el código funciona correctamente:

   - Ejecutar las pruebas incluidas (`ansible-playbook -i tests/inventory tests/main.yml`).

   - En caso de añadir o modificar funcionalidad, actualizar la documentación correspondiente (`README.md` y `README.es.md`).

4. Abrir una Pull Request (PR) desde la rama y completar la plantilla de PR, indicando el motivo y el alcance de los cambios.

5. Participar en la revisión respondiendo a los comentarios y sugerencias que puedan surgir durante el proceso.

---

## Reporte de errores y sugerencias

Si se detecta un bug, problema o se desea sugerir una mejora:

- Abrir un **Issue** en GitHub, proporcionando la mayor cantidad de información posible:

  - Versión del sistema operativo y de Ansible.

  - Versión del rol utilizada.

  - Pasos para reproducir el problema.

  - Logs, mensajes de error o fragmentos relevantes de configuración.

---

## Comunicación

Toda la interacción y el soporte se gestionan a través de Issues y Pull Requests en GitHub.

No se responderán consultas por correo electrónico.

---

## Código de conducta

Se solicita mantener siempre un tono respetuoso, constructivo y colaborativo.

No se tolerarán ataques personales ni comportamientos ofensivos.

---

## Licencia

Al contribuir, se acepta que el código aportado será distribuido bajo la licencia MIT.

---

¡Gracias por contribuir a rotatrix!

