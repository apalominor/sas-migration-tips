# Case 011: Appending Datasets with Different Columns (PROC APPEND/Data Step SET Option)
## Scenario
You found SAS code that appends datasets but they have some different columns. The rows are for "Audi", "BMW" and "Mercedes-Benz" cars, and the information is about `code`, `make`, `model`, `type`, `origin`, `msrp`, `cylinders` and `horsepower`.

## SAS Environment
### Code
```sas
data audi_cars (keep = code make model type origin msrp);
    set datalib.cars_data;

    if make = "Audi" and msrp > 50000;
run;

data bmw_cars (keep = code make model origin msrp cylinders horsepower);
    set datalib.cars_data;

    if make = "BMW" and msrp > 50000;
run;

data mercedes_cars (keep = code make model msrp horsepower);
    set datalib.cars_data;

    if make = "Mercedes-Benz" and msrp > 50000;
run;

data all_cars;
    set audi_cars bmw_cars;
run;

proc append base = all_cars data = mercedes_cars force nowarn;
run;
```
### Understanding the Code
We can see the creation of 3 datasets with different columns. Each one represents one car `make` with an additional `msrp` filter. At the end, we are using the 2 ways that SAS has to complete an append operation.

The first one using a data step to create a new one: `all_cars`, setting the `audi_cars` and `bmw_cars` datasets together, and the second one using the `proc append`, to append the `mercedes_cars` dataset to the previously created `all_cars` dataset. In this case, we are using the `force` and `nowarn` options to set the behaviour for the append operation.

`force`[^1]: forces the APPEND statement to concatenate data sets when the DATA= data set contains variables that meet one of the following criteria:
- are not in the BASE= data set
- do not have the same type as the variables in the BASE= data set
- are longer than the variables in the BASE= data set

`nowarn`[^1]: suppresses the warning when used with the FORCE option to concatenate two data sets with different variables.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/9d8e5305-9c41-4f13-b058-59b431dbb492)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.
- `unionByName()`: merges 2 dataframes with the same schema based on column names. When the parameter `allowMissingColumns` is `True`, the set of column names in this and other dataframe can differ; missing columns will be filled with null. Further, the missing columns of this dataFrame will be added at the end in the schema of the union result.[^2]

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

audi_cars = df_cars_data.select(fn.col("code"),
                                fn.col("make"),
                                fn.col("model"),
                                fn.col("type"),
                                fn.col("origin"),
                                fn.col("msrp"))\
                        .filter((fn.col("make") == "Audi") & (fn.col("msrp") > 50000))

bmw_cars = df_cars_data.select(fn.col("code"),
                               fn.col("make"),
                               fn.col("model"),
                               fn.col("origin"),
                               fn.col("msrp"),
                               fn.col("cylinders"),
                               fn.col("horsepower"))\
                       .filter((fn.col("make") == "BMW") & (fn.col("msrp") > 50000))

mercedes_cars = df_cars_data.select(fn.col("code"),
                                    fn.col("make"),
                                    fn.col("model"),
                                    fn.col("msrp"),
                                    fn.col("horsepower"))\
                            .filter((fn.col("make") == "Mercedes-Benz") & (fn.col("msrp") > 50000))

all_cars = audi_cars.unionByName(bmw_cars, allowMissingColumns = True)
all_cars = all_cars.unionByName(mercedes_cars, allowMissingColumns = True)
all_cars.show(40, truncate = False)
```

### Results
```
+-----------+-------------+------------------------------+------+------+------+---------+----------+
|code       |make         |model                         |type  |origin|msrp  |cylinders|horsepower|
+-----------+-------------+------------------------------+------+------+------+---------+----------+
|AUD2655C09E|Audi         |A8 L Quattro 4dr              |Sedan |Europe|69190 |null     |null      |
|AUD354BE625|Audi         |RS 6 4dr                      |Sports|Europe|84600 |null     |null      |
|BMWB12AEE17|BMW          |X5 4.4i                       |null  |Europe|52195 |8        |325       |
|BMW7063B9A0|BMW          |545iA 4dr                     |null  |Europe|54995 |8        |325       |
|BMW70DCCF5D|BMW          |745i 4dr                      |null  |Europe|69195 |8        |325       |
|BMW64E08F4C|BMW          |745Li 4dr                     |null  |Europe|73195 |8        |325       |
|BMWCCAB8998|BMW          |M3 convertible 2dr            |null  |Europe|56595 |6        |333       |
|MER78AF29A9|Mercedes-Benz|G500                          |null  |null  |76870 |null     |292       |
|MERE62B8253|Mercedes-Benz|C32 AMG 4dr                   |null  |null  |52120 |null     |349       |
|MER1DCF8F33|Mercedes-Benz|CL500 2dr                     |null  |null  |94820 |null     |302       |
|MER57C9074F|Mercedes-Benz|CL600 2dr                     |null  |null  |128420|null     |493       |
|MER876504DD|Mercedes-Benz|CLK500 coupe 2dr (convertible)|null  |null  |52800 |null     |302       |
|MER0810A9D8|Mercedes-Benz|E500 4dr                      |null  |null  |57270 |null     |302       |
|MERE94D4BE3|Mercedes-Benz|S430 4dr                      |null  |null  |74320 |null     |275       |
|MER1F1CCBB8|Mercedes-Benz|S500 4dr                      |null  |null  |86970 |null     |302       |
|MERB2256530|Mercedes-Benz|SL500 convertible 2dr         |null  |null  |90520 |null     |302       |
|MER3E1CCF76|Mercedes-Benz|SL55 AMG 2dr                  |null  |null  |121770|null     |493       |
|MERD7D90B93|Mercedes-Benz|SL600 convertible 2dr         |null  |null  |126670|null     |493       |
|MER16FF9E4A|Mercedes-Benz|SLK32 AMG 2dr                 |null  |null  |56170 |null     |349       |
|MERB50E9B1C|Mercedes-Benz|E320                          |null  |null  |50670 |null     |221       |
|MERC072210C|Mercedes-Benz|E500                          |null  |null  |60670 |null     |302       |
+-----------+-------------+------------------------------+------+------+------+---------+----------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)

[^1]: SAS PROC APPEND FORCE: https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/proc/n19kwc3onglzh2n1l2k4e39edv3x.htm#n04oq5r04dlf78n1t047xjhzfyw8
[^2]: Spark unionByName Documentation: https://spark.apache.org/docs/3.1.1/api/python/reference/api/pyspark.sql.DataFrame.unionByName.html
