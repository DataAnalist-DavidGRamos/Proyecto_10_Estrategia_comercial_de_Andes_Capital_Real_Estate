# 📊 Proyecto Analítico: Desempeño e Inteligencia Comercial Inmobiliaria — Grupo Andes

Este repositorio alberga la infraestructura analítica completa para el análisis comercial del **Grupo Andes** (Andes Capital Real Estate). El objetivo principal es responder de forma ágil a preguntas estratégicas de negocio asociadas a ingresos brutos, efectividad de canales, categorización de clientes e inteligencia de tiempo basada en cohortes transaccionales de recompra.

---

## 📂 Estructura del Proyecto

El repositorio está organizado bajo las mejores prácticas de gobernanza de Inteligencia de Negocios:

```text
├── data/
│   ├── hecho_ventas_propiedades.csv
│   ├── dim_clientes.csv
│   └── dim_propiedades.csv
├── pbix/
│   └── Proyecto_10_Estrategia_comercial_de_Andes_Capital_Real_Estate.pbix
├── notebooks/
│   └── S11_Estudiante_Proyecto_InmobiliarioGrupoAndes.ipynb
├── docs/
│   └── images/
│       ├── CH1.png
│       ├── CH 2.png
│       ├── FinalH1_A.png
│       ├── FinalH1_B.png
│       ├── FinalH1_C.png
│       ├── FinalH2_A.png
│       ├── FinalH2_B.png
│       └── FinalH2_C.png
└── src/
    └── dax/
        ├── calculated_objects/
        │   ├── dim_fecha_table.dax
        │   └── hecho_ventas_columns.dax
        ├── ingresos_base_measures.dax
        └── contexto_filtro_measures.dax
```

---

## 📌 1. Arquitectura del Modelo de Datos (Esquema en Estrella)

El ecosistema de datos de Power BI se modela bajo relaciones unidireccionales directas (1:*), con filtros simples activos:

* **`hecho_ventas_propiedades` (Central):** Matriz transaccional de cierres comerciales. Contiene métricas de precio, comisión y referencias cruzadas.
* **`dim_clientes`:** Segmentación cualitativa de compradores (Inversionistas, Alto Patrimonio, Primera Vez).
* **`dim_propiedades`:** Desglose físico, tipología e indexación geográfica de bienes raíces.
* **`dim_fecha`:** Dimensión de tiempo autocontenida y dinámica.

### Relaciones del Modelo
* `dim_clientes[id_cliente]` 1 ─── * `hecho_ventas_propiedades[id_cliente]`
* `dim_propiedades[id_propiedad]` 1 ─── * `hecho_ventas_propiedades[id_propiedad]`
* `dim_fecha[Date]` 1 ─── * `hecho_ventas_propiedades[fecha_venta]`

---

## 🛠️ 2. Gobernanza del Código DAX

La lógica analítica prescinde de transformaciones complejas en Power Query para priorizar el rendimiento, centralizando el cálculo en expresiones estructuradas DAX:

### 2.1 Métricas Base (ver [ingresos_base_measures.dax](file:///d:/Anty_Gravity_Proyectos/SNIPER_CORE/Proyectos_GitHub/Proyecto_10_Estrategia_comercial_de_Andes_Capital_Real_Estate/src/dax/ingresos_base_measures.dax))
* **Ingreso Total:** Sumatoria explícita del precio de venta (`SUM('hecho_ventas_propiedades'[precio_venta])`).
* **Cantidad de Ventas:** Total de transacciones cerradas.
* **Ticket Promedio:** Relación de ingresos sobre volumen de ventas.
* **Comisión Total:** Ingresos generados para la empresa basados en la comisión pactada.

### 2.2 Modificación de Contexto de Filtro (ver [contexto_filtro_measures.dax](file:///d:/Anty_Gravity_Proyectos/SNIPER_CORE/Proyectos_GitHub/Proyecto_10_Estrategia_comercial_de_Andes_Capital_Real_Estate/src/dax/contexto_filtro_measures.dax))
* **% Participación por Tipo de Propiedad, Canal de Venta y Segmento:** Cálculos dinámicos que recalculan porcentajes sobre el total nacional removiendo filtros cruzados específicos utilizando `CALCULATE` y `REMOVEFILTERS`.
* **% de Retención (Cohortes):** Compara el ingreso de transacciones recurrentes contra el mes de adquisición (Mes 0) para evaluar la retención neta del cliente.

### 2.3 Objetos Calculados (ver [calculated_objects/](file:///d:/Anty_Gravity_Proyectos/SNIPER_CORE/Proyectos_GitHub/Proyecto_10_Estrategia_comercial_de_Andes_Capital_Real_Estate/src/dax/calculated_objects/))
* **`dim_fecha`:** Tabla de tiempo construida dinámicamente según la ventana operativa del negocio.
* **Columnas de Cohorte:** Determinan la primera fecha de compra del usuario (`Primera compra por cliente`), el inicio de mes de la adquisición (`Mes Cohorte`), y la distancia en meses respecto al evento transaccional actual (`Meses Desde Primera Compra`).

---

## 📈 3. Reportes y Dashboards

### 3.1 Overview Ejecutivo (Página 1)
Presenta un balance de alto nivel con los KPIs financieros y geográficos.
![Overview Dashboard A](docs/images/FinalH1_A.png)
![Overview Dashboard B](docs/images/FinalH1_B.png)

### 3.2 Análisis Comercial (Página 2)
Análisis pormenorizado del rendimiento por tipo de propiedad, segmentos de clientes y formatos de comercialización.
![Comercial Dashboard A](docs/images/FinalH2_A.png)
![Comercial Dashboard B](docs/images/FinalH2_B.png)

### 3.3 Matriz de Cohortes (Página 3)
Evaluación del ciclo de vida del cliente y patrones transaccionales repetitivos en tiempo relativo.
![Cohortes Dashboard 1](docs/images/CH1.png)
![Cohortes Dashboard 2](docs/images/CH%202.png)

---

## 📝 4. Resumen Ejecutivo & Insights de Negocio

A partir de los tableros analíticos diseñados, se extraen las siguientes métricas y comportamientos comerciales:

### 🎯 Métricas Principales (Consolidado Histórico)
* **Ingreso Total del Negocio:** **$6,012,502,170** (seis mil doce millones de pesos).
* **Volumen Operativo:** **8,500 propiedades** comercializadas.
* **Ticket Promedio Global:** **$707,353.20** por transacción.
* **Margen Neto (Comisión Total):** **$200,627,166** acumulados.

### 📊 Desglose de Rendimiento por Tipo de Propiedad
1. **Casa:** Genera **$2,240,535,304** (37.26% de participación) con **2,324 unidades vendidas** y un ticket promedio de **$964,085.76**. Representa el motor financiero principal.
2. **Comercial:** Genera **$1,924,691,634** (32.01% de participación) con solo **1,071 unidades vendidas** pero registrando el mayor ticket promedio unitario: **$1,797,097.70**.
3. **Departamento:** Genera **$1,847,275,232** (30.72% de participación) liderando el volumen físico con **5,105 unidades vendidas**, pero con el ticket promedio más bajo: **$361,856.07**.

### 💡 Hallazgos Comerciales y de Clientes
* **Perfil de Adquisición:** El segmento de clientes **"Primera vez"** es el pilar de ingresos del negocio con **$3.78 mil millones (62.93%)**, superando ampliamente a los **Inversionistas ($1.47 mil millones)** y a los clientes de **Alto Patrimonio ($0.76 mil millones)**.
* **Canales de Venta:** Alta dependencia de intermediarios; el canal **Corredor** aporta el **72.85% ($4,379.99 millones)** frente al canal **Directo ($1,632.51 millones)**.
* **Distribución Territorial:** La **Ciudad de México** concentra el **53.92% ($3,242.23 millones)** de la facturación, mientras que **Bogotá** representa el restante **$2,770.27 millones**.

---

## 🔍 5. Áreas de Oportunidad y Sugerencias de Mejora

Como resultado de una auditoría técnica y analítica experta sobre el dashboard y la data, se identifican las siguientes oportunidades críticas de mejora:

### 🛠️ 5.1 Oportunidades Técnicas (Visuales y de Modelo)
* **Ajuste de Visualización en Tarjetas de KPI (Truncamiento):** La tarjeta de "Ingreso Total" en el Overview Ejecutivo presenta truncamiento visual en el valor (`$6,0...5 mill.`). Se sugiere aumentar el ancho del contenedor visual, disminuir el tamaño de la fuente a 35pt, o modificar el formato de visualización a millones (`$6,012.5 M`) para garantizar una lectura limpia y profesional.
* **Control de Contexto en Inteligencia de Tiempo (Valores en Blanco):** Al no seleccionar un año específico, las métricas de `Crecimiento YoY` y `Ventas YTD` muestran valores vacíos (`--` / `(En blanco)`). Es recomendable refactorizar las fórmulas DAX para que, en caso de no haber filtro activo, muestren dinámicamente el rendimiento del último año cerrado (ej. 2024) utilizando funciones como `ISFILTERED` o `SELECTEDVALUE`.
* **Gobernanza Monetaria y Tipo de Cambio:** El dashboard consolida y compara directamente facturaciones de **Ciudad de México** (MXN) y **Bogotá** (COP) bajo una misma escala numérica sin una tasa de conversión explícita. Esto distorsiona la comparación real de valor financiero. Se debe integrar una tabla de tipos de cambio históricos en el modelo estrella para normalizar la moneda (ej. a USD) o implementar un selector dinámico de divisa.

### 💼 5.2 Oportunidades de Estrategia de Negocio
* **Eficiencia Operativa en Departamentos:** El segmento de Departamentos consume el **60% del volumen operativo (5,105 transacciones)** pero aporta únicamente el **30% de los ingresos** debido a su bajo ticket promedio. Se sugiere optimizar los procesos de originación y cierre digital para este segmento para reducir costos operativos transaccionales.
* **Desarrollo de Canal Directo:** El negocio paga altas comisiones externas dado que el 72.85% de las ventas ocurren vía Corredores. Incentivar la venta directa mediante marketing digital propio y plataformas exclusivas de Andes Capital aumentará la rentabilidad neta (reteniendo mayor porcentaje de comisión).
* **Fidelización y Recompra de Clientes:** Dado que los clientes de "Primera Vez" representan el 62.93% de las ventas y las cohortes recientes muestran una retención menor, se requiere implementar un plan de CRM dirigido a la conversión de compradores primerizos en Inversionistas recurrentes mediante ofertas de portafolios comerciales (que poseen mayor ticket).

---

## ⚙️ 6. Inicialización de Despliegue en Consola de Comandos (Git)

Una vez estructurado el proyecto, inicialice y publique los cambios en Git:

```bash
git init
git add .
git commit -m "feat: expanded analytical insights, UX opportunities, and business improvements"
git branch -M main
git push -u origin main
```
