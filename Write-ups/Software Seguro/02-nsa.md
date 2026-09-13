# 2. NSA — Software Seguro
 
## EN
 
### Description
An SQLi challenge presented as an already-authenticated user viewing a filterable list of projects. According to the challenge statement, the goal was to view records the current user shouldn't have access to (a specific project classification level), despite the UI not exposing that filter option.
 
### Methodology
1. Inspected the filtering mechanism and found that each filter action triggered a `GET` request to `index.php?type=N`, where `N` changed depending on the selected category.
2. Requesting the "forbidden" category value directly (by modifying the URL) returned `HTTP 200 OK`, but with an empty result set (`"projects": []`) — unlike other categories, which returned actual data. This suggested the restriction happened at the query level, not via an access-control check that outright blocked the request.
3. Tested for SQL injection by appending a single quote (`'`) to the parameter value, which triggered a MySQL syntax error. The error message leaked part of the underlying query structure.
This confirmed the app was building a query that explicitly excluded results belonging to a restricted classification level via a subquery.
4. Since the `type` parameter was numeric (not wrapped in quotes in the original query), injection didn't require breaking out of a string — SQL could be appended directly after the number.
5. Reasoned through SQL operator precedence (`AND` binds tighter than `OR`) to determine that appending an `OR 1=1` clause at the end of the condition would make the entire condition evaluate to true, regardless of the restrictive subquery preceding it.
6. Built the payload via Chrome DevTools' "Resend as fetch", getting the full dataset, including previously hidden entries — confirming the injection worked at the database level.
8. However, the page's rendered HTML didn't update, since "Resend as fetch" executes the request in isolation, bypassing the app's own JavaScript logic that normally receives the fetch response and re-renders the DOM.
9. To trigger the full application flow (real fetch → app.js handling → DOM update), directly modified the relevant DOM element controlling the filter value instead of only replaying the raw request, letting the app's own script pick up the injected value and render the results as it normally would.
### Key Takeaways
- A `200 OK` with an empty result is not proof of a hard access-control block — it can just mean the query executed but matched nothing due to an additional filtering condition.
- Triggering a raw SQL error by injecting a single quote is a fast way to leak partial query structure, which is often enough to infer the rest of the `WHERE` clause.
- Understanding operator precedence (`AND` before `OR`) is essential for correctly placing an injected `OR` condition so it neutralizes prior restrictions instead of being swallowed by them.
- Numeric parameters (unquoted in the original query) don't require a leading quote to inject — SQL can be appended directly.
- Tools like "Resend as fetch" are useful for confirming a payload works at the server/DB level, but they bypass the frontend's own JS logic — if the UI doesn't update, the issue may be in how the request is delivered, not in the payload itself. Modifying the DOM directly (as in the "Uso del inspector" lab) can let the app's own script handle the response naturally.
### Tools Used
- Chrome DevTools (Network, Elements, Console — including "Resend as fetch")

## ES

### Descripción
Un desafío de SQLi presentado como un usuario ya autenticado, viendo una lista filtrable de proyectos. Según el enunciado, el objetivo era visualizar registros a los que el usuario actual no debería tener acceso (un nivel de clasificación específico de proyecto), aunque la interfaz no exponía esa opción de filtro.

### Metodología
1. Inspeccioné el mecanismo de filtrado y encontré que cada acción de filtro disparaba una request `GET` a `index.php?type=N`, donde `N` cambiaba según la categoría seleccionada.
2. Al pedir directamente el valor de la categoría "prohibida" (modificando la URL) obtuve un `HTTP 200 OK`, pero con un resultado vacío (`"projects": []`) — a diferencia de otras categorías, que sí devolvían datos. Esto sugería que la restricción ocurría a nivel de la consulta, no mediante un control de acceso que bloqueara directamente la request.
3. Probé inyección SQL agregando una comilla simple (`'`) al valor del parámetro, lo cual disparó un error de sintaxis de MySQL. El mensaje de error filtró parte de la estructura de la consulta subyacente.
Esto confirmó que la app armaba una query que excluía explícitamente los resultados pertenecientes a un nivel de clasificación restringido, mediante una subquery.
4. Como el parámetro `type` era numérico (sin comillas en la query original), la inyección no requería escapar de un string — se podía agregar SQL directamente después del número.
5. Razoné sobre la precedencia de operadores en SQL (`AND` se evalúa antes que `OR`) para determinar que agregar una cláusula `OR 1=1` al final de la condición haría que toda la condición evaluara como verdadera, sin importar la subquery restrictiva que la precedía.
6. Armé el payload mediante "Resend as fetch" de Chrome DevTools, obtuve el dataset completo, incluyendo entradas previamente ocultas — confirmando que la inyección funcionaba a nivel de base de datos.
8. Sin embargo, el HTML renderizado de la página no se actualizó, ya que "Resend as fetch" ejecuta la request de forma aislada, sin pasar por la lógica propia de JavaScript de la app que normalmente recibe la respuesta del fetch y vuelve a renderizar el DOM.
9. Para disparar el flujo completo de la aplicación (fetch real → manejo por app.js → actualización del DOM), en vez de solo reenviar la request cruda, modifiqué directamente el elemento del DOM que controla el valor del filtro, dejando que el propio script de la app tomara el valor inyectado y renderizara los resultados normalmente.

### Aprendizajes clave
- Un `200 OK` con un resultado vacío no es prueba de un bloqueo estricto de control de acceso — puede simplemente significar que la query se ejecutó pero no matcheó nada debido a una condición de filtrado adicional.
- Disparar un error SQL crudo inyectando una comilla simple es una forma rápida de filtrar parte de la estructura de la query, lo cual suele ser suficiente para inferir el resto de la cláusula `WHERE`.
- Entender la precedencia de operadores (`AND` antes que `OR`) es esencial para ubicar correctamente una condición `OR` inyectada, de modo que neutralice restricciones previas en vez de quedar "absorbida" por ellas.
- Los parámetros numéricos (sin comillas en la query original) no requieren una comilla inicial para inyectar — el SQL se puede agregar directamente.
- Herramientas como "Resend as fetch" son útiles para confirmar que un payload funciona a nivel servidor/DB, pero no pasan por la lógica propia del JS del frontend — si la interfaz no se actualiza, el problema puede estar en cómo se entrega la request, no en el payload en sí. Modificar el DOM directamente (como en el lab de "Uso del inspector") puede hacer que el propio script de la app maneje la respuesta con normalidad.

### Herramientas usadas
- Chrome DevTools (Network, Elements, Console — incluyendo "Resend as fetch")