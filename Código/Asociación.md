### 1. Asociación
#### Los resultados que obtenidos con la correlación de Pearson sugieren que se podría ampliar el análisis para medir la asociación entre los cambios en el precio ajustado, 'diff_log_adjusted' de las acciones de Apple y el volumen de transacciones, 'AAPL.Volume', utilizando un enfoque de asociación.
- **Correlación Baja**: La correlación baja (-0.0798) sugiere una relación lineal débil, indicando posibles factores adicionales o una relación no lineal.
- **Modelo de Asociación**: Un modelo de regresión puede revelar una asociación más compleja entre las variables que la simple correlación no captó.
- **Incorporación de Otros Factores**: Incluir variables adicionales como indicadores macroeconómicos podría fortalecer la relación entre el volumen y los cambios en el precio ajustado.

### 2. Ampliando el modelo: extrayendo más variables
Variables que se incorporan:
- Índice S&P 500 ('GSPC.Adjusted'): Captura el comportamiento del mercado en general.
- Índice de Volatilidad ('VIX.Adjusted'): Mide la volatilidad esperada del mercado.
- Medias Móviles ('SMA_50', 'SMA_200'): Detectan tendencias en el precio de Apple.
  - 'SMA_50': Captura la tendencia a corto plazo (50 días) del precio ajustado de las acciones de Apple.
  - 'SMA_200': Captura la tendencia a largo plazo (200 días) del precio ajustado de las acciones de Apple.
- Precios ajustados de competidores como Microsoft ('MSFT.Adjusted').

```r
# Precio de Cierre Ajustado de Otros Índices:
getSymbols("^GSPC", src = "yahoo", from = "2000-01-01", to = "2023-12-31")
sp500_data <- data.frame(date = index(GSPC), coredata(GSPC))

# Volatilidad del Mercado:
getSymbols("^VIX", src = "yahoo", from = "2000-01-01", to = "2023-12-31")
vix_data <- data.frame(date = index(VIX), coredata(VIX))

# Medias Móviles:
library(TTR)
aapl_selected$SMA_50 <- SMA(aapl_selected$AAPL.Adjusted, n = 50)
aapl_selected$SMA_200 <- SMA(aapl_selected$AAPL.Adjusted, n = 200)

# Precios de Cierre Ajustados de Competidores:
getSymbols("MSFT", src = "yahoo", from = "2000-01-01", to = "2023-12-31")
msft_data <- data.frame(date = index(MSFT), coredata(MSFT))
```
### 3. Ampliando el modelo incorporando más variables

```r
# Unir los datos de SP500 y VIX con los datos de AAPL basados en la fecha
aapl_selected <- merge(aapl_selected, sp500_data[, c("date", "GSPC.Adjusted")], by = "date", all.x = TRUE)
aapl_selected <- merge(aapl_selected, vix_data[, c("date", "VIX.Adjusted")], by = "date", all.x = TRUE)

aapl_selected$SMA_50 <- SMA(aapl_selected$AAPL.Adjusted, n = 50)
aapl_selected$SMA_200 <- SMA(aapl_selected$AAPL.Adjusted, n = 200)

# Unir la variable de Microsoft (precio ajustado) con el data frame de Apple
aapl_selected <- merge(aapl_selected, msft_data, by = "date", all.x = TRUE)
```

### 4. Verificamos la existencia de valores perdidos y evaluamos su posible eliminación e imputación

```r
# Calcular el porcentaje de NA en cada columna
na_percentage <- colSums(is.na(aapl_selected)) / nrow(aapl_selected) * 100

# Mostrar los porcentajes de NA para cada columna
print(na_percentage)

# Crear una nueva data frame eliminando las filas con NA en SMA_200
aapl_selected_clean <- aapl_selected[!is.na(aapl_selected$SMA_200), ]
```

### 5. Comprobar la Estacionariedad (Test de Dickey-Fuller)

```r
# Instalar si no los tienes
install.packages("tseries")
install.packages("urca")

# Cargar los paquetes
library(tseries)
library(urca)

# Aplicar el test de Dickey-Fuller a cada variable
adf_test_results <- list()

# Test para AAPL.Adjusted
adf_test_results$AAPL_Adj <- adf.test(aapl_selected_clean$AAPL.Adjusted, alternative = "stationary")

# Test para AAPL.Volume
adf_test_results$AAPL_Volume <- adf.test(aapl_selected_clean$AAPL.Volume, alternative = "stationary")

# Test para SMA_200
adf_test_results$SMA_200 <- adf.test(aapl_selected_clean$SMA_200, alternative = "stationary")

# Test para GSPC.Adjusted
adf_test_results$GSPC_Adj <- adf.test(aapl_selected_clean$GSPC.Adjusted, alternative = "stationary")

# Test para VIX.Adjusted
adf_test_results$VIX_Adj <- adf.test(aapl_selected_clean$VIX.Adjusted, alternative = "stationary")

# Test para MSFT.Adjusted
adf_test_results$MSFT_Adj <- adf.test(aapl_selected_clean$MSFT.Adjusted, alternative = "stationary")

# Mostrar los resultados
adf_test_results
```
