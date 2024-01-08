# Case 017: Categorizing and Counting Group Data (SELECT-WHEN Option)
## Scenario
You found SAS code that creates categories for availability in videogames, based on the number of times a car can be found in platforms. Then, a total for each category is shown.

The business rule about categories is:
- If the car is available in no videogames, it should be marked as "NOT AVAILABLE".
- If the car is available in just 1 videogame, it should be marked as "SINGLE".
- If the car is available in more than 1 videogame but less than 6, it should be marked as "MEDIUM".
- If the car is available in all the 6 videogames, it should be marked as "FULL".

In any other cases the car should be marked as "NO DATA".

## SAS Environment
### Code
```sas
data gaming;
    set datalib.gaming_data;
    total_availability = gt7_available + acc_available + fh5_available + pc2_available + ir_available + rf2_available;

    select;
        when (total_availability = 0) availability_category = "NOT AVAILABLE";
        when (total_availability = 1) availability_category = "SINGLE";
        when (2 <=total_availability <=5) availability_category = "MEDIUM";
        when (total_availability = 6) availability_category = "FULL";
        otherwise availability_category = "NO DATA";
    end;
run;

proc sort data = gaming;
    by availability_category;
run;

data gaming_total_by_cat (keep = availability_category total);
    set gaming;
    by availability_category;

    if first.availability_category then
        total = 1;
    else
        total + 1;

    if last.availability_category;
run;
```
### Understanding the Code
As a first step, we hace the creation of the new column called `total_availability`, that has the result value the sum of all the numeric columns that represents the availability of the car in each videogame. Then we use the `SELECT - WHEN`[^1] statement to create the categories based on the conditions given. Each category value is stored in a new column called `availability_category`.

We use an artifice to count each group, using `by`, and that's why a `proc sort` is used before this operation. The variable `total` is storing the "rownum" for each car and every new group appears it's initialized to "1" again. At the end, we only keep the last row in each group with the `last.` operation because at this time it should have the final number of rows.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/9732a6ae-bf20-468c-b5db-a69023191653)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `col()`: returns a column based on a name specified.
- `withColumn()`: adds a column to a dataframe.
- `withColumnRenamed()`: renames an existing column.
- `when()`: evaluates a list of conditions and returns one result based on them.
- `groupBy()`: groups data based on column specified.
- `count()`: counts rows based on the column in the groupBy function.
- `orderBy()`: orders data based on column/s specified.
- `asc()`: sets the ASCENDING order.
- `lit()`: sets literal or constant value to the new column.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_gaming = df_gaming_data.withColumn("total_availability", fn.col("gt7_available") + fn.col("acc_available") + fn.col("fh5_available") + fn.col("pc2_available") + fn.col("ir_available") + fn.col("rf2_available"))
df_gaming = df_gaming.withColumn("availability_category", fn.when(fn.col("total_availability") == 0, fn.lit("NOT AVAILABLE"))\
                                                            .when(fn.col("total_availability") == 1, fn.lit("SINGLE"))\
                                                            .when((fn.col("total_availability") >= 2) & (fn.col("total_availability") <= 5), fn.lit("MEDIUM"))\
                                                            .when(fn.col("total_availability") == 6, fn.lit("FULL"))\
                                                            .otherwise(fn.lit("NO DATA")))

df_gaming_total_by_cat = df_gaming.groupBy(fn.col("availability_category")).count()
df_gaming_total_by_cat = df_gaming_total_by_cat.withColumnRenamed("count", "total").orderBy(fn.col("availability_category").asc())

df_gaming_total_by_cat.show(5, False)
```

### Results
```
+---------------------+-----+
|availability_category|total|
+---------------------+-----+
|FULL                 |5    |
|MEDIUM               |372  |
|NOT AVAILABLE        |8    |
|SINGLE               |43   |
+---------------------+-----+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)

[^1]: More information about SELECT-WHEN in: https://blogs.sas.com/content/iml/2016/06/20/select-when-sas-data-step.html
