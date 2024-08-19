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
#### *3.1 Explicación del código*
- `aapl_data <-`: asigna el resultado de la operación a un nuevo objeto que contendrá las fechas y los datos principales de la serie temporal de AAPL.
- `data.frame(`: crea un nuevo data.frame en R.
- `date = index(AAPL),`: extrae las fechas de la serie temporal AAPL y las almacena en una columna llamada date dentro del data.frame.
- `coredata(AAPL)`: obtiene la parte principal de los datos (es decir, los valores numéricos) y los integra en el data.frame.


### 4. Seleccionar las variables de interés para la correlación
#### Las variables de interés seleccionadas para la correlación son AAPL.Adjusted y AAPL.Volume:
- `AAPL.Adjusted` (Precio de Cierre Ajustado): refleja el valor de las acciones de Apple después de ajustar por eventos corporativos como dividendos, splits, etc.
- `AAPL.Volume` (Volumen de Transacciones): muestra cuántas acciones se compran y venden en un día determinado.
- Seleccionamos el precio de cierre ajustado (`AAPL.Adjusted`) y el volumen de transacciones (`AAPL.Volume`) para investigar la relación entre los cambios en el precio de las acciones y la actividad de mercado.

```r
# Seleccionar las columnas de interés: precio de cierre ajustado y volumen
aapl_selected <- aapl_data %>% select(date, AAPL.Adjusted, AAPL.Volume)
```

#### *4.1 Explicación del código*
- `aapl_selected <-`: almacena el resultado de la selección en un nuevo objeto para su uso posterior.
- `aapl_data %>%`: toma el data.frame original aapl_data y lo pasa a la siguiente función (select()), que operará sobre él.
- `select(date, AAPL.Adjusted, AAPL.Volume)`: selecciona únicamente las columnas date, AAPL.Adjusted, y AAPL.Volume del data.frame original.
- `date`: Incluye la columna de fechas para mantener la referencia temporal.
- `AAPL.Adjusted`: Selecciona el precio de cierre ajustado de Apple, que es esencial para analizar el valor de las acciones ajustado por eventos corporativos.
- `AAPL.Volume`: Selecciona el volumen de transacciones, que es clave para evaluar la actividad del mercado.

### 5. Verificar la estacionariedad
#### Test de Dickey-Fuller Aumentado (ADF).- El ADF test se utiliza para determinar si una serie temporal es estacionaria, lo que significa que sus propiedades estadísticas, como la media y la varianza, son constantes a lo largo del tiempo.
Importancia:
