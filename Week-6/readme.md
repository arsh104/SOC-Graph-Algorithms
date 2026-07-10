# Week-6

## Course Schedule (Topo sort)
```cpp
bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    vector<vector<int>> adj(numCourses);
    vector<int> indegree(numCourses, 0);
    for (auto& p : prerequisites) {
        adj[p[1]].push_back(p[0]);
        indegree[p[0]]++;
    }
    queue<int> q;
    for (int i = 0; i < numCourses; i++)
        if (indegree[i] == 0) q.push(i);

    int visited = 0;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        visited++;
        for (int next : adj[node]) {
            if (--indegree[next] == 0) q.push(next);
        }
    }
    return visited == numCourses;
}
```

## Path With Minimum Effort
```cpp
int minimumEffortPath(vector<vector<int>>& heights) {
    int rows = heights.size(), cols = heights[0].size();
    vector<vector<int>> dist(rows, vector<int>(cols, INT_MAX));
    priority_queue<tuple<int,int,int>, vector<tuple<int,int,int>>, greater<>> pq;
    dist[0][0] = 0;
    pq.push({0, 0, 0});
    vector<int> dx = {-1, 1, 0, 0};
    vector<int> dy = {0, 0, -1, 1};

    while (!pq.empty()) {
        auto [d, x, y] = pq.top(); pq.pop();
        if (d > dist[x][y]) continue;
        if (x == rows - 1 && y == cols - 1) return d;
        for (int i = 0; i < 4; i++) {
            int nx = x + dx[i], ny = y + dy[i];
            if (nx >= 0 && nx < rows && ny >= 0 && ny < cols) {
                int newEffort = max(d, abs(heights[nx][ny] - heights[x][y]));
                if (newEffort < dist[nx][ny]) {
                    dist[nx][ny] = newEffort;
                    pq.push({newEffort, nx, ny});
                }
            }
        }
    }
    return 0;
}
```

## Number of Connected Components (DSU)
```cpp
class DSU {
public:
    vector<int> parent, rank_;
    DSU(int n) {
        parent.resize(n);
        rank_.resize(n, 0);
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    void unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx == ry) return;
        if (rank_[rx] < rank_[ry]) swap(rx, ry);
        parent[ry] = rx;
        if (rank_[rx] == rank_[ry]) rank_[rx]++;
    }
};

int countComponents(int n, vector<vector<int>>& edges) {
    DSU dsu(n);
    for (auto& e : edges) dsu.unite(e[0], e[1]);
    unordered_set<int> roots;
    for (int i = 0; i < n; i++) roots.insert(dsu.find(i));
    return roots.size();
}
```

## Number of Operations to Make Network Connected
```cpp
class DSU2 {
public:
    vector<int> parent;
    int components;
    DSU2(int n) : components(n) {
        parent.resize(n);
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    bool unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx == ry) return false;
        parent[rx] = ry;
        components--;
        return true;
    }
};

int makeConnected(int n, vector<vector<int>>& connections) {
    if (connections.size() < n - 1) return -1;
    DSU2 dsu(n);
    for (auto& c : connections) dsu.unite(c[0], c[1]);
    return dsu.components - 1;
}
```

## Critical Connections in a Network
```cpp
void dfsCritical(int node, int parent, int& timer, vector<int>& disc, vector<int>& low,
                  vector<vector<int>>& adj, vector<vector<int>>& result) {
    disc[node] = low[node] = timer++;
    for (int neighbor : adj[node]) {
        if (neighbor == parent) continue;
        if (disc[neighbor] == -1) {
            dfsCritical(neighbor, node, timer, disc, low, adj, result);
            low[node] = min(low[node], low[neighbor]);
            if (low[neighbor] > disc[node]) {
                result.push_back({node, neighbor});
            }
        } else {
            low[node] = min(low[node], disc[neighbor]);
        }
    }
}

vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
    vector<vector<int>> adj(n);
    for (auto& c : connections) {
        adj[c[0]].push_back(c[1]);
        adj[c[1]].push_back(c[0]);
    }
    vector<int> disc(n, -1), low(n, -1);
    vector<vector<int>> result;
    int timer = 0;
    dfsCritical(0, -1, timer, disc, low, adj, result);
    return result;
}
```

## Network Delay Time (Dijkstra)
```cpp
int networkDelayTime(vector<vector<int>>& times, int n, int k) {
    vector<vector<pair<int,int>>> adj(n + 1);
    for (auto& t : times) {
        adj[t[0]].push_back({t[1], t[2]});
    }
    vector<int> dist(n + 1, INT_MAX);
    dist[k] = 0;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, k});

    while (!pq.empty()) {
        auto [d, node] = pq.top(); pq.pop();
        if (d > dist[node]) continue;
        for (auto& [next, weight] : adj[node]) {
            if (dist[node] + weight < dist[next]) {
                dist[next] = dist[node] + weight;
                pq.push({dist[next], next});
            }
        }
    }

    int maxTime = 0;
    for (int i = 1; i <= n; i++) {
        if (dist[i] == INT_MAX) return -1;
        maxTime = max(maxTime, dist[i]);
    }
    return maxTime;
}
```

## Min Cost to Connect All Points (MST)
```cpp
int minCostConnectPoints(vector<vector<int>>& points) {
    int n = points.size();
    vector<int> dist(n, INT_MAX);
    vector<bool> visited(n, false);
    dist[0] = 0;
    int totalCost = 0;

    for (int i = 0; i < n; i++) {
        int u = -1;
        for (int j = 0; j < n; j++) {
            if (!visited[j] && (u == -1 || dist[j] < dist[u])) u = j;
        }
        visited[u] = true;
        totalCost += dist[u];

        for (int v = 0; v < n; v++) {
            if (!visited[v]) {
                int cost = abs(points[u][0] - points[v][0]) + abs(points[u][1] - points[v][1]);
                if (cost < dist[v]) dist[v] = cost;
            }
        }
    }
    return totalCost;
}
```

## Redundant Connection
```cpp
class DSU3 {
public:
    vector<int> parent, rank_;
    DSU3(int n) {
        parent.resize(n + 1);
        rank_.resize(n + 1, 0);
        for (int i = 0; i <= n; i++) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    bool unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx == ry) return false;
        if (rank_[rx] < rank_[ry]) swap(rx, ry);
        parent[ry] = rx;
        if (rank_[rx] == rank_[ry]) rank_[rx]++;
        return true;
    }
};

vector<int> findRedundantConnection(vector<vector<int>>& edges) {
    int n = edges.size();
    DSU3 dsu(n);
    for (auto& edge : edges) {
        if (!dsu.unite(edge[0], edge[1])) {
            return edge;
        }
    }
    return {};
}
```

## Alien Dictionary (Topo sort)
```cpp
string alienOrder(vector<string>& words) {
    unordered_map<char, unordered_set<char>> adj;
    unordered_map<char, int> indegree;
    for (string& w : words) {
        for (char c : w) indegree[c] = 0;
    }

    for (int i = 0; i < words.size() - 1; i++) {
        string& w1 = words[i];
        string& w2 = words[i + 1];
        int minLen = min(w1.size(), w2.size());
        if (w1.size() > w2.size() && w1.substr(0, minLen) == w2.substr(0, minLen)) {
            return "";
        }
        for (int j = 0; j < minLen; j++) {
            if (w1[j] != w2[j]) {
                if (adj[w1[j]].find(w2[j]) == adj[w1[j]].end()) {
                    adj[w1[j]].insert(w2[j]);
                    indegree[w2[j]]++;
                }
                break;
            }
        }
    }

    queue<char> q;
    for (auto& [c, deg] : indegree) {
        if (deg == 0) q.push(c);
    }

    string result;
    while (!q.empty()) {
        char c = q.front(); q.pop();
        result += c;
        for (char next : adj[c]) {
            if (--indegree[next] == 0) q.push(next);
        }
    }

    return result.size() == indegree.size() ? result : "";
}
```

## Cheapest Flights Within K Stops
```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
    vector<int> dist(n, INT_MAX);
    dist[src] = 0;

    for (int i = 0; i <= k; i++) {
        vector<int> temp = dist;
        for (auto& f : flights) {
            int u = f[0], v = f[1], w = f[2];
            if (dist[u] != INT_MAX && dist[u] + w < temp[v]) {
                temp[v] = dist[u] + w;
            }
        }
        dist = temp;
    }

    return dist[dst] == INT_MAX ? -1 : dist[dst];
}
```

## Accounts Merge (DSU)
```cpp
class DSU4 {
public:
    vector<int> parent;
    DSU4(int n) {
        parent.resize(n);
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    void unite(int x, int y) {
        int rx = find(x), ry = find(y);
        if (rx != ry) parent[rx] = ry;
    }
};

vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
    int n = accounts.size();
    DSU4 dsu(n);
    unordered_map<string, int> emailToAccount;

    for (int i = 0; i < n; i++) {
        for (int j = 1; j < accounts[i].size(); j++) {
            string& email = accounts[i][j];
            if (emailToAccount.find(email) != emailToAccount.end()) {
                dsu.unite(i, emailToAccount[email]);
            } else {
                emailToAccount[email] = i;
            }
        }
    }

    unordered_map<int, set<string>> groupedEmails;
    for (auto& [email, idx] : emailToAccount) {
        groupedEmails[dsu.find(idx)].insert(email);
    }

    vector<vector<string>> result;
    for (auto& [idx, emails] : groupedEmails) {
        vector<string> account;
        account.push_back(accounts[idx][0]);
        for (const string& email : emails) account.push_back(email);
        result.push_back(account);
    }

    return result;
}
```

## Reconstruct Itinerary
```cpp
void dfsItinerary(string node, unordered_map<string, multiset<string>>& adj, vector<string>& result) {
    while (adj[node].size() > 0) {
        string next = *adj[node].begin();
        adj[node].erase(adj[node].begin());
        dfsItinerary(next, adj, result);
    }
    result.push_back(node);
}

vector<string> findItinerary(vector<vector<string>>& tickets) {
    unordered_map<string, multiset<string>> adj;
    for (auto& t : tickets) {
        adj[t[0]].insert(t[1]);
    }

    vector<string> result;
    dfsItinerary("JFK", adj, result);
    reverse(result.begin(), result.end());
    return result;
}
```
