# Case 005: Deleting Rows from Dataset with Condition
## Scenario
You found SAS code that deletes data based on a condition for the result dataset. In this case, we delete cars with `msrp` lower than $100000 and show some information about them.

## SAS Environment
### Code
```sas
data cars (keep = code make model type msrp enginesize cylinders);
    set datalib.cars_data;

    if msrp < 100000 then
        delete;
run;
```
### Understanding the Code
The `if` condition is used to restrict the results doing a `delete` action. Every car with `msrp` lower than $100000 will be deleted from the result.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/5d674829-4eaf-41e4-8b31-b5d358c8ac86)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `col()`: returns a column based on a name specified.
- `filter()`: filters the values required for the result.

For this case, we will apply the logic in the opposite way by selecting and keeping cars with `msrp` greater or equal than $100000.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.select(fn.col("code"),
                                fn.col("make"),
                                fn.col("model"),
                                fn.col("type"),
                                fn.col("msrp"),
                                fn.col("enginesize"),
                                fn.col("cylinders"))\
                        .filter(fn.col("msrp") >= 100000)

df_result.show(truncate = False)
```

### Results
```
+-----------+-------------+---------------------+------+------+----------+---------+
|code       |make         |model                |type  |msrp  |enginesize|cylinders|
+-----------+-------------+---------------------+------+------+----------+---------+
|MER57C9074F|Mercedes-Benz|CL600 2dr            |Sedan |128420|5.5       |12       |
|MER3E1CCF76|Mercedes-Benz|SL55 AMG 2dr         |Sports|121770|5.5       |8        |
|MERD7D90B93|Mercedes-Benz|SL600 convertible 2dr|Sports|126670|5.5       |12       |
|POR3F3F7451|Porsche      |911 GT2 2dr          |Sports|192465|3.6       |6        |
+-----------+-------------+---------------------+------+------+----------+---------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
