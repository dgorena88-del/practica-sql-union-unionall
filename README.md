### 1. ¿Cuántas filas devuelve cada consulta y por qué son distintas?
* **Consulta 1 (UNION):** Devuelve **11 filas**.
* **Consulta 2 (UNION ALL):** Devuelve **14 filas** (7 de la sucursal norte + 7 de la sucursal sur).

**Explicación:** 
Son distintas porque **`UNION` aplica una eliminación de duplicados** sobre todo el conjunto de resultados. En los datos provistos, los productos **103 (Monitor 4K)**, **104 (Teclado Mecánico)** y **106 (SSD Externo 1TB)** tienen exactamente el mismo ID, nombre y categoría en ambas tablas. Al remover estas **3 filas duplicadas**, el total baja de 14 a 11.

> ⚠️ **Nota sobre la Webcam:** El producto *"Webcam HD 1080p"* aparece dos veces en el catálogo resultante porque en el Norte tiene el **ID 107** y en el Sur el **ID 111**. Al tener IDs distintos, SQL **no los considera** filas duplicadas idénticas.

---

### 2. ¿Por qué UNION ALL es más eficiente que UNION? ¿Qué operación adicional realiza UNION internamente?
**`UNION ALL` es sustancialmente más eficiente** porque es una operación de **concatenación directa**. SQL Server simplemente lee las filas de la primera tabla, lee las de la segunda y las entrega juntas en el resultado sin analizar su contenido.

Por el contrario, **`UNION` obliga al motor a realizar un paso extra muy costoso: la eliminación de duplicados.** Para lograr esto, SQL Server procesa los datos internamente de dos formas posibles:
1. **Sort (Ordenamiento):** Ordena todo el resultado consolidado para juntar las filas idénticas y eliminar las repetidas.
2. **Hash Match:** Construye una tabla Hash en memoria para ir validando fila por fila si ya fue procesada.

Ambas operaciones **consumen mucha memoria RAM y tiempo de CPU**, especialmente si se manejan millones de registros.

---

### 3. ¿En qué casos de negocio usarías cada uno?

#### Casos para UNION (Garantizar unicidad / Listas limpias)
* **E-mail Marketing:** Consolidar listas de clientes de distintas campañas para enviar un Newsletter unificado, **evitando enviar correos duplicados** al mismo cliente.
* **Maestros de Clientes:** Unificar bases de datos de usuarios registrados en una App móvil y en el sitio web para **generar un padrón único** sin registros repetidos.

#### Casos para UNION ALL (Preservar todos los registros / Máximo rendimiento)
* **Auditoría de Transacciones:** Consolidar ventas históricas de múltiples sucursales o plataformas donde **cada registro cuenta** y no se debe omitir ninguna operación, aunque parezca idéntica.
* **Reportes Financieros y Métricas:** Sumar ingresos totales o calcular volúmenes de datos. Al no eliminar filas, **no se alteran los totales matemáticos** de la organización.
* **Carga de Datos Excluyentes:** Unir tablas que por diseño **saben que no comparten registros duplicados** (ej. Histórico de año 2024 + Histórico de año 2025).
* **Optimización de Rendimiento Crítico:** Procesamiento de **grandes volúmenes de datos (Big Data)** donde la velocidad es la prioridad absoluta y se quiere evitar el cuello de botella del ordenamiento interno.
