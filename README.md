# README

## 1. ¿Qué hace exactamente el bloque `let...in` en lenguaje M? ¿Por qué cada paso puede referenciar al anterior?

El bloque `let...in` permite organizar una consulta de Power Query en diferentes pasos. Dentro de `let` se definen los pasos o variables que contienen cada transformación que se realiza sobre los datos. Finalmente, `in` indica cuál de esos pasos será el resultado que se devuelve.
Cada paso puede utilizar el resultado del paso anterior porque los pasos están definidos de manera secuencial y pueden referenciar el nombre de una transformación anterior. Por ejemplo, en esta práctica `EstandarizarCategoria` utiliza como entrada `LimpiarNombresProducto`, que es el paso anterior:

```m
EstandarizarCategoria = Table.TransformColumns(
    LimpiarNombresProducto,
    {{"categoria", Text.Proper, type text}}
),
```

Esto permite construir el proceso de transformación de manera ordenada, haciendo que cada modificación se aplique sobre los datos que resultaron del paso anterior.

---

## 2. ¿Por qué M es Case Sensitive y qué consecuencia práctica tiene? Da un ejemplo de un error que esto puede causar.

M es Case Sensitive porque diferencia entre letras mayúsculas y minúsculas. Esto significa que los nombres de las funciones, pasos y referencias deben escribirse respetando exactamente su forma.
Por ejemplo, `Table.SelectRows` es una función válida, mientras que escribirla como:

```m
table.selectrows
```

puede generar un error porque M diferencia las mayúsculas de las minúsculas.
En la práctica, esto significa que al modificar código directamente en el Editor Avanzado hay que tener cuidado con la escritura exacta de las funciones y de los nombres de los pasos. Un error de mayúsculas o minúsculas puede hacer que la consulta no se ejecute correctamente.

---

## 3. ¿Cuál es la diferencia entre usar `Text.Trim` y `Text.Clean` en M?

`Text.Trim` se utiliza para eliminar espacios u otros caracteres especificados que se encuentran al inicio y al final de un texto. En esta práctica lo utilicé para limpiar la columna `nombre_producto`, eliminando los espacios innecesarios que estaban en los extremos de los nombres.
Por ejemplo:

```m
Text.Trim(" Laptop ")
```

produce:

```text
Laptop
```

Por otro lado, `Text.Clean` tiene como finalidad eliminar caracteres de control que pueden encontrarse dentro de un texto, como algunos caracteres no imprimibles.

Por lo tanto, ambas funciones sirven para limpiar texto, pero tienen objetivos diferentes: `Text.Trim` se enfoca principalmente en los caracteres que están en los extremos del texto, mientras que `Text.Clean` elimina caracteres de control no imprimibles.

---

## 4. ¿Por qué filtraste los registros "PRUEBA" después de estandarizar la categoría y no antes?

Filtré los registros después de estandarizar la categoría porque `Text.Proper` modifica la forma en que está escrito el texto.
Por ejemplo, una categoría escrita como:

```text
PRUEBA
```

después de aplicar `Text.Proper` queda como:

```text
Prueba
```

Por esta razón, primero apliqué:

```m
EstandarizarCategoria = Table.TransformColumns(
    LimpiarNombresProducto,
    {{"categoria", Text.Proper, type text}}
),
```

y después realicé el filtro:

```m
FiltrarRegistrosPrueba = Table.SelectRows(
    EstandarizarCategoria,
    each [categoria] <> "Prueba"
),
```

De esta manera, el filtro se realiza sobre una categoría que ya tiene un formato estandarizado. Si hubiera filtrado antes, tendría que considerar las diferentes formas en que podría estar escrita la palabra, como `PRUEBA`, `Prueba` o `prueba`.
Esto también permite evitar problemas relacionados con la sensibilidad a mayúsculas y minúsculas del lenguaje M.
