# Social Network Analyzer

A full-stack social network simulation and analysis tool built with C++, PHP, MySQL, and vanilla JavaScript. Users register, pick friends from the existing user pool, and visualize the resulting network as an interactive graph — with real graph algorithms (centrality, community detection, echo chambers, PageRank) running under the hood.

---

## Overview

The app has two halves:

- **A C++ seed script** (`main.cpp`) that generates a randomized network of users, runs graph analysis on it, and exports the result as SQL.
- **A web app** (PHP + MySQL + vanilla JS/Canvas) where users can register, connect with existing users, and explore the live network through an interactive, force-rendered graph.

---

## Features

- **User auth** — register with a username, password, and primary interest; login to restore your network
- **Friend picker** — select connections from the existing user pool on signup
- **Interactive graph** — Canvas-rendered force-layout graph with hover highlighting; nodes color-coded by role (you / direct friends / others)
- **Degree centrality** — identifies the most-connected users (influencers) in the network
- **Community detection** — DFS-based traversal groups users into connected components
- **Echo chamber detection** — flags users where ≥60% of connections share the same interest
- **Graph seed script** — C++ program generates a randomized 12-user network and exports it to SQL

---

## Tech Stack

| Layer | Technology |
|---|---|
| Graph logic / seed | C++ (STL, `<random>`) |
| Backend APIs | PHP 8+ |
| Database | MySQL (`sim_network` DB) |
| Frontend | HTML5, CSS3, Vanilla JS (Canvas API) |
| Local server | XAMPP (Apache + MySQL) |

---

## Project Structure

```
Social_Media_Analyzer/
├── index.html          # Full single-page frontend (auth → friend picker → dashboard)
├── main.cpp            # C++ seed script — generates network & exports data.sql
├── data.sql             # Pre-generated seed data (12 nodes, 14 edges)
├── register.php        # User registration endpoint
├── getNetwork.php      # Returns all nodes + edges as JSON
├── addEdges.php        # Saves a friendship connection to the DB
└── saveNetwork.php     # Replaces entire network state in the DB
```

---

## Database Schema

```sql
CREATE DATABASE sim_network;
USE sim_network;

CREATE TABLE nodes (
    id       INT PRIMARY KEY,
    name     VARCHAR(100),
    interest VARCHAR(100)
);

CREATE TABLE edges (
    id   INT AUTO_INCREMENT PRIMARY KEY,
    src  INT,
    dest INT
);
```

---

## Setup & Installation

### Prerequisites

- [XAMPP](https://www.apachefriends.org/) (Apache + MySQL)
- A C++ compiler (g++ / MinGW)
- A web browser

### Steps

1. **Clone the project** into your XAMPP web root:
   ```
   C:\xampp\htdocs\Social_Media_Analyzer\
   ```

2. **Start XAMPP** — enable both Apache and MySQL from the control panel.

3. **Create the database** — open [phpMyAdmin](http://localhost/phpmyadmin), create a database named `sim_network`, then run the schema above.

4. **Import seed data** — import `data.sql` into the `sim_network` database, or run the C++ seed script to generate fresh data (see below).

5. **Open the app** in your browser:
   ```
   http://localhost/Social_Media_Analyzer/index.html
   ```

---

## Running the C++ Seed Script

The seed script generates a randomized network of 12 users with shuffled names and random interests, runs graph analysis, and writes the result to `data.sql`.

```bash
g++ main.cpp -o seed
./seed
```

Then re-import the generated `data.sql` into your database.

> **Note:** The output path in `main.cpp` is currently hardcoded to `C:/xampp/htdocs/Social Network Analyzer/data.sql`. Update this path to match your folder name/location before running.

---

## Graph Algorithms

All four algorithms are implemented in `main.cpp` and run on the generated graph:

| Algorithm | Description |
|---|---|
| **Degree Centrality** | Finds the user(s) with the most connections |
| **DFS Community Detection** | Groups the graph into connected components using depth-first search |
| **Echo Chamber Detection** | Flags nodes where ≥60% of neighbors share the same interest |
| **PageRank** | Ranks users by influence using 10 iterations of the PageRank formula (damping factor = 0.85) |

---

## API Endpoints

| File | Method | Description |
|---|---|---|
| `register.php` | GET | Returns `{"status": "test_ok"}` (stub) |
| `getNetwork.php` | GET | Returns `{"nodes": [...], "edges": [...]}` as JSON |
| `addEdges.php` | POST | Adds a friendship edge (`userId`, `friendId`) |
| `saveNetwork.php` | POST | Replaces all nodes and edges with a new network payload |

---

## Known Limitations

- Passwords are stored in plaintext — add hashing (e.g. `password_hash()`) before any real deployment
- `register.php` is currently a stub and doesn't persist users to the database
- PHP queries use string interpolation in places (e.g. `addEdges.php`) — switch to prepared statements to prevent SQL injection
- The C++ seed path is hardcoded to a Windows XAMPP path
- PageRank is computed in C++ only; the frontend dashboard currently shows degree centrality as the influencer metric

---

## Future Improvements

- Persist user accounts properly (hashed passwords, real registration flow)
- Surface PageRank results in the dashboard alongside degree centrality
- Make the seed script's output path configurable (CLI arg or relative path)
- Add prepared statements / parameterized queries across all PHP endpoints
