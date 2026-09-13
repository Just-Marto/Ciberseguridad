# 3. Home Banking — Software Seguro

## EN

### Description
A classic authentication-bypass SQLi challenge presented as a home banking login form. The goal was to log in without valid credentials by exploiting a SQL injection vulnerability in the login logic.

### Methodology
1. Identified the login form as the point of entry, assuming common patterns desings that the backend built a query of `SELECT * FROM users WHERE user='' AND password=''`.
2. Used the classic payload `' OR '1'='1` in the relevant field, which bypassed the login on the first attempt — the challenge was already resolved at this point.


### Tools Used
- Browser login form (manual payload testing)

## ES

### Descripción
Un desafío clásico de bypass de autenticación por SQLi, presentado como un formulario de login de home banking. El objetivo era iniciar sesión sin credenciales válidas explotando una vulnerabilidad de inyección SQL en la lógica de login.

### Metodología
1. Identifiqué el formulario de login como el punto de entrada, asumiendo patrones comunes donde el backend es una query del tipo `SELECT * FROM users WHERE user='' AND password=''`.
2. Usé el payload clásico `' OR '1'='1` en el campo correspondiente, el cual bypaseó el login en el primer intento — el desafío ya estaba resuelto en ese momento.


### Herramientas usadas
- Formulario de login del navegador (prueba manual de payloads)