1. ¿Cuántas filas devuelve cada consulta y por qué son distintas?
 Consulta 1 (UNION): Devuelve 11 filas.
 Consulta 2 (UNION ALL): Devuelve 14 filas (7 de la sucursal norte + 7 de la sucursal sur).
 Explicación: Son distintas porque UNION aplica una eliminación de duplicados sobre todo el conjunto de resultados.
 En los datos provistos, los productos 103 (Monitor 4K), 104 (Teclado Mecánico) y 106 (SSD Externo 1TB) tienen exactamente el mismo ID, nombre y categoría en ambas tablas.
   Al remover estas 3 filas duplicadas, el total baja de 14 a 11.Nota sobre la Webcam: El producto "Webcam HD 1080p" aparece dos veces en el catálogo resultante porque en el Norte tiene el ID 107 y en el Sur el ID 111. Al tener IDs distintos, SQL no los considera filas duplicadas idénticas..
  
 2. ¿Por qué UNION ALL es más eficiente que UNION? ¿Qué operación adicional realiza UNION internamente?
      UNION ALL es sustancialmente más eficiente porque es una operación de concatenación directa. SQL Server simplemente lee las filas de la primera tabla, lee las de la segunda y las escupe juntas en el resultado sin mirar qué contienen.
      Por el contrario, UNION obliga al motor a realizar un paso extra muy costoso: la eliminación de duplicados. Para lograr esto, SQL Server procesa los datos internamente de dos formas posibles:Sort (Ordenamiento): Ordena todo el resultado consolidado para juntar las filas idénticas y eliminar las repetidas.Hash Match: Construye una tabla Hash en memoria para ir validando fila por fila si ya fue procesada.Ambas operaciones consumen mucha memoria RAM y tiempo de CPU, especialmente si manejamos millones de registros.
  
3. ¿En qué casos de negocio usarías cada uno?
Casos para UNION (Garantizar unicidad / Listas limpias):E-mail Marketing: Consolidar listas de clientes de distintas campañas para enviar un Newsletter unificado, evitando enviar correos duplicados al mismo cliente.
Maestros de Clientes: Unificar bases de datos de usuarios registrados en una App móvil y en el sitio web para generar un padrón único de clientes de la empresa.
Casos para UNION ALL (Rendimiento / Auditoría / Volumetría):Reportes Financieros: Consolidar las tablas de ventas históricas mensuales (Ventas_Enero UNION ALL Ventas_Febrero) para calcular la facturación anual total. Si usáramos UNION, perderíamos transacciones legítimas que tengan exactamente el mismo monto y fecha.Logs de Sistema: Agrupar registros de errores de múltiples servidores web para auditorías de seguridad, donde se necesita el 100% de los eventos cronológicos sin omitir ninguno.

4. ¿Qué pasa si las columnas de ambas consultas no coinciden en número o tipo? ¿Qué error genera SQL?
SQL Server es estrictamente tipado. Si las consultas no coinciden, se producen los siguientes errores:
Distinto número de columnas:SQL Server frena la ejecución del script y lanza el Error 205: "All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists." (Todas las consultas combinadas deben tener un número igual de expresiones).
Distinto tipo de datos: Si en la primera consulta la columna 2 es un INT y en la segunda consulta la columna 2 es un VARCHAR, SQL Server intentará hacer una conversión implícita basada en la precedencia de tipos. Si no puede convertir el texto a número, romperá la ejecución lanzando el Error 245: "Conversion failed when converting the varchar value '...' to data type int."
