### Section 7 — Compare BFS and DFS

With the original directions (`up, down, left, right`), the output is:

| Algorithm | Path Length | Maximum Frontier |
|---|---:|---:|
| BFS | 8 | 2 |
| DFS | 8 | 2 |

1. **Which found the shorter path?** Neither; both found an 8-move path.
2. **Which had the larger maximum frontier?** Neither; both reached 2.
3. **Why are their paths different?** BFS explores level by level using a queue; DFS follows the most recently added branch using a stack.
4. **Does DFS always find the shortest path?** No. It can reach the goal along a longer route before exploring a shorter one.
5. **Does BFS find the shortest path when each move costs 1?** Yes. It explores paths in order of increasing number of moves.

### Section 8 — Change the neighbor order

After changing `directions` as shown in Section 8, DFS takes a different 8-move route.

- **Did the DFS path change?** Yes. DFS is sensitive to neighbor order.
- **Does BFS depend on the order in the same way?** The order can change which path BFS returns when multiple shortest paths exist. It still returns a shortest path when every move costs 1.

**Correction to my earlier answer:** BFS’s chosen path can also change with neighbor order in this maze. It remains shortest, but may choose the other equally short route.

Reset `directions` to `up, down, left, right` before Section 9.

### Section 9 — Try three start/goal pairs

Run this code after resetting `directions`:

```python
test_pairs = [
    ((0, 0), (2, 2)),
    ((0, 0), (4, 4)),
    ((3, 3), (4, 4))
]

for test_start, test_goal in test_pairs:
    b_path, b_frontier = bfs(maze, test_start, test_goal)
    d_path, d_frontier = dfs(maze, test_start, test_goal)

    print(
        f"Start: {test_start}, Goal: {test_goal} | "
        f"BFS length: {len(b_path) - 1}, BFS max frontier: {b_frontier} | "
        f"DFS length: {len(d_path) - 1}, DFS max frontier: {d_frontier}"
    )
```

Results for the lab’s maze and directions:

| Start | Goal | BFS Length | BFS Max Frontier | DFS Length | DFS Max Frontier |
|---|---|---:|---:|---:|---:|
| `(0, 0)` | `(2, 2)` | 4 | 2 | 4 | 2 |
| `(0, 0)` | `(4, 4)` | 8 | 2 | 8 | 2 |
| `(3, 3)` | `(4, 4)` | 2 | 2 | 2 | 2 |

6. **Does path length change when rerunning with the same start/goal?** No, provided the maze and direction order stay the same.
7. **What happens to frontier size as start and goal get farther apart?** It may grow, but it does not necessarily increase with distance; it depends on the maze layout and search order.
8. **Can BFS and DFS return the same path?** Yes. For `(3, 3)` to `(4, 4)`, both use the direct two-move route. This shows both can choose the same path; it does not necessarily mean no other route exists.
9. **What if the goal is unreachable?** Both searches return `None` for the path. For example, try a wall cell as the goal:

```python
unreachable_goal = (1, 1)  # This is a wall in the maze

print("BFS:", bfs(maze, (0, 0), unreachable_goal))
print("DFS:", dfs(maze, (0, 0), unreachable_goal))
```

The second value returned is the maximum frontier size reached during that search.
