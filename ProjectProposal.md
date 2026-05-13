# Music Streaming Database System Proposal

**Name of System:** Spotify Music Streaming Database System  
**Author:** Connor Prout  
**Course:** CSCI-C 442 Database Systems  
**Date:** May 12, 2026  
**Version:** 1.0.0  

---

## Revision History

| Date | Version | Note |
|---|---|---|
| 5/12/26 | 1.0.0 | Initial Phase 0 proposal |

---

# Table of Contents

1. Introduction  
   - Purpose  
   - Project Scope  
   - Intended Audience  

2. High-Level System Overview  
   - System Functionality  
   - Key Use Cases  

3. Preliminary Data Model  
   - Entities and Attributes  
   - Relationships  

4. Conclusion

5. Appendix

---

# 1. Introduction

## Purpose

The purpose of the Spotify Music Streaming Database System is to design and build a database capable of supporting a large-scale music streaming platform. The database will manage users, songs, albums, artists, playlists, subscriptions, and more as the project develops.

The system is intended to efficiently store and retrieve music-related data while effetively showing relationships between entities.

---

## Project Scope

### What the system covers
- User account management
- Music library management
- Artists and albums
- Playlist creation and management
- Song streaming history
- User likes/saved songs
-

### What the system does NOT cover:
- Audio file storage implementation
- Music recommendation algorithms
- Real-time streaming infrastructure
- Payment processing systems
- Frontend/mobile application development

---

## Intended Audience

### End Users
Listeners who stream music, create playlists, follow artists, and manage accounts.

### Developers
Backend developers integrating application features with the database system.

### Administrators
System administrators responsible for maintaining platform data integrity and monitoring usage.

### Business Analysts
Personnel analyzing streaming trends, artist popularity, and user engagement statistics.

---

# 2. High-Level System Overview

## System Functionality

The Spotify Music Streaming Database System will:
- Store user account and profile information
- Manage artists, albums, songs, and podcasts
- Track playlists created by users
- Record listening history and song interactions
- Support followers for artists and playlists
- Store subscription information for premium users
- Maintain relationships between songs, albums, and artists

---

## Key Use Cases

### 1. User Registration & Login
Users create accounts, log in, and manage profile information.

### 2. Music Streaming
Users stream songs and podcasts from the platform.

**Example:**  
A user searches for a song and streams it from an album page.

### 3. Playlist Creation
Users create playlists and add or remove songs.

### 4. Artist Following
Users follow artists to receive updates about new music releases.

### 5. Library Management
Users save songs, albums, and playlists to their personal library.

### 6. Subscription Management
The platform tracks whether users are using free or premium accounts.

---

# 3. Preliminary Data Model

This section will contain any entities, attributes, and relationships for the database system shown through an ERD.

---

## Entities and Attributes

### User
**Attributes:**
- UserID (PK)
- Username
- Email
- Password
- DateCreated
- Country
- SubscriptionType

**Relationships:**
- Creates Playlists
- Streams Songs
- Follows Artists
- Saves Songs

---

### Artist
**Attributes:**
- ArtistID (PK)
- ArtistName
- Genre
- MonthlyListeners
- Biography

**Relationships:**
- Creates Albums
- Performs Songs

---

### Album
**Attributes:**
- AlbumID (PK)
- Title
- ReleaseDate
- AlbumType
- ArtistID (FK)

**Relationships:**
- Contains Songs

---

### Song
**Attributes:**
- SongID (PK)
- Title
- Duration
- Genre
- ReleaseDate
- AlbumID (FK)

**Relationships:**
- Belongs to Album
- Appears in Playlists
- Streamed by Users

---

### Playlist
**Attributes:**
- PlaylistID (PK)
- PlaylistName
- CreationDate
- Visibility
- UserID (FK)

**Relationships:**
- Contains Songs
- Owned by User

---

### PlaylistSong
(Associative entity for many-to-many relationship)

**Attributes:**
- PlaylistID (FK)
- SongID (FK)
- DateAdded
- PositionInPlaylist

---

### Subscription
**Attributes:**
- SubscriptionID (PK)
- UserID (FK)
- PlanType
- StartDate
- EndDate
- PaymentStatus

---

### ListeningHistory
**Attributes:**
- HistoryID (PK)
- UserID (FK)
- SongID (FK)
- Timestamp
- DeviceUsed

---

### Podcast
**Attributes:**
- PodcastID (PK)
- PodcastTitle
- Category
- ReleaseDate
- HostName

---

### SavedSong
**Attributes:**
- SaveID (PK)
- UserID (FK)
- SongID (FK)
- DateSaved

---

# Relationships

## User — Playlist
**One-to-Many**  
A user can create multiple playlists.

---

## Artist — Album
**One-to-Many**  
An artist can create multiple albums.

---

## Album — Song
**One-to-Many**  
An album contains multiple songs.

---

## Playlist — Song
**Many-to-Many**  
A playlist contains many songs, and songs may appear in many playlists.

Implemented through the `PlaylistSong` associative entity.

---

## User — Song (Listening History)
**Many-to-Many**  
Users can stream many songs, and songs can be streamed by many users.

Implemented through the `ListeningHistory` entity.

---

## User — Artist
**Many-to-Many**  
Users may follow multiple artists, and artists may have many followers.

---

## User — SavedSong
**One-to-Many**  
A user can save many songs to their library.

---

# 4. Future Expansion Ideas

Potential future features for later project phases include:
- Friend/follower social system
- Collaborative playlists
- Song recommendation system
- Concert/event tracking
- Lyrics support
- Podcast episode tracking
- Advertisement management for free users
- Music label management

# 5. Conclusion
--- 
