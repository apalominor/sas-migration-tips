# Case 004: Counting Rows by Group, Ordering and Getting Top 10 Rows
## Scenario
You found SAS code that gets count for each group and then return the top 10 rows. The column used for grouping is `make`.

## SAS Environment
### Code
```sas
data cars (keep = make total);
    set datalib.cars_data;
    by make;

    if first.make then
        total = 0;
    total + 1;

    if last.make;
run;

proc sort data = cars out = cars_ordered;
    by descending total;
run;

data cars_top10;
    set cars_ordered (obs = 10);
run;
```
### Understanding the Code
We can see 3 big steps:
1. The logic uses the `first` and `last` functions to separate each group and add 1 to the counter `total` during the iteration in the data step. The `first.make` represents the first row of the group, while `last.make` represents the last one.
2. Ordering is done using a SAS procedure (or **proc**), in this case, the `proc sort`. This procedure is used to order datasets based on one or more columns using the `by` statement. The default order is ASCENDING, and thats why in this case the DESCENDING order is specified (always before column name). The code also indicates that the data ordered will be stored in a differente dataset: `cars_ordered`.
3. A new dataset is created, `cars_top10`, taking data from `cars_ordered` limited by `obs = 10` option in the data step.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/f685aab1-1e1d-400c-b791-a38595183a0c)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `groupBy()`: groups data based on column specified.
- `count()`: counts rows based on the column in the groupBy function.
- `withColumnRenamed()`: renames an existing column.
- `col()`: returns a column based on a name specified.
- `orderBy()`: orders data based on column/s specified.
- `desc()`: sets the DESCENDING order.
- `asc()`: sets the ASCENDING order.
- `limit()`: limits rows to return.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_cars = df_cars_data.groupBy(fn.col("make")).count()
df_ordered = df_cars.withColumnRenamed("count","total").orderBy(fn.col("total").desc(),fn.col("make").asc())
df_top10 = df_ordered.limit(10)
df_top10.show(truncate = False)
```

### Results
```
+-------------+-----+
|make         |total|
+-------------+-----+
|Toyota       |28   |
|Chevrolet    |27   |
|Mercedes-Benz|26   |
|Ford         |23   |
|BMW          |20   |
|Audi         |19   |
|Honda        |17   |
|Nissan       |17   |
|Chrysler     |15   |
|Volkswagen   |15   |
+-------------+-----+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
