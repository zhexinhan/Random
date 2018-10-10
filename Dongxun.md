# First Solution
Go to https://cran.r-project.org/bin/windows/base/ to download R.
Then open R and run the following 3 lines.

```
data <- read.csv(file="~/Downloads/data.csv", header=TRUE)
fit <- lm(Vds ~ SF6 + Ar + O2 + RF + Pressure, data=data)
fit
```

Example run:
```
> data <- read.csv(file="~/Downloads/data.csv", header=TRUE)
> fit <- lm(Vds ~ SF6 + Ar + O2 + RF + Pressure, data=data)
> fit

Call:
lm(formula = Vds ~ SF6 + Ar + O2 + RF + Pressure, data = data)

Coefficients:
(Intercept)          SF6           Ar           O2           RF     Pressure
  316.06501      0.04836      0.28276      0.27668      1.65943     -3.61056
```

From the above output, the predicted function is:
`Vds = 316.06501 + 0.04836 * SF6 + 0.28276 * Ar + 0.27668 * O2 + 1.65943 * RF - 3.61056 * Pressure`

# Second Solution (may only work on Linux or Max OS X)
Install Python and pyspark, then use the following block of code

```
from pyspark import SparkContext
from pyspark.ml import Pipeline
from pyspark.ml.feature import RFormula
from pyspark.ml.regression import LinearRegression
from pyspark.sql import SQLContext

sc = SparkContext('local[*]')
sqlContext = SQLContext(sc)

df = sqlContext.read.format('com.databricks.spark.csv').options(header='true', inferschema='true').load('data.csv')

formula = RFormula(
    formula="Vds ~ SF6 + Ar + O2 + RF + Pressure",
    featuresCol="features",
    labelCol="label")

model = Pipeline(stages=[formula, LinearRegression()]).fit(df)

print("Coefficients are: " + str(model.stages[-1].coefficients))
print("Intercept is: " + str(model.stages[-1].intercept))
```

Example output:
```
Coefficients are: [0.0483569430964,0.282760166909,0.276679173932,1.65942987267,-3.6105573702]
Intercept is: 316.0650106988338
```
