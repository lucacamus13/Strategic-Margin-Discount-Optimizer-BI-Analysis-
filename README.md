# Strategic-Margin-Discount-Optimizer-BI-Analysis-
📊 Optimización de Margen en Retail: Análisis Forense y Simulación What-If
🎯 Problema de Negocio
A pesar de tener un volumen de ventas superior a los $2.3 millones, la operación de retail presentaba "fugas" de beneficio significativas. El objetivo fue identificar las categorías que erosionan el margen y proponer una solución basada en datos.

🛠️ Stack Tecnológico
Google BigQuery: Procesamiento y limpieza de datos en la nube (SQL).

Power BI: Modelado de datos y visualización avanzada.

DAX: Implementación de lógica de simulación de escenarios (What-If Analysis).

🔍 Hallazgos Principales (Insights)
Identificación del "Profit Killer": La categoría Tables (Mesas) opera con una pérdida neta de -$17.7k, debido a que el 64% de sus ventas se realizan con descuentos agresivos.

Punto de Quiebre: Se identificó mediante un gráfico de dispersión que cualquier descuento superior al 20% destruye la rentabilidad del producto.

Impacto de la Optimización: Al implementar un límite de descuento del 18%, el modelo proyecta una recuperación de margen global de $175k, incrementando la utilidad neta en un 61%.

💻 Lógica Técnica Destacada (DAX)
Para la simulación de recuperación, se desarrolló la siguiente medida que recalcula la ganancia omitiendo descuentos excesivos:

Fragmento de código

Ganancia Simulada = 
VAR Limite = [Limite de Descuento Valor]
RETURN
SUMX(
    'tabla',
    IF('tabla'[Discount] > Limite, 
        ( ('tabla'[Sales]/(1-'tabla'[Discount])) * (1-Limite) ) - 'tabla'[Cost], 
        'tabla'[Profit]
    )
)
