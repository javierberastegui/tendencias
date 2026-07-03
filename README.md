# tendencias

Indicador compuesto de tendencia para TradingView (Pine Script v6).

## QRCE Fractal Swing V3 — `QRCE_Fractal_Swing_V3.pine`

Evolución intradía de la V2 con fractalidad **Diario / 1H / 15 minutos**
(el marco de entrada pasa de 5m a **15m**), gatillo por **EMA50** como
soporte/resistencia dinámica y filtro **RSI de sobrecompra/sobreventa**
(70/30, el clásico respetado en forex).

### Flujo de decisión (respetando los fractales)

1. **Diario** — dirección de fondo (contexto mayor).
2. **1H** — confirmación de que la tendencia operativa acompaña.
3. **15m** — sincronía final y gatillo fino. *Gráfico recomendado: 15m.*
4. **EMA50** — por encima solo LONGS (soporte dinámico); por debajo solo
   SHORTS (resistencia dinámica). El gatillo por defecto es el *pullback*
   a la EMA50 con reacción a favor de la tendencia.
5. **RSI extremo** — con RSI ≥ 70 no se buscan longs nuevos (no comprar
   techos); con RSI ≤ 30 no se buscan shorts nuevos (no vender suelos).

### Estados del panel

`BUSCAR LONG/SHORT` cuando todo alinea; `EN PAUSA` cuando la fractalidad
está sincronizada pero la EMA50 o el RSI piden esperar mejor precio;
`ESPERAR FRACTALIDAD` cuando D/1H/15m no están alineados. El panel muestra
el lado de la EMA50, el estado del RSI y el motivo exacto de bloqueo.

Sin repintado (cierres confirmados vía `request.security(..., serie[1])`),
alertas al cierre de vela y alertas JSON para webhook. La V2 (W/D/1H, swing)
se mantiene intacta en `QRCE_Fractal_Swing_V2.pine`.

## QRCE-Lite V1 — `QRCE_Lite_V1.pine`

Primera versión funcional, robusta y **compilable** del *Quantum Regime Confluence
Engine* (QRCE), basada en los dos documentos de diseño (ICT-R y QRCE). Implementa
el núcleo viable del diseño en Pine v6, dejando fuera lo que dependía de datos
premium o rompía la compilación (footprint/order flow → reservado a V2).

### Componentes (7 módulos → Score 0–100 bipolar)

| Módulo | Peso | Qué mide |
|---|---|---|
| 1 · Dirección | 20 | KAMA adaptativa (KER) + SuperTrend adaptativa |
| 2 · TQI | 15 | Calidad de tendencia: KER + volatilidad + estructura + persistencia |
| 3 · Volumen | 15 | Delta proxy / Vol. relativo / OBV / VWAP (fallback, sin footprint) |
| 4 · Gate volatilidad | 10 | Filtro anti-rango (KER + ratio ATR + ADX) |
| 5 · Estructura | 15 | Pivotes + BOS/CHoCH confirmados al cierre |
| 6 · RSI Boosted | 10 | Momentum con umbrales 34/66 y confirmación |
| 7 · HTF | 15 | Confirmación multi-timeframe **no repintable** |

### Estados
Alcista fuerte · Alcista débil · Neutral/Rango · Bajista débil · Bajista fuerte ·
**Agotamiento/Reversión probable** (por kurtosis leptocúrtica + tendencia madura).

### Características
- Señales confirmadas **al cierre de vela**, **sin repintado**.
- HTF no repintable: `request.security(..., serie[1], lookahead = barmerge.lookahead_on)`.
- Alertas con `alertcondition` (configurar *Once per bar close*).
- Tabla con Score, Estado, HTF, Gate de volatilidad y Agotamiento.
- Todo configurable por inputs.

### Uso rápido
1. Instala el `.pine` en el editor Pine de TradingView y añádelo al gráfico.
2. Configura TF de operación + HTF de confirmación (p.ej. 15m/1H + 4H/D1).
3. Opera solo con **Gate = ABIERTA**. Triángulos BUY/SELL = entradas confirmadas;
   "X" gris = ruptura sin respaldo (posible barrido de liquidez).

Las **limitaciones reales** y la **propuesta V2** (footprint real, SuperTrend
asimétrica con Character-Flip, motor SMC con polylines, score percentilizado,
backtester) están documentadas dentro del propio archivo `.pine`.
