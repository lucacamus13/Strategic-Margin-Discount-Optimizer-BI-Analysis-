# Retail Profitability Optimization: De la Fuga de Margen a la Simulación Estratégica

## Resumen del Proyecto
Este proyecto aborda un problema crítico de rentabilidad en una operación de Retail. A pesar de mantener un alto volumen de ventas ($2.3M), la utilidad neta se veía comprometida por "fugas" de margen no identificadas.

A través de un flujo de trabajo que integra **Cloud Computing (Google BigQuery)** y **Business Intelligence (Power BI)**, se desarrolló una solución de inteligencia de negocios que no solo diagnostica el problema, sino que permite simular escenarios de recuperación financiera en tiempo real.

---

## Stack Tecnológico
* **Google BigQuery (SQL):** Extracción, limpieza y "Feature Engineering" en la nube.
* **Power BI:** Modelado de datos (Esquema en Estrella) y visualización avanzada.
* **DAX (Data Analysis Expressions):** Lógica de negocio compleja para simulaciones *What-If*.

---

## Fase 1: Detección del Problema (SQL & Cloud)
El análisis comenzó fuera de Power BI. Utilizando **SQL en BigQuery**, se procesaron los datos crudos para identificar qué categorías generaban flujo de caja pero destruían valor ("Profit Killers").

**Desafío Técnico:** Se gestionó la residencia de datos conectando al proyecto específico en la región `southamerica-west1` para garantizar la integridad de la consulta.

```sql
-- Query de diagnóstico: Identificación de márgenes negativos por subcategoría
SELECT 
    Sub_Category, 
    ROUND(SUM(Sales), 2) AS Total_Sales,
    ROUND(SUM(Profit), 2) AS Total_Profit,
    ROUND(AVG(Discount), 4) AS Avg_Discount,
    ROUND((SUM(Profit) / SUM(Sales)) * 100, 2) AS Margin_Percentage
FROM `proyecto-bi-483420.analisis_retail.tabla_final`
GROUP BY 1
HAVING Total_Profit < 0
ORDER BY Margin_Percentage ASC;
```

**Hallazgo:** La subcategoría **"Tables" (Mesas)** operaba con pérdidas sistemáticas a pesar de ser un "Top Seller" en volumen.

---

## Fase 2: Análisis Forense (Visualización)
Ya en Power BI, se aplicaron técnicas de análisis visual para encontrar la causa raíz del déficit en muebles:

* **Correlación Descuento vs. Margen:** Mediante un gráfico de dispersión, se identificó un **"Punto de Quiebre" del 20%**. Cualquier venta con un descuento superior a este umbral resulta matemáticamente en pérdida.
* **Validación de Transacciones:** Se crearon medidas de control que revelaron que el **64% de las transacciones** en la categoría de mesas destruyen valor.

![Gráfico de Dispersión - Scatter Chart](<img width="450" height="284" alt="image" src="https://github.com/user-attachments/assets/5e683fe7-c6f1-485a-ac67-0e55449ae448" />
)

---

## Fase 3: Solución Prescriptiva (Simulador What-If)
Para pasar del "diagnóstico" a la "acción", se construyó un **Simulador de Escenarios**. Utilizando parámetros de campo y DAX avanzado, la gerencia puede ajustar el límite máximo de descuento permitido y ver instantáneamente cuánto dinero se recuperaría.

**Lógica DAX de la Simulación:**

```dax
Ganancia Simulada = 
VAR LimiteSeleccionado = [Limite de Descuento Valor]
RETURN
SUMX(
    'tabla_final',
    IF('tabla_final'[Discount] > LimiteSeleccionado,
        -- Recálculo: Si el descuento excede el límite, se simula la venta al tope permitido
        ( ('tabla_final'[Sales] / (1 - 'tabla_final'[Discount])) * (1 - LimiteSeleccionado) ) 
        - 'tabla_final'[Estimated_Cost],
        -- Si está dentro del límite, se mantiene la ganancia real
        'tabla_final'[Profit]
    )
)
```

---

## Impacto de Negocio
El modelo final cuantifica el costo de oportunidad de la política de precios actual:

* **Estado Actual (Ganancia Real):** $286.40k
* **Recuperación por Optimización:** **$175.54k** (Al limitar descuentos al 18-20%).
* **Proyección Final:** Incremento del **61%** en la utilidad neta global.

> **Visualización Clave:** Se utilizó un **Gráfico de Cascada (Waterfall Chart)** para representar el puente financiero entre la situación actual y el escenario optimizado.

![Gráfico de Cascada - Waterfall Chart](link_de_tu_imagen_waterfall_aqui)

---

## Autor
Proyecto desarrollado por Luca Camus.
* [Enlace a mi LinkedIn](https://www.linkedin.com/in/luca-camus/)

