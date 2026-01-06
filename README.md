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
