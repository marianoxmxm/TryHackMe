# Explotación de Subdominio Oculto (TryHackMe - TakeOver / FutureVera)

Este documento detalla el proceso de resolución del CTF **TakeOver** (conocido como *FutureVera*), que se centra en técnicas de enumeración y descubrimiento de subdominios para encontrar una flag oculta.

---

## Resumen

El desafío consiste en una serie de páginas web estáticas alojadas en el dominio `futurevera.thm`.
La clave para resolver el CTF no reside en explotar una vulnerabilidad de software tradicional, sino en realizar un **reconocimiento exhaustivo** para descubrir un **subdominio oculto**.

Este subdominio, al ser accedido específicamente mediante **HTTP** en lugar de HTTPS, revela la flag a través de una **redirección HTTP**.

---

## Paso 1: Configuración Inicial y Reconocimiento

Se añadió la siguiente entrada al archivo `/etc/hosts`:

```
10.66.187.232 futurevera.thm
```

Luego, se realizó un escaneo de puertos con `nmap`:

```
nmap -sV 10.66.187.232
```

El escaneo reveló los puertos abiertos:

* **22 (SSH)**
* **80 (HTTP)**
* **443 (HTTPS)**

---

## Paso 2: Enumeración de Subdominios

Como la página principal era estática, el siguiente paso fue buscar subdominios.

Se añadió:

```
blog.futurevera.thm
```

al `/etc/hosts`, confirmando que alojaba un blog estático.

Luego se probó:

```
support.futurevera.thm
```

el cual mostraba una página de **"en construcción"**.

---

## Paso 3: Análisis de Certificados SSL/TLS

Se inspeccionó el certificado del subdominio usando:

```
openssl s_client -connect support.futurevera.thm:443 -servername support.futurevera.thm 2>/dev/null \
| openssl x509 -noout -text | grep -A 1 "X509v3 Subject Alternative Name"
```

El resultado reveló un subdominio oculto:

```
DNS:secrethelpdesk934752.support.futurevera.thm
```

---

## Paso 4: Descubrimiento y Acceso Final

Se añadió al `/etc/hosts`:

```
10.66.187.232 secrethelpdesk934752.support.futurevera.thm
```

Acceder mediante **HTTPS** resultaba en redirección a la página principal.
Se probó entonces con **HTTP**, ya que el comportamiento puede variar.

---

## Obtención de la Flag

Se utilizó `curl` con `-v` para ver detalles de la respuesta:

```
curl -v http://secrethelpdesk934752.support.futurevera.thm
```

La respuesta fue una redirección **HTTP 302** que contenía la flag en la cabecera `Location`:

```
< HTTP/1.1 302 Found
< Location: http://flag{beea0d6edfcee06a59b83fb50ae81b2f}.s3-website-us-west-3.amazonaws.com/
```

---

## Flag

```
flag{beea0d6edfcee06a59b83fb50ae81b2f}
```

---

## Herramientas Utilizadas

* **nmap** → escaneo de puertos
* **openssl** → análisis de certificados SSL/TLS
* **curl** → peticiones web y análisis de respuestas HTTP

---

Si querés, también puedo:

✅ armar un **README ya con estilo para GitHub** (tabla de contenido, imágenes, badges)
✅ generar **diagramas** (flujo de resolución)
✅ crear **versión en inglés** para repos público

Decime qué preferís 👌
