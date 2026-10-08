# Cargills Ceylon PLC – Optimal Rice Distribution Routes (Excel Solver)

Operations Research assignment (226044G).
Cargills Ceylon PLC distributes rice in bulk from its largest warehouse in **Katana (Gampaha District)**.
This project finds the shortest route from the warehouse to every Cargills Food City outlet and back.

## Problem

1. **Unlimited truck capacity** – find the shortest route that visits every outlet and returns to the warehouse.
2. **Truck capacity 5,000 kg per trip** – change the model so the truck returns to the warehouse to reload when needed.

There are **20 outlets** plus the warehouse (21 locations). Total rice demand is **8,700 kg**, so a 5,000 kg truck needs at least 2 trips.

## Files

| File | Description |
|---|---|
| `226044G_OR_Assignment.xlsx` | Excel workbook with the data, workings and final answer |
| `docs/Cargills_Optimal_Routing_Analysis.pdf` | Written report: assumptions, formulation and discussion |

Workbook sheets:

- **Data** – location names, latitude, longitude and rice demand (kg).
- **Workings** – distance matrix, route models and Solver setup.
- **Final answer** – the best route found for the 5,000 kg truck, with cumulative distance.

## Method

### 1. Distance matrix
Straight-line distances in km are calculated between all 21 locations from their latitude and longitude
(haversine formula). The result is a 21 × 21 matrix in the `Workings` sheet.

### 2. Route model (permutation approach)
Each location has a number (1 = warehouse, 2–21 = outlets). A route is a **permutation** of these numbers:
- The distance of each leg is looked up from the matrix with `INDEX(...)`.
- Total distance = sum of all legs, including the return to the warehouse.
- A check cell confirms that the route starts at the warehouse (location 1).

### 3. Solving with Excel Solver
- **Objective:** minimise total distance (`Min`).
- **Changing cells:** the route order.
- **Constraint:** `AllDifferent`, so each location is visited exactly once.
- **Solving method:** Evolutionary.

### 4. Truck capacity (5,000 kg)
For the second question, extra rows are added to the same model:
- **Load of the location** is looked up for each stop.
- **Cumulative load** adds up the loads along the route.
- If adding the next stop would pass 5,000 kg, the truck first **returns to the warehouse**.
  The return leg and the leg back out to the next stop replace the direct leg (`IF` formula).

## Mathematical formulation

Let `d_ij` = distance between locations i and j, and `x_ij` = 1 if the truck drives from i to j (otherwise 0).

```
Minimise   Z = Σ_i Σ_{j≠i} d_ij · x_ij
```

**Unlimited capacity**
- Every location is visited exactly once.
- The truck returns to the warehouse.

**5,000 kg capacity** (same objective, plus)
- The load carried on each trip must not exceed 5,000 kg.

## Results

### Q1 – Unlimited capacity: **93.94 km** (1 trip)

Warehouse → Raddolugama → Kandana → Elakanda → Mattakkuliya → Peliyagoda → Kiribathgoda-1 →
Bandarawatte → Delgoda → Kirillawala (Express) → Weliweriya → Yakkala (Express) → Yakkala-2 → Yakkala-1 →
Gampaha-3 → Gampaha (Square) → Gampaha-2 → Gampaha-1 → Ganemulla-1 → Ganemulla-2 → Minuwangoda → Warehouse

### Q2 – 5,000 kg capacity: **134.26 km** (2 trips)

| Trip | Load | Route |
|---|---|---|
| 1 | 4,700 kg | Warehouse → Kirillawala (Express) → Weliweriya → Delgoda → Bandarawatte → Kiribathgoda-1 → Peliyagoda → Mattakkuliya → Elakanda → Kandana → Ganemulla-1 → Ganemulla-2 → Raddolugama → Warehouse |
| 2 | 4,000 kg | Warehouse → Gampaha-1 → Gampaha-2 → Gampaha (Square) → Gampaha-3 → Yakkala (Express) → Yakkala-2 → Yakkala-1 → Minuwangoda → Warehouse |

**Effect of the capacity limit:** the truck must return to the warehouse to reload, so one loop becomes
two trips and the distance increases from 93.94 km to 134.26 km.

## Assumptions

- The distribution centre in Katana is the start and end of every route.
- Distances are straight-line (haversine); traffic and road conditions are not considered.
- Each outlet is served in one visit (the order is not split across trips).
- The truck is the same for every trip.

## Limitations

- Solver's **Evolutionary** method is a heuristic search. It gives a good answer, but it does not prove
  that the answer is the very best. The 5,000 kg result in particular may be improvable by changing the
  order of stops inside a trip.
- The straight-line distances are shorter than real road distances.

## How to reproduce

1. Open `226044G_OR_Assignment.xlsx` and go to the **Workings** sheet.
2. Open **Data → Solver**. Check that the objective is the total-distance cell, the changing cells are the
   route order, and the `AllDifferent` constraint is set.
3. Click **Solve** (Evolutionary method). Run it again or increase the time limit to try for a shorter route.
