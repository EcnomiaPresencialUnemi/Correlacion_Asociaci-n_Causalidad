### 1. Causalidad de Granger
La causalidad de Granger es una prueba estadística utilizada en el análisis de series temporales para determinar si una variable puede prever (o "causar" en el sentido de Granger) cambios en otra variable. 
A diferencia de la correlación, que mide la relación lineal entre dos variables en un mismo punto temporal, y la asociación, que evalúa cómo las variables están relacionadas en un modelo multivariado, la causalidad de Granger analiza si los valores pasados de una variable aportan información predictiva sobre otra. 
Aunque no implica causalidad en el sentido estricto, proporciona una visión más profunda sobre las posibles relaciones de causa y efecto en el tiempo.

### 2. Selecciona las Variables:
las variables seleccionadas son AAPL.Adjusted y AAPL.Volume

### 3. Comprobar la Estacionariedad de las Variables
Ya hemos verificado que AAPL.Adjusted no es estacionaria (se la transforma a logaritmo y se trabaja con la primera diferencia) y AAPL.Volume si es estacionaria.

### 4. Aplicar la Prueba de Causalidad de Granger

```r
# Instalar el paquete necesario si aún no lo tienes
install.packages("lmtest")
library(lmtest)

# Probar si diff_log_adjusted causa AAPL.Volume
granger_test_1 <- grangertest(AAPL.Volume ~ diff_log_adjusted, order = 2, data = aapl_selected)

# Probar si AAPL.Volume causa diff_log_adjusted
granger_test_2 <- grangertest(diff_log_adjusted ~ AAPL.Volume, order = 2, data = aapl_selected)

# Mostrar los resultados
print(granger_test_1)
print(granger_test_2)
```
#### *4.1 Interpretación de los resultados*
- Prueba 1: diff_log_adjusted causa AAPL.Volume:
p-valor = 0.3155: Este p-valor es mayor que 0.05, lo que indica que no podemos rechazar la hipótesis nula. Esto significa que no hay suficiente evidencia para afirmar que diff_log_adjusted tiene un poder predictivo sobre AAPL.Volume.

- Prueba 2: AAPL.Volume causa diff_log_adjusted:
p-valor = 0.5522: Este p-valor también es mayor que 0.05, lo que sugiere que no podemos rechazar la hipótesis nula. Esto implica que no hay suficiente evidencia para afirmar que AAPL.Volume tiene un poder predictivo sobre diff_log_adjusted.
