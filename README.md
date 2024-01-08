# Migrating SAS Processes to PySpark
## Scenario
As title said, this space is trying to be a guide when there is a situation when you have to work in a migration from **SAS**. In this case, the destination language will be **Pyspark with DataFrame API**. The idea is to replicate all the logic from some cases, working and considering the special functions that (sometimes) SAS has for managing data.

## Experience
It's recommended to have at least basic experience or knowledge about Python/Pyspark to understand faster the guide. Also, if you have experience in SAS, even better (although the code is explained in each case).

## Environment
- For SAS: *SAS® OnDemand for Academics* - https://welcome.oda.sas.com/
- For Pyspark: *Google Colab* - https://colab.research.google.com/

Both sites are free to use (only with registration) and are avilable to use for learning.

For Google Colab, **DO NOT** forget to execute these previous steps before executing anything. In this case, they are for Spark installation and initialization.

```python
# Installing pyspark
!pip3 install pyspark

# Connecting spark
import pyspark
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
```

## Used Data
We will be using data from SAS® dataset *cars*, that belongs to library *SASHELP*, with little additional modification: we are adding a code (unique identifier) for each row. In this case, the file with the data is available in the repository to use also with Google Colab. The file name is **cars_data.txt**. We will be using also some complementary data to understang all scenarios:
- **customization_data.txt**: Data about allowed upgrades for cars. Also we have there information about delivery time.
- **gaming_data.txt**: Data about availability in console and PC videogames.

Here you have some additional information about the main dataset:

| **Column** | **Datatype** | **Length** | **Additional Information** |
| --- | --- | --- | --- |
| `code` | Character | 11 | Unique identifier |
| `make` | Character | 13 | . |
| `model` | Character | 40 | . |
| `type` | Character | 8 | . |
| `origin` | Character | 6 | . |
| `drivetrain` | Character | 5 | . |
| `msrp` | Numeric | 8 | Manufacturer Suggested Retail Price. Value in dollars ($). |
| `invoice` | Numeric | 8 | Value in dollars ($) |
| `enginesize` | Numeric | 8 | Value in liters |
| `cylinders` | Numeric | 8 | . |
| `horsepower` | Numeric | 8 | . |
| `mpg_city` | Numeric | 8 | Miles per Gallon in city |
| `mpg_highway` | Numeric | 8 | Miles per Gallon in highway |
| `weight` | Numeric | 8 | Value in pounds |
| `wheelbase` | Numeric | 8 | Value in inches |
| `length` | Numeric | 8 | Value in inches |

And here you have additional information about complementary data:

*customization_data.txt*
| **Column** | **Datatype** | **Length** | **Additional Information** |
| --- | --- | --- | --- |
| `code` | Character | 11 | Unique identifier. Related to *cars_data.txt* code. |
| `color_custom` | Numeric | 1 | Indicator if color can be customized. 1 = Yes - 0 = No |
| `chassis_custom` | Numeric | 1 | Indicator if chassis can be customized. 1 = Yes - 0 = No |
| `engine_custom` | Numeric | 1 | Indicator if engine can be customized. 1 = Yes - 0 = No |
| `wheels_custom` | Numeric | 1 | Indicator if wheels can be customized. 1 = Yes - 0 = No |
| `suspension_custom` | Numeric | 1 | Indicator if suspension can be customized. 1 = Yes - 0 = No |
| `delivery_time` | Numeric | 3 | Estimated delivery time for the car. Time in weeks. |

*gaming_data.txt*
| **Column** | **Datatype** | **Length** | **Additional Information** |
| --- | --- | --- | --- |
| `code` | Character | 11 | Unique identifier. Related to *cars_data.txt* code. |
| `gt7_available` | Numeric | 1 | Indicator if the car is available in the *Gran Turismo 7®* videogame. 1 = Yes - 0 = No |
| `acc_available` | Numeric | 1 | Indicator if the car is available in the *Assetto Corsa Competizione®* videogame. 1 = Yes - 0 = No |
| `fh5_available` | Numeric | 1 | Indicator if the car is available in the *Forza Horizon 5®* videogame. 1 = Yes - 0 = No |
| `pc2_available` | Numeric | 1 | Indicator if the car is available in the *Project Cars 2®* videogame. 1 = Yes - 0 = No |
| `ir_available` | Numeric | 1 | Indicator if the car is available in the *iRacing®* videogame. 1 = Yes - 0 = No |
| `rf2_available` | Numeric | 1 | Indicator if the car is available in the *rFactor2®* videogame. 1 = Yes - 0 = No |

## Index of Contents

| **Item** | **Name** | **Link**
| --- | --- | ---
| 1 | Importing Data to *SAS® OnDemand for Academics* | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/import-to-sas.md)
| 2 | Importing Data to *Google Colab* | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/importing-to-colab.md)
| 3 | Case 001: Adding New Column to Dataset | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-001.md)
| 4 | Case 002: Filtering Data and Selecting Columns (KEEP Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-002.md)
| 5 | Case 003: Filtering Data and Selecting Columns (DROP Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-003.md)
| 6 | Case 004: Counting Rows by Group, Ordering and Getting Top 10 Rows | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-004.md)
| 7 | Case 005: Deleting Rows from Dataset with Condition | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-005.md)
| 8 | Case 006: Getting Statistics for Columns (PROC MEANS/PROC FREQ) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-006.md)
| 9 | Case 007: Getting Metadata for Dataset (PROC CONTENTS) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-007.md)
| 10 | Case 008: Ordering Data in Dataset and Getting Top N Rows (PROC SORT BY Option / KEY Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-008.md)
| 11 | Case 009: Deleting Duplicates in Dataset (PROC SORT NODUPKEY Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-009.md)
| 12 | Case 010: Appending Datasets (PROC APPEND/Data Step SET Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-010.md)
| 13 | Case 011: Appending Datasets with Different Columns (PROC APPEND/Data Step SET Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-011.md)
| 14 | Case 012: Creating a Rownum in a Dataset | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-012.md)
| 15 | Case 013: Creating a Rownum in a Dataset (by GROUPS) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-013.md)
| 16 | Case 014: Creating a Dataset Using SQL | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-014.md)
| 17 | Case 015: Joining Datasets and Count Results | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-015.md)
| 18 | Case 016: Joining Filtered Datasets and Transform Column Results (IF/IF-ELSE Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-016.md)
| 19 | Case 017: Categorizing and Counting Group Data (SELECT-WHEN Option) | [Go](https://github.com/apalominor/sas-migration-guide/blob/main/contents/case-017.md)
