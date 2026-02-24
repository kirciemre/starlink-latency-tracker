# 🛰️ Starlink Latency Tracker - Project Documentation 

## Project Overview
Starlink Latency Tracker is a real-time visualization and routing simulation tool for the Starlink satellites. It calculates optimal communication paths between any two points on Earth using Inter-Satellite Links (ISL) and simulates real-world constraints like orbital mechanics and weather-induced signal degradation (Rain Fade).

The inspiration for this project came from the HFTTracker application we discussed in HFT class. As we have covered in class, the speed of light in fiber optic cables (~200,000 km/s) is significantly lower than the speed of light in a vacuum (~300,000 km/s). Theoretically, the Starlink LEO (Low Earth Orbit) network can outperform transoceanic fiber lines in terms of speed. However, this advantage is contingent upon optimizing the number of inter-satellite "hops" (acting as routers) and minimizing processing delays. This challenge transforms the scenario into a Shortest Path Optimization problem over a dynamic network.

The project compares two routing strategies:
1.  **HFT (High-Frequency Trading) Route**: Prioritizes minimum absolute latency using all available Inter-Satellite Links (up to 1500km).
2.  **Standard Route**: Prioritizes "stable" links with shorter hops (up to 800km) to simulate reliable commercial connectivity.

## Disclaimer 
This project is not affiliated with, authorized, or endorsed by Starlink or SpaceX. It is an independent educational simulation. Latency calculations are theoretical estimates based on TLE data and Dijkstra's algorithm; actual results may vary due to real-time network conditions and environmental factors.

---

## System Architecture

The application follows a classic Frontend/Backend decoupled architecture:

### **Backend (Python / FastAPI)**
- **API Layer**: Handles request routing, graph lifecycle management (rebuilding every 60s), and pathfinding logic.
  - TLE data can be obtained from CelesTrak API or passive radar using LEO signals once you have real time locations of the satellite array may be used.
  - CelesTrak endpoint address: https://celestrak.org/NORAD/elements/gp.php?GROUP=starlink&FORMAT=tle
  - Sample Data:
STARLINK-1008           
1 44714U 19074B   26054.54089312  .00000969  00000+0  43797-4 0  9997
2 44714  53.1568 244.5638 0001273  95.0474 265.0672 15.31036083346702
STARLINK-1012           
1 44718U 19074F   26054.51354291  .00003198  00000+0  12005-3 0  9993
2 44718  53.1615 244.6539 0001009  82.1810 277.9305 15.30200706346695
STARLINK-1017           
1 44723U 19074L   26054.76604862  .00034704  00000+0  14784-2 0  9990
2 44723  53.0507 235.6253 0003379 133.3313 226.7965 15.22666233346845
    
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
  `Weight = (Distance / Speed_of_Light) + Router_Processing_Delay (2ms)`
  We can add a 2ms latency between each router.

### **2. Pathfinding**
- **Dijkstra's Algorithm**: We can use `networkx.shortest_path` to find the minimum weight (latency) path.
- **Routing Constraints**:
  We can try two separate approaches here. First, we maximize the range, minimizing the number of routers used, and attempting to achieve low latency.
  Secondly, we take a more reliable approach, minimizing the risk of packet loss and therefore using more routers.
    - **HFT**: Finds the longest possible Inter-Satellite Links to minimize the number of "hops" (router processing steps). Since each hop adds a 2ms (the routers in Starlink are FPGA or special ASIC units so latency may drop 0.5ms but for precaution I simply use 2ms) delay, fewer hops result in lower total latency.
    - **Standard**: Restricted to ISL edges ≤ 800km. Using shorter, more stable links reduces the risk of packet loss. This typically results in more hops and higher latency but ensures a more reliable communication "pipe" for standard data traffic. 

### **3. Rain Fade Simulation (Ka-Band)**
- Ground-to-satellite links are penalized based on WMO weather codes:
    - **Low Risk (Clear)**: 0ms penalty.
    - **Medium Risk (Clouds/Light Rain)**: +5ms penalty / 5% packet loss.
    - **High Risk (Storm/Heavy Rain)**: +25ms penalty / 20% packet loss.

### **4. Uplink and Downlink** 
- Slant Range may change distance significantly,
- We need to calculate the dynamic slope of the satellite and calculate the correct distance.
---

## 🛠️ Technology Stack
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

