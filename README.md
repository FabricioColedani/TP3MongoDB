### 📊 Pipeline de Agregación (MongoDB Compass)

Para generar el reporte de ventas por categoría y calcular la venta promedio en tiempo real, se implementó un pipeline de **5 etapas (stages)** utilizando el Aggregation Framework de MongoDB:

```json
[
  {
    "$match": {
      "precio": { "$gt": 0 }
    }
  },
  {
    "$project": {
      "categoria": 1,
      "cantidad": 1,
      "recaudacionVenta": {
        "$multiply": ["$precio", "$cantidad"]
      }
    }
  },
  {
    "$group": {
      "_id": "$categoria",
      "totalRecaudado": { "$sum": "$recaudacionVenta" },
      "cantidadItems": { "$sum": "$cantidad" }
    }
  },
  {
    "$sort": {
      "totalRecaudado": -1
    }
  },
  {
    "$project": {
      "_id": 1,
      "totalRecaudado": 1,
      "cantidadItems": 1,
      "ventaPromedio": {
        "$divide": ["$totalRecaudado", "$cantidadItems"]
      }
    }
  }
]
```

* **`$match`**: Filtra los documentos para procesar solo aquellos productos con un precio mayor a 0.
* **`$project` (1)**: Selecciona los campos necesarios y calcula la recaudación de cada venta multiplicando precio por cantidad.
* **`$group`**: Agrupa los elementos por su categoría, sumando el total recaudado y la cantidad de ítems vendidos por cada una.
* **`$sort`**: Ordena el resultado de mayor a menor según el total recaudado.
* **`$project` (2)**: Modela la salida final y calcula el valor de la **Venta Promedio** dividiendo la recaudación total por los ítems vendidos.
