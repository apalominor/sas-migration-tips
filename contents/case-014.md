# Case 014: Create Dataset Using SQL
## Scenario
You found SAS code that creates a dataset based on a SQL query. The rows are for "Lexus Sedan" cars with the price higher than $40000. The information is about `code`, `make`, `model`, `type`, `origin`, `drivetrain`, `msrp`, `invoice`, `horsepower` and `weight`.

## SAS Environment
### Code
```sas
data cars;
    set datalib.cars_data;
run;

proc sql;
    create table lexus_sedan_cars as
    select code,
           make,
           model,
           type,
           origin,
           drivetrain,
           msrp,
           invoice,
           horsepower,
           weight
      from cars
     where make = 'Lexus'
       and type = 'Sedan'
       and invoice > 40000;
run;
```
### Understanding the Code
We can see in the `proc sql` used to create the `lexus_sedan_cars` dataset. This procedures allows us to use SQL syntax to create a "table", so it's easy to understand.

The filters are inside the query with `where` clause and `and` clause. The `from` clause is used with the dataset previously created: `cars`.

### Results
![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/666c7217-4bde-4df4-8e0e-53a77d4424e2)

## Pyspark Environment
### Strategy
We will use the next functions and transformations to replicate the logic:
- `createOrReplaceTempView()`: creates a temporary view that represents the dataframe attached with an specific name.
- `sql()`: returns a dataframe based on the result of the query sent as a parameter.

Note: The SAS query can be replicated using Pyspark functions without any problems. The idea of using this method is to demonstrate the exact same behaviour in the same conext (using a SQL query).

### Code
_After executing lines detailed in [Index Item 2: Importing Data to Google Colab](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md) file for connecting Spark and importing data_:
```python
df_cars_data.createOrReplaceTempView("cars")

sql_query = """select code,
                      make,
                      model,
                      type,
                      origin,
                      drivetrain,
                      msrp,
                      invoice,
                      horsepower,
                      weight
                 from cars
                where make = 'Lexus'
                  and type = 'Sedan'
                  and invoice > 40000"""

lexus_sedan_cars = spark.sql(sql_query)
lexus_sedan_cars.show(5, truncate = False)
```

### Results
```
+-----------+-----+----------+-----+------+----------+-----+-------+----------+------+
|code       |make |model     |type |origin|drivetrain|msrp |invoice|horsepower|weight|
+-----------+-----+----------+-----+------+----------+-----+-------+----------+------+
|LEX162C84B3|Lexus|GS 430 4dr|Sedan|Asia  |Rear      |48450|42232  |300       |3715  |
|LEX31B02686|Lexus|LS 430 4dr|Sedan|Asia  |Rear      |55750|48583  |290       |3990  |
+-----------+-----+----------+-----+------+----------+-----+-------+----------+------+
```

[Return to Index](https://github.com/apalominor/sas-migration-guide#index-of-contents)
