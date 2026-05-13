# Music Streaming Database System Proposal — Phase 3

**Name of System:** Spotify Music Streaming Database System  
**Author:** Connor Prout  
**Course:** CSCI-C 442 Database Systems  
**Date:** May 13, 2026  
**Version:** 1.4.0

---

## Revision History

| Date | Version | Note |
|---|---|---|
| 5/12/26 | 1.0.0 | Initial Phase 0 proposal |
| 5/12/26 | 1.1.0 | Refined scope of project and preliminary model |
| 5/12/26 | 1.2.0 | Phase 1 ER model development |
| 5/12/26 | 1.2.1 | Phase 1 grammar changes |
| 5/13/26 | 1.2.2 | Phase 1 ER model final version |
| 5/13/26 | 1.3.0 | Phase 2 use cases and query requirements added |
| 5/13/26 | 1.4.0 | Phase 3 SQL queries and strategic insights added |
---

# Table of Contents

1. Introduction  
2. Entity-Relationship (ER) Diagram Overview  
3. Entities and Data Dictionary  
4. Relationship Summary (Cardinality)  
5. Design Justifications  
6. Constraints  
7. Phase 2: Use Cases & Query Requirements
8. Phase 3: SQL Queries & Strategic Insights  
9. Conclusion  
10. Appendix (ER Diagram Reference)  

---

# 1. Introduction

This phase builds upon the previous phase 2 additions of use cases through the integration of aggregate functions, grouping, joins, and nested queries. These use cases define how the database will be used and help validate whether the current ER model properly supports real-world interactions in a music streaming system.

---

# 2. Entity-Relationship (ER) Diagram Overview

The system is designed using a ER model consisting of 15 entities. The model includes core music data (songs, albums, artists), user interaction data (playlists, listening history, follows), and system data (subscriptions, payments+methods, genres).

ER diagrams in this project will represent the following:
- Entities with primary keys and attributes
- Relationships between entities
- Cardinality constraints (1:1, 1:M, M:N)
- Associative entities for resolving many-to-many relationships

---

# 3. Entities and Data Relationships

## User
- **Description:** Represents a listener using the platform
- **PK:** UserID
- Attributes:
  - Username
  - Email
  - Password
  - DateCreated
  - Country

---

## Artist
- **Description:** Music creators who release albums and songs
- **PK:** ArtistID
- Attributes:
  - ArtistName
  - Genre
  - MonthlyListeners
  - Biography

---

## Album
- **Description:** Collection of songs released by an artist
- **PK:** AlbumID
- Attributes:
  - Title
  - ReleaseDate
  - AlbumType
- FK:
  - ArtistID

---

## Song
- **Description:** Individual track available for streaming
- **PK:** SongID
- Attributes:
  - Title
  - Duration
  - ReleaseDate
- FK:
  - AlbumID
  - GenreID

---

## Playlist
- **Description:** User-created collection of songs
- **PK:** PlaylistID
- Attributes:
  - PlaylistName
  - CreationDate
  - Visibility
- FK:
  - UserID

---

## PlaylistSong (Associative Entity)
- **Description:** Resolves M:N relationship between Playlist and Song
- Attributes:
  - PlaylistID (FK)
  - SongID (FK)
  - DateAdded
  - PositionInPlaylist

---

## SavedSong
- **Description:** Songs saved by a user to their library
- Attributes:
  - SaveID (PK)
  - UserID (FK)
  - SongID (FK)
  - DateSaved

---

## ListeningHistory
- **Description:** Tracks user streaming activity over time
- Attributes:
  - HistoryID (PK)
  - UserID (FK)
  - SongID (FK)
  - Timestamp
  - DeviceID (FK)

---

## ArtistFollow
- **Description:** Tracks users following artists
- Attributes:
  - FollowID (PK)
  - UserID (FK)
  - ArtistID (FK)
  - FollowDate

---

## PlaylistFollow
- **Description:** Tracks users following playlists
- Attributes:
  - PlaylistFollowID (PK)
  - UserID (FK)
  - PlaylistID (FK)
  - FollowDate

---

## Subscription
- **Description:** Represents user subscription plans
- **PK:** SubscriptionID
- Attributes:
  - UserID (FK)
  - PlanType
  - StartDate
  - EndDate
  - PaymentStatus

---

## Podcast
- **Description:** Podcast shows available on the platform
- **PK:** PodcastID
- Attributes:
  - PodcastTitle
  - Category
  - HostName
  - ReleaseDate

---

## PodcastEpisode
- **Description:** Individual episodes of a podcast
- **PK:** EpisodeID
- Attributes:
  - EpisodeTitle
  - Duration
  - ReleaseDate
- FK:
  - PodcastID

---

## Genre
- **Description:** Classification for songs and artists
- **PK:** GenreID
- Attributes:
  - GenreName

---

## Device
- **Description:** Device used for streaming activity
- **PK:** DeviceID
- Attributes:
  - DeviceType
  - OperatingSystem
  - UserID

---

# 4. Relationship Summary (Cardinality)

| Entity A | Entity B | Relationship | Cardinality |
|----------|----------|--------------|-------------|
| User | Playlist | creates | 1:M |
| Playlist | Song | contains | M:N |
| User | Song | listens to | M:N |
| User | Artist | follows | M:N |
| Playlist | User | followed by | M:N |
| Artist | Album | creates | 1:M |
| Album | Song | contains | 1:M |
| Song | Genre | belongs to | M:1 |
| Podcast | Episode | contains | 1:M |
| User | Subscription | has | 1:1 |
| User | Podcast Episode | listens to | 1:M |

---

# 5. Design Justifications

## Associative Entities
Many-to-many relationships such as Playlist–Song and User–Artist require associative entities to:
- Maintain normalization
- Allow additional attributes (e.g., DateAdded, FollowDate)

## Listening History
Separated into its own entity to support:
- Time-based tracking
- Device-specific analytics
- Historical user behavior analysis

## Subscription Model
Modeled as a separate entity to support:
- Plan upgrades/downgrades
- Payment tracking
- Future expansion into billing systems

---

# 6. Constraints

- A Song must belong to exactly one Album
- A Playlist must belong to exactly one User
- A Subscription must belong to exactly one User
- PlaylistSong must reference valid Playlist and Song IDs
- Episode must belong to exactly one Podcast
- Genre must be assigned to Song

---

# 7. Phase 2: Use Cases & Query Requirements

## Use Case 1: Retrieve all songs in a user’s playlist

### Objective
Allow a user to view all songs contained in a specific playlist.

### Assumptions
- Playlist belongs to a valid user
- Songs are linked through PlaylistSong associative entity

### Expected Output
List of songs including title, duration, and order in playlist

### Pseudo-SQL
    SELECT Song.Title, Song.Duration, PlaylistSong.PositionInPlaylist
    FROM Playlist
    JOIN PlaylistSong ON Playlist.PlaylistID = PlaylistSong.PlaylistID
    JOIN Song ON PlaylistSong.SongID = Song.SongID
    WHERE Playlist.PlaylistID = [given_playlist_id];

## Use Case 2: Retrieve listening history for a user

### Objective
Show all songs a user has listened to along with timestamps and devices.

### Assumptions
- ListeningHistory tracks all streaming events

### Expected Output
List of songs with timestamp and device used

### Pseudo-SQL
    SELECT Song.Title, ListeningHistory.Timestamp, Device.DeviceType
    FROM ListeningHistory
    JOIN Song ON ListeningHistory.SongID = Song.SongID
    JOIN Device ON ListeningHistory.DeviceID = Device.DeviceID
    WHERE ListeningHistory.UserID = [given_user_id];

## Use Case 3: Find all albums by a specific artist

### Objective
Retrieve all albums released by a given artist.

### Expected Output
List of album titles and release dates

### Pseudo-SQL
    SELECT Album.Title, Album.ReleaseDate
    FROM Artist
    JOIN Album ON Artist.ArtistID = Album.ArtistID
    WHERE Artist.ArtistID = [given_artist_id];

## Use Case 4: Get all songs in an album

### Objective
Display all songs belonging to a specific album.

### Expected Output
Song titles and durations

### Pseudo-SQL
    SELECT Song.Title, Song.Duration
    FROM Album
    JOIN Song ON Album.AlbumID = Song.AlbumID
    WHERE Album.AlbumID = [given_album_id];

## Use Case 5: Find all artists a user follows

### Objective
Show all artists followed by a specific user.

### Expected Output
Artist names and genres

### Pseudo-SQL
    SELECT Artist.ArtistName, Artist.Genre
    FROM ArtistFollow
    JOIN Artist ON ArtistFollow.ArtistID = Artist.ArtistID
    WHERE ArtistFollow.UserID = [given_user_id];

## Use Case 6: Retrieve podcast episodes

### Objective
Get all episodes for a specific podcast.

### Expected Output
Episode titles and durations

### Pseudo-SQL
    SELECT PodcastEpisode.EpisodeTitle, PodcastEpisode.Duration
    FROM Podcast
    JOIN PodcastEpisode ON Podcast.PodcastID = PodcastEpisode.PodcastID
    WHERE Podcast.PodcastID = [given_podcast_id];


# 8. Phase 3: SQL Queries & Strategic Insights  

## Query 1 — Most Streamed Songs
### Purpose
This query identifies the songs with the highest number of streams across the platform. It uses aggregation functions and grouping to count how many times each song appears in the ListeningHistory table.

### SQL Query
    SELECT Song.Title, COUNT(ListeningHistory.SongID) AS StreamCount
    FROM Song
    JOIN ListeningHistory
    ON Song.SongID = ListeningHistory.SongID
    GROUP BY Song.Title
    ORDER BY StreamCount DESC;
### Strategic Insight
This query helps identify trending or popular songs among users. Spotify could use this information to build charts, playlists, or recommendation systems. Music labels and artists may also use this data to evaluate song popularity and audience engagement.

## Query 2 — Users with the Most Playlists
### Purpose
This query retrieves users who have created the highest number of playlists using aggregation and grouping.

### SQL Query
    SELECT User.Username, COUNT(Playlist.PlaylistID) AS PlaylistCount
    FROM User
    JOIN Playlist
    ON User.UserID = Playlist.UserID
    GROUP BY User.Username
    ORDER BY PlaylistCount DESC;
    Strategic Insight
### Strategic Insight
This query can help identify highly active users on the platform. Users who create many playlists are likely more engaged and may be good candidates for premium subscription promotions or social/community features.

## Query 3 — Average Song Duration by Genre
### Purpose
This query calculates the average duration of songs grouped by genre.

### SQL Query
    SELECT Genre.GenreName, AVG(Song.Duration) AS AverageDuration
    FROM Song
    JOIN Genre
    ON Song.GenreID = Genre.GenreID
    GROUP BY Genre.GenreName;
    Strategic Insight
### Strategic Insight
This information could help analysts understand listening trends across genres. For example, some genres may generally contain shorter songs while others contain longer tracks. This may influence playlist generation or recommendation behavior.

## Query 4 — Artists with More Than 5 Followers
### Purpose
This query uses GROUP BY and HAVING to identify artists followed by more than five users.

### SQL Query
    SELECT Artist.ArtistName, COUNT(ArtistFollow.UserID) AS FollowerCount
    FROM Artist
    JOIN ArtistFollow
    ON Artist.ArtistID = ArtistFollow.ArtistID
    GROUP BY Artist.ArtistName
    HAVING COUNT(ArtistFollow.UserID) > 5;
    Strategic Insight
### Strategic Insight
This query identifies artists with strong user engagement. Spotify could use this information for homepage recommendations, promotional campaigns, or featured artist sections.

## Query 5 — Users Listening to Songs from a Specific Genre
### Purpose
This query uses a nested query to retrieve users who have listened to songs belonging to a specific genre.

### SQL Query
    SELECT Username
    FROM User
    WHERE UserID IN (
        SELECT ListeningHistory.UserID
        FROM ListeningHistory
        JOIN Song
        ON ListeningHistory.SongID = Song.SongID
        JOIN Genre
        ON Song.GenreID = Genre.GenreID
        WHERE Genre.GenreName = 'Pop'
    );

### Strategic Insight
This query helps identify users interested in specific genres of music. The platform could use this information for targeted recommendations, advertisements, curated playlists, or genre-specific notifications.

# 9. Conclusion

This phase 3 extends the database design by introducing queries. They expand the database project by demonstrating the use of joins, aggregations, grouping, HAVING clauses, WHERE clauses, ordering, and nested queries. In the real world this project also shows how companies can use these techniques to provide valuable business insights related to their user engagement, music popularity, artist performance, and listening behavior of users.

---

# 10. Appendix

## ER Diagram
Removed due to technical issues(same as Phase 1)

## External Tools
https://www.visual-paradigm.com/features/database-design-with-erd-tools/
https://sqlfiddle.com/
Visual Studio Code (Windows OS)


