**Estudiante:** Alejandra León Vargas 
**Profesor:** Diego Mauricio Zuluaga Rodríguez  
**Fecha:** 15 de Abril 2026


## Descripción del Proyecto

Encontramos la solución de el **Parcial Final Corte 2 **  
de la materia Programación y Decisiones. **Cafetería Universidad de La Sabana**, realizamos un ETL (Extracción, Transformación y Carga) completo sobre datos sucios, con limpiandolos al final a una base de datos SQLite.


## Estructura del Repositorio

parcial_cafeteria_sabana/

parcial_corte_2_solucion.ipynb        # Notebook principal con toda la solución

parcial_2_productos_sucios.csv        # Datos originales sucios - Productos

parcial_2_clientes_sucios.csv         # Datos originales sucios - Clientes

parcial_2_proveedores_sucios.csv      # Datos originales sucios - Proveedores

parcial_2_productos_limpios.csv       # Datos normalizados - Productos

parcial_2_clientes_limpios.csv        # Datos normalizados - Clientes

parcial_2_proveedores_limpios.csv     # Datos normalizados - Proveedores

parcial_2_cafeteria.db                # Base de datos SQLite final (4 tablas)

README.md                             # Este archivo


## Paso a Paso 

### FASE 1 — Archivos CSV

1. Se ejecutó la celda generadora de datos para crear los **3 archivos CSV con datos sucios** directamente desde strings en Python.
2. Los 3 archivos se cargan en **DataFrames independientes** con `pd.read_csv()`.


### FASE 2 — Limpieza con Pandas

#### Productos (`df_prod`)
Problema encontrado y solución aplicada 

Columna `producto_categoria` combinada (nombre + categoría) | Separada en `nombre_producto` y `categoria` con `.str.split(' - ')` → **1NF** 
Precios con símbolo `$` como string  
Limpieza con `.str.replace('$', '')` y conversión a `float` 
Textos en mayúsculas/minúsculas inconsistentes 
Estandarización con `.str.title()` 
Valores `NaN` en `stock` 
Rellenados con `0` (sin stock registrado) y convertidos a `int` 

#### Clientes (`df_cli`)
Problema encontrado 
Solución aplicada 
Columna `cliente_tipo` combinada (nombre + tipo) 
Separada en `nombre_cliente` y `tipo_cliente` → **1NF** 
Emails y teléfonos faltantes (`NaN`) | Rellenados con `'No Registra'` 
Columna `edad` (dato derivado de `fecha_nacimiento`) | **Eliminada** por violar **3NF** 
Textos inconsistentes 
Estandarización con `.str.title()` 

#### Proveedores (`df_prov`)
Problema encontrado 
Solución aplicada
Columna `empresa_ciudad` combinada (empresa + ciudad) 
Separada en `nombre_empresa` y `ciudad` → **1NF** 
Contacto, teléfono y email faltantes 
Rellenados con `'No Registra'` 
Textos inconsistentes 
Estandarización con `.str.title()` 

#### Exportación
Los 3 DataFrames limpios fueron exportados como CSV con `df.to_csv(..., index=False)`.


### FASE 3 Base de Datos Relacional (SQLite)

1. Conexión a `parcial_2_cafeteria.db` con `sqlite3.connect()`.
2. Los 3 DataFrames cargados como tablas SQL usando `df.to_sql()` con `if_exists='replace'`.


### FASE 4 — Arquitectura SQL 

#### Tabla `ventas` (creada con SQL puro)
```sql
CREATE TABLE ventas (
    id_venta      INTEGER PRIMARY KEY AUTOINCREMENT,
    id_cliente    INTEGER NOT NULL,
    id_producto   INTEGER NOT NULL,
    id_proveedor  INTEGER NOT NULL,
    cantidad      INTEGER NOT NULL,
    total_venta   REAL    NOT NULL,
    fecha_venta   TEXT    NOT NULL,
    FOREIGN KEY (id_cliente)   REFERENCES clientes(id_cliente),
    FOREIGN KEY (id_producto)  REFERENCES productos(id_producto),
    FOREIGN KEY (id_proveedor) REFERENCES proveedores(nit_proveedor)
);
```

#### CRUD Ejecutado

**CREATE** — `INSERT INTO ventas` con 5 registros de ventas reales.

**READ** — `SELECT` con `INNER JOIN` a las 3 tablas para obtener nombre del cliente, producto y total.

**UPDATE** — Actualización de `cantidad = 5` y `total_venta = 25000` para `id_venta = 1`.

**DELETE** — Eliminación del registro con `id_venta = 3`.
