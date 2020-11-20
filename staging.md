
Listing files present in S3 bucket:

```
list @my_s3_bucket;

list @my_s3_bucket/load;

list @my_s3_bucket/unload;

list @my_s3_bucket/load/L;
```
Creating Staging table:

```
CREATE TABLE WEIGHT_INGEST (
    NDB_NO	VARCHAR(7)
    ,SEQ	VARCHAR(4)
    ,AMOUNT	NUMBER(6,3)
    ,MSRE_DESC	VARCHAR(86)
    ,GM_WGT	NUMBER(7,1)
    ,NUM_DATA_PTS	NUMBER(4,0)
    ,STD_DEV	NUMBER(7,3)
  );



select * from weight_ingest;
```

Loading files into staging table from S3 bucket:

```
//copying data from AWS s3 source to staging WEIGHT_INGEST
COPY INTO WEIGHT_INGEST
FROM @my_s3_bucket/load/
FILES = ('WEIGHT.txt')
FILE_FORMAT = (FORMAT_NAME = USDA_FILE_FORMAT )
FORCE = FALSE;

select count(*) from "USDA_NUTRIENT_STDREF"."PUBLIC"."WEIGHT_INGEST";

TRUNCATE "USDA_NUTRIENT_STDREF"."PUBLIC"."WEIGHT_INGEST";

select * from "USDA_NUTRIENT_STDREF"."PUBLIC"."WEIGHT_INGEST";


CREATE OR REPLACE TABLE WEIGHT (
    NDB_NO	VARCHAR(5)
    ,SEQ	VARCHAR(2)
    ,AMOUNT	NUMBER(6,3)
    ,MSRE_DESC	VARCHAR(84)
    ,GM_WGT	NUMBER(7,1)
    ,NUM_DATA_PTS	NUMBER(4,0)
    ,STD_DEV	NUMBER(7,3)
  );
  
  
//ETL to move WEIGHT data from WEIGHT_INGEST to WEIGHT
//LOAD STEP
INSERT INTO WEIGHT(
SELECT 
  //TRANSFORM STEP
    REPLACE(NDB_NO,'~') as NDB_NO
    ,REPLACE(SEQ,'~') as SEQ
    ,AMOUNT
    ,REPLACE(MSRE_DESC,'~') as MSRE_DESC
    ,GM_WGT
    ,NUM_DATA_PTS
    ,STD_DEV
//EXTRACT STEP 
FROM WEIGHT_INGEST);
```
Loading data into table from S3 source without using a staging table:

```
//loading data from s3 without creating a staging table we do the transformation in the COPY STATEMENT
//querying the data before loading it.since the file doesn't have headers- columns are refered in sequential order
//selecting columns using $ sign

USE DATABASE "USDA_NUTRIENT_STDREF";

SELECT $1,$2
FROM @MY_S3_BUCKET/load/LANGDESC.txt
(FILE_FORMAT => USDA_FILE_FORMAT);


COPY INTO LANGDESC(FACTOR_CODE,DESCRIPTION)
FROM
(SELECT REPLACE($1,'~'),REPLACE($2,'~')
FROM @MY_S3_BUCKET/load/LANGDESC.txt)

FILE_FORMAT=USDA_FILE_FORMAT;

```
