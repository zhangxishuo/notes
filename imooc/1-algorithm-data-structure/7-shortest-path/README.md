# 最短路径

## Dijkstra

前提：图中不能有负权边。

```cpp
template<typename Graph, typename Weight>
class Dijkstra {
private:
    Graph& G;
    int source;
    Weight* distTo;
    bool* marked;
    vector<Edge<Weight>*> from;

public:
    Dijkstra(Graph& graph, int s): G(graph) {
        this->source = s;
        this->distTo = new Weight[G.V()];
        this->marked = new bool[G.V()];

        for (int i = 0; i < G.V(); i ++) {
            this->distTo[i] = Weight();
            this->marked[i] = false;
            this->from.push_back(NULL);
        }

        IndexMinHeap<Weight> iheap(G.V());
        distTo[source] = Weight();
        marked[source] = true;
        iheap.insert(source, distTo[source]);
        while (!iheap.isEmpty()) {
            int v = iheap.extractMinIndex();
            marked[v] = true;

            typename Graph::adjIterator adj(G, v);
            for (Edge<Weight>* e = adj.begin(); !adj.end(); e = adj.next()) {
                int w = e->other(v);
                if (!marked[w]) {
                    if (from[w] == NULL || distTo[v] + e->wt() < distTo[w]) {
                        distTo[w] = distTo[v] + e->wt();
                        from[w] = e;
                        if (iheap.contain(w)) {
                            iheap.change(w, distTo(w));
                        } else {
                            iheap.insert(w, distTo[w]);
                        }
                    }
                }
            }
        }
    }

    ~Dijkstra() {
        delete[] distTo;
        delete[] marked;
    }

    Weight shortestPathTo(int w) {
        return distTo[w];
    }

    bool hasPathTo(int w) {
        return marked[w];
    }

    void shortestPath(int w, vector<Edge<Weight>> &vec) {
        stack<Edge<Weight>*> s;
        Edge<Weight> *e = from[w];
        while (e->V() != e->W()) {
            s.push(e);
            e = from[e->V()];
        }

        while (!s.empty()) {
            e = s.top();
            vec.push_back(*e);
            s.pop();
        }
    }
};
```