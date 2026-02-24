# 🛰️ Starlink Latency Tracker - Project Documentation 

## Project Overview
Starlink Latency Tracker is a real-time visualization and routing simulation tool for the Starlink satellites. It calculates optimal communication paths between any two points on Earth using Inter-Satellite Links (ISL) and simulates real-world constraints like orbital mechanics and weather-induced signal degradation (Rain Fade).

The project compares two routing strategies:
1.  **HFT (High-Frequency Trading) Route**: Prioritizes minimum absolute latency using all available Inter-Satellite Links (up to 1500km).
2.  **Standard Route**: Prioritizes "stable" links with shorter hops (up to 800km) to simulate reliable commercial connectivity.

---

## System Architecture

The application follows a classic Frontend/Backend decoupled architecture:

### **Backend (Python / FastAPI)**
- **API Layer**: Handles request routing, graph lifecycle management (rebuilding every 60s), and pathfinding logic.
- **Orbital Engine**: Uses `Skyfield` to propagate satellite TLEs (Two-Line Elements) to Geocentric coordinates and builds a dynamic connectivity graph using `NetworkX`.
- **Data Fetcher**: Periodically fetches and caches fresh TLE data from CelesTrak.
- **Weather Service**: Integrates with Open-Meteo API to simulate Ka-band rain fade penalties on ground-to-satellite links.

### **Frontend (HTML5 / Vanilla JS / Leaflet)**
- **Visualization and UI**: Renders thousands of active satellites on a 2D canvas map. (with API Layer)
- **Interaction**: Allows users to select Start/End points via Map clicks or Geocoder search. (For now, we can provide a simple introduction focusing on capturing two points.)
- **Real-time HUD**: Displays latency statistics, hop counts, and weather risk levels.

---

## Core Algorithms

### **1. Graph Construction (ISL Connectivity)**
- **Spatial Indexing**: Proximity searches among ~9500+ satellites.
- **Line-of-Sight Check**: Implements a geometric check to ensure inter-satellite links do not pass through the Earth's core.
- **Edge Weighting**:
  `Weight = (Distance / Speed_of_Light) + Router_Processing_Delay (5ms)`
  We can add a 5ms latency between each router.

### **2. Pathfinding**
- **Dijkstra's Algorithm**: We can use `networkx.shortest_path` to find the minimum weight (latency) path.
- **Routing Constraints**:
  We can try two separate approaches here. First, we maximize the range, minimizing the number of routers used, and attempting to achieve low latency.
  Secondly, we take a more reliable approach, minimizing the risk of packet loss and therefore using more routers.
    - **HFT**: Unrestricted search on the full graph.
    - **Standard**: Restricted to a subgraph where edges are ≤ 800km.

### **3. Rain Fade Simulation (Ka-Band)**
- Ground-to-satellite links are penalized based on WMO weather codes:
    - **Low Risk (Clear)**: 0ms penalty.
    - **Medium Risk (Clouds/Light Rain)**: +5ms penalty / 5% packet loss.
    - **High Risk (Storm/Heavy Rain)**: +25ms penalty / 20% packet loss.

---

## 🛠️ Technical Stack
| Component | Technology |
| :--- | :--- |
| **Backend** | Python 3.9+ |
| **Web Framework** | FastAPI / Uvicorn (Web server implementation for Python) |
| **Orbital Mechanics** | Skyfield (SGP4) (Python Library) |
| **Graph Theory** | NetworkX (Python Library) |
| **Spatial Math** | NumPy / SciPy (Python Library) |
| **Map Rendering** | Leaflet.js |
| **Weather Data** | Open-Meteo API |

---

## 🚀 Setup & Installation

### **Quick Start**


## 📝 Configuration

