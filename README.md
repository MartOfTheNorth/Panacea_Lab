# Panacea_Lab
Hello Panacea_Lab!!

## Lab Rules
- ALL lab work MUST be done in the server. 
- We are on protected data and data can not take out of the lab server. 


## Machine Learning reseach paper collection
[https://github.com/MartOfTheNorth/Panacea_Lab/wiki/Welcome-to-the-Panacea_Lab-wiki!](https://github.com/MartOfTheNorth/Panacea_Lab/wiki/Welcome-to-the-Panacea_Lab-wiki!)

## Contents
- Part 1 : PostgreSQL       
- Part 2 : MIMIC-III import 
- Part 3 : OMOP mapping     
- Part 4 : OHDSI - APHRODITE    

# Part 1 - Learning Postgres
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

# Part 2 - Create MIMIC-III in a local Postgres database
- Reference:
  - PostgreSQL loading scripts:
  - [https://github.com/MIT-LCP/mimic-code/tree/master/buildmimic/postgres](https://github.com/MIT-LCP/mimic-code/tree/master/buildmimic/postgres)


- Procedures:
  - Installing MIMIC-III in a local Postgres database
  - [https://mimic.physionet.org/tutorials/install-mimic-locally-ubuntu/](https://mimic.physionet.org/tutorials/install-mimic-locally-ubuntu/)

## Part 2.1 Lab procedure  (6 hours)
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



## Part 2.2 MIMIC-III database validation (3 minutes)
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
 chartevents        |      330712483 |      330712483 | PASSED
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
## Part 2.3 Adding indexes for MIMIC-III database (30 minutes)
    - $ psql 'dbname=mimic1 user=mimicuser options=--search_path=mimic1' -f postgres_add_indexes.sql 


# Part 3 Mapping the MIMIC-III database to the OMOP schema 
- References:  
  - MIMIC-OMOP:
    - Mapping the MIMIC-III database to the OMOP schema.
    - [https://github.com/MIT-LCP/mimic-omop](https://github.com/MIT-LCP/mimic-omop)
          
  - OMOP Common Data Model Version 5.3:
    - Definition and DDLs for the OMOP Common Data Model (CDM) 
    - [https://github.com/OHDSI/CommonDataModel/tree/v5.3.0](https://github.com/OHDSI/CommonDataModel/tree/v5.3.0)      
     
   - mimic-omop/README-run-etl.md:
     - Running the ETL on PostgreSQL:
     - [https://github.com/MIT-LCP/mimic-omop/blob/master/README-run-etl.md](https://github.com/MIT-LCP/mimic-omop/blob/master/README-run-etl.md)  

   - MIT-LCP/mimic-omop:
     - Build OMOP tables with standard concepts
     - [https://github.com/MIT-LCP/mimic-omop/blob/master/omop/build-omop/postgresql/README.md](https://github.com/MIT-LCP/mimic-omop/blob/master/omop/build-omop/postgresql/README.md)           
 
    - Download the vocabulary:
      - ATHENA standardized vocabularies
      - [https://www.ohdsi.org/analytic-tools/athena-standardized-vocabularies/ ](https://www.ohdsi.org/analytic-tools/athena-standardized-vocabularies/ )            

    - Maven (Not required for this lab):
      - Install Apache Maven on Linux
      - [https://www.javahelps.com/2017/10/install-apache-maven-on-linux.html](https://www.javahelps.com/2017/10/install-apache-maven-on-linux.html)   


## Part 3.1 Set environment (3 minutes)
    - $ postgres -V
    - $ export OMOP_SCHEMA='omop1'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'    
    - $ sudo apt-get install pgtap
    
## Part 3.2 Pulling OMOP and CDM v5.3 from github  (3 minutes)
    - $ rm -rf mimic-omop
    - $ git clone git@github.com:MIT-LCP/mimic-omop.git
    - $ git clone https://github.com/MIT-LCP/mimic-omop.git
    - $ cd mimic-omop/
    -#Clone Common Data Model v5.3 (or version master)
    - $ git clone https://github.com/OHDSI/CommonDataModel/tree/v5.3.0    (unavilable)
    - $ git clone https://github.com/OHDSI/CommonDataModel.git
    - $ cd CommonDataModel
    - $ git reset --hard 0ac0f4bd56c7372dcd3417461a91f17a6b118901
    - $ cd ..
    - $ cp CommonDataModel/PostgreSQL/*.txt omop/build-omop/postgresql/
    - $ sed -i 's/^CREATE TABLE \([a-z_]*\)/CREATE UNLOGGED TABLE \1/' "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    
## Part 3.3 Build omop schema (2 minutes)  (If new omop is needed, repeat here.)
    -#Set environment
    - $ export OMOP_SCHEMA='omop1'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    -#Build omop schema (1 minute)
    - $ cd /home/mart/mimic-omop
    - $ set search_path to mimic1;
    - $ psql "$OMOP" -c "DROP SCHEMA IF EXISTS $OMOP_SCHEMA CASCADE;"
    - $ psql "$OMOP" -c "CREATE SCHEMA $OMOP_SCHEMA;"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    -#Alter (1 minute)
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/mimic-omop-alter.sql"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_cdm_comments.sql"
    -# ()    
    
## Part 3.4 Import the vocabulary (3 minutes)
    -#To import the vocabulary. Note: In this lab, instead of compiling new Vocal, copy from /home/large_data/OHDSI_vocabulary-May2019 to /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019 (3 minutes)
    - $ ls -l /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019
    - $ cd /home/mart/mimic-omop
    - $ ln -s /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019  extras/athena
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_vocab_load.sql"  >>  /home/mart/output20191006.log    (3 minutes)

## Part 3.5 Create indexes on the vocabulary (90 minutes)
    -#Very important to have indexes for vacabulary and MIMIC-III. It will reduce from weeks to 5 days of ETL tasks. 
    -#MIMIC-III indexing was done in Part 2.3. This is step is for indexing vocabulary.
    -#Run only indexes and constraints of Standardized vocabulary.
    -#Reference to  /mimic-omop/omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt"
    - $ cd /home/mart/mimic-omop    
    - $ vi omop/build-omop/postgresql/OMOP_vocabulary_indexes.txt
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP_vocabulary_indexes.txt"  >>  /home/mart/output20191012.log &
 ```
$ cat omop/build-omop/postgresql/OMOP_vocabulary_indexes.txt
/************************
*************************
*************************
*************************

Primary key constraints

*************************
*************************
*************************
************************/



/************************

Standardized vocabulary

************************/

ALTER TABLE concept ADD CONSTRAINT xpk_concept PRIMARY KEY (concept_id);
ALTER TABLE vocabulary ADD CONSTRAINT xpk_vocabulary PRIMARY KEY (vocabulary_id);
ALTER TABLE domain ADD CONSTRAINT xpk_domain PRIMARY KEY (domain_id);
ALTER TABLE concept_class ADD CONSTRAINT xpk_concept_class PRIMARY KEY (concept_class_id);
ALTER TABLE concept_relationship ADD CONSTRAINT xpk_concept_relationship PRIMARY KEY (concept_id_1,concept_id_2,relationship_id);
ALTER TABLE relationship ADD CONSTRAINT xpk_relationship PRIMARY KEY (relationship_id);
ALTER TABLE concept_ancestor ADD CONSTRAINT xpk_concept_ancestor PRIMARY KEY (ancestor_concept_id,descendant_concept_id);
ALTER TABLE source_to_concept_map ADD CONSTRAINT xpk_source_to_concept_map PRIMARY KEY (source_vocabulary_id,target_concept_id,source_code,valid_end_date);
ALTER TABLE drug_strength ADD CONSTRAINT xpk_drug_strength PRIMARY KEY (drug_concept_id, ingredient_concept_id);
ALTER TABLE cohort_definition ADD CONSTRAINT xpk_cohort_definition PRIMARY KEY (cohort_definition_id);
ALTER TABLE attribute_definition ADD CONSTRAINT xpk_attribute_definition PRIMARY KEY (attribute_definition_id);

/************************
*************************
*************************
*************************

Indices

*************************
*************************
*************************
************************/

/************************

Standardized vocabulary

************************/

CREATE UNIQUE INDEX  idx_concept_concept_id  ON  concept  (concept_id ASC);
CLUSTER  concept  USING  idx_concept_concept_id ;
CREATE INDEX idx_concept_code ON concept (concept_code ASC);
CREATE INDEX idx_concept_vocabluary_id ON concept (vocabulary_id ASC);
CREATE INDEX idx_concept_domain_id ON concept (domain_id ASC);
CREATE INDEX idx_concept_class_id ON concept (concept_class_id ASC);
CREATE UNIQUE INDEX  idx_vocabulary_vocabulary_id  ON  vocabulary  (vocabulary_id ASC);
CLUSTER  vocabulary  USING  idx_vocabulary_vocabulary_id ;
CREATE UNIQUE INDEX  idx_domain_domain_id  ON  domain  (domain_id ASC);
CLUSTER  domain  USING  idx_domain_domain_id ;
CREATE UNIQUE INDEX  idx_concept_class_class_id  ON  concept_class  (concept_class_id ASC);
CLUSTER  concept_class  USING  idx_concept_class_class_id ;
CREATE INDEX idx_concept_relationship_id_1 ON concept_relationship (concept_id_1 ASC); 
CREATE INDEX idx_concept_relationship_id_2 ON concept_relationship (concept_id_2 ASC); 
CREATE INDEX idx_concept_relationship_id_3 ON concept_relationship (relationship_id ASC); 
CREATE UNIQUE INDEX  idx_relationship_rel_id  ON  relationship  (relationship_id ASC);
CLUSTER  relationship  USING  idx_relationship_rel_id ;
CREATE INDEX  idx_concept_synonym_id  ON  concept_synonym  (concept_id ASC);
CLUSTER  concept_synonym  USING  idx_concept_synonym_id ;
CREATE INDEX  idx_concept_ancestor_id_1  ON  concept_ancestor  (ancestor_concept_id ASC);
CLUSTER  concept_ancestor  USING  idx_concept_ancestor_id_1 ;
CREATE INDEX idx_concept_ancestor_id_2 ON concept_ancestor (descendant_concept_id ASC);
CREATE INDEX  idx_source_to_concept_map_id_3  ON  source_to_concept_map  (target_concept_id ASC);
CLUSTER  source_to_concept_map  USING  idx_source_to_concept_map_id_3 ;
CREATE INDEX idx_source_to_concept_map_id_1 ON source_to_concept_map (source_vocabulary_id ASC);
CREATE INDEX idx_source_to_concept_map_id_2 ON source_to_concept_map (target_vocabulary_id ASC);
CREATE INDEX idx_source_to_concept_map_code ON source_to_concept_map (source_code ASC);
CREATE INDEX  idx_drug_strength_id_1  ON  drug_strength  (drug_concept_id ASC);
CLUSTER  drug_strength  USING  idx_drug_strength_id_1 ;
CREATE INDEX idx_drug_strength_id_2 ON drug_strength (ingredient_concept_id ASC);
CREATE INDEX  idx_cohort_definition_id  ON  cohort_definition  (cohort_definition_id ASC);
CLUSTER  cohort_definition  USING  idx_cohort_definition_id ;
CREATE INDEX  idx_attribute_definition_id  ON  attribute_definition  (attribute_definition_id ASC);
CLUSTER  attribute_definition  USING  idx_attribute_definition_id ;
 
 ```

## Part 3.6 Create local MIMIC-III concepts (30 minutes)
    -#Creating local MIMIC-III concepts is needed one time in MIMIC3. Extra omop won't need this step. (5 minutes)
    - $ cd /home/mart/mimic-omop
    - $ psql "$MIMIC" -f mimic/build-mimic/postgres_create_mimic_id.sql   

    -#Load the concepts from the CSV files (25 minutes)
    - $ cd /home/mart/mimic-omop    
    - $ vi mimic-omop.cfg 
    - dbname=mimic1
    - user=mimicuser
    - $ R
    - > install.packages(c("RPostgres"))
    - > quit("yes")
    - $ Rscript etl/ConceptTables/loadTables.R mimiciii
    
## Part 3.7 Run the ETL  (4 days)
    -#Set environment
    - $ cd /home/mart/mimic-omop       
    - $ set search_path to mimic1;
    - $ export OMOP_SCHEMA='omop'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    -#Run ETL
    - $ psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql"
    - $ psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql" >>  /home/mart/output20191006.log     

    -#Note: If feeding a big pile of SQL to psql then add \echo into "etl/etl.sql" to help tracking as below;
```
--BEGIN;
\echo 'BEGIN'
--echo-all;
\set ON_ERROR_STOP true
\timing
\echo 'TRUNCATE'

TRUNCATE TABLE  :OMOP_SCHEMA.care_site CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.cohort_definition CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.cohort_attribute CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.attribute_definition CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.person CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.death CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.visit_occurrence CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.observation_period CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.visit_detail CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.procedure_occurrence CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.provider CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.condition_occurrence CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.observation CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.drug_exposure CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.measurement CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.specimen CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.note CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.note_nlp CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.fact_relationship CASCADE;
TRUNCATE TABLE  :OMOP_SCHEMA.dose_era CASCADE;
\echo 'Complete TRUNCATE'

\echo 'Start etl'

--\i omop/build-omop/postgresql/mimic-omop-disable-trigger.sql
\echo 'Start etl/pg_function.sql'
\i etl/pg_function.sql
\echo 'etl/StandardizedVocabularies/CONCEPT/etl.sql'
\i etl/StandardizedVocabularies/CONCEPT/etl.sql
\echo 'etl/StandardizedHealthSystemDataTables/CARE_SITE/etl.sql'
\i etl/StandardizedHealthSystemDataTables/CARE_SITE/etl.sql
\echo 'etl/StandardizedHealthSystemDataTables/PROVIDER/etl.sql'
\i etl/StandardizedHealthSystemDataTables/PROVIDER/etl.sql
\echo 'etl/StandardizedClinicalDataTables/PERSON/etl.sql'
\i etl/StandardizedClinicalDataTables/PERSON/etl.sql
\echo 'etl/StandardizedClinicalDataTables/DEATH/etl.sql'
\i etl/StandardizedClinicalDataTables/DEATH/etl.sql
\echo 'etl/StandardizedClinicalDataTables/VISIT_OCCURRENCE/etl.sql'
\i etl/StandardizedClinicalDataTables/VISIT_OCCURRENCE/etl.sql
\echo 'etl/StandardizedClinicalDataTables/OBSERVATION_PERIOD/etl.sql'
\i etl/StandardizedClinicalDataTables/OBSERVATION_PERIOD/etl.sql
\echo 'etl/StandardizedClinicalDataTables/VISIT_DETAIL/etl.sql'
\i etl/StandardizedClinicalDataTables/VISIT_DETAIL/etl.sql
\echo 'etl/StandardizedClinicalDataTables/NOTE/etl.sql'
\i etl/StandardizedClinicalDataTables/NOTE/etl.sql
\echo 'etl/StandardizedClinicalDataTables/PROCEDURE_OCCURRENCE/etl.sql'
\i etl/StandardizedClinicalDataTables/PROCEDURE_OCCURRENCE/etl.sql
\echo 'etl/StandardizedClinicalDataTables/CONDITION_OCCURRENCE/etl.sql'
\i etl/StandardizedClinicalDataTables/CONDITION_OCCURRENCE/etl.sql

\echo '#####################DRUG_EXPOSURE = 60 hours##########################'
\echo 'etl/StandardizedClinicalDataTables/DRUG_EXPOSURE/etl.sql'
\i etl/StandardizedClinicalDataTables/DRUG_EXPOSURE/etl.sql
\echo '#############################################################'

\echo 'etl/StandardizedClinicalDataTables/OBSERVATION/etl.sql'
\i etl/StandardizedClinicalDataTables/OBSERVATION/etl.sql

\echo '#####################MEASUREMENT/ = 6 hours##########################'
\echo 'etl/StandardizedClinicalDataTables/MEASUREMENT/etl.sql'
\i etl/StandardizedClinicalDataTables/MEASUREMENT/etl.sql
\echo '#############################################################'

\echo 'etl/StandardizedClinicalDataTables/SPECIMEN/etl.sql'
\i etl/StandardizedClinicalDataTables/SPECIMEN/etl.sql
\echo 'tl/StandardizedDerivedElements/DOSE_ERA/etl.sql'
\i etl/StandardizedDerivedElements/DOSE_ERA/etl.sql
\echo 'omop/build-omop/postgresql/mimic-omop-enable-trigger.sql'
--\i omop/build-omop/postgresql/mimic-omop-enable-trigger.sql
\echo 'ROLLBACK'
--ROLLBACK;
--COMMIT;
\echo 'COMMIT'
COMMIT;
\echo 'The END.'
```

    -#Check the ETL has run properly
    - psql "$MIMIC" -c "CREATE EXTENSION pgtap;"
    - psql "$MIMIC" -f "etl/check_etl.sql"

    -#(Optional) Indexes may slow down importing of data - so you may want to only build these after running the full ETL.
    - $ cd /home/mart/mimic-omop    
    -## Refer to Part 3.9
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt" >> output20190930omopindex.log  &
    -## Refer to Part 3.10
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql constraints.txt" >> output20190930cdmcon.log  &


### Part 3.8 Validation - MIMIC-III database to the OMOP schema   
    -#Check number of row of all tables in schema (2 minutes)
```
select table_schema, 
       table_name, 
       (xpath('/row/cnt/text()', xml_count))[1]::text::int as row_count
from (
  select table_name, table_schema, 
         query_to_xml(format('select count(*) as cnt from %I.%I', table_schema, table_name), false, true, '') as xml_count
  from information_schema.tables 
    ) t   
order by table_schema, table_name , row_count;
```
    -#Expected output
    
```
    table_schema    |                 table_name                  | row_count 
--------------------+---------------------------------------------+-----------
 mimic3             | admissions                                  |     58976
 mimic3             | callout                                     |     34499
 mimic3             | caregivers                                  |      7567
 mimic3             | chartevents                                 | 330712483
 mimic3             | chartevents_1                               |  18386320
 mimic3             | chartevents_10                              |  19591030
 mimic3             | chartevents_11                              |  18164226
 mimic3             | chartevents_12                              |  20501149
 mimic3             | chartevents_13                              |  21704145
 mimic3             | chartevents_14                              |  19407331
 mimic3             | chartevents_15                              |  20452747
 mimic3             | chartevents_16                              |  20194567
 mimic3             | chartevents_17                              |  10721694
 mimic3             | chartevents_2                               |  19647241
 mimic3             | chartevents_3                               |  20928152
 mimic3             | chartevents_4                               |  19129634
 mimic3             | chartevents_5                               |  21679452
 mimic3             | chartevents_6                               |  17612000
 mimic3             | chartevents_7                               |  20703171
 mimic3             | chartevents_8                               |  21889411
 mimic3             | chartevents_9                               |  20000213
 mimic3             | cptevents                                   |    573146
 mimic3             | datetimeevents                              |   4485937
 mimic3             | d_cpt                                       |       134
 mimic3             | diagnoses_icd                               |    651047
 mimic3             | d_icd_diagnoses                             |     14567
 mimic3             | d_icd_procedures                            |      3882
 mimic3             | d_items                                     |     12487
 mimic3             | d_labitems                                  |       753
 mimic3             | drgcodes                                    |    125557
 mimic3             | gcpt_admission_location_to_concept          |         9
 mimic3             | gcpt_admissions_diagnosis_to_concept        |      1523
 mimic3             | gcpt_admission_type_to_concept              |         4
 mimic3             | gcpt_atb_to_concept                         |        30
 mimic3             | gcpt_care_site                              |        30
 mimic3             | gcpt_chart_label_to_concept                 |       319
 mimic3             | gcpt_chart_observation_to_concept           |        57
 mimic3             | gcpt_continuous_unit_carevue                |        12
 mimic3             | gcpt_cpt4_to_concept                        |        23
 mimic3             | gcpt_cv_input_label_to_concept              |      2782
 mimic3             | gcpt_datetimeevents_to_concept              |       155
 mimic3             | gcpt_derived_to_concept                     |         8
 mimic3             | gcpt_discharge_location_to_concept          |        17
 mimic3             | gcpt_drgcode_to_concept                     |      1355
 mimic3             | gcpt_ethnicity_to_concept                   |        41
 mimic3             | gcpt_heart_rhythm_to_concept                |        47
 mimic3             | gcpt_inputevents_drug_to_concept            |       108
 mimic3             | gcpt_insurance_to_concept                   |         5
 mimic3             | gcpt_lab_label_to_concept                   |       118
 mimic3             | gcpt_labs_from_chartevents_to_concept       |        80
 mimic3             | gcpt_labs_specimen_to_concept               |        17
 mimic3             | gcpt_lab_unit_to_concept                    |        71
 mimic3             | gcpt_lab_value_to_concept                   |      3703
 mimic3             | gcpt_map_route_to_concept                   |        28
 mimic3             | gcpt_marital_status_to_concept              |         7
 mimic3             | gcpt_microbiology_specimen_to_concept       |        88
 mimic3             | gcpt_mv_input_label_to_concept              |       159
 mimic3             | gcpt_note_category_to_concept               |        15
 mimic3             | gcpt_note_section_to_concept                |      1113
 mimic3             | gcpt_org_name_to_concept                    |       362
 mimic3             | gcpt_output_label_to_concept                |      1154
 mimic3             | gcpt_prescriptions_ndcisnullzero_to_concept |      1354
 mimic3             | gcpt_procedure_to_concept                   |       113
 mimic3             | gcpt_religion_to_concept                    |        20
 mimic3             | gcpt_resistance_to_concept                  |         4
 mimic3             | gcpt_route_to_concept                       |        78
 mimic3             | gcpt_seq_num_to_concept                     |        44
 mimic3             | gcpt_spec_type_to_concept                   |        87
 mimic3             | gcpt_unit_doseera_concept_id                |        27
 mimic3             | icustays                                    |     61532
 mimic3             | inputevents_cv                              |  17527935
 mimic3             | inputevents_mv                              |   3618991
 mimic3             | labevents                                   |  27854055
 mimic3             | microbiologyevents                          |    631726
 mimic3             | noteevents                                  |   2083180
 mimic3             | outputevents                                |   4349218
 mimic3             | patients                                    |     46520
 mimic3             | prescriptions                               |   4156450
 mimic3             | procedureevents_mv                          |    258066
 mimic3             | procedures_icd                              |    240095
 mimic3             | services                                    |     73343
 mimic3             | transfers                                   |    261897
 omop               | attribute_definition                        |         0
 omop               | care_site                                   |        93
 omop               | cdm_source                                  |         0
 omop               | cohort                                      |         0
 omop               | cohort_attribute                            |    228382
 omop               | cohort_definition                           |         0
 omop               | concept                                     |   6045915
 omop               | concept_ancestor                            |  61981870
 omop               | concept_class                               |       358
 omop               | concept_relationship                        |  40742164
 omop               | concept_synonym                             |   7657628
 omop               | condition_era                               |         0
 omop               | condition_occurrence                        |    716595
 omop               | cost                                        |         0
 omop               | death                                       |     14849
 omop               | device_exposure                             |         0
 omop               | domain                                      |        46
 omop               | dose_era                                    |   5131795
 omop               | drug_era                                    |         0
 omop               | drug_exposure                               |  24934751
 omop               | drug_strength                               |   2624545
 omop               | fact_relationship                           | 136935388
 omop               | location                                    |         0
 omop               | measurement                                 | 365181104
 omop               | metadata                                    |         0
 omop               | note                                        |   2082294
 omop               | note_nlp                                    |         0
 omop               | observation                                 |   6721040
 omop               | observation_period                          |     58976
 omop               | payer_plan_period                           |         0
 omop               | person                                      |     46520
 omop               | procedure_occurrence                        |   1063525
 omop               | provider                                    |      7567
 omop               | relationship                                |       488
 omop               | source_to_concept_map                       |         0
 omop               | specimen                                    |  39874171
 omop               | visit_detail                                |    271812
 omop               | visit_detail_assign                         |    198469
 omop               | visit_occurrence                            |     58976
 omop               | vocabulary                                  |        79


```

## Part 3.9 Create indexes (90 minutes)

    -#MIMIC-III indexing was done in Part 2.3. Vocabulary indexing was done in Part 3.5.
    -#Run the rest of indexes and constraints.
    -#Reference to  /mimic-omop/omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt"
    - $ cd /home/mart/mimic-omop    
    - $ vi  omop/build-omop/postgresql/OMOP_CDM_postgresql_indexes_no_vocabulary.txt
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP_CDM_postgresql_indexes_no_vocabulary.txt"  >>  /home/mart/output20191020.log &
    

## Part 3.10 Create Foreign key constraints (20 minutes)

    - $ cd /home/mart/mimic-omop    
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql constraints.txt"  >>  /home/mart/output20191020a.log &



# Part 4 - OHDSI - APHRODITE 
- References:
  - Banda, Juan M., Yoni Halpern, David Sontag, and Nigam H. Shah. 2017. “Electronic Phenotyping with APHRODITE and the Observational Health Sciences and Informatics (OHDSI) Data Network.” AMIA Joint Summits on Translational Science Proceedings. AMIA Joint Summits on Translational Science 2017 (July): 48–57.
   - [https://www.ncbi.nlm.nih.gov/pubmed/28815104](https://www.ncbi.nlm.nih.gov/pubmed/28815104)

  - Github: Automated PHenotype Routine for Observational Definition, Identification, Training and Evaluation (APHRODITE)
   - [https://github.com/OHDSI/Aphrodite](https://github.com/OHDSI/Aphrodite)  
  
## Part 4.1 Current state
- TDB
## Part 4.2 Methodology
- TDB
## Part 4.3 Implementation
- TDB
## Part 4.4 Evaluation and discussion
- TDB
