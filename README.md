# Switch table
A simple table structure schema for handling high write volumes of non time critical data.

## Background
This table structure was created to cope with hight volumes log data that were not needed to be accessable right away but where accessing old data were crucial. The original setup was made in around 2006 with MySQL and MYISAM tables. Alternative methods such as INSERT DELAYED and using InnoDB tables were evaluated with live data but without compelling results.

## Setup
The switch table setup requires three tables, two identical tables where new raw data is inserted and a third target data table:

- switch_table_1
- switch_table_2
- target_data_table

The process is as follows:

1. Data is inserted into switch_table_1
2. A background job, as a screen or daemon, and tells the application to stop inserting data into swtich_table_1 and instead insert into switch_table_2.
3. The background job then process the data from switch_table_1 and inserts it into the target_data_table until switch_table_1 is empty.
4. The background job tells the application to stop inserting data into switch_table_2 and instead insert data into switch_table_1.
5. The process is repeated.

In the original application a forth table was used to keep track of the current switch table.
