Important note:
- The actual numbers from hill climbing are random.
- So the exact output will vary from run to run.
- The sample results below are therefore representative, not fixed.

1) Task 1: Create the state and print it

```python
state = [1, 3, 0, 2]

print("State:", state)
```

Expected output:

```python
State: [1, 3, 0, 2]
```

2) Task 2: Run the experiment several times

This is the code from the lab:

```python
import random

def random_state(n):
    return [random.randrange(n) for _ in range(n)]

def count_conflicts(state):
    conflicts = 0
    n = len(state)

    for i in range(n):
        for j in range(i + 1, n):
            if state[i] == state[j]:
                conflicts += 1
            elif abs(state[i] - state[j]) == abs(i - j):
                conflicts += 1

    return conflicts

def evaluation(state):
    return -count_conflicts(state)

def get_neighbors(state):
    neighbors = []
    n = len(state)

    for col in range(n):
        for row in range(n):
            if row != state[col]:
                neighbor = state.copy()
                neighbor[col] = row
                neighbors.append(neighbor)

    return neighbors

def hill_climbing(n):
    current = random_state(n)
    scores = []

    while True:
        current_score = evaluation(current)
        scores.append(current_score)

        neighbors = get_neighbors(current)
        best_neighbor = max(neighbors, key=evaluation)
        best_score = evaluation(best_neighbor)

        if best_score <= current_score:
            break

        current = best_neighbor

    return current, scores
```

Now run the experiment:

```python
for n in [4, 8, 10, 12]:
    solution, scores = hill_climbing(n)

    print(
        f"N = {n:2d} | "
        f"Conflicts = {count_conflicts(solution):2d} | "
        f"Iterations = {len(scores):3d} | "
        f"Solved = {count_conflicts(solution) == 0}"
    )
```

Example sample output:

```text
N =  4 | Conflicts =  0 | Iterations =   3 | Solved = True
N =  8 | Conflicts =  0 | Iterations =  31 | Solved = True
N = 10 | Conflicts =  2 | Iterations =  45 | Solved = False
N = 12 | Conflicts =  4 | Iterations =  65 | Solved = False
```

Sample observation table (illustrative):

| N | Run | Final Conflicts | Iterations | Solved? |
|---:|---:|---:|---:|---|
| 4 | 1 | 0 | 3 | Yes |
| 4 | 2 | 0 | 2 | Yes |
| 8 | 1 | 0 | 31 | Yes |
| 8 | 2 | 2 | 42 | No |
| 8 | 3 | 0 | 18 | Yes |
| 10 | 1 | 2 | 45 | No |
| 10 | 2 | 0 | 61 | Yes |
| 10 | 3 | 4 | 70 | No |

Discussion:
- The same board size does not always produce the same result.
- Hill-climbing depends heavily on the random starting state.
- Smaller boards like N = 4 are easier and often solve quickly.
- Larger boards such as N = 10 or 12 are harder and more likely to get stuck in local maxima.

3) Task 3: Compare basic and random-restart hill climbing

Complete code:

```python
import random

def random_state(n):
    return [random.randrange(n) for _ in range(n)]

def count_conflicts(state):
    conflicts = 0
    n = len(state)

    for i in range(n):
        for j in range(i + 1, n):
            if state[i] == state[j]:
                conflicts += 1
            elif abs(state[i] - state[j]) == abs(i - j):
                conflicts += 1

    return conflicts

def evaluation(state):
    return -count_conflicts(state)

def get_neighbors(state):
    neighbors = []
    n = len(state)

    for col in range(n):
        for row in range(n):
            if row != state[col]:
                neighbor = state.copy()
                neighbor[col] = row
                neighbors.append(neighbor)
    return neighbors

def hill_climbing(n):
    current = random_state(n)
    scores = []

    while True:
        current_score = evaluation(current)
        scores.append(current_score)

        neighbors = get_neighbors(current)
        best_neighbor = max(neighbors, key=evaluation)
        best_score = evaluation(best_neighbor)

        if best_score <= current_score:
            break

        current = best_neighbor

    return current, scores

def random_restart_hill_climbing(n, max_restarts=100):
    best_state = None
    best_score = float("-inf")
    best_scores = []

    for restart in range(max_restarts):
        state, scores = hill_climbing(n)
        score = evaluation(state)

        if score > best_score:
            best_state = state
            best_score = score

        best_scores.append(best_score)

        if best_score == 0:
            return best_state, restart + 1, best_scores

    return best_state, max_restarts, best_scores
```

Run both methods:

```python
print("Basic Hill-Climbing")
print("-------------------")

for i in range(5):
    state, scores = hill_climbing(8)
    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Iterations = {len(scores)}, "
        f"Solved = {conflicts == 0}"
    )

print("\nRandom-Restart Hill-Climbing")
print("----------------------------")

for i in range(5):
    state, restarts, best_scores = random_restart_hill_climbing(8, max_restarts=100)
    conflicts = count_conflicts(state)

    print(
        f"Run {i + 1}: "
        f"Conflicts = {conflicts}, "
        f"Restarts = {restarts}, "
        f"Solved = {conflicts == 0}"
    )
```

Example sample output:

```text
Basic Hill-Climbing
-------------------
Run 1: Conflicts = 0, Iterations = 11, Solved = True
Run 2: Conflicts = 2, Iterations = 34, Solved = False
Run 3: Conflicts = 0, Iterations = 26, Solved = True
Run 4: Conflicts = 4, Iterations = 28, Solved = False
Run 5: Conflicts = 2, Iterations = 41, Solved = False

Random-Restart Hill-Climbing
----------------------------
Run 1: Conflicts = 0, Restarts = 4, Solved = True
Run 2: Conflicts = 0, Restarts = 2, Solved = True
Run 3: Conflicts = 0, Restarts = 8, Solved = True
Run 4: Conflicts = 2, Restarts = 100, Solved = False
Run 5: Conflicts = 0, Restarts = 3, Solved = True
```

Answer to the Task 3 questions:
1. Did basic Hill-Climbing find a solution in every run?
   - No. It often fails because it may stop at a local maximum.

2. Did Random-Restart Hill-Climbing improve the results?
   - Yes, it usually improves the chance of success by trying multiple random starting states.

3. How many restarts were needed to find a solution?
   - It depends on the random runs. It may require a few restarts or many.

4. Did any random-restart experiment reach its restart limit without finding a solution?
   - It is possible, especially if the restart limit is too low or the random process does not find a good initial state.

5. Why can Random-Restart improve the probability of success without guaranteeing success within a fixed number of attempts?
   - Because each restart is an independent chance to start from a better region of the search space. If the chance of success per run is p, then the chance of success after k runs is:
     P(success within k runs) = 1 - (1 - p)^k
   - This improves the probability, but it does not guarantee success in a fixed number of tries.

4) Answers to lab questions

Question 1: What is the purpose of the evaluation function in Hill-Climbing?

Answer:
- The evaluation function measures how good a state is.
- In N-Queens, it counts the number of attacking queen pairs.
- Since we want fewer conflicts, we define:
  evaluation(state) = -count_conflicts(state)
- Higher evaluation means better state.
- Hill-Climbing uses this to compare current state and neighboring states and choose the best move.

Question 2: Why is an evaluation score of zero the best result in our N-Queens implementation?

Answer:
- The number of conflicts is counted.
- A valid N-Queens solution has zero attacking pairs.
- We define evaluation as negative conflicts:
  evaluation = -conflicts
- So if conflicts = 0, then evaluation = 0, which is the maximum possible score.
- Therefore, 0 means the board is a valid solution.

Question 3: What is the difference between a local maximum and a global maximum?

Answer:
- A local maximum is a state that is better than all its immediate neighbors, but not necessarily the best state overall.
- A global maximum is the best state in the entire search space.
- In N-Queens, a local maximum may still contain conflicts, even though no single neighboring move improves it.
- A global maximum corresponds to a valid solution with zero conflicts.

Question 4: Why can basic Hill-Climbing stop without finding a solution even when a solution exists?

Answer:
- Hill-Climbing is greedy.
- It only moves to a better neighbor.
- If it reaches a local maximum, there may be no immediate improvement.
- But a valid solution may exist elsewhere in the search space.
- Since the algorithm does not explore other regions, it stops prematurely.

Question 5: Does reaching a local maximum always mean that the algorithm has failed? Explain.

Answer:
- No.
- A local maximum can also be the global maximum.
- In N-Queens, if the current state has zero conflicts, then it is a valid solution.
- So reaching a local maximum does not always mean failure; it only means the algorithm has stopped improving locally.
- The problem is that sometimes it is not the goal state.

Question 6: For N = 8, how many neighbors does each state have in our implementation?

Answer:
- Each state has N columns.
- For each column, we try N rows.
- We exclude the current row position in that column.
- So for each column, there are N - 1 possible moves.
- Total neighbors:
  N × (N - 1)
- For N = 8:
  8 × 7 = 56 neighbors

Question 7: What does the evaluation-score graph tell you about the behavior of Hill-Climbing?

Answer:
- The graph shows how the evaluation score changes over time.
- The score usually increases as the algorithm moves to better states.
- It may stay flat when no improving move exists.
- Once the algorithm reaches a local maximum, the score stops increasing.
- If the final score is 0, a solution has been found.
- If the final score is negative, the algorithm stopped before reaching a valid solution.

Question 8: Explain probabilistic completeness in your own words.

Answer:
- An algorithm is probabilistically complete if the chance of finding a solution becomes very high as more independent attempts are made.
- This does not mean it will always succeed immediately.
- It means that with enough random restarts, the probability of success approaches 1.
- In other words, repeated attempts give the algorithm more opportunities to find a good starting point.

Question 9: Why does Random-Restart Hill-Climbing improve the chance of finding a solution?

Answer:
- A single Hill-Climbing run may start from a poor state and get stuck in a local maximum.
- Random-Restart Hill-Climbing tries many different random starting states.
- Some restarts may reach the global optimum or a valid solution.
- Over repeated independent runs, the chance of finding a solution increases.

Question 10: Does probabilistic completeness guarantee that a solution will be found within 100 restarts? Why or why not?

Answer:
- No.
- Probabilistic completeness says the probability of succeeding approaches 1 as the number of restarts grows.
- But with only 100 attempts, there is still a nonzero chance of failure.
- The result depends on the random initial states and the problem difficulty.
- So 100 restarts increases the chance, but does not guarantee success.

5) Extension Task: Solve N = 20

You can use the same random-restart algorithm:

```python
import random
import matplotlib.pyplot as plt

def random_state(n):
    return [random.randrange(n) for _ in range(n)]

def count_conflicts(state):
    conflicts = 0
    n = len(state)

    for i in range(n):
        for j in range(i + 1, n):
            if state[i] == state[j]:
                conflicts += 1
            elif abs(state[i] - state[j]) == abs(i - j):
                conflicts += 1

    return conflicts

def evaluation(state):
    return -count_conflicts(state)

def get_neighbors(state):
    neighbors = []
    n = len(state)

    for col in range(n):
        for row in range(n):
            if row != state[col]:
                neighbor = state.copy()
                neighbor[col] = row
                neighbors.append(neighbor)

    return neighbors

def hill_climbing(n):
    current = random_state(n)
    scores = []

    while True:
        current_score = evaluation(current)
        scores.append(current_score)

        neighbors = get_neighbors(current)
        best_neighbor = max(neighbors, key=evaluation)
        best_score = evaluation(best_neighbor)

        if best_score <= current_score:
            break

        current = best_neighbor

    return current, scores

def random_restart_hill_climbing(n, max_restarts=500):
    best_state = None
    best_score = float("-inf")
    best_scores = []

    for restart in range(max_restarts):
        state, scores = hill_climbing(n)
        score = evaluation(state)

        if score > best_score:
            best_state = state
            best_score = score

        best_scores.append(best_score)

        if best_score == 0:
            return best_state, restart + 1, best_scores

    return best_state, max_restarts, best_scores
```

Example run for N = 20:

```python
n = 20
state, scores = hill_climbing(n)
print("Basic Hill-Climbing for N=20")
print("Final conflicts:", count_conflicts(state))
print("Iterations:", len(scores))
print("Solved:", count_conflicts(state) == 0)

state2, restarts, best_scores = random_restart_hill_climbing(20, max_restarts=500)
print("\nRandom-Restart Hill-Climbing for N=20")
print("Final conflicts:", count_conflicts(state2))
print("Restarts:", restarts)
print("Solved:", count_conflicts(state2) == 0)
```

Example output (illustrative only):

```text
Basic Hill-Climbing for N=20
Final conflicts: 12
Iterations: 220
Solved: False

Random-Restart Hill-Climbing for N=20
Final conflicts: 0
Restarts: 45
Solved: True
```

Discussion:
- N = 20 is much harder than N = 8.
- Basic hill-climbing often gets stuck in a local maximum.
- Random restarts improve the chance of finding a solution.
- A larger board has significantly more possible states, so the search becomes more difficult.
- The random initial state matters a lot because different starts can lead to different local maxima.


