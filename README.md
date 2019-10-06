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
    -#Define PSQL connection
    - $ export OMOP_SCHEMA='omop1'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - # no need$ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    -#Build omop schema (1 minute)
    - $ cd /home/mart/mimic-omop
    - $ set search_path to mimic1;
    - $ psql "$OMOP" -c "DROP SCHEMA IF EXISTS $OMOP_SCHEMA CASCADE;"
    - $ psql "$OMOP" -c "CREATE SCHEMA $OMOP_SCHEMA;"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql ddl.txt"
    -#Alter (1 minute)
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/mimic-omop-alter.sql"
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_cdm_comments.sql"
    -#To import the vocabulary. Copy from /home/large_data/OHDSI_vocabulary-May2019 to /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019 (3 minutes)
    - $ ls -l /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019
    - $ cd /home/mart/mimic-omop
    - $ ln -s /mnt/local/hdd/mimicIII/OHDSI_vocabulary-May2019  extras/athena
    - $ psql "$OMOP" -f "omop/build-omop/postgresql/omop_vocab_load.sql"  >>  /home/mart/output20191006.log    (3 minutes)
    
    -#Create local MIMIC-III concepts  (A3+B3+C60 minutes)
    - $ cd /home/mart/mimic-omop
    - $ psql "$MIMIC" -f mimic/build-mimic/postgres_create_mimic_id.sql     (A3 minutes)

    -#Load the concepts from the CSV files (B3 minutes)
    - $ cd /home/mart/mimic-omop    
    - $ vi mimic-omop.cfg 
    - $ R
    - > install.packages(c("RPostgres"))
    - $ Rscript etl/ConceptTables/loadTables.R mimiciii
    
    -#Run the ETL  (C60 minutes)
    - $ cd /home/mart/mimic-omop       
    - $ set search_path to mimic1;
    - $ export OMOP_SCHEMA='omop'
    - $ export OMOP='host=localhost dbname=mimic1 user=mimicuser options=--search_path='$OMOP_SCHEMA
    - $ export MIMIC='host=localhost dbname=mimic1 user=mimicuser options=--search_path=mimic1'
    - $ psql "$MIMIC" --set=OMOP_SCHEMA="$OMOP_SCHEMA" -f "etl/etl.sql"
    -
    -
    -
    -#Check the ETL has run properly
    - psql "$MIMIC" -c "CREATE EXTENSION pgtap;"
    - psql "$MIMIC" -f "etl/check_etl.sql"

```
    -#Check number of row of all tables in schema
    -select table_schema, 
       table_name, 
       (xpath('/row/cnt/text()', xml_count))[1]::text::int as row_count
from (
  select table_name, table_schema, 
         query_to_xml(format('select count(*) as cnt from %I.%I', table_schema, table_name), false, true, '') as xml_count
  from information_schema.tables
  where table_schema = 'mimic3' and table_name NOT IN ('admissions') ) t ;
```
    - https://stackoverflow.com/questions/2596670/how-do-you-find-the-row-count-for-all-your-tables-in-postgres/2611745#2611745
```
SELECT schemaname,relname,n_live_tup 
  FROM pg_stat_user_tables 
  ORDER BY n_live_tup DESC;
  
SELECT 
  nspname AS schemaname,relname,reltuples
FROM pg_class C
LEFT JOIN pg_namespace N ON (N.oid = C.relnamespace)
WHERE 
  nspname NOT IN ('pg_catalog', 'information_schema') AND
  relkind='r' 
ORDER BY reltuples DESC;  
```
    - 
    -#(Optional) Indexes may slow down importing of data - so you may want to only build these after running the full ETL.
    - $ cd /home/mart/mimic-omop    
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql indexes.txt" >> output20190930omopindex.log  &
    - $ nohup psql "$OMOP" -f "omop/build-omop/postgresql/OMOP CDM postgresql constraints.txt" >> output20190930cdmcon.log  &

#### Part 3.2 Validation - MIMIC-III database to the OMOP schema   
    - $ cd ~ 
    - vi mimic3_omop_table_count.sql
    - $ nohup  psql -d mimic3 -f mimic3_omop_table_count.sql  >> output20190930sql.log  &  
    - $ tail -f output20190930sql.log 
    
```

1	mimic3.callout	34499
2	mimic3.caregivers	7567
3	mimic3.chartevents	330712483
4	mimic3.chartevents_1	18386320
5	mimic3.chartevents_10	19591030
6	mimic3.chartevents_11	18164226
7	mimic3.chartevents_12	20501149
8	mimic3.chartevents_13	21704145
9	mimic3.chartevents_14	19407331
10	mimic3.chartevents_15	20452747
11	mimic3.chartevents_16	20194567
12	mimic3.chartevents_17	10721694
13	mimic3.chartevents_2	19647241
14	mimic3.chartevents_3	20928152
15	mimic3.chartevents_4	19129634
16	mimic3.chartevents_5	21679452
17	mimic3.chartevents_6	17612000
18	mimic3.chartevents_7	20703171
19	mimic3.chartevents_8	21889411
20	mimic3.chartevents_9	20000213
21	mimic3.cptevents	573146
22	mimic3.d_cpt	134
23	mimic3.d_icd_diagnoses	14567
24	mimic3.d_icd_procedures	3882
25	mimic3.d_items	12487
26	mimic3.d_labitems	753
27	mimic3.datetimeevents	4485937
28	mimic3.diagnoses_icd	651047
29	mimic3.drgcodes	125557
30	mimic3.gcpt_admission_location_to_concept	9
31	mimic3.gcpt_admission_type_to_concept	4
32	mimic3.gcpt_admissions_diagnosis_to_concept	1523
33	mimic3.gcpt_atb_to_concept	30
34	mimic3.gcpt_care_site	30
35	mimic3.gcpt_chart_label_to_concept	319
36	mimic3.gcpt_chart_observation_to_concept	57
37	mimic3.gcpt_cpt4_to_concept	23
38	mimic3.gcpt_cv_input_label_to_concept	2782
39	mimic3.gcpt_datetimeevents_to_concept	155
40	mimic3.gcpt_derived_to_concept	8
41	mimic3.gcpt_discharge_location_to_concept	17
42	mimic3.gcpt_drgcode_to_concept	1355
43	mimic3.gcpt_ethnicity_to_concept	41
44	mimic3.gcpt_heart_rhythm_to_concept	47
45	mimic3.gcpt_inputevents_drug_to_concept	108
46	mimic3.gcpt_insurance_to_concept	5
47	mimic3.gcpt_lab_label_to_concept	118
48	mimic3.gcpt_lab_unit_to_concept	71
49	mimic3.gcpt_lab_value_to_concept	3703
50	mimic3.gcpt_labs_from_chartevents_to_concept	80
51	mimic3.gcpt_labs_specimen_to_concept	17
52	mimic3.gcpt_map_route_to_concept	28
53	mimic3.gcpt_marital_status_to_concept	7
54	mimic3.gcpt_microbiology_specimen_to_concept	88
55	mimic3.gcpt_mv_input_label_to_concept	159
56	mimic3.gcpt_note_category_to_concept	15
57	mimic3.gcpt_note_section_to_concept	1113
58	mimic3.gcpt_org_name_to_concept	362
59	mimic3.gcpt_output_label_to_concept	1154
60	mimic3.gcpt_prescriptions_ndcisnullzero_to_concept	1354
61	mimic3.gcpt_procedure_to_concept	113
62	mimic3.gcpt_religion_to_concept	20
63	mimic3.gcpt_resistance_to_concept	4
64	mimic3.gcpt_route_to_concept	78
65	mimic3.gcpt_seq_num_to_concept	44
66	mimic3.gcpt_spec_type_to_concept	87
67	mimic3.gcpt_unit_doseera_concept_id	27
68	mimic3.icustays	61532
69	mimic3.inputevents_cv	17527935
70	mimic3.inputevents_mv	3618991
71	mimic3.labevents	27854055
72	mimic3.microbiologyevents	631726
73	mimic3.noteevents	2083180
74	mimic3.outputevents	4349218
75	mimic3.patients	46520
76	mimic3.prescriptions	4156450
77	mimic3.procedureevents_mv	258066
78	mimic3.procedures_icd	240095
79	mimic3.services	73343
80	mimic3.transfers	261897
81	omop.attribute_definition	0
82	omop.care_site	0
83	omop.cdm_source	0
84	omop.cohort	0
85	omop.cohort_attribute	0
86	omop.cohort_definition	0
87	omop.concept_ancestor	61981870
88	omop.concept_class	358
89	omop.concept_relationship	40742164
90	omop.concept_synonym	7657628
91	omop.condition_era	0
92	omop.condition_occurrence	0
93	omop.cost	0
94	omop.death	0
95	omop.device_exposure	0
96	omop.domain	46
97	omop.dose_era	0
98	omop.drug_era	0
99	omop.drug_strength	2624545
100	omop.fact_relationship	17202174
101	omop.location	0
102	omop.measurement	0
103	omop.metadata	0
104	omop.note	4164588
105	omop.note_nlp	0
106	omop.observation	0
107	omop.observation_period	0
108	omop.payer_plan_period	0
109	omop.person	0
110	omop.procedure_occurrence	0
111	omop.provider	0
112	omop.relationship	488
113	omop.source_to_concept_map	0
114	omop.specimen	0
115	omop.visit_detail	0
116	omop.visit_occurrence	0
117	omop.vocabulary	79
118	mimic3.admissions	None
119	mimic3.gcpt_continuous_unit_carevue	None
120	omop.concept	None
121	omop.drug_exposure	None
122	omop.visit_detail_assign	None


```
