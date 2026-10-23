---
title: "Ejercicio 2: Agente de GitHub Copilot en la nube"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Instrucciones personalizadas][previous-lesson] |
|:--|

Probablemente casi todas las organizaciones tienen problemas con la deuda técnica: incidencias de seguridad sin resolver, código heredado que necesita actualizarse o solicitudes de funcionalidad que han quedado olvidadas en el backlog por falta de tiempo. El agente de GitHub Copilot en la nube está diseñado para realizar tareas como actualizar código y añadir funcionalidades de forma autónoma. Cuando termina, genera una PR en borrador para que la revise un desarrollador. Así puedes delegar tareas tediosas, acelerar el desarrollo y dedicar más tiempo a asuntos de mayor alcance.

En este ejercicio explorarás cómo Copilot en la nube permite:

- personalizar el entorno de generación de código.
- garantizar que las operaciones se realicen de forma segura.
- comprender la importancia de delimitar bien las incidencias.
- asignar incidencias a Copilot.

## Escenarios

Tailspin Toys quiere resolver parte de su deuda técnica. Los contratistas que crearon la primera versión del sitio dejaron la documentación en un estado deficiente: comprobarás que no existe. Como primer paso, el equipo quiere añadir comentarios TSDoc a todas las funciones exportadas de la aplicación.

Además, el equipo de diseño quiere mejorar el descubrimiento de juegos. En la página de detalles de cada juego, quieren mostrar otros juegos relacionados de la misma categoría para que los visitantes puedan seguir explorando. Por ahora no necesitan una implementación pulida: quieren algo que les permita probar la experiencia de usuario. Esta tarea está bloqueada, aunque hay otras incidencias con mayor prioridad.

Son ejemplos de tareas que suelen perder prioridad y que conviene asignar al agente de Copilot en la nube. El agente trabaja en ellas de forma asíncrona mientras el desarrollador se ocupa de otras tareas; después se pueden revisar sus resultados.

## Presentación del agente de GitHub Copilot en la nube

El [agente de GitHub Copilot en la nube](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede trabajar en segundo plano de forma parecida a un desarrollador. Por ejemplo, puedes [asignar una incidencia de GitHub a Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Cuando se asigna una incidencia, Copilot crea una solicitud de incorporación de cambios en borrador para hacer seguimiento del trabajo, configura un entorno y empieza a trabajar. Puedes consultar la sesión mientras está en curso o cuando termine. Cuando la solución esté lista para revisión, Copilot te mencionará en la PR.

## La importancia de delimitar bien las instrucciones

GitHub Copilot no hace magia: no elimina la necesidad de pensar. Incluso las operaciones aparentemente sencillas pueden tener cierta complejidad, así que [delimita cuidadosamente las tareas para el agente de Copilot en la nube][cloud-agent-best-practices]. Trátalo como un compañero de programación con IA: trabaja por etapas, aprende, experimenta y adapta el proceso. La IA generativa no cambia los fundamentos del desarrollo de software.

## Instrucciones personalizadas en este repositorio

En ejercicios anteriores se presentaron las instrucciones personalizadas y su función. Si ya has hecho el ejercicio práctico sobre instrucciones personalizadas, habrás visto cómo funciona `.github/copilot-instructions.md`; si no, este es un buen momento para echar un vistazo. Antes de asignar trabajo al agente en la nube, revisa los archivos de instrucciones existentes sin modificarlos para que puedas observar sus efectos más adelante.

Abre estos archivos en GitHub o en un Codespace:

- `.github/copilot-instructions.md`: revisa la sección **Code standards**, en particular las convenciones de TypeScript y los comentarios de documentación TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observa el frontmatter `applyTo`, que aplica estas instrucciones a los archivos `**/*.test.ts`.

Cuando asignes la incidencia `Code lacks documentation` al agente en la nube en la próxima sección, fíjate en si la PR resultante incluye comentarios TSDoc, respeta las convenciones de TypeScript y añade cabeceras de comentarios. Estas reglas proceden de los archivos de instrucciones y volveremos a revisarlas al analizar las PR en una lección posterior.

## Configura el entorno de desarrollo del agente de Copilot en la nube

El desarrollo de código suele requerir un entorno específico y la ejecución de algunos scripts de configuración para que todo esté preparado. Lo mismo ocurre al asignar tareas a Copilot, que trabaja de forma parecida a un ingeniero de software.

El agente en la nube utiliza [GitHub Actions][github-actions] para configurar su entorno. Puedes personalizarlo con un [flujo de trabajo de configuración especial][setup-workflow], definido en `.github/workflows/copilot-setup-steps.yml`, que se ejecuta antes de que empiece el trabajo. Esto permite instalar las herramientas y dependencias necesarias. El flujo ya está configurado para el taller y proporciona Node.js, las dependencias del proyecto, los binarios del navegador para las pruebas de extremo a extremo y la base de datos SQLite migrada y cargada con datos de la aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

Se parece a cualquier otro archivo de flujo de trabajo de GitHub, pero hay algunos aspectos importantes:

- Contiene un único trabajo llamado `copilot-setup-steps`. Se ejecuta en GitHub Actions antes de que Copilot empiece a trabajar en la PR.
- El activador `workflow_dispatch` permite ejecutar manualmente el flujo desde la pestaña **Actions** del repositorio. Así puedes verificar que funciona en lugar de esperar a que Copilot lo ejecute.

## Añade documentación

Aunque todos entendemos la importancia de la documentación, muchos proyectos tienen información desactualizada o carecen de ella. Esta deuda técnica reduce la productividad y dificulta mantener el código o incorporar desarrolladores nuevos. Copilot puede generar documentación, por lo que es un buen candidato para esta tarea. Trabajará en segundo plano y más adelante revisarás sus resultados.

1. Abre tu repositorio en github.com en una pestaña nueva.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de incidencia.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Code lacks documentation`.
6. En **Description**, escribe:

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. Selecciona **Create** para crear la incidencia.
8. En el lado derecho, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

  ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

  ![Detalles de asignación de Copilot](../../../_images/ex4-assign-copilot-details.png)

10. Selecciona la pestaña **Pull Requests**.
11. Abre la nueva solicitud de incorporación de cambios (PR), que tendrá un título parecido a `[WIP]: Code lacks documentation`. Si no aparece, espera unos minutos y actualiza la ventana del navegador.
12. Al cabo de unos minutos, Copilot debería crear una lista de tareas.

> [!NOTE]
> La lista de tareas puede tardar varios minutos en aparecer en la PR. Copilot está configurando su entorno mediante el flujo descrito, analizando el proyecto y definiendo un enfoque para la tarea.

13. Revisa la lista y las tareas que va a completar.
14. Desplázate por la cronología de la PR y comprueba que Copilot ha empezado a trabajar en la incidencia.
15. Selecciona el botón **View session**.

  ![Vista de la sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Puede que tengas que actualizar la ventana para ver el indicador actualizado.

16. Observa la sesión en directo para ver cómo Copilot resuelve el problema: explora el código y su estado, se detiene a pensar y decidir el plan adecuado y, después, crea código.

El proceso puede tardar varios minutos. Una de las principales ventajas del agente de Copilot en la nube es que trabaja de forma asíncrona y te permite ocuparte de otras tareas. Aprovecharemos esa capacidad asignándole otra tarea mientras añadimos funcionalidades a la aplicación.

## Añade una sección de juegos relacionados a la página de detalles

Una ventaja importante del agente de Copilot en la nube es que permite repartir el trabajo: tú puedes ocuparte de una tarea mientras él trabaja en otra. Aunque añadir una sección de juegos relacionados para el equipo de diseño no debería llevar mucho tiempo, puedes dedicar ese tiempo a otras tareas. Asignemos el trabajo al agente.

1. Vuelve a tu repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue**.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Show related games on the game details page`.
6. En **Description**, escribe:

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. Selecciona **Create** para crear la incidencia.
8. En el lado derecho, selecciona **Assign to Copilot** para abrir el cuadro de asignación.

  ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Poco después, deberías ver 👀 en el primer comentario de la incidencia; indica que Copilot está trabajando.

![Copilot utiliza el emoji de ojos para indicar que está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

Copilot está trabajando en la segunda solicitud. El agente de Copilot en la nube trabaja de forma similar a un ingeniero de software: no hace falta supervisarlo continuamente; puedes revisar el resultado cuando termine. Ahora pasarás a crear y utilizar agentes personalizados.

## Resumen y próximos pasos

En esta lección exploraste el [agente de GitHub Copilot en la nube][copilot-agents]. Puedes asignarle incidencias para que las resuelva de forma asíncrona. Úsalo para resolver deuda técnica, crear funcionalidades o ayudar a migrar código de un framework a otro.

Exploraste estos conceptos:

- personalizar el entorno de generación de código.
- garantizar que las operaciones se realicen de forma segura.
- comprender la importancia de delimitar bien las incidencias.
- asignar incidencias a Copilot.

Mientras el agente trabaja en segundo plano, puedes crear y utilizar agentes personalizados. El [agente de Copilot en la nube también admite servidores MCP][cloud-agent-mcp] e instrucciones personalizadas, que ya exploramos en módulos anteriores.

## Recursos

- [Acerca del agente de Copilot en la nube][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de configuración del agente de Copilot en la nube][cloud-agent-best-practices]

| [Siguiente lección: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
