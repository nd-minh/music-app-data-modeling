# Database Modeling for a Music Streaming App (Part 1)

(This project presents a relational database model for a music streaming app, for a design of NoSQL database version of the app, please refer to Part 2 [here](https://github.com/nd-minh/music-app-data-modeling-part-2). For a design of a Data Warehouse for this music app, please refer to the project [here](https://github.com/nd-minh/music-app-data-warehouse))

In this project, our aim is to design a relational database to store relevant data from a music streaming app. We are given two sources of data as follows.

- Source 1: Logs of user activity on the app, available in JSON format.
Example:

![alt text](/images/log-data.png "log-ex")

- Source 2: Metadata of available songs in the app, available also in JSON format.

Example:

```JSON
{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}
```

We design the database so that it is optimized for queries on song play analysis. To that end, we propose a star schema and an ETL pipeline to transfer the data from the two sources to tables in a PostgreSQL database.

Our schema consists of one fact table and four dimension tables. The descriptions of the tables and their relationship is as follows.

#### Dimension Tables:
- Table `Artists`: contains information about artists in the app. *Columns:* artist_id, name, location, latitude, longitude. *Primary key (PK):* artist_id. 
- Table `Songs`: contains information about songs in the app. *Columns:* song_id, title, artist_id, year, duration. *PK:* song_id. *Foreign key (FK):* artist_id REFERENCES `Artists.artist_id`.
- Table `Users`: contains information about users in the app. *Columns:* user_id, first_name, last_name, gender, level. *PK:* user_id.
- Table `Time`: contains information about timestamps of records broken down into specific units. *Columns:* start_time, hour, day, week, month, year, weekday. *PK:* start_time.

#### Fact Tables:
- Tables `SongPlays`: contains records in log data associated with page `NextSong`. *Columns:* songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent. *PK:* songplay_id. *FK:* user_id REFERENCES `Users.user_id`, start_time REFERENCES `Time.start_time`. 

With this design, one can easily query songs that users are listening to, as well as related information about the users, artists, and temporal information of the listening sessions. The details of the transferring process from JSON to database is in `etl.py`. At a glance, we pull the relevant information from JSON source files and then insert it into tables in the database. Instruction to run the ETL pipeline is given belows.

### Build Instruction
1. Run `python create_tables.py` to create the database.
2. Run `python etl.py` to run the ETL pipeline (load JSON files, extract relevant information, insert into database tables.)

**Appendix:** SQL commands to create, insert, and query tables in the database are available in `sql_queries.py`. An interactive environment for the ETL pipeline can be found in `etl.ipynb`. Sample queries of the database are written in `test.ipynb`.  
