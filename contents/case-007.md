# Case 007: Getting Metadata for Dataset (PROC CONTENTS)
## Scenario
You found SAS code that gets and stores metadata from a dataset to use later with other purpose.

## SAS Environment
### Code
```sas
data cars;
    set datalib.cars_data;
run;

proc contents data = cars out = cars_metadata order = varnum;
    title 'The Contents of the CARS Data Set';
run;

proc sort data = cars_metadata;
    by varnum;
run;

data cars_metadata (keep = varnum name datatype length format formatl);
    set cars_metadata;
    length DataType $15;

    if type = 1 then
        do;
            DataType = "Numeric";
        end;
    else
        do;
            DataType = "String";
        end;
run;
```
### Understanding the Code
After creating the dataset `cars`, the `proc contents` is executed to retreive all the metadata information for the dataset. Then, the dataset with the result (`cars_metadata`) is ordered by the `varnum` variable. This variable is used to set the order of the columns in the dataset.

Next, we select some columns with relevant information, and also do a transformation for the `type` column. With an `if-else` logic we transform the numeric values to its corresponding text values.

Remember that this `proc contents` displays information in a visual mode, but in this oportunity we try to focus in storing this data to use later.

### Results
Visual results (click to expand):

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/f0c5d27b-4e92-429a-87d8-d09c14be9602)

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/cd1941fe-287e-4d24-a152-6ec6f29961f6)

Dataset results:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/8e8c9c2f-f26f-4b99-9f42-4bb441ac4ab1)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `printSchema()`: displays metadata (schema) for a dataframe.
- `createDataFrame()`: creates a dataframe manually with data values specified.
- `union()`: merges 2 dataframes with the same schema.
- `col()`: returns a column based on a name specified.
- `filter()`: filters the values required for the result.

We will use additional properties to get the required information: the `dtypes` is a property for dataframe that returns all column names and their data types as a list. Also, we will use a `for` to iterate that list. We will use `schema[<column_name>].nullable` property to get the nullability of the columns, too.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

# Just for showing metadata information
df_cars_data.printSchema();

# Get metadata information for use later
columns = ["name", "datatype", "nullable"]
values = [("-", "-", False)]
df_metadata = spark.createDataFrame(values, columns)

for column_data in df_cars_data.dtypes:
  newRow = spark.createDataFrame([(column_data[0], column_data[1], df_cars_data.schema[column_data[0]].nullable)], columns)
  df_metadata = df_metadata.union(newRow)

df_metadata = df_metadata.filter(fn.col("name") != "-")
df_metadata.show(truncate = False)
```

### Results
```
root
 |-- code: string (nullable = true)
 |-- make: string (nullable = true)
 |-- model: string (nullable = true)
 |-- type: string (nullable = true)
 |-- origin: string (nullable = true)
 |-- drivetrain: string (nullable = true)
 |-- msrp: integer (nullable = true)
 |-- invoice: integer (nullable = true)
 |-- enginesize: double (nullable = true)
 |-- cylinders: integer (nullable = true)
 |-- horsepower: integer (nullable = true)
 |-- mpg_city: integer (nullable = true)
 |-- mpg_highway: integer (nullable = true)
 |-- weight: integer (nullable = true)
 |-- wheelbase: integer (nullable = true)
 |-- length: integer (nullable = true)

+-----------+--------+--------+
|name       |datatype|nullable|
+-----------+--------+--------+
|code       |string  |true    |
|make       |string  |true    |
|model      |string  |true    |
|type       |string  |true    |
|origin     |string  |true    |
|drivetrain |string  |true    |
|msrp       |int     |true    |
|invoice    |int     |true    |
|enginesize |double  |true    |
|cylinders  |int     |true    |
|horsepower |int     |true    |
|mpg_city   |int     |true    |
|mpg_highway|int     |true    |
|weight     |int     |true    |
|wheelbase  |int     |true    |
|length     |int     |true    |
+-----------+--------+--------+
```

Results are slightly different (in showed information not values) but the idea is the same.

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
