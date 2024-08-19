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
#### *5.1 Explicación de los resultados*
- 'AAPL.Adjusted': La serie no es estacionaria (p-value = 0.99) y presenta una raíz unitaria.
- 'AAPL.Volume': La serie es estacionaria (p-value = 0.01) y no presenta una raíz unitaria.
- 'SMA_200': La serie no es estacionaria (p-value = 0.99) y presenta una raíz unitaria.
- 'GSPC.Adjusted': La serie no es estacionaria (p-value = 0.7409) y presenta una raíz unitaria.
- 'VIX.Adjusted': La serie es estacionaria (p-value = 0.01) y no presenta una raíz unitaria.
- 'MSFT.Adjusted': La serie no es estacionaria (p-value = 0.99) y presenta una raíz unitaria.

### 6. Diferenciar las Variables No Estacionarias
```r
# Diferenciar las variables no estacionarias
aapl_selected_diff <- data.frame(
  diff_AAPL_Adj = diff(aapl_selected_clean$AAPL.Adjusted, differences = 1),
  diff_SMA_200 = diff(aapl_selected_clean$SMA_200, differences = 1),
  diff_GSPC_Adj = diff(aapl_selected_clean$GSPC.Adjusted, differences = 1),
  diff_MSFT_Adj = diff(aapl_selected_clean$MSFT.Adjusted, differences = 1)
)

# También recortamos las demás columnas para alinearlas con las series diferenciadas
aapl_selected_diff <- aapl_selected_diff %>%
  mutate(
    AAPL_Volume = aapl_selected_clean$AAPL.Volume[-1],
    VIX_Adj = aapl_selected_clean$VIX.Adjusted[-1]
  )
```
```r
# Aplicar el test de Dickey-Fuller a las variables diferenciadas
adf_diff_test_results <- list()

# Test para diff_AAPL_Adj
adf_diff_test_results$diff_AAPL_Adj <- adf.test(aapl_selected_diff$diff_AAPL_Adj, alternative = "stationary")

# Test para diff_SMA_200
adf_diff_test_results$diff_SMA_200 <- adf.test(aapl_selected_diff$diff_SMA_200, alternative = "stationary")

# Test para diff_GSPC_Adj
adf_diff_test_results$diff_GSPC_Adj <- adf.test(aapl_selected_diff$diff_GSPC_Adj, alternative = "stationary")

# Test para diff_MSFT_Adj
adf_diff_test_results$diff_MSFT_Adj <- adf.test(aapl_selected_diff$diff_MSFT_Adj, alternative = "stationary")

# Mostrar los resultados
adf_diff_test_results
```

### 7. Asociación: Modelo de Regresión

```r
# Preparar el data frame para el modelo de regresión
regression_data <- data.frame(
  diff_AAPL_Adj = aapl_selected_diff$diff_AAPL_Adj,
  AAPL_Volume = aapl_selected_diff$AAPL_Volume,
  diff_SMA_200 = aapl_selected_diff$diff_SMA_200,
  diff_GSPC_Adj = aapl_selected_diff$diff_GSPC_Adj,
  VIX_Adj = aapl_selected_diff$VIX_Adj,
  diff_MSFT_Adj = aapl_selected_diff$diff_MSFT_Adj
)
```
```r
# Construir el modelo de regresión lineal
regression_model <- lm(diff_AAPL_Adj ~ AAPL_Volume + diff_SMA_200 + diff_GSPC_Adj + VIX_Adj + diff_MSFT_Adj, data = regression_data)

# Resumen del modelo
summary(regression_model)
```
#### 7. *Explicación de los resultados*
- Intercepción (-0.05212): La intercepción es negativa, pero su valor es pequeño y tiene un p-valor significativo (0.0462), lo que indica que el modelo predice un pequeño ajuste constante negativo en la variable dependiente cuando todas las variables independientes son cero.
- AAPL.Volume:(3.465e-11): El coeficiente es positivo, lo que indica que un aumento en el volumen de transacciones de Apple está asociado con un pequeño aumento en diff_AAPL_Adj. Sin embargo, el p-valor es 0.2045, lo que sugiere que esta relación no es estadísticamente significativa.
- diff_SMA_200:(0.6619): El coeficiente es positivo y significativo (p-valor = 6.63e-06), lo que indica que un aumento en diff_SMA_200 está asociado con un aumento en diff_AAPL_Adj. Esta variable tiene una relación fuerte y significativa con la variable dependiente.
-diff_GSPC_Adj:(0.01651): Este coeficiente es altamente significativo (p-valor < 2e-16) y positivo, lo que indica que un aumento en diff_GSPC_Adj (S&P 500 ajustado) está asociado con un aumento en diff_AAPL_Adj. Esta es una de las variables más fuertes en el modelo.
-VIX_Adj:(0.001344): El coeficiente es positivo, pero el p-valor (0.2420) sugiere que la relación no es estadísticamente significativa. Esto indica que la volatilidad del mercado, tal como se mide por el VIX, no tiene un impacto significativo en la variación ajustada de Apple.
-diff_MSFT_Adj:(0.2364): Este coeficiente es altamente significativo (p-valor < 2e-16) y positivo, lo que indica que un aumento en diff_MSFT_Adj (Microsoft ajustado) está asociado con un aumento en diff_AAPL_Adj. Esta es otra de las variables más fuertes en el modelo.
