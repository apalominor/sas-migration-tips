# Case 010: Appending Datasets (PROC APPEND/Data Step SET Option)
## Scenario
You found SAS code that appends some datasets. The rows are for "Audi", "BMW" and "Mercedes-Benz" cars, and the information is about `code`, `make`, `model`, `type`, `origin` and `msrp`.

## SAS Environment
### Code
```sas
data audi_cars (keep = code make model type origin msrp);
    set datalib.cars_data;

    if make = "Audi" and msrp > 50000;
run;

data bmw_cars (keep = code make model type origin msrp);
    set datalib.cars_data;

    if make = "BMW" and msrp > 50000;
run;

data mercedes_cars (keep = code make model type origin msrp);
    set datalib.cars_data;

    if make = "Mercedes-Benz" and msrp > 50000;
run;

data all_cars;
    set audi_cars bmw_cars;
run;

proc append base = all_cars data = mercedes_cars force;
run;
```
### Understanding the Code
We can see the creation of 3 datasets. Each one represents one car `make` with an additional `msrp` filter. At the end, we are using the 2 ways that SAS has to complete an append operation. The first one using a data step to create a new one: `all_cars`, setting the `audi_cars` and `bmw_cars` datasets together, and the second one using the `proc append`, to append the `mercedes_cars` dataset to the previously created `all_cars` dataset.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/80ae5e7f-96d7-4c74-9e0f-3976db227a09)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.
- `union()`: merges 2 dataframes with the same schema.

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
                               fn.col("type"),
                               fn.col("origin"),
                               fn.col("msrp"))\
                       .filter((fn.col("make") == "BMW") & (fn.col("msrp") > 50000))

mercedes_cars = df_cars_data.select(fn.col("code"),
                                    fn.col("make"),
                                    fn.col("model"),
                                    fn.col("type"),
                                    fn.col("origin"),
                                    fn.col("msrp"))\
                            .filter((fn.col("make") == "Mercedes-Benz") & (fn.col("msrp") > 50000))

all_cars = audi_cars.union(bmw_cars).union(mercedes_cars)
all_cars.show(40, truncate = False)
```

### Results
```
+-----------+-------------+------------------------------+------+------+------+
|code       |make         |model                         |type  |origin|msrp  |
+-----------+-------------+------------------------------+------+------+------+
|AUD2655C09E|Audi         |A8 L Quattro 4dr              |Sedan |Europe|69190 |
|AUD354BE625|Audi         |RS 6 4dr                      |Sports|Europe|84600 |
|BMWB12AEE17|BMW          |X5 4.4i                       |SUV   |Europe|52195 |
|BMW7063B9A0|BMW          |545iA 4dr                     |Sedan |Europe|54995 |
|BMW70DCCF5D|BMW          |745i 4dr                      |Sedan |Europe|69195 |
|BMW64E08F4C|BMW          |745Li 4dr                     |Sedan |Europe|73195 |
|BMWCCAB8998|BMW          |M3 convertible 2dr            |Sports|Europe|56595 |
|MER78AF29A9|Mercedes-Benz|G500                          |SUV   |Europe|76870 |
|MERE62B8253|Mercedes-Benz|C32 AMG 4dr                   |Sedan |Europe|52120 |
|MER1DCF8F33|Mercedes-Benz|CL500 2dr                     |Sedan |Europe|94820 |
|MER57C9074F|Mercedes-Benz|CL600 2dr                     |Sedan |Europe|128420|
|MER876504DD|Mercedes-Benz|CLK500 coupe 2dr (convertible)|Sedan |Europe|52800 |
|MER0810A9D8|Mercedes-Benz|E500 4dr                      |Sedan |Europe|57270 |
|MERE94D4BE3|Mercedes-Benz|S430 4dr                      |Sedan |Europe|74320 |
|MER1F1CCBB8|Mercedes-Benz|S500 4dr                      |Sedan |Europe|86970 |
|MERB2256530|Mercedes-Benz|SL500 convertible 2dr         |Sports|Europe|90520 |
|MER3E1CCF76|Mercedes-Benz|SL55 AMG 2dr                  |Sports|Europe|121770|
|MERD7D90B93|Mercedes-Benz|SL600 convertible 2dr         |Sports|Europe|126670|
|MER16FF9E4A|Mercedes-Benz|SLK32 AMG 2dr                 |Sports|Europe|56170 |
|MERB50E9B1C|Mercedes-Benz|E320                          |Wagon |Europe|50670 |
|MERC072210C|Mercedes-Benz|E500                          |Wagon |Europe|60670 |
+-----------+-------------+------------------------------+------+------+------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
