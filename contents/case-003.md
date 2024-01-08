# Case 003: Filtering Data and Select Columns (DROP Option)
## Scenario
You found SAS code that takes some rows from a filter and drop some columns for a dataset. The rows are for "Kia" cars and the information to show is about `make`, `model`, `msrp` and `invoice`.

## SAS Environment
### Code
```sas
data cars (drop = type origin drivetrain enginesize cylinders horsepower mpg_city mpg_highway weight wheelbase length);
    set datalib.cars_data;

    if make = 'Kia';
run;
```
### Understanding the Code
We can see in the SAS data step, that the `if` conditional is used to filter only "Kia" cars. That means, the result dataset will have only information about cars that meet the condition.
On the other hand, the `drop` option is used to "delete" specific columns in the final dataset.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/b78d436f-8cd2-4693-95da-27748c822604)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `drop()`: drops a column or columns from a dataframe.
- `select()`: selects columns to return.
- `col()` : returns a column based on a name specified.
- `filter()`: filters rows based on condition.

Option 1:
Remove the unnecessary columns (as in the case).

Option 2:
Reverse the original logic: select the requested columns (as simple as that).

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:

Option 1 Code:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.drop("type", "origin", "drivetrain", "enginesize", "cylinders", "horsepower", "mpg_city", "mpg_highway", "weight", "wheelbase", "length")\
                        .filter(fn.col("make") == "Kia")

df_result.show(truncate = False)
```

Option 2 Code:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.select(fn.col("code"),
                                fn.col("make"),
                                fn.col("model"),
                                fn.col("msrp"),
                                fn.col("invoice"))\
                        .filter(fn.col("make") == "Kia")

df_result.show(truncate=False)
```

### Results
Option 1 Results:
```
+-----------+----+---------------------+-----+-------+
|code       |make|model                |msrp |invoice|
+-----------+----+---------------------+-----+-------+
|KIA0F23DEC0|Kia |Sorento LX           |19635|18630  |
|KIA42D38E1F|Kia |Optima LX 4dr        |16040|14910  |
|KIADCECC88F|Kia |Rio 4dr manual       |10280|9875   |
|KIA025E0068|Kia |Rio 4dr auto         |11155|10705  |
|KIA8CFE35EE|Kia |Spectra 4dr          |12360|11630  |
|KIAC5E4EE3C|Kia |Spectra GS 4dr hatch |13580|12830  |
|KIA416A6876|Kia |Spectra GSX 4dr hatch|14630|13790  |
|KIA0A88B290|Kia |Optima LX V6 4dr     |18435|16850  |
|KIA8F6E3563|Kia |Amanti 4dr           |26000|23764  |
|KIADCEDDC6C|Kia |Sedona LX            |20615|19400  |
|KIA37A6D301|Kia |Rio Cinco            |11905|11410  |
+-----------+----+---------------------+-----+-------+
```
Option 2 Results:
```
+-----------+----+---------------------+-----+-------+
|code       |make|model                |msrp |invoice|
+-----------+----+---------------------+-----+-------+
|KIA0F23DEC0|Kia |Sorento LX           |19635|18630  |
|KIA42D38E1F|Kia |Optima LX 4dr        |16040|14910  |
|KIADCECC88F|Kia |Rio 4dr manual       |10280|9875   |
|KIA025E0068|Kia |Rio 4dr auto         |11155|10705  |
|KIA8CFE35EE|Kia |Spectra 4dr          |12360|11630  |
|KIAC5E4EE3C|Kia |Spectra GS 4dr hatch |13580|12830  |
|KIA416A6876|Kia |Spectra GSX 4dr hatch|14630|13790  |
|KIA0A88B290|Kia |Optima LX V6 4dr     |18435|16850  |
|KIA8F6E3563|Kia |Amanti 4dr           |26000|23764  |
|KIADCEDDC6C|Kia |Sedona LX            |20615|19400  |
|KIA37A6D301|Kia |Rio Cinco            |11905|11410  |
+-----------+----+---------------------+-----+-------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
