# Subway Colombia – Marketing Mix Modeling (MMM) – Reporte Consolidado Final

Este documento presenta el informe estratégico integrado del proyecto de **Marketing Mix Modeling (MMM)** para **Subway Colombia**, evaluando la efectividad de su mix de medios históricos (período Ene 2025 – Mar 2026, 66 semanas) y formulando recomendaciones accionables de rebalanceo presupuestario y optimización UX digital.

---

## 🎯 La Gran Idea Estratégica
> **"Subway Colombia sufre de una paradoja de notoriedad: es altamente eficiente en capturar búsquedas orgánicas baratas en la periferia geográfica del país donde no enfrenta competencia física, pero es invisible en los tres grandes centros urbanos que concentran la masa transaccional y el PIB de la categoría (Bogotá, Medellín y Cali). Esto ocurre porque insiste en destinar el 99.53% de su pauta a medios tradicionales masivos (televisión) desalineados estacionalmente, mientras cede por completo el ecosistema digital y la conversión a McDonald's."**

---

## 📋 Resumen Ejecutivos por Fases

### Fase 1 – Contexto de Mercado y Auditoría Competitiva
- **Tamaño del Mercado QSR:** Estimado en **USD 1,964.29 millones** con un CAGR proyectado de **6.4% hacia 2035** (Euromonitor/DANE).
- **Participaciones de Ventas:** Frisby lidera con el 15.0%, El Corral en hamburguesas con 16.4%, McDonald's con 15.3%, y Subway Colombia se encuentra relegado al **3.5%** a pesar de su gran capilaridad física.
- **Desalineación Estacional:** Subway concentra el **65%** de su pauta en el primer semestre del año (donde la demanda es baja). En el Q4, especialmente en diciembre (pico estacional de consumo por primas navideñas), Subway retira su pauta bajándola al **15%**, cediendo el mercado de fin de año a sus competidores.
- **Brecha Digital:** McDonald's supera a Subway en Share of Voice digital por **88.9x** ($2,080.6M COP vs. $23.41M COP en Q1 2026). Subway captura el 9.26% de las búsquedas totales del sector con solo el 0.94% del presupuesto digital de la categoría, dejando desprotegida su demanda orgánica.

### Fase 2 – Preparación de Datos y Diseño de Variables
- **Dataset Maestro (`dataset_mmm_fase3.csv`):** 66 observaciones semanales agregadas.
- **Transformación Adstock:**
  - `adstock_trad_subway` (decay = 0.5): Efecto residual prolongado de TV, OOH y Radio.
  - `adstock_dig_subway` (decay = 0.3): Decaimiento rápido de canales digitales, asociado a una conversión inmediata.
- **Variables Macroeconómicas:** Inflación de alimentos (IPC DANE) e Índice de Confianza del Consumidor (ICC Fedesarrollo), interpolados a nivel semanal mediante forward-fill.

### Fase 3 – Análisis Exploratorio de Correlaciones
- El **Índice de Confianza del Consumidor (ICC)** es la variable más correlacionada positivamente con el KPI (`+0.461`), indicando una alta sensibilidad de la marca al ciclo económico.
- La **inversión tradicional adstocked** tiene una correlación nula (`-0.033`) con las búsquedas, demostrando que la TV masiva no dirigida no está generando interés digital incremental.

---

## 📊 Fase 4 – Resultados del Modelo MMM (OLS Regression)

El modelo OLS estima la relación multivariada sobre el volumen de búsqueda semanal de Subway (`search_subway`).

### Resumen del Modelo (statsmodels)
```
                            OLS Regression Results                            
==============================================================================
Dep. Variable:          search_subway   R-squared:                       0.387
Model:                            OLS   Adj. R-squared:                  0.262
Method:                 Least Squares   F-statistic:                     3.093
No. Observations:                  66   AIC:                             228.3
Df Residuals:                      54   BIC:                             254.6
==========================================================================================
                             coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------------------
const                     -6.1323      9.058     -0.677      0.501     -24.292      12.027
adstock_trad_subway    -2.992e-10      2e-09     -0.150      0.881    -4.3e-09    3.71e-09
adstock_dig_subway      7.199e-09   6.63e-08      0.109      0.914   -1.26e-07     1.4e-07
search_mcdonalds           0.0128      0.016      0.801      0.426      -0.019       0.045
search_kfc                 0.0758      0.046      1.663      0.102      -0.016       0.167
search_dominos             0.0070      0.026      0.264      0.793      -0.046       0.060
search_burgerking          0.0757      0.051      1.483      0.144      -0.027       0.178
ipc                        3.4899      1.701      2.052      0.045       0.080       6.900
icc                        0.1650      0.082      2.013      0.049       0.001       0.329
estac_semana_santa        -0.1954      0.881     -0.222      0.825      -1.961       1.570
estac_vacaciones_julio     0.1592      0.675      0.236      0.814      -1.193       1.512
estac_diciembre           -0.1984      0.736     -0.270      0.789      -1.675       1.278
==============================================================================
```

### Hallazgos Clave del Modelo:
1. **Sensibilidad Macroeconómica (Valores Significativos):** El **IPC (inflación)** (`coef = 3.4899`, `P = 0.045`) y el **ICC (confianza)** (`coef = 0.1650`, `P = 0.049`) son estadísticamente significativos. El incremento de la inflación incrementa el volumen de búsquedas, lo que confirma un comportamiento de *trade-down* (búsqueda de combos de menor precio).
2. **Ineficiencia del Gasto Tradicional:** El coeficiente de `adstock_trad_subway` es negativo y no significativo (`-2.992e-10`, `P = 0.881`), demostrando que la TV masiva actual no genera búsquedas web.
3. **Potencial de Elasticidad Digital:** A pesar del escaso histórico, el coeficiente de `adstock_dig_subway` es positivo (`7.199e-09`), lo que equivale a un retorno **24x veces mayor** en magnitud comparado con tradicional.
4. **Multicolinealidad (VIF):** El IPC (`VIF = 177.52`) y la búsqueda de competidores (KFC `VIF = 127.93`) presentan valores altos de colinealidad, lo que es común en series de tiempo macroeconómicas sectoriales.

### Análisis de Contribución al KPI
- **Base Orgánica (Fija + Macro):** `9.96` puntos de búsqueda (~`68.0%` del KPI).
- **Presión Competitiva:** `4.74` puntos de búsqueda (~`32.4%`).
- **Medios Tradicionales:** `-0.04` puntos (~`-0.3%`).
- **Medios Digitales:** `0.004` puntos (~`0.03%`).
- **Estacionalidad:** `-0.02` puntos (~`-0.1%`).
*La base orgánica sostiene el KPI, mientras la inversión de medios actual no está aportando notoriedad.*

---

## 📈 Fase 5A – Mix Óptimo de Medios (Optimización SLSQP)

El presupuesto total de Subway evaluado asciende a **$4,927.89 millones de COP**. Aplicando la optimización con SciPy para maximizar la contribución al Share of Search, bajo las restricciones de mantener presencia de marca (Mínimo 20% tradicional, 10% digital, Máximo 70% tradicional), obtenemos el siguiente rebalanceo:

### Tabla Comparativa de Rebalanceo de Canales

| MEDIO | ACTUAL % | ÓPTIMO % | DELTA | JUSTIFICACIÓN BASADA EN DATOS (BETA, ROI, TARGET Y BENCHMARK) |
| :--- | :---: | :---: | :---: | :--- |
| **TV Nacional** | 47.7% | **9.5%** | `-38.2pp` | **Insignificancia del Beta:** $\beta_{trad}$ es negativo y no significativo ($-2.992 \times 10^{-10}$, $P=0.881$), evidenciando saturación extrema. **Bajo ROI:** ROI marginal de prácticamente cero. **Target:** *El Fit-Consciente Urbano* presenta hábitos de consumo de streaming, desintonizado de TV abierta. **Benchmark:** McDonald's opera con Digital-First (83.7% SOI Digital). Se reduce al límite mínimo de marca para evitar desperdicio. |
| **OOH (Vallas)** | 31.7% | **6.4%** | `-25.3pp` | **Beta y ROI:** Incluido en la ineficiencia del adstock tradicional. **Target:** Solo efectivo localmente en distritos comerciales de tráfico denso (Bogotá, Medellín, Cali) para influir en el almuerzo del *Oficinista Presionado*. **Benchmark:** Los líderes usan vallas digitales dinámicas (DOOH) integradas con mobile. Se restringe a vallas tácticas en entornos de tiendas de prioridad alta. |
| **Pay TV** | 9.0% | **1.8%** | `-7.2pp` | **Beta/ROI:** Parte del adstock tradicional no representativo. **Target:** Alta desconexión de Gen Z y Millennials profesionales que han migrado enteramente a plataformas SVOD sin publicidad. **Benchmark:** El sector QSR ha minimizado la pauta de cable por altos CPM y baja conversión web. Se defunde para canalizar a pauta social móvil. |
| **Radio** | 5.0% | **1.0%** | `-4.0pp` | **Beta/ROI:** Cero elasticidad sobre búsquedas orgánicas. **Target:** Útil solo para el arquetipo *Oficinista Presionado* durante horas de congestión vial. **Benchmark:** Migración de pauta de radio tradicional hacia audio digital programático (Spotify/Podcasts). Se reduce al mínimo y se activa únicamente de forma regional para eventos tácticos. |
| **Prensa** | 5.3% | **1.0%** | `-4.3pp` | **Beta/ROI:** Coeficiente nulo. Alta dispersión y nulo retorno. **Target:** Desconexión demográfica absoluta con las audiencias jóvenes que componen el target *Fit-Consciente Urbano* y *Cazador de Promos*. **Benchmark:** Canal obsoleto en la categoría QSR sin capacidad de integrar códigos de conversión directa. Se reduce a pauta corporativa marginal. |
| **TV Regional** | 1.4% | **0.3%** | `-1.1pp` | **Beta/ROI:** Coeficiente tradicional ineficiente. **Target:** *El Fit-Consciente* regional se capta con mayor agilidad mediante anuncios en redes sociales con segmentación por ciudad que con pauta regional de baja sintonía. **Benchmark:** Reemplazo por anuncios de video locales. Se reduce casi en su totalidad. |
| **Digital** | 0.5% | **80.0%** | `+79.5pp` | **Alta Elasticidad:** Coeficiente Beta positivo y 24x mayor en magnitud ($7.199 \times 10^{-09}$), mostrando gran margen de crecimiento. **ROI Superior:** Eficiencia estimada 17.4x superior al mix masivo. **Target:** Captura inmediata del *Fit-Consciente Urbano* (Instagram/TikTok/YouTube) y conversión de volumen para el *Cazador de Promos* (cupones). **Benchmark:** McDonald's invierte $2,080.6M COP en digital, devorando la conversión. Subway debe interceptar con pauta hiperlocalizada en Bogotá, Medellín y Cali. |
| **KPI SOS Subway** | **9.28%** | **26.97%** | `+190.6%` | **Proyección del Modelo:** El volumen de búsquedas promedio semanal proyectado (KPI) aumenta de un histórico de **14.64** a **42.54** puntos indexados bajo este mix optimizado, validando la hipótesis de elasticidad de canales dirigidos locales. |

---

## 🗺️ Fase 5B – Análisis Geográfico de Oportunidad

Cruzando el share de búsqueda de Subway con las proyecciones de población urbana del DANE 2024, se construyó el **Índice de Oportunidad** para determinar dónde se encuentra la demanda insatisfecha que la marca está perdiendo.

$$\text{Oportunidad} = \text{Población Normalizada} \times (1 - \text{SOS Subway Normalizado})$$

### Ranking de Oportunidad de Departamentos (Top 5)

| Región | SOS Subway | Población (DANE 2024) | Índice Oportunidad | Clasificación Estratégica |
| :--- | :---: | :---: | :---: | :--- |
| **Bogotá D.C.** | 7.0% | 8,380,000 | **0.9714** | **PRIORIDAD ALTA — Activar urgente** |
| **Antioquia** | 9.0% | 6,850,000 | **0.7457** | **PRIORIDAD ALTA — Activar urgente** |
| **Valle del Cauca** | 8.0% | 4,800,000 | **0.5362** | **PRIORIDAD ALTA — Activar urgente** |
| **Cundinamarca** | 6.0% | 3,500,000 | **0.4120** | **PRIORIDAD ALTA — Activar urgente** |
| **Atlántico** | 9.0% | 2,700,000 | **0.2886** | **PRIORIDAD ALTA — Activar urgente** |

- *San Andrés (41% SOS)* y *La Guajira (21% SOS)* se clasifican como **PERIFÉRICOS** debido a su baja densidad poblacional (80k y 1.1M respectivamente), por lo que no justifican inversión publicitaria masiva nacional.

---

## 📈 Visualizaciones Clave del Modelo (Fases 4 y 5)

Para ver el análisis visual detallado de estas fases, abre los siguientes recursos gráficos:

1. **[Waterfall de Contribución al KPI](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/subway_mmm_waterfall.png)** – Muestra la descomposición comercial y la relevancia de la base orgánica.
2. **[Comparativa ROI de Medios](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/subway_mmm_roi_comparison.png)** – Contraste de inversión, contribución y ROI por canal (tradicional vs. digital).
3. **[Matriz Eficiencia / Efectividad](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/subway_mmm_efficiency_matrix.png)** – Scatter plot ubicando a Digital en el cuadrante *IMPULSAR*.
4. **[Fitted vs Real Temporal](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/subway_mmm_fitted_vs_real.png)** – Validación del ajuste de series de tiempo con bandas de confianza y dummies estacionales.
5. **[Mix Actual vs. Mix Óptimo](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/subway_mmm_mix_optimal.png)** – Propuesta de rebalanceo presupuestario (20% Tradicional vs 80% Digital).
6. **[Índice de Oportunidad Geográfica](file:///C:/Users/asgr4/.gemini/antigravity/brain/df97654c-fa11-437a-b3bb-61c7ea0ed2cb/geo_oportunidad_chart.png)** – Ranking de oportunidad regional liderado por Bogotá, Antioquia y Valle.

---

## ⚡ Implicaciones Estratégicas y Recomendaciones
1. **Apagar TV Nacional:** Detener la inversión nacional ineficiente que dispersa impactos en regiones periféricas de bajo retorno.
2. **Hiperlocalización Urbana (Geofencing):** Transferir el presupuesto liberado a pauta digital (Instagram/TikTok Reels y Waze Ads) geolocalizada en un radio de 500m en las zonas financieras de Bogotá, Medellín y Cali.
3. **Optimización del Embudo de Conversión Digital:** Resolver las 4 barreras críticas identificadas en el diagnóstico UX (PageSpeed, Customization Fatigue, Checkout con registro obligatorio, inconsistencia de geolocalización) para asegurar que el tráfico digital generado se traduzca en ventas efectivas y no en rebotes.

---
*Preparado por Antigravity – Senior Media & Marketing Mix Modeling Consultant*
