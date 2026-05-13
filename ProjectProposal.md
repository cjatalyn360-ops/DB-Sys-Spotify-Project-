# Music Streaming Database System Proposal — Phase 1

**Name of System:** Spotify Music Streaming Database System  
**Author:** Connor Prout  
**Course:** CSCI-C 442 Database Systems  
**Date:** May 13, 2026  
**Version:** 1.3.0 

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

---

# Table of Contents

1. Introduction  
2. Entity-Relationship (ER) Diagram Overview  
3. Entities and Data Dictionary  
4. Relationship Summary (Cardinality)  
5. Design Justifications  
6. Constraints  
7. Phase 2: Use Cases & Query Requirements  
8. Conclusion  
9. Appendix (ER Diagram Reference)  

---

# 1. Introduction

This phase builds upon the previous ER design + proposal + phase 1 by introducing the **use cases and query requirements** section. These use cases define how the database will be used in practice and help validate whether the current ER model properly supports real-world interactions in a music streaming system, as well as psuedocode for its future implementation.

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
-List of album titles and release dates

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



# 8. Conclusion

This phase 2 extends the database design by introducing realistic use cases and query requirements. The existing ER model supports all identified queries without requiring major redesign. Will be using the existing structure for implementation in future phase.

---

# 9. Appendix

## ER Diagram
<img width="3037" height="1528" alt="Project Phase 1_ ER Diagrams" src="https://github.com/user-attachments/assets/371b3971-3cf3-4c65-a501-3ee403266305" />

## External Tools
https://www.visual-paradigm.com/features/database-design-with-erd-tools/
https://sqlfiddle.com/
Visual Studio Code (Windows OS)


