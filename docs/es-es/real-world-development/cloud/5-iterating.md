---
title: "Ejercicio 5: Itera sobre el trabajo de GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Gestión de agentes][previous-lesson] |
|:--|

## Revisa el trabajo

Durante este laboratorio has trabajado con GitHub Copilot en varias tareas para mejorar la experiencia de usuario y añadir funcionalidades. Le pediste que añadiera documentación al código, desarrollara una funcionalidad de juegos relacionados para que el equipo de diseño pudiera probarla e implementara funciones de accesibilidad, incluidos los modos de alto contraste y claro. Revisemos los cambios de código y, si hace falta, enviemos comentarios a Copilot para mejorarlos.

### Escenario

Como ya hemos señalado varias veces, la IA generativa no cambia los fundamentos del diseño de software ni de DevOps. Siempre hay que revisar el código generado y seguir el proceso habitual. Revisarás las propuestas de GitHub Copilot para la documentación, los juegos relacionados y las funciones de accesibilidad antes de habilitar la revisión para el resto del equipo.

## Seguridad y el agente de GitHub Copilot en la nube

Como el agente de Copilot en la nube ejecuta tareas de forma asíncrona y sin supervisión, se han establecido restricciones de seguridad:

- Copilot solo tiene acceso de lectura al repositorio y acceso de escritura **únicamente** a la rama que utiliza para trabajar.
- El agente se ejecuta en GitHub Actions, en un entorno independiente y efímero.
- Una persona debe aprobar los flujos de trabajo de GitHub Actions antes de que se ejecuten.
- [El acceso a recursos externos está limitado de forma predeterminada](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall), incluidos los servidores MCP.

## Revisa la documentación generada

Empecemos por la primera solicitud de incorporación de cambios (PR) que generó el agente de Copilot en la nube: la que añade documentación al código. Utilizarás la interfaz habitual de PR de GitHub.com.

> [!NOTE]
> Puede que veas un aviso de que el firewall ha bloqueado GitHub Copilot. Es **normal**, ya que el acceso del agente a recursos externos está limitado de forma predeterminada, incluidas las llamadas a servidores MCP externos. Si quieres, puedes [personalizar o desactivar el firewall del agente de Copilot en la nube][agent-firewall].

1. Vuelve a tu repositorio en github.com.
2. Selecciona **Pull Requests** para abrir la lista de solicitudes.
3. Abre la PR titulada, por ejemplo, **Add missing documentation** o algo parecido.

> [!NOTE]
> Si Copilot aún está trabajando, la PR tendrá la etiqueta **[WIP]**. Espera a que termine; puede tardar unos minutos. Mientras tanto, puedes descansar o repasar lo aprendido.

4. Cuando la PR esté lista, selecciona la pestaña **Files changed** y revisa los cambios.

    ![Pestaña Files changed](../../../_images/shared-pr-files-changed.png)

5. Explora el código actualizado, incluidos los comentarios TSDoc y la documentación que se haya añadido. Los cambios exactos variarán.

    Busca comentarios TSDoc, convenciones de TypeScript y cabeceras de comentarios. Estas reglas proceden de los archivos de instrucciones personalizadas que revisaste al principio del ejercicio 2.

6. Cuando hayas revisado las actualizaciones, vuelve a la pestaña **Conversation** y desplázate hacia abajo.
7. Debería aparecer un indicador de que algunos flujos de trabajo están pendientes de aprobación.
8. Si hay flujos de trabajo pendientes, selecciona **Approve and run workflows**.

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. Los flujos de trabajo deberían aparecer en la sección de comprobaciones de la PR. Si todo va bien, las comprobaciones del proyecto para la aplicación Astro deberían superarse. Puede tardar unos minutos.

## Pide cambios a GitHub Copilot

La colaboración con Copilot en una PR no es un proceso unidireccional. Puedes mencionarlo en comentarios de la PR o en comentarios insertados en el código, como harías con cualquier compañero. Copilot verá esos comentarios e iniciará otra sesión para resolverlos. Como los resultados son no deterministas, no podemos proporcionar un texto exacto que debas utilizar. Puedes pedirle, por ejemplo, que:

- añada cabeceras de comentario al principio de cada archivo de código con una breve explicación de su propósito.
- añada comentarios TSDoc a los archivos TypeScript y Astro.
- cree un README que describa la estructura de la aplicación Astro.

1. Añade un comentario solicitando un cambio a la documentación generada y menciona a **@copilot** como mencionarías a cualquier usuario. Utiliza alguna de las ideas anteriores u otra sugerencia relacionada con la documentación del código.
2. Selecciona **View Session** para observar el trabajo de Copilot. Verás que inicia una sesión nueva para aplicar las actualizaciones.
3. Selecciona **Back to pull request** para volver a la solicitud.

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Cuando Copilot termine, debería aparecer una confirmación nueva en la PR.
5. Selecciona **Files changed** para revisar los cambios.

Puedes seguir iterando hasta que estés conforme. Después, puedes marcar la PR en borrador como lista para revisión y combinarla en la rama principal.

![Marcar la PR como lista para revisión](../../../_images/ex7-ready-for-review.png)

## Revisa la funcionalidad de juegos relacionados

Volvamos a la PR que Copilot creó para la incidencia de mostrar juegos relacionados en la página de detalles.

1. Vuelve a tu repositorio en GitHub.com.
2. Selecciona la pestaña **Pull Requests**.
3. Selecciona la PR titulada, por ejemplo, **Show related games on the game details page**.
4. Selecciona **Files changed** para revisar el código generado.
5. Cuando hayas revisado las actualizaciones, vuelve a la pestaña **Conversation** y desplázate hacia abajo.
6. Debería aparecer un indicador de que algunos flujos de trabajo están pendientes de aprobación.
7. Si hay flujos de trabajo pendientes, selecciona **Approve and run workflows**.

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. Los flujos de trabajo deberían aparecer en la sección de comprobaciones de la PR. Si todo va bien, las comprobaciones del proyecto para la aplicación Astro deberían superarse. Puede tardar unos minutos.
9. **Opcional:** puedes cambiar a esta rama en el Codespace para probar manualmente la funcionalidad de juegos relacionados. Abre un terminal en tu Codespace y ejecuta los siguientes comandos (sustituye `<branch-name>` por el nombre de la rama creada por Copilot, por ejemplo, **copilot/fix-8**):

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot ha creado la funcionalidad de juegos relacionados. Como antes, puedes pedirle actualizaciones mediante comentarios nuevos en la pestaña **Conversation**. Por ejemplo, puedes cambiar cuántos juegos relacionados se muestran o pedir que añada cabeceras de comentarios y comentarios TSDoc. Recuerda que esta incidencia se asignó **antes** de actualizar las instrucciones. Copilot verá el comentario e iniciará otra sesión para resolverlo.

## Revisa las funciones de accesibilidad

Por último, revisemos las funciones de accesibilidad implementadas mediante el agente personalizado de accesibilidad. Esta PR debe incluir tanto el modo de alto contraste que asignaste en el ejercicio 3 como el modo claro solicitado en el centro de control en el ejercicio 4.

1. Vuelve a tu repositorio en GitHub.com.
2. Selecciona la pestaña **Pull Requests**.
3. Selecciona la PR titulada, por ejemplo, **Add high contrast mode to website**.

> [!NOTE]
> Si Copilot aún está trabajando, la PR tendrá la etiqueta **[WIP]**. Espera a que termine; puede tardar unos minutos.

4. Selecciona **Files changed** para revisar el código generado.
5. Revisa la implementación, prestando especial atención a:
   - los componentes de interfaz para alternar entre los modos.
   - el almacenamiento local de las preferencias del usuario.
   - los cambios CSS o de estilo para los modos de alto contraste y claro.
   - los atributos de accesibilidad (etiquetas ARIA, navegación con teclado, etc.).
   - el código JavaScript/TypeScript que gestiona el cambio de modo.

6. Cuando hayas revisado las actualizaciones, vuelve a la pestaña **Conversation** y desplázate hacia abajo.
7. Debería aparecer un indicador de que algunos flujos de trabajo están pendientes de aprobación.
8. Si hay flujos de trabajo pendientes, selecciona **Approve and run workflows**.

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. Los flujos de trabajo deberían aparecer en la sección de comprobaciones de la PR. Si todo va bien, las comprobaciones del proyecto para la aplicación Astro deberían superarse. Puede tardar unos minutos.
10. **Opcional:** puedes cambiar a esta rama en tu Codespace para probar manualmente las funciones de accesibilidad. Abre un terminal en el Codespace y ejecuta los siguientes comandos (sustituye `<branch-name>` por el nombre de la rama creada por Copilot):

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    Después, inicia la aplicación y prueba los controles de alto contraste y modo claro en el navegador. Comprueba que funcionan y que las preferencias se conservan al volver a cargar la página.

Observa cómo el agente personalizado de accesibilidad ayudó a guiar a Copilot para que implementara estas funciones siguiendo prácticas recomendadas. Si encuentras problemas o mejoras de accesibilidad, menciona a **@copilot** en un comentario y pídele que actualice el código.

## Ejercicio opcional: sigue delegando

El agente en la nube resulta útil para asignarle trabajo real del backlog mientras tú te ocupas de otras tareas. Para desarrollar este hábito, registra más incidencias en el repositorio y asígnaselas a Copilot. Algunas ideas:

- crear un formulario para mostrar interés de patrocinadores en la página de detalles del juego.
- implementar la paginación de la lista de juegos.
- añadir validación de entradas y gestión de errores a los auxiliares de acceso a datos.

## Resumen

Has completado el itinerario del agente en la nube. En estas lecciones:

- **Inspeccionaste los archivos de instrucciones personalizadas del repositorio** para observar su efecto en el trabajo del agente.
- **Asignaste incidencias al agente de Copilot en la nube** y observaste cómo configura el entorno, planifica y trabaja de forma asíncrona.
- **Creaste y utilizaste un agente personalizado** de accesibilidad para añadir controles de alto contraste y modo claro.
- **Utilizaste la página de agentes de Copilot como centro de control** para supervisar y orientar la sesión de accesibilidad.
- **Revisaste e iteraste sobre las PR del agente**, mencionando a `@copilot` para solicitar cambios y aprobando flujos de trabajo.

## Repaso y próximos pasos

Has terminado el itinerario del agente en la nube. Si quieres seguir explorando, los otros itinerarios complementan lo que has practicado:

- 🖥️ **[Itinerario de VS Code](../../vscode/)**: explora el modo agente de Copilot Chat y la integración MCP directamente desde el IDE.
- 💻 **[Itinerario de CLI](../../cli/)**: entrega dos cambios revisados desde el terminal con Plan y Autopilot, habilidades de agente, un agente de QA personalizado, Playwright MCP, Agent Merge y una integración opcional `/delegate` con el agente en la nube.

En tu propio repositorio, prueba estas ideas:

- asigna al agente una incidencia de refactorización o cobertura de pruebas.
- crea un agente personalizado para otro ámbito.
- configura `copilot-setup-steps.yml` para otra pila tecnológica.

## Recursos

- [GitHub Copilot][github-copilot]
- [Acerca de los agentes de Copilot][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de configuración del agente de Copilot en la nube][cloud-agent-best-practices]
- [Configurar el firewall del agente de Copilot en la nube][agent-firewall]

---

| [← Lección anterior: Gestión de agentes][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
