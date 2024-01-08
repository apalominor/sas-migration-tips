# Case 013: Creating a Rownum in a Dataset (by GROUPS)
## Scenario
You found SAS code that creates a row number by groups in a dataset. The rows are for "Europe Sports" cars and they are grouped by `make`. The information is about `code`, `make`, `model`, `type` and `origin`.

## SAS Environment
### Code
```sas
data cars (keep = code make model type origin);
    set datalib.cars_data;

    if origin = 'Europe' and type = 'Sports';
run;

data cars_rownum_group;
    set cars;
    by make;
    rownum + 1;

    if first.make then
        rownum = 1;
run;
```
### Understanding the Code
We can see the creation of 2 datasets. The first one is filtering the data to be used. In this case, the `origin` = "Europe" and the `type` = "Sports". The second one is creating the new column `rownum` and adding 1 for each row within a `make` group. The `first.` variable helps to identify the row for the first observation in a `by` group.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/eb145ff5-cfa0-4a7b-b195-ada3d32b3f44)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.
- `withColumn()`: adds a new column to a dataframe.

We will need also to import the `Window` module to use the next functions and methods:
- `partitionBy()`: creates a window specification with the partitioning defined.
- `row_number()`: returns a sequential number starting at 1 within a window partition.
- `over()`: defines a windowing column.
- `orderBy()`: sets the order for a window specification based on a column.

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

window_spec = Window.partitionBy("make").orderBy("model")
cars_rownum = cars.withColumn("rownum", fn.row_number().over(window_spec))

cars_rownum.show(25, truncate = False)
```

### Results
```
+-----------+-------------+-----------------------------------+------+------+------+
|code       |make         |model                              |type  |origin|rownum|
+-----------+-------------+-----------------------------------+------+------+------+
|AUD354BE625|Audi         |RS 6 4dr                           |Sports|Europe|1     |
|AUD603655C3|Audi         |TT 1.8 Quattro 2dr (convertible)   |Sports|Europe|2     |
|AUDDFA0F828|Audi         |TT 1.8 convertible 2dr (coupe)     |Sports|Europe|3     |
|AUD98633396|Audi         |TT 3.2 coupe 2dr (convertible)     |Sports|Europe|4     |
|BMWCCAB8998|BMW          |M3 convertible 2dr                 |Sports|Europe|1     |
|BMWE0E5643E|BMW          |M3 coupe 2dr                       |Sports|Europe|2     |
|BMWEE4E9DE5|BMW          |Z4 convertible 2.5i 2dr            |Sports|Europe|3     |
|BMW363EF657|BMW          |Z4 convertible 3.0i 2dr            |Sports|Europe|4     |
|JAG1AF71FA3|Jaguar       |XK8 convertible 2dr                |Sports|Europe|1     |
|JAGD2749AED|Jaguar       |XK8 coupe 2dr                      |Sports|Europe|2     |
|JAG28F2B038|Jaguar       |XKR convertible 2dr                |Sports|Europe|3     |
|JAGE179760E|Jaguar       |XKR coupe 2dr                      |Sports|Europe|4     |
|MERB2256530|Mercedes-Benz|SL500 convertible 2dr              |Sports|Europe|1     |
|MER3E1CCF76|Mercedes-Benz|SL55 AMG 2dr                       |Sports|Europe|2     |
|MERD7D90B93|Mercedes-Benz|SL600 convertible 2dr              |Sports|Europe|3     |
|MER87A5A03D|Mercedes-Benz|SLK230 convertible 2dr             |Sports|Europe|4     |
|MER16FF9E4A|Mercedes-Benz|SLK32 AMG 2dr                      |Sports|Europe|5     |
|POR31514F20|Porsche      |911 Carrera 4S coupe 2dr (convert) |Sports|Europe|1     |
|PORC2C27112|Porsche      |911 Carrera convertible 2dr (coupe)|Sports|Europe|2     |
|POR3F3F7451|Porsche      |911 GT2 2dr                        |Sports|Europe|3     |
|POR07229E38|Porsche      |911 Targa coupe 2dr                |Sports|Europe|4     |
|POR01FFC027|Porsche      |Boxster S convertible 2dr          |Sports|Europe|5     |
|POR418B21D0|Porsche      |Boxster convertible 2dr            |Sports|Europe|6     |
+-----------+-------------+-----------------------------------+------+------+------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
