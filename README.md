# LTV/CAC Explorer

Herramienta interactiva de análisis de LTV:CAC por canal y campaña. Desarrollada por [MrPink](https://mrpink.vc) con fines didácticos para acompañar el artículo sobre análisis de clusters y niveles de agregación en métricas de adquisición.

🔗 [ltv.mrpink.vc](https://ltv.mrpink.vc)

---

# Dataset: LTV/CAC por canal y campaña

Dataset sintético generado para el artículo sobre análisis de clusters y niveles de agregación en métricas de adquisición. Los datos cubren enero 2023 – abril 2026.

---

## Archivos

### `ad_spend.csv`
Gasto publicitario a nivel de transacción.

| Campo | Tipo | Descripción |
|---|---|---|
| `fecha` | date | Fecha de la transacción de gasto |
| `medio` | string | Canal: `meta`, `google`, `instagram_influencer` |
| `gasto` | float | Monto en USD |
| `id_campaña` | string | Identificador de campaña |

### `mrr.csv`
MRR mensual por cliente desde su adquisición hasta que churna o hasta el fin del período.

| Campo | Tipo | Descripción |
|---|---|---|
| `id_campaña_adquisicion` | string / null | Campaña que adquirió al cliente. **Null = orgánico** |
| `id_cliente` | string | Identificador único de cliente (C0001, C0002, ...) |
| `fecha` | date | Primer día del mes correspondiente |
| `importe` | float | MRR del cliente ese mes |

---

## Campañas y escenarios embebidos

### XMAS23 / XMAS24 — *CAC bajo, LTV malo*
Campañas navideñas (diciembre 2023 y 2024).
- CAC: ~$89 — entre los más bajos del portfolio
- LTV promedio: ~$257 — el peor de todos los canales
- Comportamiento: los clientes pagan 1–2 meses y churnan masivamente. El churn arranca en el mes 3.
- Trampa: la métrica de adquisición del mes parece excelente. El daño aparece 90 días después.

### GOOGLE_BRAND — *CAC alto, mejor relación LTV:CAC*
Búsqueda de marca en Google, activa todo el período.
- CAC: ~$272 — el más alto entre canales recurrentes
- LTV promedio: ~$1,446
- LTV:CAC ratio: **5.3x** — el mejor real del portfolio
- Comportamiento: clientes con alta intención de compra, churn mensual ~4%. Muchos hacen upsell.
- Lección: optimizar por CAC eliminaría el mejor canal.

### META_RETARGET — *el CAC "mágico"*
Retargeting sobre audiencias calientes (ya tuvieron contacto previo con el producto).
- CAC: ~$76 — el más bajo del portfolio
- LTV:CAC ratio: 8.3x (parece el mejor canal)
- Pero: estos clientes ya estaban convencidos. El crédito de conversión lo toma retargeting, el trabajo lo hicieron otros canales.
- Comportamiento de LTV: similar al orgánico ($628 vs $663) → no hay magia, hay atribución incorrecta.
- Trampa: si escalás este canal, la audiencia caliente se agota y el CAC explota.

### META_PROSP — *el outlier enterprise que infla el promedio*
Prospección en Meta, activa desde enero 2024.
- CAC promedio: ~$199
- LTV promedio: $583 → ratio 2.9x (mediocre)
- **Outlier**: en el cohort de abril 2024 entró un cliente enterprise con MRR ~$2,100.
  - LTV promedio del cohort ABR-2024 **con** outlier: $1,191
  - LTV promedio del cohort ABR-2024 **sin** outlier: $720
  - LTV **mediana** del cohort: $598
- Lección: el promedio miente. Un solo cliente enterprise puede hacer parecer buena una campaña mediocre.

### INFLUENCER_Q324 — *el cohort joven mal juzgado*
Campaña con influencers de Instagram, julio–agosto 2024.
- CAC: ~$347 — el más alto del portfolio
- LTV realizado a la fecha: $927 → ratio 2.7x (parece malo)
- Pero el cohort tiene solo 9–21 meses de historia. El churn mensual es 7.2% (el segundo más bajo).
- LTV proyectado a 36 meses: ~$1,800+ → ratio potencial >5x
- Trampa: comparar este cohort con GOOGLE_BRAND (24+ meses de historia) es comparar peras con manzanas.

### ORGÁNICO (id_campaña_adquisicion = null)
- Clientes: 1,883 únicos
- LTV promedio: $663
- CAC aparente: $0
- Costos reales no capturados en ad_spend:
  - Producción de blog / contenido
  - PR y relaciones públicas
  - Tiempo del equipo en comunidad y SEO
- Además: el volumen orgánico está correlacionado con el paid spend mensual (viento de cola). Parte de los clientes "orgánicos" vieron un ad pero convirtieron días después por canal directo → el tracking los marca como orgánico.

---

## Tabla resumen

| Campaña | Spend total | Clientes | CAC | LTV promedio | LTV:CAC |
|---|---|---|---|---|---|
| META_RETARGET | $61,517 | 805 | $76 | $628 | 8.3x ⚠️ |
| XMAS23 | $9,800 | 110 | $89 | $261 | 2.9x ❌ |
| XMAS24 | $11,400 | 128 | $89 | $254 | 2.9x ❌ |
| META_PROSP | $152,949 | 768 | $199 | $583 | 2.9x ⚠️ |
| GOOGLE_BRAND | $144,868 | 532 | $272 | $1,446 | 5.3x ✅ |
| INFLUENCER_Q324 | $21,500 | 62 | $347 | $927 | 2.7x ❓ |
| ORGÁNICO | — | 1,883 | $0 ⚠️ | $663 | ∞ |

---

## Qué no captura este dataset (intencionalmente)

- Los costos de PR y producción de contenido que "financian" el orgánico
- La atribución multitoque (qué campañas interactuaron antes de cada conversión orgánica)
- El tamaño de cliente (SMB vs enterprise) dentro de cada cohort
- El canal de soporte o éxito del cliente, que también afecta el LTV

Esas omisiones son parte del argumento del artículo.
