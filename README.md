# 📊 Retail Optimization: De la Fuga de Margen a la Simulación de Beneficios

## 📝 Resumen del Proyecto
Este proyecto aborda un problema crítico de rentabilidad en una operación de Retail. A través de un flujo de trabajo que integra **Cloud Computing (BigQuery)** y **Business Intelligence (Power BI)**, se identificó que el volumen de ventas no se traducía en ganancias debido a una política de descuentos ineficiente en categorías específicas.

El resultado final es un **Dashboard Estratégico** que permite a la gerencia realizar análisis forenses de rentabilidad y simular cambios en las políticas de precios para recuperar margen operativo en tiempo real.

---

## 🛠️ Fase 1: Detección del Problema (SQL & BigQuery)
El análisis comenzó en la nube. Se procesaron los datos crudos en **Google BigQuery** para identificar los "Profit Killers" (productos que generan ventas pero destruyen valor).

**Hallazgo Técnico:** Se realizó un proceso de limpieza y modelado en la región `southamerica-west1` (Santiago) para cumplir con requisitos de latencia y residencia de datos.

```sql
-- Query para identificar subcategorías con margen negativo
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

Este análisis reveló que la categoría Tables (Mesas) presentaba las pérdidas más críticas de la operación.🔍 Fase 2: Análisis Forense (Power BI)Con los datos modelados, se implementó un análisis de causa raíz en Power BI utilizando técnicas avanzadas de visualización:Análisis de Correlación (Scatter Chart): Se mapeó la relación entre el Descuento y el Margen. Se descubrió un "Punto de Quiebre" en el 20%. Cualquier descuento superior a este umbral genera una caída drástica en la rentabilidad.Validación de Transacciones (DAX): Se detectó que en categorías críticas, hasta el 64% de las transacciones operan bajo pérdida.Métrica de Margen Real:$$Profit Margin \% = \frac{\sum(Profit)}{\sum(Sales)}$$💡 Fase 3: Solución y Simulación (What-If Analysis)Para transformar el reporte en una herramienta de decisión, se desarrolló una lógica de simulación basada en Parámetros de Escenario. El usuario puede ajustar el límite de descuento permitido y observar el impacto financiero instantáneo.Lógica de Simulación (DAX):Fragmento de códigoGanancia Simulada = 
VAR LimiteSeleccionado = [Limite de Descuento Valor]
RETURN
SUMX(
    'tabla_final_pbi',
    IF('tabla_final_pbi'[Discount] > LimiteSeleccionado,
        -- Recálculo de venta con tope de descuento sugerido
        ( ('tabla_final_pbi'[Sales] / (1 - 'tabla_final_pbi'[Discount])) * (1 - LimiteSeleccionado) ) 
        - 'tabla_final_pbi'[Estimated_Cost],
        'tabla_final_pbi'[Profit]
    )
)
📈 Impacto y ResultadosEl modelo cuantificó un potencial de optimización masivo para la organización:Estado Actual: Ganancia real de $286.40k.Margen Recuperable: $175.54k (mediante el ajuste de topes promocionales).Mejora Proyectada: Incremento del 61% en la utilidad neta global.🚀 Habilidades Técnicas DemostradasData Engineering: Extracción, transformación y carga (ETL) en Google BigQuery SQL.Business Logic: Implementación de modelos de simulación financiera con DAX.Data Storytelling: Diseño de dashboards orientados a la toma de decisiones ejecutivas.UX/UI for BI: Creación de tooltips dinámicos, navegación y diseño de "Bridge Charts" (Cascada).
### Instrucciones de finalización:
1.  **Sube tus imágenes:** En los lugares donde puse `[Image of...]`, borra ese texto y arrastra tus capturas de pantalla de Power BI directamente en el editor de GitHub.
2.  **Publica:** Haz clic en **"Commit changes"**.

**¿Te gustaría que te ayude a preparar un "Cheat Sheet" con las 5 preguntas técnicas de SQL 
