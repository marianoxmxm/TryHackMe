# IDOR con Hashes MD5 en Imagen Mapeada (CTF)

Este documento describe la resolución de un desafío basado en **Insecure Direct Object Reference (IDOR)**, donde los objetos eran accesibles mediante identificadores predecibles: los **hashes MD5** de números secuenciales.

---

## Paso 1: Análisis Inicial

Al acceder a la dirección IP proporcionada, se mostró una página con la imagen de un corredor.
Al inspeccionar el código fuente, se descubrió que la imagen contenía un **image map** con varias áreas clickeables.

Cada área apuntaba a una URL cuyo final era una cadena de **32 caracteres hexadecimales**.

---

## Paso 2: Examen de los Endpoints

La pista del desafío indicaba que estos valores parecían **hashes**, lo que llevó a sospechar que eran **MD5**.

Ejemplo de uno de los hashes encontrados:

```
c4ca4238a0b923820dcc509a6f75849b
```

---

## Paso 3: Verificación de los Hashes

Para confirmar la teoría, se buscó el primer hash en una base pública de MD5.
El resultado obtenido fue:

```
1
```

Esto confirmó que los enlaces representaban los **hashes MD5 de los números 1, 2, 3, ...**

---

## Paso 4: Resolución del Acertijo

La pista del desafío decía:

> “¿Puedes encontrar el camino de regreso a de donde viniste?”

Esto sugería buscar el **inicio** de la secuencia.
En una secuencia numérica, el origen natural es el **0**.

---

## Paso 5: Cálculo del Hash Objetivo

Se calculó el **hash MD5 del número 0**:

```
echo -n 0 | md5sum
```

Resultado:

```
cfcd208495d565ef66e7dff9f98764da
```

---

## Paso 6: Acceso a la Flag

Se construyó la URL utilizando el hash calculado:

```
http://10.64.158.139/cfcd208495d565ef66e7dff9f98764da
```

Al acceder a esa página, se reveló la flag.

---

## Conclusión

El desafío se trataba de un caso de **IDOR (Insecure Direct Object Reference)**:

* Los objetos (páginas) eran accesibles mediante un identificador predecible.
* El identificador era el **hash MD5** de números secuenciales.
* La solución consistió en **predecir el hash del objeto oculto**, que correspondía al número **0**.

---