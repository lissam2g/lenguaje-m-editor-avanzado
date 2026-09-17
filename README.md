# Práctica: Lenguaje M - Explorando el Editor Avanzado de Power Query

## Código M

A continuación se presenta el código final generado en el Editor Avanzado de Power Query, incluyendo la modificación manual realizada sobre uno de los pasos y el comentario agregado mediante `//`.

### Código completo

let
    Origen = Csv.Document(File.Contents("I:\Mi unidad\Varios\Curso Data Analytics\Módulo 6\Practica M\data.csv"),[Delimiter=",", Columns=8, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Encabezados promovidos" = Table.PromoteHeaders(Origen, [PromoteAllScalars=true]),
    #"Tipo cambiado con configuración regional" = Table.TransformColumnTypes(#"Encabezados promovidos", {{"InvoiceDate", type datetime}}, "en-US"),
    #"Tipo cambiado" = Table.TransformColumnTypes(#"Tipo cambiado con configuración regional",{{"InvoiceDate", type date}, {"Quantity", Int64.Type}}),
    // Filtrar las ventas para conservar únicamente registros con cantidad mayor que cero.
    #"Filtrar a cantidad positiva"  = Table.SelectRows(#"Tipo cambiado", each [Quantity] > 0),
    #"Columnas con nombre cambiado" = Table.RenameColumns(#"Filtrar a cantidad positiva",{{"CustomerID", "id_cliente"}, {"Country", "pais"}, {"InvoiceDate", "fecha_venta"}, {"Quantity", "cantidad"}, {"Description", "descripcion"}, {"InvoiceNo", "id_venta"}, {"UnitPrice", "precio_unitario"}, {"StockCode", "id_producto"}})
in
    #"Columnas con nombre cambiado"

---

## Explicación

### 1. ¿Por qué es útil para un analista de datos entender la estructura `let ... in`?

Considero que es útil porque permite entender qué está haciendo Power Query con los datos en cada paso. Aunque muchas transformaciones se pueden hacer desde los botones de la interfaz, estas acciones se convierten en código M y quedan organizadas dentro de la estructura `let ... in`.
Entender esta estructura permite revisar el código, saber de dónde sale cada transformación y hacer modificaciones directamente en el Editor Avanzado cuando sea necesario. También ayuda a identificar en qué paso puede estar un error, ya que cada transformación depende del resultado de la anterior.

### 2. ¿Qué significa que el lenguaje M sea Case Sensitive y cuál es la consecuencia práctica de ignorarlo?

Que M sea **Case Sensitive** significa que diferencia entre mayúsculas y minúsculas. Por ejemplo, `Table.SelectRows` y `table.selectrows` no se consideran la misma función.

Esto es importante cuando se modifica el código manualmente, porque escribir una función, un paso o una referencia con una mayúscula o minúscula incorrecta puede generar un error y hacer que la consulta no funcione. Por eso, al trabajar en el Editor Avanzado hay que tener cuidado con la forma exacta en que están escritos los nombres.

### 3. ¿Por qué elegiste ese dataset y qué criterios usaste para seleccionarlo?

Elegí el dataset "E-Commerce Analysis - UK" porque está relacionado con ventas de comercio electrónico y tiene información variada que permite practicar diferentes transformaciones en Power Query.
Para seleccionarlo tuve en cuenta principalmente los criterios de la actividad: que fuera un dataset disponible públicamente, que tuviera más de cinco columnas y que manejara diferentes tipos de datos, como fechas, números y texto. También me interesaba que tuviera datos que permitieran identificar situaciones como valores faltantes y otros aspectos que pudieran ser revisados durante el proceso de transformación.
Además, me pareció apropiado para la práctica porque permite ver claramente la relación entre las transformaciones que se realizan desde la interfaz de Power Query y el código M que se genera automáticamente.

**Fuente del dataset:**  
https://www.kaggle.com/datasets/atharvaarya25/e-commerce-analysis-uk
