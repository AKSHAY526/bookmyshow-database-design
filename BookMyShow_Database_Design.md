# BookMyShow - Database Design & SQL Solution

## Problem Statement

Design the database schema for a movie ticketing platform (BookMyShow) where users can view shows running at a given theatre for the next 7 days. For a selected date, the system displays all movies along with their show timings.

---

## 1. Entity Identification

From the given scenario, the following entities are identified:

| Entity | Description |
|--------|-------------|
| **Theatre** | A cinema hall complex (e.g., PVR Nexus, INOX) located in a city |
| **Screen** | An individual auditorium inside a theatre with specific audio/visual capabilities |
| **Movie** | A film with attributes like title, language, certification, and format |
| **Show** | A scheduled screening that maps a movie to a screen on a specific date and time |

---

## 2. Table Structures & Attributes

### theatres

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| theatre_id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| name | VARCHAR(100) | NOT NULL | Theatre name |
| address | VARCHAR(255) | NOT NULL | Full address |
| city | VARCHAR(50) | NOT NULL | City where theatre is located |

### screens

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| screen_id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| theatre_id | INT | FOREIGN KEY → theatres(theatre_id), NOT NULL | Parent theatre |
| screen_name | VARCHAR(50) | NOT NULL | Display name (e.g., Screen 1) |
| screen_type | VARCHAR(50) | NOT NULL | Technology type (e.g., 4K ATMOS, Dolby 7.1) |
| seating_capacity | INT | NOT NULL | Total seats in this screen |

### movies

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| movie_id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| title | VARCHAR(150) | NOT NULL | Movie title |
| language | VARCHAR(30) | NOT NULL | Language (Hindi, English, Telugu, etc.) |
| format | VARCHAR(10) | NOT NULL | Display format (2D, 3D, IMAX) |
| certification | VARCHAR(5) | NOT NULL | Censor rating (UA, A, U, etc.) |
| duration_minutes | INT | NOT NULL | Runtime in minutes |

### shows

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| show_id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| movie_id | INT | FOREIGN KEY → movies(movie_id), NOT NULL | Movie being screened |
| screen_id | INT | FOREIGN KEY → screens(screen_id), NOT NULL | Screen where it plays |
| show_date | DATE | NOT NULL | Date of the show |
| start_time | TIME | NOT NULL | Show start time |

---

## 3. Normalization Verification

### First Normal Form (1NF)
- All columns contain atomic (indivisible) values.
- Each row is uniquely identified by a primary key.
- No repeating groups or arrays in any column.

### Second Normal Form (2NF)
- Already in 1NF.
- No partial dependencies exist — every non-key attribute depends on the entire primary key (single-column PKs eliminate partial dependency by definition).

### Third Normal Form (3NF)
- Already in 2NF.
- No transitive dependencies — for example, `screen_type` depends directly on `screen_id`, not through another non-key column. City is an attribute of the theatre itself, not derived through another field.

### Boyce-Codd Normal Form (BCNF)
- Already in 3NF.
- Every determinant is a candidate key. There are no functional dependencies where a non-candidate-key determines another attribute.

---

## 4. Sample Data

### theatres

| theatre_id | name | address | city |
|------------|------|---------|------|
| 1 | PVR: Nexus (Forum Mall) | Forum Mall, Koramangala | Bangalore |
| 2 | INOX: Garuda Mall | Garuda Mall, Magrath Road | Bangalore |

### screens

| screen_id | theatre_id | screen_name | screen_type | seating_capacity |
|-----------|------------|-------------|-------------|-----------------|
| 1 | 1 | Screen 1 | 4K ATMOS | 220 |
| 2 | 1 | Screen 2 | 4K Dolby 7.1 | 180 |
| 3 | 1 | Screen 3 | Dolby 7.1 | 150 |
| 4 | 1 | Screen 4 | Playhouse 4K | 120 |

### movies

| movie_id | title | language | format | certification | duration_minutes |
|----------|-------|----------|--------|---------------|-----------------|
| 1 | Dasara | Telugu | 2D | UA | 156 |
| 2 | Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 145 |
| 3 | Tu Jhoothi Main Makkaar | Hindi | 2D | UA | 150 |
| 4 | Avatar: The Way of Water | English | 3D | UA | 192 |

### shows

| show_id | movie_id | screen_id | show_date | start_time |
|---------|----------|-----------|-----------|------------|
| 1 | 1 | 2 | 2023-04-25 | 12:15:00 |
| 2 | 2 | 1 | 2023-04-25 | 13:00:00 |
| 3 | 2 | 1 | 2023-04-25 | 16:10:00 |
| 4 | 2 | 2 | 2023-04-25 | 18:20:00 |
| 5 | 2 | 1 | 2023-04-25 | 19:20:00 |
| 6 | 2 | 1 | 2023-04-25 | 22:30:00 |
| 7 | 3 | 3 | 2023-04-25 | 13:15:00 |
| 8 | 4 | 4 | 2023-04-25 | 13:20:00 |
| 9 | 1 | 2 | 2023-04-26 | 12:15:00 |
| 10 | 2 | 1 | 2023-04-26 | 13:00:00 |

---

## 5. SQL Solution - P1 (Table Creation & Sample Data)

```sql
-- ============================================
-- BookMyShow Database Schema
-- MySQL 8.x compatible
-- ============================================

CREATE DATABASE IF NOT EXISTS bookmyshow;
USE bookmyshow;

-- -------------------------------------------
-- Table: theatres
-- -------------------------------------------
CREATE TABLE theatres (
    theatre_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    address VARCHAR(255) NOT NULL,
    city VARCHAR(50) NOT NULL
);

-- -------------------------------------------
-- Table: screens
-- -------------------------------------------
CREATE TABLE screens (
    screen_id INT AUTO_INCREMENT PRIMARY KEY,
    theatre_id INT NOT NULL,
    screen_name VARCHAR(50) NOT NULL,
    screen_type VARCHAR(50) NOT NULL,
    seating_capacity INT NOT NULL,
    FOREIGN KEY (theatre_id) REFERENCES theatres(theatre_id)
);

-- -------------------------------------------
-- Table: movies
-- -------------------------------------------
CREATE TABLE movies (
    movie_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(150) NOT NULL,
    language VARCHAR(30) NOT NULL,
    format VARCHAR(10) NOT NULL,
    certification VARCHAR(5) NOT NULL,
    duration_minutes INT NOT NULL
);

-- -------------------------------------------
-- Table: shows
-- -------------------------------------------
CREATE TABLE shows (
    show_id INT AUTO_INCREMENT PRIMARY KEY,
    movie_id INT NOT NULL,
    screen_id INT NOT NULL,
    show_date DATE NOT NULL,
    start_time TIME NOT NULL,
    FOREIGN KEY (movie_id) REFERENCES movies(movie_id),
    FOREIGN KEY (screen_id) REFERENCES screens(screen_id)
);

-- ============================================
-- Sample Data Insertion
-- ============================================

-- Theatres
INSERT INTO theatres (name, address, city) VALUES
('PVR: Nexus (Forum Mall)', 'Forum Mall, Koramangala', 'Bangalore'),
('INOX: Garuda Mall', 'Garuda Mall, Magrath Road', 'Bangalore');

-- Screens for PVR Nexus (theatre_id = 1)
INSERT INTO screens (theatre_id, screen_name, screen_type, seating_capacity) VALUES
(1, 'Screen 1', '4K ATMOS', 220),
(1, 'Screen 2', '4K Dolby 7.1', 180),
(1, 'Screen 3', 'Dolby 7.1', 150),
(1, 'Screen 4', 'Playhouse 4K', 120);

-- Screens for INOX Garuda (theatre_id = 2)
INSERT INTO screens (theatre_id, screen_name, screen_type, seating_capacity) VALUES
(2, 'Screen 1', '4K Dolby ATMOS', 200),
(2, 'Screen 2', 'IMAX', 300);

-- Movies
INSERT INTO movies (title, language, format, certification, duration_minutes) VALUES
('Dasara', 'Telugu', '2D', 'UA', 156),
('Kisi Ka Bhai Kisi Ki Jaan', 'Hindi', '2D', 'UA', 145),
('Tu Jhoothi Main Makkaar', 'Hindi', '2D', 'UA', 150),
('Avatar: The Way of Water', 'English', '3D', 'UA', 192);

-- Shows at PVR Nexus on 2023-04-25
INSERT INTO shows (movie_id, screen_id, show_date, start_time) VALUES
(1, 2, '2023-04-25', '12:15:00'),
(2, 1, '2023-04-25', '13:00:00'),
(2, 1, '2023-04-25', '16:10:00'),
(2, 2, '2023-04-25', '18:20:00'),
(2, 1, '2023-04-25', '19:20:00'),
(2, 1, '2023-04-25', '22:30:00'),
(3, 3, '2023-04-25', '13:15:00'),
(4, 4, '2023-04-25', '13:20:00');

-- Shows at PVR Nexus on 2023-04-26
INSERT INTO shows (movie_id, screen_id, show_date, start_time) VALUES
(1, 2, '2023-04-26', '12:15:00'),
(2, 1, '2023-04-26', '13:00:00'),
(2, 1, '2023-04-26', '16:10:00'),
(3, 3, '2023-04-26', '14:00:00'),
(4, 4, '2023-04-26', '13:20:00');
```

---

## 6. SQL Solution - P2 (Query: List all shows on a given date at a given theatre)

```sql
-- ============================================
-- P2: List all shows on a given date at a given theatre
--     along with their respective show timings
-- ============================================

SELECT 
    m.title AS movie_title,
    m.language,
    m.format,
    m.certification,
    s.show_date,
    s.start_time,
    sc.screen_name,
    sc.screen_type
FROM 
    shows s
    JOIN movies m ON s.movie_id = m.movie_id
    JOIN screens sc ON s.screen_id = sc.screen_id
    JOIN theatres t ON sc.theatre_id = t.theatre_id
WHERE 
    t.theatre_id = 1
    AND s.show_date = '2023-04-25'
ORDER BY 
    m.title, s.start_time;
```

### Expected Output for P2

| movie_title | language | format | certification | show_date | start_time | screen_name | screen_type |
|-------------|----------|--------|---------------|-----------|------------|-------------|-------------|
| Avatar: The Way of Water | English | 3D | UA | 2023-04-25 | 13:20:00 | Screen 4 | Playhouse 4K |
| Dasara | Telugu | 2D | UA | 2023-04-25 | 12:15:00 | Screen 2 | 4K Dolby 7.1 |
| Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 2023-04-25 | 13:00:00 | Screen 1 | 4K ATMOS |
| Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 2023-04-25 | 16:10:00 | Screen 1 | 4K ATMOS |
| Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 2023-04-25 | 18:20:00 | Screen 2 | 4K Dolby 7.1 |
| Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 2023-04-25 | 19:20:00 | Screen 1 | 4K ATMOS |
| Kisi Ka Bhai Kisi Ki Jaan | Hindi | 2D | UA | 2023-04-25 | 22:30:00 | Screen 1 | 4K ATMOS |
| Tu Jhoothi Main Makkaar | Hindi | 2D | UA | 2023-04-25 | 13:15:00 | Screen 3 | Dolby 7.1 |

---
