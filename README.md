# sqlite_database_operations
HHA 504 Assignment 3


# Dataset info:
df1 is a dataset from Stony Brook Hospital found here https://www.cdmpricing.com/952453295c1ee89ee3ba06e6e42496e5/shoppable-services
df2 is a dataset from NewYork-Presbyterian found here https://www.nyp.org/patients-visitors/paying-for-care/hospital-price-transparency/standard-charges

# Installing and importing
First you would need to install these packages
! pip install https://github.com/pandas-profiling/pandas-profiling/archive/master.zip
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from pandas_profiling import ProfileReport
from sqlalchemy import create_engine
import sqlite3

Then load in the datasets
df1 = pd.read_csv('https://raw.githubusercontent.com/johncduran/sqlite_database_operations/main/datasets/113243405_StonyBrookUniversityHospital_standardcharges.csv')
df2 = pd.read_csv('https://raw.githubusercontent.com/johncduran/sqlite_database_operations/main/datasets/133957095_NewYorkPresbyterianHospital_standardcharges.csv')
df2

## 1. Data Exploration and Analysis:
I used the following functions to do analysis on both datasets:

.describe()

Q1 = df1.quantile(0.25)
Q3 = df1.quantile(0.75)
IQR = Q3 - Q1
outliers = ((df1 < (Q1 - 1.5 * IQR)) | (df1 > (Q3 + 1.5 * IQR))).any(axis=1)

outliers

.info

.value_counts()

## 2. SQLite Database Operations:
I used the following code to connect to sqlite3 and create a sql database within the notebook.

conn = sqlite3.connect('health.db')
c = conn.cursor()

I then created a table manually and at the same time creating column names:
c.execute("""
            CREATE TABLE catholichealth
                (
                    hospital_name text,
                    insurance_type text,
                    patient_first_name text,
                    patient_last_name text,
                    blood_pressure_systolic real,
                    blood_pressure_diastolic real
                );
          """)

conn.commit()

Then you can check if the table was created using this code:
c.execute('''
  SELECT name
  FROM sqlite_master
  WHERE type='table';
  ''')

c.fetchall()

You then need to insert some values into the columns since currently they are empty:
sql_query = """

INSERT INTO catholichealth (
    'hospital_name',
    'insurance_type',
    'patient_first_name',
    'patient_last_name',
    'blood_pressure_systolic',
    'blood_pressure_diastolic'
  )
  values (
    'good samaritan hospital',
    'fidelis',
    'john',
    'doe',
    120,
    80
  );

"""

print(sql_query)

Commit the changes to the .db:

c.execute(sql_query)
conn.commit()

Finally you should be able to see that the values were in fact insterted in using the follwing: 
sql_query_2 = """

select *
from catholichealth;

"""

c.execute(sql_query_2)
print(c.fetchall())