================================================================================
INSTANCE SET: MULTI-WAREHOUSE DRONE ROUTING FOR POST-EARTHQUAKE RELIEF LOGISTICS
================================================================================


1. OVERVIEW
-----------

This folder contains 200 test instances for the capacitated multi-warehouse
vehicle routing problem with drones studied in the accompanying work. Drones
based at several warehouses deliver one relief package to each earthquake
gathering area (demand point). Each drone may perform several tours, subject
to a payload capacity and a per-tour flight-distance limit, and must return to
its own warehouse after every tour. The objective is to minimise the total
priority-weighted arrival time at the demand points:

    minimize  Z = Σ_{j ∈ D} p_j · A_j

where p_j is the priority weight of demand point j and A_j is its arrival time.

Parameter names inside the files follow the notation of the mathematical model
(e.g. δij, δmax, |W|), so each file can be read side by side with the
formulation. All files are UTF-8 encoded.


2. HOW THE INSTANCES WERE GENERATED
-----------------------------------

All instances are derived from one real-life post-earthquake case with
6 candidate warehouses and 171 gathering areas. In the scenario, every
gathering area is a demand point that requires exactly one relief package.

For each instance, a subset of the 6 candidate warehouses is selected at
random, with the number of warehouses depending on the instance size. Demand
points are then chosen from the 171 gathering areas such that every selected
demand point lies within 9.15 km of at least one selected warehouse. Because
9.15 km is half of the per-tour flight limit (δmax = 18.3 km), every demand
point can be reached by at least a direct out-and-back tour from some
warehouse.

Distances are aerial (Euclidean) distances in kilometres, reflecting the
assumption that drones are not affected by damaged or blocked roads.


3. FILE NAMING AND STRUCTURE
----------------------------

Instance files are named after their size:

    W<|W|>_D<|D|>_K<|K|>_<r>.dat

where |W| is the number of warehouses, |D| the number of demand points, |K|
the number of drones at each warehouse, and r a running index that
distinguishes different instances of the same size. For example,
W3_D30_K4_1.dat and W3_D30_K4_2.dat are two different instances, each with
3 warehouses, 30 demand points and 4 drones per warehouse.

Every instance file has the same layout. A small instance with 2 warehouses,
8 demand points and 1 drone per warehouse (file name W2_D8_K1_<r>.dat) is
shown below as an example:

    // Chosen Depots: [1, 2]
    // Chosen Demand Points (Original DP No): [10, 11, 24, 88, 89, 105, 128, 151]
    // Original Weights: [4, 5, 5, 6, 7, 6, 2, 8]

    |W| = 2;
    |D| = 8;
    V = 54;
    C = 3;
    |K| = 1;
    M = 100;
    S = 0.25;
    |TR| = 3;
    δmax = 18.3;
    Runtime = 2;

    // Priority weights for selected demand points
    pj = [
      4, 5, 5, 6, 7, 6, 2, 8
    ];

    δij =
    [
      [99.00,99.00,9.07,9.24,6.93,1.64,4.43,0.36,7.10,10.58],
      [99.00,99.00,9.37,6.43,8.35,11.70,13.73,11.16,13.25,3.07],
      [9.07,9.37,99.00,3.01,2.27,10.64,13.47,9.39,5.40,6.59],
      [9.24,6.43,3.01,99.00,2.96,10.59,13.33,9.49,7.82,3.58],
      [6.93,8.35,2.27,2.96,99.00,8.46,11.28,7.24,5.03,6.04],
      [1.64,11.70,10.64,10.59,8.46,99.00,2.83,1.28,8.67,11.57],
      [4.43,13.73,13.47,13.33,11.28,2.83,99.00,4.09,11.19,14.01],
      [0.36,11.16,9.39,9.49,7.24,1.28,4.09,99.00,7.45,10.73],
      [7.10,13.25,5.40,7.82,5.03,8.67,11.19,7.45,99.00,11.07],
      [10.58,3.07,6.59,3.58,6.04,11.57,14.01,10.73,11.07,99.00]
    ];

A file consists of three parts: a header of comment lines (starting with //)
that records where the instance comes from, the scalar model parameters, and
two arrays, the priority weights pj and the distance matrix δij.


4. HEADER LINES (INFORMATION ONLY)
----------------------------------

The header lines are not used by the model; they link the instance back to
the real-life dataset.

  Chosen Depots
      IDs of the warehouses selected for this instance out of the 6 candidate
      warehouses of the real-life case. Their order is the order of the
      warehouse nodes in δij.

  Chosen Demand Points (Original DP No)
      Original IDs (1-171) of the gathering areas selected for this instance.
      Their order is the order of the demand nodes in pj and δij.

  Original Weights
      Priority weights of the chosen gathering areas as given in the
      real-life dataset. These are identical to pj and are kept for
      traceability.


5. SCALAR PARAMETERS
--------------------

  |W|       Number of warehouses (size of set W).
            Example: 2

  |D|       Number of demand points (size of set D).
            Example: 8

  V         Drone flight speed.
            Unit: km/h. Example: 54

  C         Payload capacity of each drone. Since every demand point needs
            one package (d_j = 1), this is also the maximum number of demand
            points served in one tour.
            Unit: packages. Example: 3

  |K|       Number of drones available AT EACH WAREHOUSE (size of set K).
            The total fleet is |W| x |K|.
            Unit: drones. Example: 1

  M         Big-M constant used in the time-linking constraints.
            Example: 100

  S         Battery replacement and loading time at the warehouse before each
            subsequent tour of the same drone.
            Unit: hours. Example: 0.25 (= 15 minutes)

  |TR|      Maximum number of tours each drone may perform (size of set TR).
            Unit: tours. Example: 3

  δmax      Maximum flight distance per tour (battery limit).
            Unit: km. Example: 18.3

  Runtime   Time limit given to the solver for this instance. This is a
            computational setting and not part of the mathematical model.
            Example: 2


6. PRIORITY WEIGHTS (pj)
------------------------

A vector of length |D|. Its j-th entry is p_j, the priority weight of the
j-th chosen demand point. Because the objective minimises the weighted sum of
arrival times, demand points with larger weights are favoured to be served
earlier.


7. DISTANCE MATRIX (δij)
------------------------

A square, symmetric matrix of size (|W| + |D|) x (|W| + |D|) holding
distances in kilometres with two decimals. Rows and columns follow the node
set N = W ∪ D in this order:

  - The first |W| rows/columns are the warehouses, in the order of
    "Chosen Depots".
  - The remaining |D| rows/columns are the demand points, in the order of
    "Chosen Demand Points".

The value 99.00 appears on the diagonal and between any two warehouses. It is
a dummy value for arcs that are never used (self-loops and warehouse-to-
warehouse moves). Since 99.00 > δmax, these arcs are also excluded by the
arc-length constraint of the model (x = 0 whenever δij > δmax).


8. NODE INDEXING
----------------

With 1-based indices as in the files, index i <= |W| refers to warehouse
Chosen Depots[i], and index i > |W| refers to demand point
Chosen Demand Points[i - |W|], whose weight is pj[i - |W|].

For the example instance in Section 3:

  Row/column of δij   Node type      Original ID   Weight pj
  -----------------   ------------   -----------   ---------
  1                   Warehouse      1             -
  2                   Warehouse      2             -
  3                   Demand point   10            4
  4                   Demand point   11            5
  5                   Demand point   24            5
  ...                 ...            ...           ...
  10                  Demand point   151           8

For example, δ(1,3) = 9.07 km is the distance between warehouse 1 and
gathering area 10.


9. QUANTITIES NOT STORED IN THE FILES
-------------------------------------

A few model inputs are derived rather than stored:

  Travel time   τij = δij / V, in hours.
                Example: τ(1,3) = 9.07 / 54 ≈ 0.168 h ≈ 10.1 minutes.

  Demand        d_j = 1 for every demand point j ∈ D.

  Node set      N = W ∪ D, with |N| = |W| + |D|.


