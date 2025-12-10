# Explotación de Vulnerabilidad SSRF en Convertidor de PDF (TryHackMe - MD2PDF)

Este documento detalla el proceso de explotación de una vulnerabilidad de Server-Side Request Forgery (SSRF) en la sala MD2PDF de TryHackMe.

## Resumen

La aplicación web es un conversor de Markdown a PDF que utiliza una versión vulnerable de `wkhtmltopdf`. La vulnerabilidad permite la inyección de código HTML, lo que a su vez posibilita realizar peticiones a recursos internos del servidor. El objetivo final es acceder a un panel de administración no expuesto públicamente para obtener la flag.

---

### Paso 1: Reconocimiento

La primera fase consistió en interactuar con la aplicación para entender su funcionamiento. Al generar un PDF de prueba y analizar sus metadatos con la herramienta `pdfinfo`, se obtuvo información clave sobre la tecnología utilizada.
```bash
pdfinfo generated.pdf
```


La salida de este comando reveló que el PDF fue creado con `wkhtmltopdf 0.12.5`.

### Paso 2: Identificación de la Vulnerabilidad

Una búsqueda rápida sobre `wkhtmltopdf 0.12.5` confirmó que esta versión es vulnerable a **Server-Side Request Forgery (SSRF)** (CVE-2020-21365).

La vulnerabilidad se origina porque el motor de renderizado de `wkhtmltopdf` (basado en WebKit) puede procesar HTML y Javascript del lado del servidor. Si la aplicación no sanea correctamente la entrada del usuario, es posible inyectar etiquetas HTML como `<iframe>` o `<script>` para forzar al servidor a realizar peticiones a recursos internos.

### Paso 3: Explotación

Sabiendo que la aplicación era vulnerable a SSRF, el siguiente paso fue determinar a qué recurso interno se debía apuntar. Basado en la estructura común de este tipo de desafíos, se hipotetizó la existencia de un panel de administración en un puerto diferente, comúnmente `http://localhost:5000/admin`.

Se utilizó un `<iframe>` para intentar acceder a esta URL interna desde el contexto del servidor.

### Paso 4: Obtención de la Flag

Se construyó un payload final para ser introducido en el conversor. El servidor, al procesar el Markdown y el HTML inyectado, realizó una petición a la URL interna, renderizó el contenido de la página de administración dentro del `<iframe>` y lo incrustó en el PDF.

Finalmente, se utilizó la utilidad `pdftotext` para extraer el contenido de texto plano del PDF generado, revelando así la flag que se encontraba en el panel de administración.

```bash
pdftotext admin.pdf -
```

La salida del comando fue la flag: `flag{1f4a2b6ffeaf4707c43885d704eaee4b}`.

-----

## Payload Final

El siguiente payload HTML fue el utilizado para explotar la vulnerabilidad:

```html
<iframe src="http://localhost:5000/admin" width="1000" height="1000"></iframe>
```

## Herramientas Utilizadas

  * `curl`: Para interactuar con la aplicación web.
  * `pdfinfo`: Para analizar los metadatos del PDF.
  * `pdftotext`: Para extraer el contenido de texto del PDF final.

<!-- end list -->

