# Case 008: Ordering Data in Dataset and Getting Top N Rows (PROC SORT BY Option / KEY Option)
## Scenario
You found SAS code that orders 2 datasets and then return the top 25 rows. The columns used for grouping are `mpg_city` and `mpg_highway`.

## SAS Environment
### Code
```sas
data cars (keep = code make model mpg_city mpg_highway);
    set datalib.cars_data;
run;

proc sort data = cars out = cars_mpg_city_by;
    by descending mpg_city descending mpg_highway;
run;

proc sort data = cars out = cars_mpg_highway_key;
    key mpg_highway / descending;
    key mpg_city / descending;
run;

data cars_mpg_city_by;
    set cars_mpg_city_by (obs = 25);
run;

data cars_mpg_highway_key;
    set cars_mpg_highway_key (obs = 25);
run;
```
### Understanding the Code
Ordering is done using a SAS procedure (or **proc**), in this case, the `proc sort`. This procedure is used to order datasets based on one or more columns using the `by` statement. The `key` statement is also used to achieve the same result.

The KEY statement is an alternative to the BY statement. The KEY statement syntax allows for the future possibility of specifying different collation options for each KEY variable. Currently, the only options allowed are ASCENDING and DESCENDING.[^1]

The default order is ASCENDING, and thats why in this case the DESCENDING order is specified (always before column name). The code also indicates that the data ordered will be stored in a differente dataset for each case. New datasets are created, `cars_mpg_city_by` and `cars_mpg_highway_key`, taking data from themselves limited by `obs = 25` option in the data step.

### Results
Dataset `cars_mpg_city_by`:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/8dd2975c-6b71-4d22-8eb0-52395ded4fc5)

Dataset `cars_mpg_highway_key`:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/139abc2f-1050-4222-98d8-7fbe041888ee)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `select()`: selects columns to return.
- `col()`: returns a column based on a name specified.
- `orderBy()`: orders data based on column/s specified.
- `desc()`: sets the DESCENDING order.
- `asc()`: sets the ASCENDING order.
- `limit()`: limits rows to return.

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
import pyspark.sql.functions as fn

df_cars_mpg_city_by = df_cars_data.select(fn.col("code"),
                                          fn.col("make"),
                                          fn.col("model"),
                                          fn.col("mpg_city"),
                                          fn.col("mpg_highway")).orderBy(fn.col("mpg_city").desc(),fn.col("mpg_highway").desc())

df_cars_mpg_city_by = df_cars_mpg_city_by.limit(25)
df_cars_mpg_city_by.show(25, truncate = False)

cars_mpg_highway_key = df_cars_data.select(fn.col("code"),
                                           fn.col("make"),
                                           fn.col("model"),
                                           fn.col("mpg_city"),
                                           fn.col("mpg_highway")).orderBy(fn.col("mpg_highway").desc(),fn.col("mpg_city").desc())

cars_mpg_highway_key = cars_mpg_highway_key.limit(25)
cars_mpg_highway_key.show(25, truncate = False)
```

### Results
```
+-----------+----------+--------------------------------------+--------+-----------+
|code       |make      |model                                 |mpg_city|mpg_highway|
+-----------+----------+--------------------------------------+--------+-----------+
|HOND2D9698F|Honda     |Insight 2dr (gas/electric)            |60      |66         |
|TOY921AEAF1|Toyota    |Prius 4dr (gas/electric)              |59      |51         |
|HONAADA0096|Honda     |Civic Hybrid 4dr manual (gas/electric)|46      |51         |
|VOL51E923EE|Volkswagen|Jetta GLS TDI 4dr                     |38      |46         |
|HONBD9C249E|Honda     |Civic HX 2dr                          |36      |44         |
|TOYC281D3E2|Toyota    |Echo 2dr manual                       |35      |43         |
|TOY198ED124|Toyota    |Echo 4dr                              |35      |43         |
|TOYDBF99A82|Toyota    |Echo 2dr auto                         |33      |39         |
|TOY9308DBAC|Toyota    |Corolla CE 4dr                        |32      |40         |
|TOY71A162B9|Toyota    |Corolla S 4dr                         |32      |40         |
|TOY9934A399|Toyota    |Corolla LE 4dr                        |32      |40         |
|HONA556E8ED|Honda     |Civic DX 2dr                          |32      |38         |
|HON1B8B727D|Honda     |Civic LX 4dr                          |32      |38         |
|SCI1D91B052|Scion     |xA 4dr hatch                          |32      |38         |
|HON1B0FC08F|Honda     |Civic EX 4dr                          |32      |37         |
|SCI20F98655|Scion     |xB                                    |31      |35         |
|DODE1FFA9AB|Dodge     |Neon SXT 4dr                          |29      |36         |
|DOD39549D99|Dodge     |Neon SE 4dr                           |29      |36         |
|PON23E8E337|Pontiac   |Vibe                                  |29      |36         |
|TOY7038D4B2|Toyota    |Matrix XR                             |29      |36         |
|HYU580C4D20|Hyundai   |Accent 2dr hatch                      |29      |33         |
|HYUB9F2144C|Hyundai   |Accent GL 4dr                         |29      |33         |
|HYU6BB0790A|Hyundai   |Accent GT 2dr hatch                   |29      |33         |
|MIN76F419D1|MINI      |Cooper                                |28      |37         |
|NIS60E1998E|Nissan    |Sentra 1.8 4dr                        |28      |35         |
+-----------+----------+--------------------------------------+--------+-----------+

+-----------+----------+--------------------------------------+--------+-----------+
|code       |make      |model                                 |mpg_city|mpg_highway|
+-----------+----------+--------------------------------------+--------+-----------+
|HOND2D9698F|Honda     |Insight 2dr (gas/electric)            |60      |66         |
|TOY921AEAF1|Toyota    |Prius 4dr (gas/electric)              |59      |51         |
|HONAADA0096|Honda     |Civic Hybrid 4dr manual (gas/electric)|46      |51         |
|VOL51E923EE|Volkswagen|Jetta GLS TDI 4dr                     |38      |46         |
|HONBD9C249E|Honda     |Civic HX 2dr                          |36      |44         |
|TOYC281D3E2|Toyota    |Echo 2dr manual                       |35      |43         |
|TOY198ED124|Toyota    |Echo 4dr                              |35      |43         |
|TOY9308DBAC|Toyota    |Corolla CE 4dr                        |32      |40         |
|TOY71A162B9|Toyota    |Corolla S 4dr                         |32      |40         |
|TOY9934A399|Toyota    |Corolla LE 4dr                        |32      |40         |
|TOYDBF99A82|Toyota    |Echo 2dr auto                         |33      |39         |
|HONA556E8ED|Honda     |Civic DX 2dr                          |32      |38         |
|HON1B8B727D|Honda     |Civic LX 4dr                          |32      |38         |
|SCI1D91B052|Scion     |xA 4dr hatch                          |32      |38         |
|HON1B0FC08F|Honda     |Civic EX 4dr                          |32      |37         |
|MIN76F419D1|MINI      |Cooper                                |28      |37         |
|CHEBDECE4F9|Chevrolet |Cavalier 2dr                          |26      |37         |
|CHE470F741D|Chevrolet |Cavalier 4dr                          |26      |37         |
|CHECA25D5D9|Chevrolet |Cavalier LS 2dr                       |26      |37         |
|DOD39549D99|Dodge     |Neon SE 4dr                           |29      |36         |
|DODE1FFA9AB|Dodge     |Neon SXT 4dr                          |29      |36         |
|PON23E8E337|Pontiac   |Vibe                                  |29      |36         |
|TOY7038D4B2|Toyota    |Matrix XR                             |29      |36         |
|FORB93D9591|Ford      |Focus LX 4dr                          |27      |36         |
|SCI20F98655|Scion     |xB                                    |31      |35         |
+-----------+----------+--------------------------------------+--------+-----------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)

[^1]: SAS KEY Statement: https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/proc/n12jw1r2n7auqqn1urrh8jwezk00.htm
