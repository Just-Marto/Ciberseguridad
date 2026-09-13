# [Uso del Inspector] — [Software Seguro]

## **EN**

### Description
A beginner-level web challenge focused on browser inspection skills. The goal was to retrieve a code that was not visible in the page's HTML source, using only browser developer tools, and submit it through a form to complete the challenge.

### Methodology 
1. Identified that the challenge required inspecting HTTP traffic via DevTools.
2. Using the Network tab, located a custom response header (`X-Code`) 
   in the main document request.
3. Attempted to modify the corresponding hidden input's value via the Elements panel, 
   but the change wasn't reflected correctly when the form was submitted.
4. Diagnosed the issue by checking the actual DOM value via console:
   `document.getElementById("code").value`
5. Fixed it by setting the value directly via JavaScript in the console instead of 
   manual editing.
6. Submitted the form and verified the server's successful response.

### Key Takeaways
- HTTP response headers don't appear in page source or the DOM — you need to 
  check them in the Network tab.
- Manually editing attributes in the Elements panel doesn't always reflect the 
  real value a script will read; using the console (`element.value = "..."`) 
  is more reliable.

### Tools Used
- Chrome DevTools (Network, Elements, Console)


## **ES**

### Descripción
Un desafío web de nivel principiante enfocado en habilidades de inspección del navegador. El objetivo era obtener un código que no era visible en el código fuente de la página, usando únicamente las herramientas de desarrollador del navegador, y enviarlo mediante un formulario para completar el desafío.

### Metodología
1. Identifiqué que el desafío requería inspeccionar el tráfico HTTP mediante DevTools.
2. Usando la pestaña Network, localicé un header de respuesta personalizado (`X-Code`) 
   en la request del documento principal.
3. Intenté modificar el valor del input hidden correspondiente vía el panel Elements, 
   pero el cambio no se reflejaba correctamente al momento del envío.
4. Diagnostiqué el problema verificando el valor real del DOM por consola:
   `document.getElementById("code").value`
5. Corregí seteando el valor directamente por JavaScript en consola en vez de edición manual.
6. Envié el formulario y validé la respuesta exitosa del servidor.

### Aprendizajes clave
- Los headers HTTP de respuesta no aparecen en el código fuente ni en el DOM — 
  hay que revisarlos en la pestaña Network.
- Editar atributos "a mano" en el panel Elements no siempre refleja el valor real 
  que un script va a leer; usar la consola (`elemento.value = "..."`) es más confiable.

### Herramientas usadas
- Chrome DevTools (Network, Elements, Console)