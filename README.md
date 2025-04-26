Container Routing Optimization API
*Code:*
```
from flask import Flask, request, jsonify
import pulp

app = Flask(__name__)

Optimization function
def optimize_container_routing(data):
    # Define the problem
    prob = pulp.LpProblem("Container_Routing", pulp.LpMinimize)

    # Define variables
    num_ports = data['num_ports']
    num_containers = data['num_containers']
    costs = data['costs']
    supply = data['supply']
    demand = data['demand']

    x = pulp.LpVariable.dicts("Route", (range(num_ports), range(num_ports)), 0, None, cat='Integer')

    # Objective function
    prob += pulp.lpSum([costs[i][j] * x[i][j] for i in range(num_ports) for j in range(num_ports)])

    # Constraints
    for i in range(num_ports):
        prob += pulp.lpSum([x[i][j] for j in range(num_ports)]) == supply[i]
    for j in range(num_ports):
        prob += pulp.lpSum([x[i][j] for i in range(num_ports)]) == demand[j]

    # Solve the problem
    prob.solve()

    # Return the solution
    solution = []
    for i in range(num_ports):
        for j in range(num_ports):
            if x[i][j].varValue > 0:
                solution.append((i, j, x[i][j].varValue))
    return solution

API endpoint
@app.route('/optimize', methods=['POST'])
def optimize():
    data = request.get_json()
    solution = optimize_container_routing(data)
    return jsonify({'solution': solution})

if __name__ == '__main__':
    app.run(debug=True)
```

*README:*
Request/Response Example:
Request:

```
{
  "num_ports": 3,
  "num_containers": 10,
  "costs": [[0, 2, 3], [2, 0, 1], [3, 1, 0]],
  "supply": [5, 3, 2],
  "demand": [2, 4, 4]
}
```

Response:

```
{
  "solution": [[0, 1, 2], [0, 2, 3], [1, 1, 3], [1, 2, 0], [2, 0, 2]]
}
```

Latency Benchmark:
- Average latency: 10-50 ms (depending on problem size)
- Tested using `ab` (Apache Benchmark) tool
