# 最短路径

## Dijkstra

前提：图中不能有负权边。

```cpp
template<typename Graph, typename Weight>
class Dijkstra {
private:
    /** 图的引用 */
    Graph& G;
    /** 起始点 */
    int s;
    /** distTo[i]存储从起始点s到i的最短路径长度 */
    Weight *distTo;
    /** 标记数组 在算法运行过程中标记节点i是否被访问 */
    bool *marked;
    /** from[i]记录最短路径中 到达i点的边是哪一条 可以用来恢复整个最短路径 */
    vector<Edge<Weight>*> from;

public:
    Dijkstra(Graph& graph, int s): G(graph) {
        /** 算法初始化 */
        assert(s >= 0 && s < G.V());
        this->s = s;
        distTo = new Weight[G.V()];
        marked = new bool[G.V()];
        for (int i = 0; i < G.V(); i ++) {
            distTo[i] = Weight();
            marked[i] = false;
            from.push_back(NULL);
        }

        /** 使用索引堆记录当前找到的到达每个顶点的最短距离 */
        IndexMinHeap<Weight> ipq(G.V());

        /** 对于起始点 s 进行初始化 */
        distTo[s] = Weight();
        from[s] = new Edge<Weight>(s, s, Weight());
        ipq.insert(s, distTo[s]);
        marked[s] = true;
        while (!ipq.isEmpty()) {
            int v = ipq.extractMinIndex();

            /** distTo[v]就是s到v的最短距离 */
            marked[v] = true;

            /** 对v的所有相邻节点进行更新 */
            typename Graph::adjIterator adj(G, v);
            for (Edge<Weight>* e = adj.begin(); !adj.end(); e = adj.next()) {
                int w = e->other(v);
                /** 如果从s点到w点的最短路径还没有找到 */
                if (!marked[w]) {
                    /** 如果w点以前没有访问过 或者访问过 但是通过当前的v点到w点距离更短 则进行更新 */
                    if (from[w] == NULL || distTo[v] + e->wt() < distTo[w]) {
                        distTo[w] = distTo[v] + e->wt();
                        from[w] = e;
                        if (ipq.contain(w))
                            ipq.change(w, distTo[w]);
                        else
                            ipq.insert(w, distTo[w]);
                    }
                }
            }
        }
    }

    ~Dijkstra() {
        delete[] distTo;
        delete[] marked;
        delete from[0];
    }

    /** 返回从s点到w点的最短路径长度 */
    Weight shortestPathTo(int w) {
        assert(w >= 0 && w < G.V());
        assert(hasPathTo(w));
        return distTo[w];
    }

    /** 判断从s点到w点是否连通 */
    bool hasPathTo(int w) {
        assert(w >= 0 && w < G.V());
        return marked[w];
    }

    /** 寻找从s到w的最短路径 将整个路径经过的边存放在vec中 */
    void shortestPath(int w, vector<Edge<Weight>> &vec) {
        assert(w >= 0 && w < G.V());
        assert(hasPathTo(w));

        /** 通过from数组逆向查找到从s到w的路径 存放到栈中 */
        stack<Edge<Weight>*> s;
        Edge<Weight> *e = from[w];
        while (e->v() != this->s) {
            s.push(e);
            e = from[e->v()];
        }
        s.push(e);

        /** 从栈中依次取出元素 获得顺序的从s到w的路径 */
        while (!s.empty()) {
            e = s.top();
            vec.push_back(*e);
            s.pop();
        }
    }
};
```

## Bellman-Ford

前提：图中不能有负权环。

```cpp
template<typename Graph, typename Weight>
class BellmanFord {
private:
    /** 图的引用 */
    Graph& G;
    /** 起始点 */
    int s;
    /** distTo[i]存储从起始点s到i的最短路径长度 */
    Weight* distTo;
    /** from[i]记录最短路径中 到达i点的边是哪一条 可以用来恢复整个最短路径 */
    vector<Edge<Weight>*> from;
    /** 标记图中是否有负权环 */
    bool hasNegativeCycle;

    /** 判断图中是否有负权环 */
    bool detectNegativeCycle() {
        for (int i = 0; i < G.V(); i ++) {
            typename Graph::adjIterator adj(G,i);
            for (Edge<Weight>* e = adj.begin(); !adj.end(); e = adj.next())
                if (from[e->v()] && distTo[e->v()] + e->wt() < distTo[e->w()])
                    return true;
        }
        return false;
    }

public:
    BellmanFord(Graph& graph, int s): G(graph) {
        this->s = s;
        distTo = new Weight[G.V()];
        /** 初始化所有的节点s都不可达 由from数组来表示 */
        for (int i = 0; i < G.V(); i ++)
            from.push_back(NULL);

        /** 设置distTo[s] = 0 并且让from[s]不为NULL 表示初始s节点可达且距离为0 */
        distTo[s] = Weight();
        /** 这里我们from[s]的内容是new出来的 注意要在析构函数里delete掉 */
        from[s] = new Edge<Weight>(s, s, Weight());

        /** 进行V-1次循环 每一次循环求出从起点到其余所有点 最多使用pass步可到达的最短距离 */
        for (int pass = 1; pass < G.V(); pass ++) {
            /** 每次循环中对所有的边进行一遍松弛操作 遍历所有边的方式是先遍历所有的顶点 然后遍历和所有顶点相邻的所有边 */
            for (int i = 0; i < G.V(); i ++) {
                /** 使用我们实现的邻边迭代器遍历和所有顶点相邻的所有边 */
                typename Graph::adjIterator adj(G,i);
                for (Edge<Weight>* e = adj.begin(); !adj.end(); e = adj.next())
                    /**
                     * 对于每一个边首先判断e->v()可达
                     * 之后看如果e->w()以前没有到达过 显然我们可以更新distTo[e->w()]
                     * 或者e->w()以前虽然到达过 但是通过这个e我们可以获得一个更短的距离 即可以进行一次松弛操作 我们也可以更新distTo[e->w()]
                     */
                    if (from[e->v()] && (!from[e->w()] || distTo[e->v()] + e->wt() < distTo[e->w()])) {
                        distTo[e->w()] = distTo[e->v()] + e->wt();
                        from[e->w()] = e;
                    }
            }
        }
        hasNegativeCycle = detectNegativeCycle();
    }

    ~BellmanFord() {
        delete[] distTo;
        delete from[s];
    }

    /** 返回图中是否有负权环 */
    bool negativeCycle() {
        return hasNegativeCycle;
    }

    /** 返回从s点到w点的最短路径长度 */
    Weight shortestPathTo(int w) {
        assert(w >= 0 && w < G.V());
        assert(!hasNegativeCycle);
        assert(hasPathTo(w));
        return distTo[w];
    }

    /** 判断从s点到w点是否连通 */
    bool hasPathTo(int w) {
        assert(w >= 0 && w < G.V());
        return from[w] != NULL;
    }

    /** 寻找从s到w的最短路径 将整个路径经过的边存放在vec中 */
    void shortestPath(int w, vector<Edge<Weight>> &vec) {
        assert(w >= 0 && w < G.V());
        assert(!hasNegativeCycle);
        assert(hasPathTo(w));

        /** 通过from数组逆向查找到从s到w的路径 存放到栈中 */
        stack<Edge<Weight>*> s;
        Edge<Weight> *e = from[w];
        while (e->v() != this->s) {
            s.push(e);
            e = from[e->v()];
        }
        s.push(e);

        /** 从栈中依次取出元素 获得顺序的从s到w的路径 */
        while (!s.empty()) {
            e = s.top();
            vec.push_back(*e);
            s.pop();
        }
    }
};
```