# Case 006: Getting Statistics for Columns (PROC MEANS/PROC FREQ)
## Scenario
You found SAS code that calculates some basic statistics for columns in a dataset. In this case, we get this information for all columns (numeric and non-numeric ones) but in 2 different steps.

## SAS Environment
### Code
```sas
data cars;
    set datalib.cars_data;
run;

* For NUMERIC columns *;
proc means data = cars ;
    title "Statistics for Cars Data";
    output out = output_means;
run;

* For NON-NUMERIC columns *;
proc freq data = cars;
    table origin * make / out = output_freq;
run;
run;
```
### Understanding the Code
After creating the dataset `cars`, in the first step, we use the `proc means` to get statistics for **numeric** columns. This procedures calculates automatically the number of rows, mean, standard deviation, maximum and minimum.

For **non-numeric** columns, we use the `proc freq` to get basic statistics, but we have to specify the variables to use for the calculation. In this case, we are using `origin` and `make` columns.

Both cases are generating results in a visual mode, but they are also being stored in a new dataset: `output_means` and `output_freq`. These datasets are available as any other.

### Results
Visual results (click to expand):

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/be4c016d-7d0e-40a5-8879-f6d92607a1de)

Dataset results:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/e207ca25-4c69-4298-a88d-93b6c13ee144)

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/e7ba5e76-96a7-475c-b406-f58ea3a00c74)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `summary()`: gets statistics for all columns (numeric and non-numeric) in the dataframe.

For calculations that don't apply, the function shows **null**.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
df_result = df_cars_data.summary()
df_result.show(truncate = False)
```

### Results
```
+-------+-----------+-----+----------+------+------+----------+------------------+------------------+------------------+------------------+-----------------+------------------+------------------+------------------+------------------+------------------+
|summary|code       |make |model     |type  |origin|drivetrain|msrp              |invoice           |enginesize        |cylinders         |horsepower       |mpg_city          |mpg_highway       |weight            |wheelbase         |length            |
+-------+-----------+-----+----------+------+------+----------+------------------+------------------+------------------+------------------+-----------------+------------------+------------------+------------------+------------------+------------------+
|count  |428        |428  |428       |428   |428   |428       |428               |428               |428               |426               |428              |428               |428               |428               |428               |428               |
|mean   |null       |null |null      |null  |null  |null      |32774.85514018692 |30014.70093457944 |3.1967289719626195|5.807511737089202 |215.8855140186916|20.060747663551403|26.843457943925234|3577.9532710280373|108.15420560747664|186.36214953271028|
|stddev |null       |null |null      |null  |null  |null      |19431.716673717532|17642.117750314756|1.108594718351475 |1.5584426332202248|71.83603158369074|5.238217638649048 |5.74120071698423  |758.9832146098707 |8.311812991089504 |14.357991256895625|
|min    |ACU26072F0B|Acura|3.5 RL 4dr|Hybrid|Asia  |All       |10280             |9875              |1.3               |3                 |73               |10                |12                |1850              |89                |143               |
|25%    |null       |null |null      |null  |null  |null      |20320             |18821             |2.3               |4                 |165              |17                |24                |3101              |103               |178               |
|50%    |null       |null |null      |null  |null  |null      |27560             |25218             |3.0               |6                 |210              |19                |26                |3473              |107               |187               |
|75%    |null       |null |null      |null  |null  |null      |39195             |35688             |3.9               |6                 |255              |21                |29                |3977              |112               |194               |
|max    |VOLEDB1106B|Volvo|xB        |Wagon |USA   |Rear      |192465            |173560            |8.3               |12                |500              |60                |66                |7190              |144               |238               |
+-------+-----------+-----+----------+------+------+----------+------------------+------------------+------------------+------------------+-----------------+------------------+------------------+------------------+------------------+------------------+
```

Results are slightly different (in showed information not values) but the idea is the same. Notice that in non-numeric cases, maximum and minimum are calculated in alphabetical order.

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
