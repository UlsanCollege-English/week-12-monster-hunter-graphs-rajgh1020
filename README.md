```markdown
[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/80z-ZS6n)
# Week 12: Monster Hunter Graphs

## Student

Name:Raj Ghimire

Student ID:2412076

## Summary

This assignment builds a graph-based map for a monster-hunting guild across a city full of supernatural sightings.
Locations such as the Old Theater, Train Station, and Library Basement are the nodes of the graph, and the patrol routes between them are the edges.
The graph is undirected, meaning every route works in both directions, and a weighted variant attaches a danger score to each route to reflect how risky a path is.
Helper functions summarise the graph by counting locations and routes, identify the most-connected threat hub by node degree, and use a min-heap to rank monster sightings by urgency so hunters know where to go first.
The hardest function was `build_weighted_hunter_map` because duplicate edges had to be resolved correctly — keeping the lowest danger score — while also ensuring both directions stayed in sync.

## Approach

- `build_hunter_map`: Iterate over each edge pair and maintain an internal `dict` of `set`s to prevent duplicate neighbors. Add both directions for every pair, then convert each set to a `list` before returning.
- `build_weighted_hunter_map`: Same two-direction pattern, but use a nested `dict` to store weights. Raise `ValueError` immediately on any weight ≤ 0. When a duplicate edge appears, compare the new weight against the stored one with `min` and update both directions together so they stay symmetric.
- `map_summary`: Count keys for the location total. Sum the length of every neighbor list and divide by 2, since each undirected route is represented twice in the adjacency list (once from each endpoint).
- `most_connected_location`: Return `None` for an empty graph. Otherwise call `min` with a composite key of `(-degree, name)` — negating degree makes the highest-degree node sort first, and the name component breaks ties alphabetically without any extra sorting step.
- `priority_hunt_order`: Copy the report list and call `heapq.heapify` on it. Because each element is a `(priority, location)` tuple, Python's lexicographic comparison handles both numeric priority and alphabetical tiebreaking automatically. Pop all elements and return only the location strings.

## Complexity

### `build_hunter_map`

- Time: O(E)
- Space: O(V + E)
- Why: Each of the E edges is visited once to insert two directed entries. The adjacency list stores every vertex V and every directed edge slot 2E.

### `build_weighted_hunter_map`

- Time: O(E)
- Space: O(V + E)
- Why: Same single pass over E edges. The nested dict holds one entry per directed edge, and each `min` comparison is O(1).

### `map_summary`

- Time: O(V + E)
- Space: O(1)
- Why: Iterating every neighbor list touches all V vertices and all 2E directed-edge slots. No extra data structures are allocated beyond two integer counters.

### `most_connected_location`

- Time: O(V)
- Space: O(1)
- Why: `min` performs a single linear scan over all V keys. Each degree lookup via `len` is O(1) since Python lists store their length.

### `priority_hunt_order`

- Time: O(N log N)
- Space: O(N)
- Why: `heapq.heapify` runs in O(N). Each of the N `heappop` calls costs O(log N), giving O(N log N) overall. The heap holds all N reports in place, so space is O(N).

## Edge-Case Checklist

- [x] Empty graph
- [x] One route
- [x] Duplicate routes
- [x] Disconnected locations
- [x] Tie for most connected location
- [x] Positive weighted routes
- [x] Invalid zero or negative danger score
- [x] Empty priority report list

## Tests


```bash
pytest -q
```

Result:

```text
39 passed in 0.04s
```

## Assistance & Sources

AI used? Yes

If yes, what did it help with?

- Implementing all five functions, writing the test file, and completing the README.

Other sources used:

- Python docs — `heapq` module: https://docs.python.org/3/library/heapq.html
- Python docs — `dict` and `set`: https://docs.python.org/3/library/stdtypes.html
```