### 1. Asociación
#### Los resultados que obtenidos con la correlación de Pearson sugieren que se podría ampliar el análisis para medir la asociación entre los cambios en el precio ajustado, 'diff_log_adjusted' de las acciones de Apple y el volumen de transacciones, 'AAPL.Volume', utilizando un enfoque de asociación.
- **Correlación Baja**: La correlación baja (-0.0798) sugiere una relación lineal débil, indicando posibles factores adicionales o una relación no lineal.
- **Modelo de Asociación**: Un modelo de regresión puede revelar una asociación más compleja entre las variables que la simple correlación no captó.
- **Incorporación de Otros Factores**: Incluir variables adicionales como indicadores macroeconómicos podría fortalecer la relación entre el volumen y los cambios en el precio ajustado.

### 2. Ampliando el modelo
Variables que se incorporan:
- Índice S&P 500 ('GSPC.Adjusted'): Captura el comportamiento del mercado en general.
- Índice de Volatilidad ('VIX.Adjusted'): Mide la volatilidad esperada del mercado.
- Medias Móviles ('SMA_50', 'SMA_200'): Detectan tendencias en el precio de Apple.
- Precios ajustados de competidores como Microsoft ('MSFT.Adjusted').

# Precio de Cierre Ajustado de Otros Índices:
getSymbols("^GSPC", src = "yahoo", from = "2000-01-01", to = "2023-12-31")
sp500_data <- data.frame(date = index(GSPC), coredata(GSPC))

# Volatilidad del Mercado:

