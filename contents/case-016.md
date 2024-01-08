# Case 016: Joining Filtered Datasets and Transform Column Results (IF/IF-ELSE Option)
## Scenario
You found SAS code that gets information about "Porsche" cars that have less than 400 horsepower. Also, the requested data needs to show only the cars that are available in PC videogames only. The business rule about PC videogames is: the car should be available in at least one of these videogames: *Assetto Corsa Competizione®*, *Forza Horizon 5®*, *Project Cars 2®*, *iRacing®* or *rFactor2®*. The car should NOT be available in *Gran Turismo 7®* videogame. The information to show is about `code`, `make`, `model`, `type`, `horsepower` and a "Yes/No" availability indicator for each videogame.

## SAS Environment
### Code
```sas
data porsche_cars;
    set datalib.cars_data;
    
    if make = "Porsche" and horsepower < 400;
run;

data only_pc_gaming;
    set datalib.gaming_data;
    
    if (acc_available = 1 or fh5_available = 1 or pc2_available = 1 or ir_available = 1 or rf2_available = 1) and gt7_available = 0;
run;

proc sort data = porsche_cars;
    by code;
run;

proc sort data = only_pc_gaming;
    by code;
run;

data porsche_only_pc_cars (keep = code make model type horsepower acc_available fh5_available pc2_available ir_available rf2_available);
    merge porsche_cars (in = pc)
          only_pc_gaming (in = opg);
    by code;
    if pc = opg;
run;

data porsche_only_pc_cars (keep = code make model type horsepower acc_available_flag fh5_available_flag pc2_available_flag ir_available_flag rf2_available_flag);
    set porsche_only_pc_cars;

    if acc_available = 1 then acc_available_flag = "Yes";
    else acc_available_flag = "No";
    
    if fh5_available = 1 then fh5_available_flag = "Yes";
    else fh5_available_flag = "No";
    
    if pc2_available = 1 then pc2_available_flag = "Yes";
    else pc2_available_flag = "No";
    
    if ir_available = 1 then ir_available_flag = "Yes";
    else ir_available_flag = "No";
    
    if rf2_available = 1 then rf2_available_flag = "Yes";
    else rf2_available_flag = "No";
run;
```
### Understanding the Code
We can see the use of 2 datasets. Both are preiously filtered, each one with their corresponding rules. Then, both datasets are sorted and merged. You can find more information and detailed example about merging/joining in the previous case (#015) [here](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-015.md).

In this case we don't want to keep all the columns, so in the next step we are going to select and "format" the columns that were requested. The format step means transforming the binary values into "Yes" and "No" values. To accomplish this, we add a new column for each videogame with the new indicator.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/c3ae5082-b4c6-4c96-b876-09ab7b299d0c)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `filter()`: filters rows based on condition.
- `alias()`: sets an alias to a dataseat.
- `join()`: joins two datasets, specifying the columns and the join type to be used.
- `withColumn()`: adds a column to a dataframe.
- `lit()`: sets literal or constant value to the new column.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_porsche_cars = df_cars_data.select("*")\
                              .filter((fn.col("make") == "Porsche") & (fn.col("horsepower") < 400))

df_only_pc_gaming = df_gaming_data.select("*")\
                                  .filter(((fn.col("acc_available") == 1) | (fn.col("fh5_available") == 1) | (fn.col("pc2_available") == 1) | (fn.col("ir_available") == 1) | (fn.col("rf2_available") == 1)) & (fn.col("gt7_available") == 0))

porsche_only_pc_cars = df_porsche_cars.alias("pc").join(df_only_pc_gaming.alias("opg"), fn.col("pc.code") == fn.col("opg.code"), "inner")
porsche_only_pc_cars = porsche_only_pc_cars.withColumn("acc_available_flag", fn.when(fn.col("acc_available") == 1, fn.lit("Yes")).otherwise(fn.lit("No")))\
                                           .withColumn("fh5_available_flag", fn.when(fn.col("fh5_available") == 1, fn.lit("Yes")).otherwise(fn.lit("No")))\
                                           .withColumn("pc2_available_flag", fn.when(fn.col("pc2_available") == 1, fn.lit("Yes")).otherwise(fn.lit("No")))\
                                           .withColumn("ir_available_flag", fn.when(fn.col("ir_available") == 1, fn.lit("Yes")).otherwise(fn.lit("No")))\
                                           .withColumn("rf2_available_flag", fn.when(fn.col("rf2_available") == 1, fn.lit("Yes")).otherwise(fn.lit("No")))

porsche_only_pc_cars = porsche_only_pc_cars.select(fn.col("pc.code"),
                                                   fn.col("make"),
                                                   fn.col("model"),
                                                   fn.col("type"),
                                                   fn.col("horsepower"),
                                                   fn.col("acc_available_flag"),
                                                   fn.col("fh5_available_flag"),
                                                   fn.col("pc2_available_flag"),
                                                   fn.col("ir_available_flag"),
                                                   fn.col("rf2_available_flag"))

porsche_only_pc_cars.show(5, False)
```

### Results
```
+-----------+-------+----------------------------------+------+----------+------------------+------------------+------------------+-----------------+------------------+
|code       |make   |model                             |type  |horsepower|acc_available_flag|fh5_available_flag|pc2_available_flag|ir_available_flag|rf2_available_flag|
+-----------+-------+----------------------------------+------+----------+------------------+------------------+------------------+-----------------+------------------+
|POR31514F20|Porsche|911 Carrera 4S coupe 2dr (convert)|Sports|315       |Yes               |No                |Yes               |Yes              |Yes               |
|POR418B21D0|Porsche|Boxster convertible 2dr           |Sports|228       |No                |Yes               |Yes               |Yes              |No                |
+-----------+-------+----------------------------------+------+----------+------------------+------------------+------------------+-----------------+------------------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
