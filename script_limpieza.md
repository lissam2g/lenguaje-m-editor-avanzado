let // Paso 1: Fuente de datos original
    Origen = Table.FromRows(Json.Document(Binary.Decompress(Binary.FromText("XZBBasMwEEWvMmgdB0m2WndpJ4GWNBAaly5MFoqihcCWjGxBr5Mz9Ai+WEcOhai7mQeP/2faljCyIvAuh8kNcPQOmAAkG9cPYZLKzD8WV8YpXVOKE6e8yCjLqCDnVUs4ooMLo4Y3K7v51l+8UQ6hVEqPzhs3Rqn8J5eLnMfoRqtOXh0ctJpv9i4fPz53dYXDi0hFxhexWFKtmZyHYh/7qrRvIZK6PKP5IoqYWAXsGDrp9Qh1g6QKV+PuV6YWo4v1hOh02sLue9Le4ouaGh5bsjzx8r/nPCP60hcle3jdxpzHn5QideJp518=", BinaryEncoding.Base64), Compression.Deflate)), let _t = ((type nullable text) meta [Serialized.Text = true]) in type table [id_venta = _t, nombre_producto = _t, categoria = _t, precio = _t, fecha_venta = _t]),
    // Paso 2: Eliminar espacios en blanco al inicio y al final de la columna nombre_producto usando Text.Trim
    LimpiarNombresProducto = Table.TransformColumns(Origen,{{"nombre_producto", Text.Trim, type text}}),
    // Paso 3: Estandarizar la columna categoria a Title Case
    // Modificación manual: renombré este paso para identificar con mayor claridad la transformación aplicada a nombre_producto.
    EstandarizarCategoria = Table.TransformColumns(LimpiarNombresProducto, {{"categoria", Text.Proper, type text}}),
    // Paso 4: Eliminar los registros de prueba después de estandarizar la categoría.
    FiltrarRegistrosPrueba = Table.SelectRows(EstandarizarCategoria, each [categoria] <> "Prueba"),
    // Paso 5: Asignar los tipos de datos correctos a las columnas.
    TiparColumnas = Table.TransformColumnTypes(FiltrarRegistrosPrueba, {
    {"id_venta", Int64.Type},
    {"nombre_producto", type text},
    {"precio", type number},
    {"fecha_venta", type date}
    }, "en-US")
in
    TiparColumnas
