# Multicriteria Flight Itinerary Planner

A command-line flight itinerary planner written in **C++17** that models an airline dataset as a weighted, directed graph and finds routes according to two different optimization criteria:

- **Fewest layovers:** Finds a route with the minimum number of flight connections using Breadth-First Search (BFS).
- **Cheapest route:** Finds a route with the minimum total fare using Dijkstra's shortest-path algorithm with a min-heap priority queue.

The application processes **10,683 flight records** covering major Indian cities and supports both scripted demonstrations and interactive queries.

## How It Works

The application follows a simple data-processing and pathfinding pipeline:

```text
processed_data.csv
        |
        v
     parseCSV()
        |
        v
   vector<Flight>
        |
        v
    buildGraph()
        |
        +-- CityEncoder
        |     Maps city names to integer node IDs
        |
        +-- Adjacency list
              Stores neighboring cities and fares
        |
        v
   Source, destination, and query mode
        |
        +---------------------+
        |                     |
        v                     v
       BFS                Dijkstra
   Fewest hops           Lowest fare
        |                     |
        +----------+----------+
                   |
                   v
          Reconstruct route
                   |
                   v
       Display cities and results
```

### Design Highlights

- **Integer-based city encoding:** Maps city names to integer node IDs, allowing the graph to use a vector-based adjacency list for efficient access.
- **Adjacency-list representation:** Stores outgoing connections for each city along with their associated fares.
- **Parallel-edge deduplication:** When multiple records connect the same source and destination, retains the minimum fare for that directed city pair.
- **Two optimization criteria:** Uses BFS to minimize the number of edges and Dijkstra's algorithm to minimize the sum of edge weights.
- **Path reconstruction:** Tracks parent nodes during graph traversal to reconstruct and display the selected itinerary.

Both algorithms operate on the same graph. BFS ignores fare weights, whereas Dijkstra uses them to determine the lowest-cost route.

## Algorithms and Complexity

Let:

- \(V\) = number of cities (vertices)
- \(E\) = number of distinct directed routes (edges) after deduplication
- \(N\) = number of flight records in the dataset

| Operation | Algorithm | Time Complexity |
|---|---|---|
| Graph construction | Single pass over flight records, excluding implementation-specific lookup costs | Approximately \(O(N)\) with average-case hash-table operations |
| Fewest layovers | BFS | \(O(V + E)\) |
| Cheapest route | Dijkstra with a binary heap | \(O((V + E)\log V)\) |

The graph's adjacency-list representation makes it possible to explore only the outgoing connections of each visited city.

## Requirements

- A C++ compiler supporting C++17
- `make` for using the provided Makefile
- `processed_data.csv` in the working directory

## Build and Run

### Compile the program

```bash
make
```

Alternatively, compile directly:

```bash
g++ -O2 -std=c++17 -o flight_planner flight_planner.cpp
```

### Run the scripted demonstration

```bash
./flight_planner --demo
```

The demo runs predefined queries without requiring keyboard input.

### Run interactively

```bash
./flight_planner
```

The program reads `processed_data.csv` from the current working directory.

### Build and run the demo with Make

```bash
make demo
```

The Makefile also provides a cleanup target:

```bash
make clean
```

## Sample Demonstration

The scripted demo loads the dataset, constructs the graph, and executes five example queries.

### 1. Cheapest route: Banglore to Delhi

```text
[Cheapest Route — Dijkstra]
Route:      Banglore -> Delhi
Total Cost: Rs. 3257
Layovers:   0
```

### 2. Fewest layovers: Banglore to Cochin

```text
[Minimum Layovers — BFS]
Route:    Banglore -> Delhi -> Cochin
Layovers: 1
```

### 3. Cheapest route: Banglore to Cochin

```text
[Cheapest Route — Dijkstra]
Route:      Banglore -> Delhi -> Cochin
Total Cost: Rs. 7133
Layovers:   1
```

### 4. Unreachable route: Mumbai to Delhi

```text
No route found.
```

### 5. Longer itinerary: Chennai to Cochin

```text
[Cheapest Route — Dijkstra]
Route:      Chennai -> Kolkata -> Banglore -> Delhi -> Cochin
Total Cost: Rs. 13758
Layovers:   3
```

The reported fare for a city pair is the minimum fare retained for that directed connection in the processed dataset, rather than necessarily the fare in its first matching row.

A demonstration recording is available at [`demo/demo.mp4`](https://github.com/AnuragBand-07/flight-itinerary-planner/blob/main/demo/demo.mp4).

## Dataset

The application reads `processed_data.csv`, containing **10,683 flight records** with fields such as airline, source city, destination city, date/time information, and price.

- **Currency:** Indian rupees (INR)
- **Graph model:** Weighted, directed graph
- **City names:** Must match the dataset's spelling and capitalization

For example, the dataset uses `Banglore` rather than `Bengaluru`.

The graph represents the connections present in the processed dataset. If no directed path exists between two cities, the planner reports that no route is available.

## Project Structure

```text
.
├── flight_planner.cpp    # C++ implementation
├── processed_data.csv   # Flight dataset
├── Makefile              # Build and demo commands
└── README.md              # Project documentation
```

## Future Improvements

- Add input validation and clearer error handling.
- Expand automated testing for unreachable destinations and edge cases.
- Extend the dataset and support additional route constraints.
- Explore additional optimization criteria, such as travel duration or the number of airline changes.

