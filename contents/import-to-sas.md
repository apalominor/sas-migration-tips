## Importing Data to SAS® OnDemand for Academics
After login, go to the left panel to upload data files. Right click in your user folder and select "Upload Files":

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/3d6e8a86-5bd4-47f8-a6d0-1c054e9841d2)

Select your files and click in "Upload":

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/1cd76242-54ae-43cf-8e8f-7718c2824e72)

You will have your files ready to be imported into SAS:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/d3c4bed7-203d-4846-9676-103a389d69ce)

Now go the "Home" root and create a new folder:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/0d9791ea-150f-41ac-b2fc-fb0ae19bef81)

Assign a name, then "Save" and you will se your folder in the left side:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/30d73b10-7f33-48b0-876b-1affa5993aaf)

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/d41582df-91b5-4be0-846f-a0b41d80a68d)

Go to the "Libraries" tab (bottom left), and select "New Library".

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/2ccc98e5-adeb-4901-b19d-800fe2745a79)

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/79af5e85-90b8-45b2-93fe-600eb0ebb166)
 
Assign a name, select the path to the new folder previously created and check the option "Re-create this library at startup":

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/bd275ca1-5dc1-476b-93e4-29500265423a)

You will see your created library in the left side:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/8fff044d-e4c0-4b5c-98a3-4655f5c02564)

Then, copy and execute this code to load and save "cars_data.txt" data into your library recently created (do not forget to update the `your_sas_userid` variable):

```sas
proc import file = "/home/<your_sas_userid>/sasuser.v94/cars_data.txt" out = cars_data dbms = tab replace;
    delimiter = "|";
    guessingrows = 500;
run;

proc print data = cars_data;
run;

data datalib.cars_data;
    set cars_data;
run;
```

You can get the full paths of your files in the "Properties" window of each one:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/c29fe708-16a9-48d2-ae47-144128ee14c5)

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/93b580e9-2b17-49bd-bba9-55123130d52d)

Repeat the import for pending files:

```sas
proc import file = "/home/<your_sas_userid>/sasuser.v94/customization_data.txt" out = custom_data dbms = tab replace;
    delimiter = "|";
    guessingrows = 500;
run;

proc print data = custom_data;
run;

data datalib.custom_data;
    set custom_data;
run;
```

```sas
proc import file = "/home/<your_sas_userid>/sasuser.v94/gaming_data.txt" out = gaming_data dbms = tab replace;
    delimiter = "|";
    guessingrows = 500;
run;

proc print data = gaming_data;
run;

data datalib.gaming_data;
    set gaming_data;
run;
```

At the end, you will have your datasets ready to be used:

![image](https://github.com/apalominor/sas-migration-guide/assets/126201348/7005d6d9-8af0-4afe-8c4e-4206096df2b9)
