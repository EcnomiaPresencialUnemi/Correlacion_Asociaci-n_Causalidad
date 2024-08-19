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
#### *2.1 Explicación del código*
- `getSymbols("AAPL", ...)`: Este es el comando que descarga los datos de las acciones de Apple.
- `AAPL` es el símbolo de Apple en el mercado de valores.
- `src = "yahoo"`: Especifica que los datos se obtendrán desde Yahoo Finance.
- `from = "2000-01-01", to = "2023-12-31"`: Define el rango de fechas para los datos que quieres descargar. Aquí, los datos se descargarán desde el 1 de enero de 2000 hasta el 31 de diciembre de 2023.

### 3. Convertir los datos a un datafrmae
#### Es preferible convertir los datos de precios de Apple a un `data.frame` para facilitar la manipulación y exportación si fuese necesario.
```r
# Convertir los datos a un data frame
aapl_data <- data.frame(date = index(AAPL), coredata(AAPL))
```
