## Project 3: Data Warehouse

### Description
A music streaming startup, Sparkify, has grown their user base and song database and want to move their processes and 
data onto the cloud. Their data resides in S3, in a directory of JSON logs on user activity on the app, as well as a 
directory with JSON metadata on the songs in their app.
Goal is to build an ETL pipeline that extracts data from S3, stages them in Redshift, and transforms 
data into a set of dimensional tables for their analytics team to continue finding insights in what songs their users 
are listening to. 

--------------------------------------------

### Datasets

#### Song Dataset
Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by 
the first three letters of each song's track ID. For example, here are filepaths to two files in this dataset.

Example:
```
{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}
```

#### Log Dataset
The second dataset consists of log files in JSON format generated by this event simulator based on the songs in 
the dataset above. These simulate activity logs from a music streaming app based on specified configurations.

The log files in the dataset you'll be working with are partitioned by year and month. For example, here are filepaths
to two files in this dataset.
Example: 
```
{"artist":null,"auth":"Logged In","firstName":"Walter","gender":"M","itemInSession":0,"lastName":"Frye","length":null,"level":"free","location":"San Francisco-Oakland-Hayward, CA","method":"GET","page":"Home","registration":1540919166796.0,"sessionId":38,"song":null,"status":200,"ts":1541105830796,"userAgent":"\"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"","userId":"39"}
```

--------------------------------------------

### Database schema

#### Staging Tables
**staging_events**
- event_id INT PRIMARY KEY
- artist_name VARCHAR(255)
- auth VARCHAR(50)
- user_first_name VARCHAR(255)
- user_gender  VARCHAR(1)
- item_in_session	INTEGER
- user_last_name VARCHAR(255)
- song_length	DOUBLE PRECISION 
- user_level VARCHAR(50)
- location VARCHAR(255)
- method VARCHAR(25)
- page VARCHAR(35)
- registration VARCHAR(50)
- session_id	BIGINT
- song_title VARCHAR(255)
- status INTEGER 
- ts VARCHAR(50)
- user_agent TEXT
- user_id VARCHAR(100)

**staging_songs**
- song_id VARCHAR(100) PRIMARY KEY
- num_songs INTEGER
- artist_id VARCHAR(100)
- artist_latitude DOUBLE PRECISION
- artist_longitude DOUBLE PRECISION
- artist_location VARCHAR(255)
- artist_name VARCHAR(255)
- title VARCHAR(255)
- duration DOUBLE PRECISION
- year INTEGER

#### Fact Table
**songplays** - records in log data associated with song plays i.e. records with page NextSong
- songplay_id INT PRIMARY KEY
- start_time TIMESTAMP REFERENCES time(start_time)
- user_id VARCHAR(50) REFERENCES users(user_id)
- level VARCHAR(50)
- song_id VARCHAR(100) REFERENCES songs(song_id)
- artist_id VARCHAR(100) REFERENCES artists(artist_id)
- session_id BIGINT
- location VARCHAR(255)
- user_agent TEXT

#### Dimension Tables
**users** - users in the app
- user_id VARCHAR PRIMARY KEY
- first_name VARCHAR(255)
- last_name VARCHAR(255)
- gender VARCHAR(1)
- level VARCHAR(50)

**songs** - songs in music database
- song_id VARCHAR(100) PRIMARY KEY
- title VARCHAR(255)
- artist_id VARCHAR(100) NOT NULL
- year INTEGER
- duration DOUBLE PRECISION

**artists** - artists in music database
- artist_id VARCHAR(100) PRIMARY KEY
- name VARCHAR(255) NOT NULL
- location VARCHAR(255)
- latitude DOUBLE PRECISION
- longitude DOUBLE PRECISION

**time** - timestamps of records in songplays broken down into specific units
- start_time TIMESTAMP PRIMARY KEY
- hour INTEGER 
- day INTEGER 
- week INTEGER 
- month INTEGER 
- year INTEGER 
- weekday INTEGER 

--------------------------------------------
### Structure of project

The project includes files:

* <b> create_tables.py </b> - creation of fact and dimension tables for the star schema in Redshift.
* <b> etl.py </b> - is where data is load from S3 into staging tables on Redshift and then into tables on Redshift.
* <b> sql_queries.py </b> - definiton of SQL statements, which will be imported into the two other files above.
* <b> dhw.cfg </b> - Configuration file used that contains info about Redshift, IAM and S3
* <b> README.md </b> - explination of process and decisions for this ETL pipeline.
--------------------------------------------
### How to Execute Code

- Run in terminal `python create_tables.py`. This script will drop old tables (if exist) ad re-create new tables
- Run in terminal `python etl.py`. This script executes the queries that extract JSON data from the S3 bucket and ingest them to Redshift

