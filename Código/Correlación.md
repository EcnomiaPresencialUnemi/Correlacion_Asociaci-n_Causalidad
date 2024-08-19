### 1. Paquetes necesarios
#### - quantmod es un paquete en R diseñado para el modelado cuantitativo de datos financieros. Específicamente, facilita la obtención, modelado y visualización de datos de mercado.
#### - tidyverse ayudará a manipular y procesar los datos descargados con quantmod. Específicamente, tidyverse permite seleccionar columnas de interés, filtrar filas, crear nuevas variables, y transformar los datos para el análisis posterior.

```r
# Instalar los paquetes necesarios
install.packages("quantmod")
install.packages("tidyverse")

# Cargar las bibliotecas
library(quantmod)
library(tidyverse)
```

### 2.  Obtener datos históricos de los precios de las acciones
```r
# Obtener datos históricos de los precios de las acciones de Apple (AAPL)
getSymbols("AAPL", src = "yahoo", from = "2000-01-01", to = "2023-12-31")
```
