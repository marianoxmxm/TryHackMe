## Enumeración DNS y Obtención de la Flag (Dig Dug)

Al acceder al desafío, se indica que la máquina objetivo (**MACHINE_IP**) también funciona como un **servidor DNS**.
Sin embargo, presenta un comportamiento particular: **solo responde a un tipo específico de consulta DNS** para el dominio `givemetheflag.com`.

El objetivo es utilizar herramientas comunes de enumeración DNS para provocar una respuesta válida del servidor que revele la flag.

---

## Preparación del Entorno

Para resolver el desafío fue necesario desplegar:

* La **máquina vulnerable**
* La **TryHackMe AttackBox**

Ambos servicios se inician desde la interfaz de TryHackMe.

---

## Enumeración Inicial

Se comenzó con un escaneo de puertos utilizando `rustscan`:

```bash
rustscan -a 10.10.135.56 --ulimit 10000 -- -A -vvv -sV -sC -Pn
```

### Resultados Relevantes

Del escaneo se identificaron los siguientes puertos abiertos:

* **22/tcp** — SSH
* **53/tcp** — DNS

Dado el contexto del desafío, el alcance se centró exclusivamente en el **puerto 53 (DNS)**.

---

## Investigación del Servicio DNS

Se realizó una breve investigación sobre explotación y enumeración de servicios DNS.
Un recurso útil consultado fue:

* **HackTricks — Pentesting DNS (Port 53)**

Esto permitió identificar técnicas comunes para interactuar con servidores DNS mal configurados.

---

## Enumeración DNS

Se utilizó la herramienta `dig` para realizar consultas DNS directas al servidor objetivo.

El desafío indicaba que el servidor solo respondería correctamente a solicitudes relacionadas con un dominio específico.

### Consulta DNS

```bash
dig A @<DNS_IP> givemetheflag.com
```

Esta consulta provocó la respuesta esperada del servidor DNS, revelando la **flag del desafío**.

---

## Conclusión

El desafío se basó en:

* Identificar un **servicio DNS expuesto**
* Comprender su comportamiento restringido
* Realizar una **consulta DNS específica** para obtener la flag

Con una correcta enumeración y una simple consulta `dig`, el CTF quedó resuelto exitosamente.

---
