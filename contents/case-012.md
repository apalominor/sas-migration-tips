# Case 012: Creating a Rownum in a Dataset
## Scenario
You found SAS code that creates a row number in a dataset. The rows are for "Europe Sports" cars. The information is about `code`, `make`, `model`, `type` and `origin`.

## SAS Environment
### Code
```sas
data cars (keep = code make model type origin);
    set datalib.cars_data;

    if origin = 'Europe' and type = 'Sports';
run;

data cars_rownum;
    set cars;
    rownum + 1;
run;
```
### Understanding the Code
We can see the creation of 2 datasets. The first one is filtering the data to be used. In this case, the `origin` = "Europe" and the `type` = "Sports". The second one is creating the new column `rownum` and adding 1 for each row.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/6353c0bc-8fc8-42da-96a2-6ed6aa92c2f5)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.
- `withColumn()`: adds a new column to a dataframe.
- `lit()`: sets literal or constant value to the new column. In this case, this function is used to create an artifice to pass to the window function.

We will need also to import the `Window` module to use the next functions and methods:
- `row_number()`: returns a sequential number starting at 1 within a window partition.
- `over()`: defines a windowing column.
- `orderBy()`: sets the order for a window specification based on a column. That's why in this case an artifice is required (simulation of a column with value "1").

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn
from pyspark.sql.window import Window

cars = df_cars_data.select(fn.col("code"),
                           fn.col("make"),
                           fn.col("model"),
                           fn.col("type"),
                           fn.col("origin"))\
                   .filter((fn.col("origin") == "Europe") & (fn.col("type") == "Sports"))

window_spec = Window.orderBy(fn.lit("1"))
cars_rownum = cars.withColumn("rownum", fn.row_number().over(window_spec))

cars_rownum.show(25, truncate = False)
```

### Results
```
+-----------+-------------+-----------------------------------+------+------+------+
|code       |make         |model                              |type  |origin|rownum|
+-----------+-------------+-----------------------------------+------+------+------+
|AUD354BE625|Audi         |RS 6 4dr                           |Sports|Europe|1     |
|AUDDFA0F828|Audi         |TT 1.8 convertible 2dr (coupe)     |Sports|Europe|2     |
|AUD603655C3|Audi         |TT 1.8 Quattro 2dr (convertible)   |Sports|Europe|3     |
|AUD98633396|Audi         |TT 3.2 coupe 2dr (convertible)     |Sports|Europe|4     |
|BMWE0E5643E|BMW          |M3 coupe 2dr                       |Sports|Europe|5     |
|BMWCCAB8998|BMW          |M3 convertible 2dr                 |Sports|Europe|6     |
|BMWEE4E9DE5|BMW          |Z4 convertible 2.5i 2dr            |Sports|Europe|7     |
|BMW363EF657|BMW          |Z4 convertible 3.0i 2dr            |Sports|Europe|8     |
|JAGD2749AED|Jaguar       |XK8 coupe 2dr                      |Sports|Europe|9     |
|JAG1AF71FA3|Jaguar       |XK8 convertible 2dr                |Sports|Europe|10    |
|JAGE179760E|Jaguar       |XKR coupe 2dr                      |Sports|Europe|11    |
|JAG28F2B038|Jaguar       |XKR convertible 2dr                |Sports|Europe|12    |
|MERB2256530|Mercedes-Benz|SL500 convertible 2dr              |Sports|Europe|13    |
|MER3E1CCF76|Mercedes-Benz|SL55 AMG 2dr                       |Sports|Europe|14    |
|MERD7D90B93|Mercedes-Benz|SL600 convertible 2dr              |Sports|Europe|15    |
|MER87A5A03D|Mercedes-Benz|SLK230 convertible 2dr             |Sports|Europe|16    |
|MER16FF9E4A|Mercedes-Benz|SLK32 AMG 2dr                      |Sports|Europe|17    |
|PORC2C27112|Porsche      |911 Carrera convertible 2dr (coupe)|Sports|Europe|18    |
|POR31514F20|Porsche      |911 Carrera 4S coupe 2dr (convert) |Sports|Europe|19    |
|POR07229E38|Porsche      |911 Targa coupe 2dr                |Sports|Europe|20    |
|POR3F3F7451|Porsche      |911 GT2 2dr                        |Sports|Europe|21    |
|POR418B21D0|Porsche      |Boxster convertible 2dr            |Sports|Europe|22    |
|POR01FFC027|Porsche      |Boxster S convertible 2dr          |Sports|Europe|23    |
+-----------+-------------+-----------------------------------+------+------+------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
