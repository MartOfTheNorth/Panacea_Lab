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
    - $ export OMOP_SCHEMA='omop1'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'    
    - $ sudo apt-get install pgtap
    -#Build OMOP  (3 minutes)
    - $ rm -rf mimic-omop
    - $ git clone git@github.com:MIT-LCP/mimic-omop.git
    - $ git clone https://github.com/MIT-LCP/mimic-omop.git
    - $ cd mimic-omop/
    -#Clone Common Data Model v6.0
    - $ git clone https://github.com/OHDSI/CommonDataModel.git
    - $ cd CommonDataModel
    - $ git reset --hard 0ac0f4bd56c7372dcd3417461a91f17a6b118901
    - $ cd ..
    - $ cp CommonDataModel/PostgreSQL/*.txt omop/build-omop/postgresql/
    - $ sed -i 's/^CREATE TABLE \([a-z_]*\)/CREATE UNLOGGED TABLE \1/' "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    
    -#Define PSQL connection   (If new omop2 is needed, repeat here.)
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
    -# (If new omop2 is building, skip next and go to "Run the ETL".)    
    -#To import the vocabulary. Copy from /home/large_data/OHDSI_vocabulary-May2019 to /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019 (3 minutes)
    - $ ls -l /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019
    - $ cd /home/mart/mimic-omop
    - $ ln -s /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019  extras/athena
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_vocab_load.sql"  >>  /home/mart/output20191006.log    (3 minutes)
    
    -#Create local MIMIC-III concepts  (3 minutes)
    - $ cd /home/mart/mimic-omop
    - $ psql "$MIMIC" -f mimic/build-mimic/postgres_create_mimic_id.sql   

    -#Load the concepts from the CSV files (3 minutes)
    - $ cd /home/mart/mimic-omop    
    - $ vi mimic-omop.cfg 
    - dbname=mimic1
    - user=mimicuser
    - $ R
    - > install.packages(c("RPostgres"))
    - > quit("yes")
    - $ Rscript etl/ConceptTables/loadTables.R mimiciii
    
    -#Run the ETL  (12 hours)
    - $ cd /home/mart/mimic-omop       
    - $ set search_path to mimic1;
    - $ export OMOP_SCHEMA='omop'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    - $ psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql"
    - $ psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql" >>  /home/mart/output20191006.log     

    -#Note: If feeding a big pile of SQL to psql then add \echo to help tracking into "etl/etl.sql" as below;
```
--BEGIN;
\echo 'BEGIN'
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
\echo 'etl/StandardizedClinicalDataTables/DRUG_EXPOSURE/etl.sql'
\i etl/StandardizedClinicalDataTables/DRUG_EXPOSURE/etl.sql
\echo 'etl/StandardizedClinicalDataTables/OBSERVATION/etl.sql'
\i etl/StandardizedClinicalDataTables/OBSERVATION/etl.sql
\echo 'etl/StandardizedClinicalDataTables/MEASUREMENT/etl.sql'
\i etl/StandardizedClinicalDataTables/MEASUREMENT/etl.sql
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
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt" >> output20190930omopindex.log  &
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql constraints.txt" >> output20190930cdmcon.log  &

#### Part 3.2 Validation - MIMIC-III database to the OMOP schema   
    -#Check number of row of all tables in schema
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
 omop               | concept                                     |   6046495
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
 omop               | dose_era                                    |         0
 omop               | drug_era                                    |         0
 omop               | drug_exposure                               |   7406816
 omop               | drug_strength                               |   2624545
 omop               | fact_relationship                           |   8601087
 omop               | location                                    |         0
 omop               | measurement                                 |         0
 omop               | metadata                                    |         0
 omop               | note                                        |   2082294
 omop               | note_nlp                                    |         0
 omop               | observation                                 |   4226027
 omop               | observation_period                          |     58976
 omop               | payer_plan_period                           |         0
 omop               | person                                      |     46520
 omop               | procedure_occurrence                        |   1063525
 omop               | provider                                    |      7567
 omop               | relationship                                |       488
 omop               | source_to_concept_map                       |         0
 omop               | specimen                                    |         0
 omop               | visit_detail                                |    271812
 omop               | visit_detail_assign                         |    198469
 omop               | visit_occurrence                            |     58976
 omop               | vocabulary                                  |        79

```
