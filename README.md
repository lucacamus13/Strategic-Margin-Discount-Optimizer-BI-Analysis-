Optimización de Margen en Retail: Análisis Forense y Simulación What-If
📝 Resumen del Proyecto
Este proyecto aborda un problema crítico de rentabilidad en una empresa de Retail. A través de un flujo de trabajo que integra Cloud Computing (BigQuery) y Business Intelligence (Power BI), se identificó que el volumen de ventas no se traducía en ganancias debido a una política de descuentos ineficiente.El resultado final es un Dashboard Prescriptivo que permite a la gerencia simular cambios en las políticas de precios y recuperar margen operativo en tiempo real.🛠️ Fase 1: Detección del Problema (SQL & BigQuery)El análisis comenzó en la nube. Se procesaron los datos crudos en Google BigQuery para identificar las categorías con menor rendimiento.Hallazgo Técnico: Se detectó una inconsistencia de región en los datos (Santiago vs US), lo que requirió una configuración de dataset específica para asegurar la residencia de los datos y el rendimiento de las consultas.SQL-- Query para identificar 'Profit Killers' por Subcategoría
SELECT 
    Sub_Category, 
    ROUND(SUM(Sales), 2) AS Total_Sales,
    ROUND(SUM(Profit), 2) AS Total_Profit,
    ROUND(AVG(Discount), 4) AS Avg_Discount,
    ROUND((SUM(Profit) / SUM(Sales)) * 100, 2) AS Margin_Percentage
FROM `proyecto-bi-483420.analisis_retail.tabla_final_pbi`
GROUP BY 1
HAVING Total_Profit < 0
ORDER BY Margin_Percentage ASC;
Este análisis inicial reveló que la categoría Tables (Mesas) era la principal responsable de la erosión del margen.🔍 Fase 2: Análisis Forense (Power BI)Una vez conectados los datos a Power BI, se aplicaron técnicas de visualización avanzada para encontrar la causa raíz:Gráfico de Dispersión (Correlation Analysis): Se mapeó la relación entre el Descuento y el Margen. Se descubrió un "punto de quiebre" crítico en el 20%. Cualquier descuento superior a este umbral genera pérdidas automáticas.Validación de Datos (DAX): Se crearon medidas para confirmar que en la categoría de Mesas, el 64% de las transacciones operan bajo pérdida.Medida Clave de Margen:$$\text{Profit Margin \%} = \text{DIVIDE}(\text{SUM}(\text{Profit}), \text{SUM}(\text{Sales}), 0)$$💡 Fase 3: Solución y Simulación (What-If Analysis)En lugar de un reporte estático, se desarrolló una herramienta de decisión. Utilizando Parámetros de Campo y DAX Avanzado, se creó un simulador donde el usuario puede ajustar el límite de descuento permitido.Lógica de Simulación:Fragmento de códigoGanancia Simulada = 
VAR LimiteSeleccionado = [Limite de Descuento Valor]
RETURN
SUMX(
    'tabla_final_pbi',
    IF('tabla_final_pbi'[Discount] > LimiteSeleccionado,
        -- Recalcular venta con tope de descuento
        ( ('tabla_final_pbi'[Sales] / (1 - 'tabla_final_pbi'[Discount])) * (1 - LimiteSeleccionado) ) 
        - 'tabla_final_pbi'[Estimated_Cost],
        'tabla_final_pbi'[Profit]
    )
)
📈 Impacto y ResultadosEl análisis permitió cuantificar el costo de oportunidad de las promociones actuales:Estado Actual: Ganancia real de $286.40k.Recuperación Proyectada: $175.54k mediante la optimización de descuentos en categorías críticas.Mejora de Margen: Un incremento potencial del 61% en la utilidad neta global.🚀 Cómo replicar este proyectoBigQuery: Cargar el dataset original y ejecutar el script SQL de limpieza.Power BI: Conectar mediante el conector de Google BigQuery (opción avanzada para especificar el Project ID).Modelado: Implementar el parámetro "What-If" para el límite de descuento (0% a 50%).
