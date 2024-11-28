# Spotify Advanced SQL Project and Query Optimization P-6
Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_logo.jpg)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance.

## 15 Practice Questions

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.
```sql
SELECT * FROM spotify
WHERE stream > 1000000000;
```
2. List all albums along with their respective artists
```sql
SELECT
	album,
	artist
FROM spotify;
```
3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT
	SUM(comments) as total_comments
FROM spotify
WHERE licensed = 'true';
```
4. Find all tracks that belong to the album type `single`.
```sql
SELECT * FROM spotify
WHERE album_type ILIKE 'single'; /*ILIKE to find both UPPER and lower*/
```
5. Count the total number of tracks by each artist.
```sql
SELECT
	artist,
	COUNT(*) as total_no_songs
FROM spotify
GROUP BY artist
ORDER BY 2;
```

### Medium Level
1. Calculate the average danceability of tracks in each album.
```sql
SELECT
	album,
	avg(danceability) as avg_danceability
FROM spotify
GROUP BY 1
ORDER BY 2 DESC;
```
2. Find the top 5 tracks with the highest energy values.
```sql
SELECT
	track,
	MAX(energy)
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```
3. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
SELECT
	track,
	SUM(views) as total_views,
	SUM(likes) as total_likes
FROM spotify
WHERE official_video = 'true'
GROUP BY 1
ORDER BY 2 DESC;
```
4. For each album, calculate the total views of all associated tracks.
```sql
SELECT
	album,
	track,
	SUM(views)
FROM spotify
GROUP BY 1, 2
ORDER BY 3 DESC;
```
5. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT * FROM
(
SELECT
	track,
	COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END), 0) as streamed_on_youtube,
	COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) as streamed_on_spotify
FROM spotify
GROUP BY 1
) as T1
WHERE 
	stream_on_spotify > stream_on_youtube
	AND
	streamed_on_youtube <> 0;

```

### Advanced Level
1. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
WITH ranking_artist
AS
(SELECT
	artist,
	track,
	SUM(views) as total_view,
	DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) as rank
FROM spotify
GROUP BY 1, 2
ORDER BY 1, 3 DESC
)
SELECT * FROM ranking_artist
WHERE rank <= 3;
```
2. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT 	
	track,
	artist,
	liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify);
```
3. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte
AS
(SELECT
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energy
FROM spotify
GROUP BY 1
)
SELECT
	album,
	highest_energy - lowest_energy as energy_diff
FROM cte
ORDER BY 2 DESC;
```


## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.
5. Explore query optimization techniques for large datasets.

---

## Next Steps
- **Visualize the Data**: Use a data visualization tool like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.
- **Advanced Querying**: Dive deeper into query optimization and explore the performance of SQL queries on larger datasets.

---

## License
This project is licensed under the MIT License.
