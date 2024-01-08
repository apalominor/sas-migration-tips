# Case 009: Deleting Duplicates in Dataset (PROC SORT NODUPKEY Option)
## Scenario
You found SAS code that removes duplicates from a dataset.

## SAS Environment
### Code
```sas
data cars (keep = make origin);
    set datalib.cars_data;
run;

proc sort data = cars nodupkey out = cars_distinct dupout = cars_duplicates_rep;
    by origin make;
run;
```
### Understanding the Code
We can see in the SAS data step, that the `nodupkey` option is used in the `proc sort` to remove the duplicates in the result dataset called `cars_distinct`. Also, we can see that the `dupout` option is used to store the duplicates in a new dataset called `cars_duplicate_rep`. This last option is optional (only if you are going to use this data later).

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/2d263db8-b424-43da-9838-6fef41ae7840)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `orderBy()`: orders data based on column/s specified.
- `desc()`: sets the DESCENDING order.
- `asc()`: sets the ASCENDING order.
- `distinct()`: removes duplicates based on all columns.
- `dropDuplicates()`: removes duplicates based on specific columns or all columns.

Option 1:
Using all columns.

Option 2:
Using specific columns.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:

Option 1 Code:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.select(fn.col("make"),
                                fn.col("origin"))
df_result = df_result.distinct()
df_result = df_result.orderBy(fn.col("origin").asc(),fn.col("make").asc())

df_result.show(40, truncate = False)
```

Option 2 Code:
```python
import pyspark.sql.functions as fn

df_result = df_cars_data.select(fn.col("make"),
                                fn.col("origin"))
df_result = df_result.dropDuplicates(["make","origin"])
df_result = df_result.orderBy(fn.col("origin").asc(),fn.col("make").asc())

df_result.show(40, truncate = False)
```

### Results
Option 1 Results:
```
+-------------+------+
|make         |origin|
+-------------+------+
|Acura        |Asia  |
|Honda        |Asia  |
|Hyundai      |Asia  |
|Infiniti     |Asia  |
|Isuzu        |Asia  |
|Kia          |Asia  |
|Lexus        |Asia  |
|Mazda        |Asia  |
|Mitsubishi   |Asia  |
|Nissan       |Asia  |
|Scion        |Asia  |
|Subaru       |Asia  |
|Suzuki       |Asia  |
|Toyota       |Asia  |
|Audi         |Europe|
|BMW          |Europe|
|Jaguar       |Europe|
|Land Rover   |Europe|
|MINI         |Europe|
|Mercedes-Benz|Europe|
|Porsche      |Europe|
|Saab         |Europe|
|Volkswagen   |Europe|
|Volvo        |Europe|
|Buick        |USA   |
|Cadillac     |USA   |
|Chevrolet    |USA   |
|Chrysler     |USA   |
|Dodge        |USA   |
|Ford         |USA   |
|GMC          |USA   |
|Hummer       |USA   |
|Jeep         |USA   |
|Lincoln      |USA   |
|Mercury      |USA   |
|Oldsmobile   |USA   |
|Pontiac      |USA   |
|Saturn       |USA   |
+-------------+------+
```
Option 2 Results:
```
+-------------+------+
|make         |origin|
+-------------+------+
|Acura        |Asia  |
|Honda        |Asia  |
|Hyundai      |Asia  |
|Infiniti     |Asia  |
|Isuzu        |Asia  |
|Kia          |Asia  |
|Lexus        |Asia  |
|Mazda        |Asia  |
|Mitsubishi   |Asia  |
|Nissan       |Asia  |
|Scion        |Asia  |
|Subaru       |Asia  |
|Suzuki       |Asia  |
|Toyota       |Asia  |
|Audi         |Europe|
|BMW          |Europe|
|Jaguar       |Europe|
|Land Rover   |Europe|
|MINI         |Europe|
|Mercedes-Benz|Europe|
|Porsche      |Europe|
|Saab         |Europe|
|Volkswagen   |Europe|
|Volvo        |Europe|
|Buick        |USA   |
|Cadillac     |USA   |
|Chevrolet    |USA   |
|Chrysler     |USA   |
|Dodge        |USA   |
|Ford         |USA   |
|GMC          |USA   |
|Hummer       |USA   |
|Jeep         |USA   |
|Lincoln      |USA   |
|Mercury      |USA   |
|Oldsmobile   |USA   |
|Pontiac      |USA   |
|Saturn       |USA   |
+-------------+------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
