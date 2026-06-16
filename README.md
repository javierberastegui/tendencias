# tendencias

Indicador compuesto de tendencia para TradingView (Pine Script v6).

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
