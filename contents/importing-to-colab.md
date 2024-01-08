## Importing Data to Google Colab
First of all, **DO NOT FORGET** to mount your Drive before coding (left panel):

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/fe670e84-a2c4-4094-add0-4d3d260b326c)

We are going to import data to check some contents. Also it's possible to check if the schema is the same as the SAS information previously mentioned. Some considerations to understand the import:
- `inferSchema = True` is used to infer the data types of each column automatically.
- `header = True` is used to notify that the file has a header (considering first line for that).
- `delimiter = "|"` is used to notify that the file has a different delimiter.

```python
# Importing data (CSV file should be uploaded to your Drive, and don't forget to change the "<path_to_your_drive>" value)
df_cars_data = spark.read.format("csv").load("/content/drive/<path_to_your_drive>/cars_data.txt", inferSchema = True, header = True, delimiter = "|")
df_custom_data = spark.read.format("csv").load("/content/drive/<path_to_your_drive>/customization_data.txt", inferSchema = True, header = True, delimiter = "|")
df_gaming_data = spark.read.format("csv").load("/content/drive/<path_to_your_drive>/gaming_data.txt", inferSchema = True, header = True, delimiter = "|")

df_cars_data.show(5, False)
df_custom_data.show(5, False)
df_gaming_data.show(5, False)
```
```
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+
|code       |make |model         |type |origin|drivetrain|msrp |invoice|enginesize|cylinders|horsepower|mpg_city|mpg_highway|weight|wheelbase|length|
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+
|ACU38FAFF99|Acura|MDX           |SUV  |Asia  |All       |36945|33337  |3.5       |6        |265       |17      |23         |4451  |106      |189   |
|ACU3620BF28|Acura|RSX Type S 2dr|Sedan|Asia  |Front     |23820|21761  |2.0       |4        |200       |24      |31         |2778  |101      |172   |
|ACU8A0D3A80|Acura|TSX 4dr       |Sedan|Asia  |Front     |26990|24647  |2.4       |4        |200       |22      |29         |3230  |105      |183   |
|ACUAAA8AC9F|Acura|TL 4dr        |Sedan|Asia  |Front     |33195|30299  |3.2       |6        |270       |20      |28         |3575  |108      |186   |
|ACU3CC7286B|Acura|3.5 RL 4dr    |Sedan|Asia  |Front     |43755|39014  |3.5       |6        |225       |18      |24         |3880  |115      |197   |
+-----------+-----+--------------+-----+------+----------+-----+-------+----------+---------+----------+--------+-----------+------+---------+------+
only showing top 5 rows

+-----------+------------+--------------+-------------+-------------+-----------------+-------------+
|code       |color_custom|chassis_custom|engine_custom|wheels_custom|suspension_custom|delivery_time|
+-----------+------------+--------------+-------------+-------------+-----------------+-------------+
|ACU38FAFF99|1           |0             |1            |0            |0                |10           |
|ACU3620BF28|0           |1             |1            |1            |1                |3            |
|ACU8A0D3A80|1           |0             |0            |1            |0                |4            |
|ACUAAA8AC9F|1           |0             |1            |1            |1                |8            |
|ACU3CC7286B|0           |1             |1            |0            |0                |6            |
+-----------+------------+--------------+-------------+-------------+-----------------+-------------+
only showing top 5 rows

+-----------+-------------+-------------+-------------+-------------+------------+-------------+
|code       |gt7_available|acc_available|fh5_available|pc2_available|ir_available|rf2_available|
+-----------+-------------+-------------+-------------+-------------+------------+-------------+
|ACU38FAFF99|1            |1            |1            |0            |1           |0            |
|ACU3620BF28|0            |0            |1            |0            |1           |0            |
|ACU8A0D3A80|1            |1            |1            |1            |0           |0            |
|ACUAAA8AC9F|0            |0            |0            |0            |0           |0            |
|ACU3CC7286B|0            |0            |0            |1            |1           |0            |
+-----------+-------------+-------------+-------------+-------------+------------+-------------+
only showing top 5 rows
```
```python
df_cars_data.printSchema()
df_custom_data.printSchema()
df_gaming_data.printSchema()
```
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

root
 |-- code: string (nullable = true)
 |-- color_custom: integer (nullable = true)
 |-- chassis_custom: integer (nullable = true)
 |-- engine_custom: integer (nullable = true)
 |-- wheels_custom: integer (nullable = true)
 |-- suspension_custom: integer (nullable = true)
 |-- delivery_time: integer (nullable = true)

root
 |-- code: string (nullable = true)
 |-- gt7_available: integer (nullable = true)
 |-- acc_available: integer (nullable = true)
 |-- fh5_available: integer (nullable = true)
 |-- pc2_available: integer (nullable = true)
 |-- ir_available: integer (nullable = true)
 |-- rf2_available: integer (nullable = true)
```

As you can see, the schema matches the structure reported in the previous section. There are little differences between numeric types, but they are basically the same.
