# Cargills Ceylon PLC – Optimal Routing Analysis

Operations Research assignment (226044G).

Cargills Ceylon PLC operates its largest warehouse in Katana (Gampaha District), and most goods are distributed from there.
This project determines the shortest route to distribute rice (in bulk quantities) from the warehouse to each Cargills Food City and back.

1. Determine the optimal route from the warehouse to each Cargills Food City and back (unlimited truck capacity).
2. Determine the shortest route when the truck can carry only 5,000 kg per trip.

## Files

| File | Description |
|---|---|
| `226044G_OR_Assignment.xlsx` | Excel workbook with the data, workings and final answer |
| `docs/Cargills_Optimal_Routing_Analysis.pdf` | Report: assumptions, formulations and discussion |

Workbook sheets:

- **Data** – location, latitude, longitude and rice supply/demand (kg) for the distribution centre and 20 Food City outlets.
- **Workings** – distance matrix, route calculations and Solver setup for both scenarios.
- **Final answer** – the route for the 5,000 kg truck, with distance and cumulative distance.

## Assumptions

- Scenario 1: the truck has no capacity limit.
- Scenario 2: the truck can carry up to 5,000 kg per trip.
- The distribution centre in Katana is the starting point of all routes.
- Traffic conditions and other possible delays are not considered.

## Method (Excel)

1. **Distance matrix** – distances between all 21 locations are set out in a matrix in the `Workings` sheet.
2. **Route order** – each location has a number (1 = distribution centre). A route is an order of these numbers.
   The distance of each leg is looked up from the matrix with `INDEX`, and the total distance is the sum of all legs.
3. **Excel Solver** – minimise the total distance by changing the route order, with an `AllDifferent` constraint so each
   location appears once and a check that the route starts at location 1. The Evolutionary solving method is used.
4. **Truck capacity (5,000 kg)** – the load of each stop is looked up and a cumulative load is calculated.
   When the next stop would take the load above 5,000 kg, the truck returns to the distribution centre first
   (return trip), and the distance is calculated as stop → distribution centre → next stop.

## Formulation

- `Z` – total distance travelled
- `d_ij` – distance between locations i and j
- `x_ij` – 1 if the route from i to j is taken, otherwise 0

```
Minimise  Z = Σ_i Σ_{j≠i} d_ij · x_ij
```

**Unlimited capacity:** each location is visited exactly once, and the truck returns to the starting point.

**5,000 kg capacity:** same objective, with an added constraint that the total load must not exceed the truck capacity;
each location is still visited exactly once and the truck returns to the starting point.

## Results

### 1. Unlimited capacity – 93.94 km

Distribution Centre → Raddolugama → Kandana → Elakanda → Mattakkuliya → Peliyagoda → Kiribathgoda - 1 →
Bandarawatte → Delgoda → Kirillawala (Express) → Weliweriya → Yakkala (Express) → Yakkala - 2 → Yakkala - 1 →
Gampaha - 3 → Gampaha (Square) → Gampaha - 2 → Gampaha - 1 → Ganemulla - 1 → Ganemulla - 2 → Minuwangoda →
Distribution Centre

### 2. 5,000 kg capacity – 134.26 km (2 trips)

| Trip | Load | Route |
|---|---|---|
| 1 | 4,700 kg | Distribution Centre → Kirillawala (Express) → Weliweriya → Delgoda → Bandarawatte → Kiribathgoda - 1 → Peliyagoda → Mattakkuliya → Elakanda → Kandana → Ganemulla - 1 → Ganemulla - 2 → Raddolugama → Distribution Centre |
| 2 | 4,000 kg | Distribution Centre → Gampaha - 1 → Gampaha - 2 → Gampaha (Square) → Gampaha - 3 → Yakkala (Express) → Yakkala - 2 → Yakkala - 1 → Minuwangoda → Distribution Centre |

## Discussion

**Unlimited capacity:** the truck visits all Food City locations in one trip. This is the classic Traveling Salesman Problem (TSP),
and the objective is to minimise the total distance while visiting each location once.

**Limited capacity:** with a 5,000 kg limit the truck needs more than one trip, and the total distance becomes longer
because of the extra return to the warehouse.
