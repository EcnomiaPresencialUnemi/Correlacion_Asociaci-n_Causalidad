### 1. Causalidad de Granger
La causalidad de Granger es una prueba estadística utilizada en el análisis de series temporales para determinar si una variable puede prever (o "causar" en el sentido de Granger) cambios en otra variable. 
A diferencia de la correlación, que mide la relación lineal entre dos variables en un mismo punto temporal, y la asociación, que evalúa cómo las variables están relacionadas en un modelo multivariado, la causalidad de Granger analiza si los valores pasados de una variable aportan información predictiva sobre otra. 
Aunque no implica causalidad en el sentido estricto, proporciona una visión más profunda sobre las posibles relaciones de causa y efecto en el tiempo.

### 2. Selecciona las Variables:
las variables seleccionadas son AAPL.Adjusted y AAPL.Volume

### 3. Comprobar la Estacionariedad de las Variables
Ya hemos verificado que AAPL.Adjusted no es estacionaria (se la transforma a logaritmo y se trabaja con la primera diferencia) y AAPL.Volume si es estacionaria.

### 4. Aplicar la Prueba de Causalidad de Granger

