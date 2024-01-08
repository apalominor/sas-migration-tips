# Case 001: Adding New Column to Dataset
## Scenario
You found SAS code that creates 2 new columns to a dataset, using both methods that SAS allows for this task. The new columns are `car_category` with the value "Luxury" and `engine_modifications` with the value "Stock".

## SAS Environment
### Code
```sas
data cars;
    set datalib.cars_data (obs = 5);
    
    * Option 1: Setting column name and length, then the value *;
    length car_category $10;
    car_category = 'Luxury';
    
    * Option 2: Setting column name with value directly *;
    engine_modifications = 'Stock';
run;
```

### Understanding the Code
We can see the 2 options used for creating new columns. Both cases get the same result (a new column created). The `(obs=5)` option is being used to restrict the result for the new dataset, taking only the first 5 rows of data.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/7031a132-505f-4133-99e3-5356a4568b84)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `withColumn()`: adds a column to a dataframe.
- `lit()`: sets literal or constant value to the new column.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_cars_data = df_cars_data.withColumn("car_category",fn.lit("Luxury"))\
                           .withColumn("engine_modifications",fn.lit("Stock"))

df_cars_data.limit(5).show(truncate=False)
```

### Results
```
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+------------+--------------------+
|code       |make |model         |type |origin|drivetrain|msrp |invoice|enginesize|cylinders|horsepower|mpg_city|mpg_highway|weight|wheelbase|length|car_category|engine_modifications|
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+------------+--------------------+
|ACU38FAFF99|Acura|MDX           |SUV  |Asia  |All       |36945|33337  |3.5       |6        |265       |17      |23         |4451  |106      |189   |Luxury      |Stock               |
|ACU3620BF28|Acura|RSX Type S 2dr|Sedan|Asia  |Front     |23820|21761  |2.0       |4        |200       |24      |31         |2778  |101      |172   |Luxury      |Stock               |
|ACU8A0D3A80|Acura|TSX 4dr       |Sedan|Asia  |Front     |26990|24647  |2.4       |4        |200       |22      |29         |3230  |105      |183   |Luxury      |Stock               |
|ACUAAA8AC9F|Acura|TL 4dr        |Sedan|Asia  |Front     |33195|30299  |3.2       |6        |270       |20      |28         |3575  |108      |186   |Luxury      |Stock               |
|ACU3CC7286B|Acura|3.5 RL 4dr    |Sedan|Asia  |Front     |43755|39014  |3.5       |6        |225       |18      |24         |3880  |115      |197   |Luxury      |Stock               |
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+------------+--------------------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
