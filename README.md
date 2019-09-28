# Panacea_Lab
Hello Panacea_Lab!!



## Machine Learning reseach paper collection
[https://github.com/MartOfTheNorth/Panacea_Lab/wiki/Welcome-to-the-Panacea_Lab-wiki!](https://github.com/MartOfTheNorth/Panacea_Lab/wiki/Welcome-to-the-Panacea_Lab-wiki!)



## Part 1 - Learning the Postgres
Postgres database

[https://www.postgresql.org/](https://www.postgresql.org/)

PostgreSQL 12 release is made available.
Previous versions:
- 11.5 
- 10.10 
- 9.6.15 
- 9.5.19 
- 9.4.24 

In this project, we will use 9.2.24.

## Part 2 - Create MIMIC-III in a local Postgres database
- Reference:
  - PostgreSQL loading scripts:
  - [https://github.com/MIT-LCP/mimic-code/tree/master/buildmimic/postgres](https://github.com/MIT-LCP/mimic-code/tree/master/buildmimic/postgres)


- Procedures:
  - Installing MIMIC-III in a local Postgres database
  - [https://mimic.physionet.org/tutorials/install-mimic-locally-ubuntu/](https://mimic.physionet.org/tutorials/install-mimic-locally-ubuntu/)

### Part 2.1 Panacea lab procedure  (6 hours)
    - $ psql -U mimicuser -d mimic1
    - => \c mimic1;
    - => set search_path to mimic1;
    - => SELECT schema_name FROM information_schema.schemata ; 
    - => SELECT schema_name FROM information_schema.schemata WHERE schema_name = 'mimic1';
    - => CREATE SCHEMA mimic1;
    - $ git clone https://github.com/MIT-LCP/mimic-code.git
    - $ cd /mimic-code/buildmimic/postgres
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1' -f postgres_create_tables.sql
    - $ cd /mnt/local/hdd/mimicIII/1.4
    - $ cp /mnt/local/hdd/mimicIII/1.4/* /mnt/local/hdd/mimicIII/1.4unzip/
    - $ gunzip *.gz 
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1' -f postgres_load_data.sql -v mimic_data_dir='/mnt/local/hdd/mimicIII/1.4unzip/'  > /output20190918.log 2>&1 &
    - $  nohup  psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1' -f postgres_load_data.sql -v mimic_data_dir='/mnt/local/hdd/mimicIII/1.4unzip/'  &>>  /home/mart/output20190918.log  &
    - $ tail -f  output20190918.log  
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1'  -f postgres_checks.sql 



### Part 2.2 MIMIC-III is ready (3 minutes)
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1'  -f postgres_checks.sql 
    - => grant select on all tables in schema mimic3 to mart;
    - => grant usage on schema mimic3 to mart;
    - => grant connect on database mimic3 to mart;
    - => alter user mart nosuperuser;
    - => select count(subject_id)
    - -> from mimic3.patients;
    - => select count(subject_id)    
    - -> from patients; 


```
[mimicuser@deepml postgres]$  psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1'  -f postgres_checks.sql 
        tbl         | expected_count | observed_count | row_count_check 
--------------------+----------------+----------------+-----------------
 admissions         |          58976 |          58976 | PASSED
 callout            |          34499 |          34499 | PASSED
 caregivers         |           7567 |           7567 | PASSED
 chartevents        |      330712483 |      661424966 | FAILED
 cptevents          |         573146 |         573146 | PASSED
 datetimeevents     |        4485937 |        4485937 | PASSED
 d_cpt              |            134 |            134 | PASSED
 diagnoses_icd      |         651047 |         651047 | PASSED
 d_icd_diagnoses    |          14567 |          14567 | PASSED
 d_icd_procedures   |           3882 |           3882 | PASSED
 d_items            |          12487 |          12487 | PASSED
 d_labitems         |            753 |            753 | PASSED
 drgcodes           |         125557 |         125557 | PASSED
 icustays           |          61532 |          61532 | PASSED
 inputevents_cv     |       17527935 |       17527935 | PASSED
 inputevents_mv     |        3618991 |        3618991 | PASSED
 labevents          |       27854055 |       27854055 | PASSED
 microbiologyevents |         631726 |         631726 | PASSED
 noteevents         |        2083180 |        2083180 | PASSED
 outputevents       |        4349218 |        4349218 | PASSED
 patients           |          46520 |          46520 | PASSED
 prescriptions      |        4156450 |        4156450 | PASSED
 procedureevents_mv |         258066 |         258066 | PASSED
 procedures_icd     |         240095 |         240095 | PASSED
 services           |          73343 |          73343 | PASSED
 transfers          |         261897 |         261897 | PASSED
(26 rows)

[mimicuser@deepml postgres]$ 
```
### Part 2.3 Continue improving MIMIC-III database (30 minutes)
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1' -f postgres_add_indexes.sql 


## Part 3 Mapping the MIMIC-III database to the OMOP schema 
    - References:
    - MIMIC-OMOP
      - Mapping the MIMIC-III database to the OMOP schema
      - [https://github.com/MIT-LCP/mimic-omop](https://github.com/MIT-LCP/mimic-omop)

    - OMOP Common Data Model 
      - Definition and DDLs for the OMOP Common Data Model (CDM) 
      - [https://github.com/OHDSI/CommonDataModel](https://github.com/OHDSI/CommonDataModel)      
     
    - mimic-omop/README-run-etl.md
      - Running the ETL on PostgreSQL
      - [https://github.com/MIT-LCP/mimic-omop/blob/master/README-run-etl.md](https://github.com/MIT-LCP/mimic-omop/blob/master/README-run-etl.md)  

    - MIT-LCP/mimic-omop
      - Build OMOP tables with standard concepts
      - [https://github.com/MIT-LCP/mimic-omop/blob/master/omop/build-omop/postgresql/README.md](https://github.com/MIT-LCP/mimic-omop/blob/master/omop/build-omop/postgresql/README.md)           
 
     - Download the vocabulary
      - ATHENA standardized vocabularies
      - [https://www.ohdsi.org/analytic-tools/athena-standardized-vocabularies/ ](https://www.ohdsi.org/analytic-tools/athena-standardized-vocabularies/ )            

     - Maven
      - Install Apache Maven on Linux
      - [https://www.javahelps.com/2017/10/install-apache-maven-on-linux.html](https://www.javahelps.com/2017/10/install-apache-maven-on-linux.html)   



#### Part 3.1 Panacea lab procedure 
    -#Set environment (3 minutes)
    - $ postgres -V
    - $ export OMOP_SCHEMA='omop'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'    
    - $ sudo apt-get install pgtap
    -#Build OMOP  (3 minutes)
    - $ git clone git@github.com:MIT-LCP/mimic-omop.git
    - $ git clone https://github.com/MIT-LCP/mimic-omop.git
    - $ cd mimic-omop/
    - $ git clone https://github.com/OHDSI/CommonDataModel.git
    - $ cd CommonDataModel
    - $ git reset --hard 0ac0f4bd56c7372dcd3417461a91f17a6b118901
    - $ cd ..
    - $ cp CommonDataModel/PostgreSQL/*.txt omop/build-omop/postgresql/
    - $ sed -i 's/^CREATE TABLE \([a-z_]*\)/CREATE UNLOGGED TABLE \1/' "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    -#Define PSQL connection
    - $ export OMOP_SCHEMA='omop'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - # no need$ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    -#Build omop schema (1 minute)
    - $ /home/mart/mimic-omop
    - $ psql "$OMOP" -c "DROP SCHEMA IF EXISTS $OMOP_SCHEMA CASCADE;"
    - $ psql "$OMOP" -c "CREATE SCHEMA $OMOP_SCHEMA;"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    -#Alter (1 minute)
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/mimic-omop-alter.sql"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_cdm_comments.sql"
    -#To import the vocabulary. Copy from /home/large_data/OHDSI_vocabulary-May2019 to /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019 (6 minutes)
    - $ ls -l /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019
    - $ cd /home/mart/mimic-omop
    - $ ln -s /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019  extras/athena
    - $ nohup  psql "$OMOP" -f "omop/build-omop/postgresql/omop_vocab_load.sql"  >>  /home/mart/output20190923.log  &
    - $ cd /home/mart/mimic-omop
    - $ psql "$MIMIC" -f mimic/build-mimic/postgres_create_mimic_id.sql
    -#Create local MIMIC-III concepts  (60 minutes)
    - $ cd /home/mart/mimic-omop
    - psql "$MIMIC" -f mimic/build-mimic/postgres_create_mimic_id.sql
    -#Load the concepts from the CSV files (3 minutes)
    - $ cd /home/mart/mimic-omop    
    - $ vi mimic-omop.cfg 
    - $ R
    - > install.packages(c("RPostgres"))
    - $ Rscript etl/ConceptTables/loadTables.R mimiciii
    -#Run the ETL 
    - $ cd /home/mart/mimic-omop       
    - $ set search_path to mimic1;
    - $ export MIMIC='host=localhost dbname=mimic1 user=username options=--search_path=mimic1'
    - psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql"
    -
    -#Check the ETL has run properly
    - psql "$MIMIC" -c "CREATE EXTENSION pgtap;"
    - psql "$MIMIC" -f "etl/check_etl.sql"
    - 
    -
    -
    -
    -
    -
    - 
    -#(Optional) Indexes may slow down importing of data - so you may want to only build these after running the full ETL.
    -psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt"
    -psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql constraints.txt"
