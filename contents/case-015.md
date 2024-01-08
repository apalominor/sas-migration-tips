# Case 015: Joining Datasets and Count Results
## Scenario
You found SAS code that joins 2 datasets, considering that one on them has an additional filter. The output is a message showing de number of rows after join execution.

## SAS Environment
### Code
```sas
data cars;
    set datalib.cars_data;
run;

data custom;
    set datalib.custom_data;
run;

proc sort data = cars;
    by code;
run;

proc sort data = custom;
    by code;
run;

data cars_color_customized;
    merge cars (in = ca)
          custom (in = cu where = (color_custom = 1));
    by code;
    if ca = cu;
run;

data _null_;
    if 0 then
        set cars_color_customized nobs = n;
    put "Number of Observations = " n;
    stop;
run;
```
### Understanding the Code
We can see the use of 2 datasets. Both are being sorted before applying the join (this is mandatory for SAS). Also is mandatory for both datasets to have the same column name to execute the join. In the example this column is `code`. We are creating a third dataset that stores the result of the process: `data_color_customized`. This dataset is used to count rows for the expected result.

The SAS syntax for joining is[^1]:
```sas
data <result_dataset>;
    merge <base_dataset> (in = <alias_for_base_dataset>)
          <joined_dataset> (in = <alias_for_joined_dataset> where = (<additional_dataset_filters>));
    by <column_used_to_join>;
    if <equality_that_represents_join_type>;
run;
```
At the end we are using the "dummy" dataset `_null_` to show the message with the final count. Consider that the `n` variable is an internal one, so it stores the count for the current dataset. The `put` command is used to print a message in the console. The `if 0 then` is an artifice that is used to prepare the code to be executed, even thought it never does.[^2]

### Results
```
 92         data _null_;
 93             if 0 then
 94                 set cars_color_customized nobs = n;
 95             put "Number of Observations = " n;
 96             stop;
 97         run;
 
 Number of Observations = 202
 NOTE: DATA statement used (Total process time):
       real time           0.00 seconds
       user cpu time       0.00 seconds
       system cpu time     0.00 seconds
       memory              757.65k
       OS Memory           20132.00k
```

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `col()`: returns a column based on a name specified.
- `alias()`: sets an alias to a dataseat.
- `join()`: joins two datasets, specifying the columns and the join type to be used.
- `print()`: prints a message in the console.
- `count()`: returns the number of rows in the current dataframe.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

cars_color_customized = df_cars_data.alias("ca").join(df_custom_data.alias("cu"), (fn.col("ca.code") == fn.col("cu.code")) & (fn.col("cu.color_custom") == 1), "inner")
print(f"Number of Observations = {cars_color_customized.count()}")
```

### Results
```
Number of Observations = 202
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)

[^1]: More information about join equalities in the next paper: https://support.sas.com/resources/papers/proceedings/proceedings/sugi25/25/cc/25p109.pdf
[^2]: More about `IF 0 THEN` artifice in: https://communities.sas.com/t5/SAS-Programming/If-0-then-set-statement/td-p/455017
