### Task 2.1 — Misplaced Tiles

Replace the `pass` in Cell 6 with:

```python
def misplaced_tiles(state):
    """Count tiles not in their goal position; do not count the blank."""
    return sum(
        1
        for i, tile in enumerate(state)
        if tile != 0 and tile != GOAL_STATE[i]
    )
```

### Task 2.2 — Manhattan Distance

Replace the `pass` in Cell 7 with:

```python
def manhattan_distance(state):
    """Sum each tile's distance from its goal position; do not count the blank."""
    distance = 0

    for current_index, tile in enumerate(state):
        if tile == 0:
            continue

        current_row, current_col = divmod(current_index, 3)
        goal_index = tile - 1
        goal_row, goal_col = divmod(goal_index, 3)

        distance += abs(current_row - goal_row) + abs(current_col - goal_col)

    return distance
```

Run Cell 8 afterward to check both heuristics.

### Section 2 discussion question

**Why must `h(n) = 0` at the goal? What would break if it weren’t?**

At the goal, the actual remaining cost is zero, so an admissible heuristic must not estimate a cost greater than zero. Both heuristics return zero because every numbered tile is in its goal position. If the heuristic returned a positive value at the goal, it would overestimate the remaining cost and would no longer be admissible; A*’s guarantee of finding an optimal solution could break.

### Task 3.1 — A* Search

Replace Cell 10 with this completed function:

```python
def a_star_search(start_state, heuristic_fn):
    """
    Returns a dictionary containing the solution path, cost, and
    number of non-stale nodes popped from the frontier.
    """
    counter = itertools.count()
    frontier = []

    heapq.heappush(
        frontier,
        (heuristic_fn(start_state), next(counter), 0, start_state, [])
    )

    best_g = {start_state: 0}
    nodes_expanded = 0

    while frontier:
        f, _, g, state, path = heapq.heappop(frontier)

        # Ignore an outdated queue entry if a shorter route was found later.
        if g > best_g.get(state, float("inf")):
            continue

        nodes_expanded += 1

        if is_goal(state):
            return {
                "path": path,
                "cost": g,
                "nodes_expanded": nodes_expanded
            }

        for neighbor_state, action in get_neighbors(state):
            new_g = g + 1

            if new_g < best_g.get(neighbor_state, float("inf")):
                best_g[neighbor_state] = new_g
                new_f = new_g + heuristic_fn(neighbor_state)

                heapq.heappush(
                    frontier,
                    (
                        new_f,
                        next(counter),
                        new_g,
                        neighbor_state,
                        path + [action]
                    )
                )

    return {
        "path": None,
        "cost": None,
        "nodes_expanded": nodes_expanded
    }
```

Run Cell 11 to check it.

### Section 5 — Analysis answers

Run Cells 13–16 first. Use the table printed by Cell 16 for answer 1 and the chart produced there for answer 2. Those exact results depend on the notebook run, so copy your own output rather than using made-up numbers.

For the written answers:

- **When does the heuristic gap become visible? Why can it widen?**  
  Use your table to identify the first scramble depth where Manhattan Distance expands noticeably fewer nodes. The exact depth depends on the generated puzzles. As puzzles become harder, more states may need to be considered. Manhattan Distance usually gives A* more useful guidance because it accounts for how far each tile is from its goal, not just whether it is misplaced.

- **Why is Manhattan Distance being greater than Misplaced Tiles still admissible, and why can it reduce expansions?**  
  Admissibility means a heuristic never estimates more moves than are actually required to reach the goal. Each misplaced tile must move at least once, while Manhattan Distance counts each tile’s grid distance to its goal. Both estimates remain lower bounds on the true solution cost. Manhattan Distance is usually the more informative lower bound, so A* can prioritize promising states and often expand fewer nodes.

- **Optional: Does Manhattan Distance hit the same performance limit?**  
  Record what you observe if you try deeper scrambles. Manhattan Distance often handles harder puzzles more efficiently than Misplaced Tiles, but it can still become slow as puzzle difficulty increases.
