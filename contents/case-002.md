# Case 002: Filtering Data and Select Columns (KEEP Option)
## Scenario
You found SAS code that takes some rows from a filter and keep only some columns for a dataset. The rows are for "Audi" cars and the information is about `make`, `model`, `type` and `msrp`.

## SAS Environment
### Code
```sas
data cars (keep = code make model type msrp);
    set datalib.cars_data;

    if make = 'Audi';
run;
```
### Understanding the Code
We can see in the SAS data step, that the `if` conditional is used to filter only "Audi" cars. That means, the result dataset will have only information about cars that meet the condition.
On the other hand, the `keep` option is used to "select and keep" specific columns in the final dataset.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/820224ee-b733-4fd8-bddf-4cc452dbc436)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.select(fn.col("code"),
                                fn.col("make"),
                                fn.col("model"),
                                fn.col("type"),
                                fn.col("msrp"))\
                        .filter(fn.col("make") == "Audi")

df_result.show(truncate=False)
```

### Results
```
+-----------+----+--------------------------------+------+-----+
|code       |make|model                           |type  |msrp |
+-----------+----+--------------------------------+------+-----+
|AUDCCCAA28C|Audi|A4 1.8T 4dr                     |Sedan |25940|
|AUD49B407AF|Audi|A41.8T convertible 2dr          |Sedan |35940|
|AUD36C00CFF|Audi|A4 3.0 4dr                      |Sedan |31840|
|AUD9C706211|Audi|A4 3.0 Quattro 4dr manual       |Sedan |33430|
|AUD883D761A|Audi|A4 3.0 Quattro 4dr auto         |Sedan |34480|
|AUDF1D681BD|Audi|A6 3.0 4dr                      |Sedan |36640|
|AUD822442B3|Audi|A6 3.0 Quattro 4dr              |Sedan |39640|
|AUDFD157DD6|Audi|A4 3.0 convertible 2dr          |Sedan |42490|
|AUDB029B75C|Audi|A4 3.0 Quattro convertible 2dr  |Sedan |44240|
|AUD3CA28C21|Audi|A6 2.7 Turbo Quattro 4dr        |Sedan |42840|
|AUD16A34159|Audi|A6 4.2 Quattro 4dr              |Sedan |49690|
|AUD2655C09E|Audi|A8 L Quattro 4dr                |Sedan |69190|
|AUDD1FE4ECD|Audi|S4 Quattro 4dr                  |Sedan |48040|
|AUD354BE625|Audi|RS 6 4dr                        |Sports|84600|
|AUDDFA0F828|Audi|TT 1.8 convertible 2dr (coupe)  |Sports|35940|
|AUD603655C3|Audi|TT 1.8 Quattro 2dr (convertible)|Sports|37390|
|AUD98633396|Audi|TT 3.2 coupe 2dr (convertible)  |Sports|40590|
|AUD33E2F515|Audi|A6 3.0 Avant Quattro            |Wagon |40840|
|AUD4E817D62|Audi|S4 Avant Quattro                |Wagon |49090|
+-----------+----+--------------------------------+------+-----+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
