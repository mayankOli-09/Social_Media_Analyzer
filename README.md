# 🌐 Social Network Analyzer — Graph-Powered Social Simulation

<div align="center">

**Visualize your network, detect echo chambers & find influencers — before you even post**

*Community Detection • Echo Chamber Flags • Degree Centrality • PageRank • Force-Layout Graph*

[![Made with C++](https://img.shields.io/badge/Algorithms-C%2B%2B-blue?logo=cplusplus)](https://isocpp.org/)
[![Made with PHP](https://img.shields.io/badge/Backend-PHP%208%2B-purple?logo=php)](https://www.php.net/)
[![Made with JS](https://img.shields.io/badge/Frontend-JavaScript-yellow?logo=javascript)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![MySQL](https://img.shields.io/badge/Database-MySQL-orange?logo=mysql)](https://www.mysql.com/)
[![XAMPP](https://img.shields.io/badge/Server-XAMPP-red?logo=apache)](https://www.apachefriends.org/)

</div>

---

## 🎯 What is Social Network Analyzer?

**Social Network Analyzer** is a full-stack social network simulation and analysis tool. Users register, pick friends from the existing user pool, and watch their connections appear as a live, interactive Canvas-rendered graph.

Under the hood, real graph algorithms — **Degree Centrality, DFS Community Detection, Echo Chamber Detection, and PageRank** — run on a C++-generated network seed. The frontend reads that data via PHP APIs and renders it visually so you can see who the real influencers are, which communities cluster together, and who's trapped in an echo chamber.

> 💡 **Think of it as a social network you can dissect — built so you can see exactly how the graph math works.**

---

## ✨ Key Features

| Feature | How It Works |
|---|---|
| 🔐 **User Auth** | Register with a username, password, and primary interest; login to restore your network |
| 🤝 **Friend Picker** | On signup, pick connections from the existing user pool — they're added as edges in the graph |
| 🕸️ **Interactive Graph** | Canvas-rendered force-layout graph with hover highlighting; nodes are color-coded by role (you / direct friends / others) |
| 📊 **Degree Centrality** | Identifies the most-connected users — the influencers — by counting edges per node |
| 🧭 **Community Detection** | DFS-based traversal groups all users into connected components (social clusters) |
| 🔊 **Echo Chamber Detection** | Flags any user where ≥60% of their connections share the same interest |
| 🏆 **PageRank** | Ranks users by influence using 10 iterations of the PageRank formula (damping factor = 0.85) |
| 🌱 **C++ Seed Script** | Generates a randomized 12-user, 14-edge network and exports it directly to SQL |

---

## 🏗️ Project Structure

```
Social_Media_Analyzer/
├── index.html          # Full single-page frontend (auth → friend picker → dashboard)
├── main.cpp            # C++ seed script — generates network & exports data.sql
├── data.sql            # Pre-generated seed data (12 nodes, 14 edges)
├── register.php        # User registration endpoint (stub)
├── getNetwork.php      # Returns all nodes + edges as JSON
├── addEdges.php        # Saves a friendship connection to the DB
└── saveNetwork.php     # Replaces entire network state in the DB
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Graph Logic / Seed** | C++ (STL, `<random>`) |
| **Backend APIs** | PHP 8+ |
| **Database** | MySQL (`sim_network` DB) |
| **Frontend** | HTML5, CSS3, Vanilla JS (Canvas API) |
| **Local Server** | XAMPP (Apache + MySQL) |

---

## 🗄️ Database Schema

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

## 📸 How It Works

```
🌱  C++ seed script generates 12 users with random interests & edges

        │
        ▼
🗄️  data.sql is imported into MySQL (sim_network DB)

        │
        ▼
🔐  User registers via index.html → picks friends from the pool

        │
        ▼
🔗  addEdges.php writes new friendship edges to the DB

        │
        ▼
📡  getNetwork.php returns all nodes + edges as JSON to the frontend

        │
        ▼
🕸️  Canvas graph renders force-layout with color-coded node roles

        │
        ▼
📊  Dashboard shows:  Influencers 🏆 | Communities 🧭 | Echo Chambers 🔊
```

---

## ⚙️ Setup & Installation

### Prerequisites

- [XAMPP](https://www.apachefriends.org/) (Apache + MySQL)
- A C++ compiler (`g++` / MinGW)
- A web browser

### Steps

**1. Clone the project** into your XAMPP web root:
```
C:\xampp\htdocs\Social_Media_Analyzer\
```

**2. Start XAMPP** — enable both Apache and MySQL from the control panel.

**3. Create the database** — open [phpMyAdmin](http://localhost/phpmyadmin), create a database named `sim_network`, then run the schema above.

**4. Import seed data** — import `data.sql` into the `sim_network` database, or run the C++ seed script to generate a fresh network (see below).

**5. Open the app** in your browser:
```
http://localhost/Social_Media_Analyzer/index.html
```

---

## 🧬 Running the C++ Seed Script

The seed script generates a randomized network of 12 users with shuffled names and random interests (Dancing, Music, Coding, Politics, Sports), runs all four graph algorithms, and writes the result to `data.sql`.

```bash
g++ main.cpp -o seed
./seed
```

Then re-import the generated `data.sql` into your database.

> ⚠️ **Note:** The output path in `main.cpp` is hardcoded to `C:/xampp/htdocs/Social Network Analyzer/data.sql`. Update this path to match your actual folder name/location before running.

---

## 🧮 Graph Algorithms

All four algorithms are implemented in `main.cpp` and run on the generated graph at seed time:

| Algorithm | Description |
|---|---|
| 📈 **Degree Centrality** | Finds the user(s) with the most connections — the network's top influencers |
| 🧭 **DFS Community Detection** | Groups the graph into connected components using recursive depth-first search |
| 🔊 **Echo Chamber Detection** | Flags nodes where ≥60% of neighbors share the same interest as the user |
| 🏆 **PageRank** | Ranks users by recursive influence; 10 iterations, damping factor = 0.85 |

---

## 📡 API Endpoints

| File | Method | Description |
|---|---|---|
| `register.php` | GET | Returns `{"status": "test_ok"}` — currently a stub |
| `getNetwork.php` | GET | Returns `{"nodes": [...], "edges": [...]}` as JSON |
| `addEdges.php` | POST | Adds a friendship edge given `userId` and `friendId` |
| `saveNetwork.php` | POST | Replaces all nodes and edges with a new full network payload |

---

## ⚠️ Known Limitations

- Passwords are stored in **plaintext** — add `password_hash()` before any real deployment
- `register.php` is a stub and **does not persist users** to the database
- `addEdges.php` uses string interpolation in SQL queries — switch to **prepared statements** to prevent SQL injection
- The C++ seed path is **hardcoded** to a Windows XAMPP path
- **PageRank** is computed in C++ only; the frontend dashboard currently shows degree centrality as the influencer metric

---

## 🔮 Roadmap

- [ ] Make `register.php` actually persist users with hashed passwords
- [ ] Surface PageRank scores in the frontend dashboard
- [ ] Make the C++ seed output path configurable via CLI arg
- [ ] Replace all raw SQL string interpolation with prepared statements
- [ ] Add ability to delete friendships / remove nodes from the graph

---

<div align="center">

⭐ **If this project helped you understand graph algorithms, star the repo and share it!** ⭐

*Build networks. Run the math. See the truth.*

</div>
