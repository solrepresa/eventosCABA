# Eventos extremos de calidad de aire en Buenos Aires (CABA)

Identificación de condiciones meteorológicas asociadas a eventos extremos de contaminación atmosférica en la Ciudad Autónoma de Buenos Aires, Argentina.

## Contexto

La ciudad de Buenos Aires cuenta con muy pocas estaciones de monitoreo continuo de calidad de aire, operadas por la Agencia de Protección Ambiental (APrA). Este proyecto analiza los registros horarios de tres estaciones (Centenario, Córdoba y La Boca) para detectar episodios extremos de PM₁₀ y explorar su relación con variables meteorológicas del Servicio Meteorológico Nacional (SMN).

Este trabajo fue desarrollado en el marco de un doctorado en ciencias ambientales.

## Datos

### Calidad de aire (`data/raw/CABA_datos.csv`)

| Campo | Descripción | Unidad |
|---|---|---|
| `date` | Fecha-hora (UTC) | `YYYY-MM-DD HH:MM:SS` |
| `CO_*` | Monóxido de carbono | ppm |
| `NO2_*` | Dióxido de nitrógeno | ppb |
| `PM10_*` | Material particulado ≤10 µm | µg/m³ |

Donde `*` es `CENTENARIO`, `CORDOBA` o `LA_BOCA`.

- **Período:** 2009-10-01 14:00 a 2017-10-01 00:00 (70 115 horas)
- **Registros cargados:** 67 070 (hay huecos en la serie)
- **Datos faltantes:** entre 20% y 42% según contaminante y estación

### Meteorología (`data/raw/buenosaires.csv`)

Datos horarios de la estación Buenos Aires (ID 10156) del SMN.

| Campo | Descripción | Unidad |
|---|---|---|
| `FECHA` | Fecha | `DD/MM/YYYY` |
| `HORA UTC` | Hora | entero 0–23 |
| `TEMPERATURA (ºC)` | Temperatura | °C |
| `DIRECCIÓN` | Dirección del viento | decenas de grado (×10) |
| `INTENSIDAD (km/h)` | Velocidad del viento | km/h |
| `HUMEDAD RELATIVA (%)` | Humedad relativa | % |
| `NUBOSIDAD TOTAL (octavos)` | Cobertura nubosa | octavos |

> **Nota:** El separador es `;` y el decimal es `,`.

## Estructura del repositorio

```
eventosCABA/
├── data/
│   ├── raw/                 # Datos originales (no modificar)
│   │   ├── CABA_datos.csv
│   │   └── buenosaires.csv
│   └── processed/           # Salidas intermedias y finales
├── R/
│   └── funciones.R          # Funciones auxiliares (si se separan del Rmd)
├── eventos_CABA.Rmd         # Análisis principal (R Markdown)
├── output/                  # Figuras y tablas exportadas
├── .gitignore
├── LICENSE
└── README.md
```

## Metodología

1. **Carga y completado de la serie temporal:** se genera la grilla horaria completa y se hace merge con los datos observados para evidenciar huecos.
2. **Análisis exploratorio:** estadísticos descriptivos y `summaryPlot` (paquete `openair`) por contaminante y estación.
3. **Agregación mensual de PM₁₀:** se calculan media, máximo, mínimo y desvío estándar mensual por estación.
4. **Exclusión del año 2011:** evento volcánico (erupción del Puyehue) que distorsiona la serie.
5. **Detección de eventos extremos:** se filtran los meses donde el máximo diario de PM₁₀ supera el percentil 90 de cada estación.
6. **Cruce con meteorología (pendiente):** merge de los eventos detectados con datos del SMN para caracterizar las condiciones atmosféricas asociadas.

## Requisitos

- R ≥ 4.0
- Paquetes: `openair`, `dplyr`, `reshape2`, `ggplot2`, `lubridate`

Instalación rápida:

```r
install.packages(c("openair", "dplyr", "reshape2", "ggplot2", "lubridate"))
```

## Uso

```r
# Desde la raíz del repositorio
rmarkdown::render("eventos_CABA.Rmd")
```

Esto genera `eventos_CABA.html` con el análisis completo.

## Cómo reutilizar este análisis con otros datos

1. Reemplazá `data/raw/CABA_datos.csv` con tu propio archivo de calidad de aire. Mantené la estructura: primera columna `date` en formato `YYYY-MM-DD HH:MM:SS`, y las siguientes columnas nombradas como `{CONTAMINANTE}_{ESTACION}`.
2. Reemplazá `data/raw/buenosaires.csv` con datos meteorológicos de tu zona. Respetá las columnas o ajustá el bloque de carga en el Rmd.
3. Ajustá los parámetros en el Rmd: el percentil de corte (actualmente P90), el año a excluir (2011 por el Puyehue), y los nombres de estaciones.

## Trabajo pendiente

- [ ] Completar el merge de eventos extremos con datos meteorológicos del SMN
- [ ] Agregar análisis de CO y NO₂ (actualmente solo PM₁₀ llega a la etapa de eventos)
- [ ] Evaluar umbrales normativos (OMS, normativa local) además del percentil estadístico
- [ ] Agregar rosa de vientos (`openair::windRose`) para los eventos detectados

## Licencia

GPL-3.0 — ver [LICENSE](LICENSE).

## Autoría

**Sol Represa** — Trabajo desarrollado como parte de su investigación doctoral.
