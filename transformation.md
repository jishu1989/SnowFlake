**Target:**
*  Use the SQL REPLACE function within SQL code.
*  Use the CONCAT function and || operator within SQL code.
*  Recognize the meaning of the acronyms ETL and ELT.
*  Use the worksheets area to load a text file into a Snowflake worksheet.
 
**Others:**
*  Basic Function syntax
*  Using required and optional arguments in the REPLACE function
*  Renaming a column in a SELECT statement using “AS”
*  The difference between a SQL SELECT statement and a SQL UPDATE statement
*  Using the double-forward slash “//” to put notes or comments into code
*  Mapping ETL concepts into different lines of a SQL UPDATE statement

**ELT**
```
SELECT FDGRP_DESC FROM "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST"
WHERE FDGRP_DESC LIKE '%Food%';
Select FDGRP_CD from FD_GROUP_INGEST;

//Removing ~

select replace(FDGRP_CD,'~','') as NEW_FDGRP_CODE from FD_GROUP_INGEST;
//OR
select replace(FDGRP_CD,'~') as NEW_FDGRP_CODE from FD_GROUP_INGEST;

//Updating the column FDGRP_CD

Update "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST"
set FDGRP_CD = replace(FDGRP_CD,'~','');

````
**ETL**

```
//putting the ~ back to the column
select CONCAT(CONCAT('~',FDGRP_CD),'~') as OLD_FDGRP_CD from "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST";
//OR
select '~'||FDGRP_CD||'~' as OLD_FDGRP_CD from "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST";

Update "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST"
set FDGRP_CD = '~'||FDGRP_CD||'~';

select * from "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST";

//ETL

//load - our target table is the food group table which we load using the insert command
INSERT INTO "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP"

SELECT
//TRANSFORM - we clean up the data by replacing ~
REPLACE(FDGRP_CD,'~','') AS FDGRP_CD,
REPLACE(FDGRP_DESC,'~','') AS FDGRP_DESC
//EXTRACT - source table is the food group ingest table
FROM "USDA_NUTRIENT_STDREF"."PUBLIC"."FD_GROUP_INGEST";

select * from FD_GROUP;
```
