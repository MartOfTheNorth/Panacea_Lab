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

### Part 2.1 Panacea lab procedure
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


