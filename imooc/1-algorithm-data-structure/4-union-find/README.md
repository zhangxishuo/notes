# 并查集

高效解决连接问题，网络中节点间的连接状态(用户网络)。

对于一组数据，主要支持两个动作：

- union(p, q)
- find(p)

![并查集的基本数据表示](assets/data.png)

值相同的数据表示相连，图中`0/2/4/6/8`相互连接，`1/3/5/7/9`相互连接。

## Quick Find

```cpp
class UnionFind {
private:
    int* id;
    int count;

public:
    UnionFind(int n) {
        this->count = n;
        this->id = new int[n];
        for (int i = 0; i < n; i ++) {
            this->id[i] = i;
        }
    }

    ~UnionFind() {
        delete[] id;
    }

    int find(int p) {
        assert(p >= 0 && p < this->count);
        return id[p];
    }

    bool isConnected(int p, int q) {
        return find(p) == find(q);
    }

    void unionElements(int p, int q) {
        int pId = find(p);
        int qId = find(q);

        if (pId == qId) {
            return;
        }

        for (int i = 0; i < this->count; i ++) {
            if (this->id[i] == pId) {
                this->id[i] = qId;
            }
        }
    }
};
```