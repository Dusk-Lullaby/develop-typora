# `Prim`

## 1. 概念

动态维护一个待激活顶点的权值数组。

任意取一个顶点，把这个节点激活，具有到其他节点的权值

1. 从权值数组中，找一个最小的边，激活新的点
2. 激活新的点，又有了新的发现，更新权值数组
3. 重复1
4. 直到所有顶点都激活



## 2. 申明

<font color = "blue"> C 实现：</font>

```c
#include "MatrixGraph.h"
// 定义一个边集结构 边集数组
typedef struct {
	int begin;		// 起点
	int end;		// 终点
	int weight;		// 权重
}EdgeSet;
```



<font color = "blue"> Java 实现：</font>

```java
package com.sonnet.edge_set;

public class EdgeSet {
    private int head;   // 头
    private int tail;   // 尾
    private int weight; // 权值

    public EdgeSet(int head, int tail, int weight) {
        this.head = head;
        this.tail = tail;
        this.weight = weight;
    }

    public int getHead() {
        return head;
    }

    public void setHead(int head) {
        this.head = head;
    }

    public int getTail() {
        return tail;
    }

    public void setTail(int tail) {
        this.tail = tail;
    }

    public int getWeight() {
        return weight;
    }

    public void setWeight(int weight) {
        this.weight = weight;
    }

    @Override
    public String toString() {
        return "EdgeSet{" +
                "head=" + head +
                ", tail=" + tail +
                ", weight=" + weight +
                '}';
    }
}

```

```java
package com.sonnet.prim;

import com.Sonnet.matrix_graph.MatrixGraph;
import com.sonnet.edge_set.EdgeSet;

import java.util.List;

public class PrimGraph<T> {
    private final MatrixGraph<T> matrixGraph;           // 邻接矩阵
    private List<EdgeSet> result;                       // 集合，存储最短路径

    public PrimGraph(MatrixGraph<T> matrixGraph, List<EdgeSet> result) {
        this.matrixGraph = matrixGraph;
        this.result = result;
    }

    public List<EdgeSet> getResult() {
        return result;
    }

    @Override
    public String toString() {
        return "PrimGraph{" +
                "result=" + result +
                '}';
    }
}

```



## 3. `Prim`

<font color = "blue"> C 实现：</font>

```c
int PrimMatrixGraph(const MatrixGraph* graph, int startV, EdgeSet* result) {
	// 权值cost
	int* cost = malloc(sizeof(int) * graph->nodeNum);
	// 激活点
	int* mark = malloc(sizeof(int) * graph->nodeNum);
	// 从哪个点开始访问
	int* visited = malloc(sizeof(int) * graph->nodeNum);
	if (cost == NULL || mark == NULL || visited == NULL) return 0;

	// 1. 更新第一个节点激活的状态
	for (int i = 0; i < graph->nodeNum; i++) {
		mark[i] = 0;
		cost[i] = graph->edges[startV][i];
		// 更新visit信息，说明从哪个节点开始访问i
		if (graph->edges[startV][i] < INF) {
			visited[i] = startV;
		}
	}
	mark[startV] = 1;

	int sum = 0;
	// 2. 动态激活节点，查找最小值，添加到result边集数组
	// 查找 n - 1 个最小生成树的边
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		// 寻找最小值
		int min = INF;
		int k = 0;
		// 从权值数组里找到未激活顶点的最小值
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && cost[j] < min) {
				min = cost[j];
				k = j;
			}
		}

		// 激活最小值的节点
		mark[k] = 1;
		// 确定从哪个节点来
		result[i].begin = visited[k];
		result[i].end = k;
		result[i].weight = min;
		sum += min;

		// 激活的k号节点，它的边比之前的cost小
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && cost[j] > graph->edges[k][j]) {
				cost[j] = graph->edges[k][j];
				visited[j] = k;
			}
		}
	}

	free(cost);
	free(visited);
	free(mark);

	return sum;
}
```



<font color = "blue"> Java 实现：</font>

```java
    public int primMatrixGraph(int startV) {
        // 赋值
        int vertexNum = this.matrixGraph.getVertexNum();
        MatrixGraph.MatrixEdge[][] edges = this.matrixGraph.getEdges();
        // 权值cost
        int[] cost = new int[vertexNum];
        // 从哪个点开始访问
        int[] visited = new int[vertexNum];
        // 激活点
        boolean[] mark = new boolean[vertexNum];

        // 更新第一个节点激活状态
        for (int i = 0; i < vertexNum; i++) {
            cost[i] = edges[startV][i].getMatrixEdge();
            // 更新visit信息，说明从哪个节点开始访问i
            if (edges[startV][i].getMatrixEdge() < this.matrixGraph.getINF()) {
                visited[i] = startV;
            }
        }
        mark[startV] = true;

        int sum = 0;
        // 动态激活节点，查找最小值，添加到result边集数组
        // 查找 n - 1 个最小生成树的的边
        for (int i = 0; i < vertexNum - 1; i++) {
            int k = 0;
            int min = this.matrixGraph.getINF();
            for (int j = 0; j < vertexNum; j++) {
                // 从权值数组里找到未激活的点。并且小于当前最小值
                if (!mark[j] && min > cost[j]) {
                    min = cost[j];
                    k = j;
                }
            }

            // 激活最小值的节点
            mark[k] = true;
            // 加入result集合
            this.result.add(new EdgeSet(visited[k], k, min));
            sum += min;

            for (int j = 0; j < vertexNum; j++) {
                // 激活k号节点，它的边比之前的cost小
                if (!mark[j] && cost[j] > edges[k][j].getMatrixEdge()) {
                    cost[j] = edges[k][j].getMatrixEdge();
                    visited[j] = k;
                }
            }
        }

        return sum;
    }

```



## 4. 完整实现

<font color = "blue"> C 实现：</font>

`Prim.h`

```c
#pragma once

#include "MAtrixGraph.h"

// 定义一个边集结构 边集数组
typedef struct {
	int begin;		// 起点
	int end;		// 终点
	int weight;		// 权重
}EdgeSet;

int PrimMatrixGraph(const MatrixGraph* graph, int startV, EdgeSet* result);
```

`Prim.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include "Prim.h"

int PrimMatrixGraph(const MatrixGraph* graph, int startV, EdgeSet* result) {
	// 权值cost
	int* cost = malloc(sizeof(int) * graph->nodeNum);
	// 激活点
	int* mark = malloc(sizeof(int) * graph->nodeNum);
	// 从哪个点开始访问
	int* visited = malloc(sizeof(int) * graph->nodeNum);
	if (cost == NULL || mark == NULL || visited == NULL) return 0;
	int sum = 0;

	// 1. 更新第一个节点激活的状态
	for (int i = 0; i < graph->nodeNum; i++) {
		cost[i] = graph->edges[startV][i];
		mark[i] = 0;
		// 更新visit信息，说明从哪个节点开始访问i
		if (cost[i] < INF) {
			visited[i] = startV;
		}
		else {
			visited[i] = -1;
		}
	}
	mark[startV] = 1;

	// 2. 动态激活节点，查找最小值，添加到result边集数组
	// 查找 n - 1 个最小生成树的边
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		int min = INF;
		// 从权值数组里找到未激活顶点的最小值
		int k = 0;
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && cost[j] < min) {
				min = cost[j];
				k = j;
			}
		}
		// 激活最小值的节点
		mark[k] = 1;
		// 确定从哪个节点来
		result[i].begin = visited[k];
		result[i].end = k;
		result[i].weight = min;
		sum += min;
		for (int j = 0; j < graph->nodeNum; j++) {
			// 激活的k号节点，它的边比之前的cost小
			if (mark[j] == 0 && cost[j] > graph->edges[k][j]) {
				cost[j] = graph->edges[k][j];
				visited[j] = k;
			}
		}
	}

	free(cost);
	free(mark);
	free(visited);

	return sum;
}
```

`main.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include "Prim.h"

void setupMatrixGraph(MatrixGraph* graph, int edgeValue) {
	char* names[] = { "A", "B", "C", "D", "E", "F", "G" };
	initGraph(graph, names, sizeof(names) / sizeof(names[0]), 0, edgeValue);
	addMatrixGraph(graph, 0, 1, 12);
	addMatrixGraph(graph, 0, 5, 16);
	addMatrixGraph(graph, 0, 6, 14);
	addMatrixGraph(graph, 1, 2, 10);
	addMatrixGraph(graph, 1, 5, 7);
	addMatrixGraph(graph, 2, 3, 3);
	addMatrixGraph(graph, 2, 4, 5);
	addMatrixGraph(graph, 2, 5, 6);
	addMatrixGraph(graph, 3, 4, 4);
	addMatrixGraph(graph, 4, 5, 2);
	addMatrixGraph(graph, 4, 6, 8);
	addMatrixGraph(graph, 5, 6, 9);
}

void test01() {
	MatrixGraph* graph = malloc(sizeof(MatrixGraph));
	if (graph == NULL) return;
	setupMatrixGraph(graph, INF);

	EdgeSet* result = malloc(sizeof(EdgeSet) * (graph->nodeNum) - 1);
	if (result == NULL) return;

	int sumWeight = PrimMatrixGraph(graph, 0, result);
	printf("Prim weight = %d\n", sumWeight);
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		printf("edge %d: [%s] --- [%d] --- [%s]\n", i + 1,
			graph->vex[result[i].begin].show, result[i].weight, graph->vex[result[i].end].show);
	}

	free(result);
	free(graph);
}

int main() {
	test01();
}
```



<font color = "blue"> Java 实现：</font>

`EdgeSet`

```java
package com.sonnet.edge_set;

import com.Sonnet.matrix_graph.MatrixGraph;

public class EdgeSet {
    private int head;   // 头
    private int tail;   // 尾
    private int weight; // 权值

    public EdgeSet(int head, int tail, int weight) {
        this.head = head;
        this.tail = tail;
        this.weight = weight;
    }

    public int getHead() {
        return head;
    }

    public void setHead(int head) {
        this.head = head;
    }

    public int getTail() {
        return tail;
    }

    public void setTail(int tail) {
        this.tail = tail;
    }

    public int getWeight() {
        return weight;
    }

    public void setWeight(int weight) {
        this.weight = weight;
    }

    @Override
    public String toString() {
        return "EdgeSet{" +
                "head=" + head +
                ", tail=" + tail +
                ", weight=" + weight +
                '}' + "\n";
    }
}

```

`PrimGraph`

```java
package com.sonnet.prim;

import com.Sonnet.matrix_graph.MatrixGraph;
import com.sonnet.edge_set.EdgeSet;

import java.util.ArrayList;
import java.util.List;

public class PrimGraph<T> {
    private final MatrixGraph<T> matrixGraph;           // 邻接矩阵
    private List<EdgeSet> result;                       // 集合，存储最短路径

    public PrimGraph(MatrixGraph<T> matrixGraph) {
        this.matrixGraph = matrixGraph;
        this.result = new ArrayList<>();
    }

    public List<EdgeSet> getResult() {
        return result;
    }

    @Override
    public String toString() {
        return "PrimGraph{" +
                "result=" + result +
                '}';
    }

    public int primMatrixGraph(int startV) {
        // 赋值
        int vertexNum = this.matrixGraph.getVertexNum();
        MatrixGraph.MatrixEdge[][] edges = this.matrixGraph.getEdges();
        // 权值cost
        int[] cost = new int[vertexNum];
        // 从哪个点开始访问
        int[] visited = new int[vertexNum];
        // 激活点
        boolean[] mark = new boolean[vertexNum];

        // 更新第一个节点激活状态
        for (int i = 0; i < vertexNum; i++) {
            cost[i] = edges[startV][i].getMatrixEdge();
            // 更新visit信息，说明从哪个节点开始访问i
            if (edges[startV][i].getMatrixEdge() < this.matrixGraph.getINF()) {
                visited[i] = startV;
            }
        }
        mark[startV] = true;

        int sum = 0;
        // 动态激活节点，查找最小值，添加到result边集数组
        // 查找 n - 1 个最小生成树的的边
        for (int i = 0; i < vertexNum - 1; i++) {
            int k = 0;
            int min = this.matrixGraph.getINF();
            for (int j = 0; j < vertexNum; j++) {
                // 从权值数组里找到未激活的点。并且小于当前最小值
                if (!mark[j] && min > cost[j]) {
                    min = cost[j];
                    k = j;
                }
            }

            // 激活最小值的节点
            mark[k] = true;
            // 加入result集合
            this.result.add(new EdgeSet(visited[k], k, min));
            sum += min;

            for (int j = 0; j < vertexNum; j++) {
                // 激活k号节点，它的边比之前的cost小
                if (!mark[j] && cost[j] > edges[k][j].getMatrixEdge()) {
                    cost[j] = edges[k][j].getMatrixEdge();
                    visited[j] = k;
                }
            }
        }

        return sum;
    }
}

```

`PrimTest`

```java
package com.sonnet.prim;

import com.Sonnet.matrix_graph.MatrixGraph;

public class PrimTest {

    public static void main(String[] args) {
        test();
    }

    public static MatrixGraph<String> toMatrixGraph() {
        String[] show = {"A", "B", "C", "D", "E", "F", "G"};
        MatrixGraph<String> matrixGraph = new MatrixGraph<>(show, show.length, false, (int) 1E4);
        matrixGraph.addMatrixGraph(0, 1, 12);
        matrixGraph.addMatrixGraph(0, 5, 16);
        matrixGraph.addMatrixGraph(0, 6, 14);
        matrixGraph.addMatrixGraph(1, 2, 10);
        matrixGraph.addMatrixGraph(1, 5, 7);
        matrixGraph.addMatrixGraph(2, 3, 3);
        matrixGraph.addMatrixGraph(2, 4, 5);
        matrixGraph.addMatrixGraph(2, 5, 6);
        matrixGraph.addMatrixGraph(3, 4, 4);
        matrixGraph.addMatrixGraph(4, 5, 2);
        matrixGraph.addMatrixGraph(4, 6, 8);
        matrixGraph.addMatrixGraph(5, 6, 9);

        return matrixGraph;
    }

    public static void test() {
        PrimGraph<String> graph = new PrimGraph<>(toMatrixGraph());
        System.out.println(graph.primMatrixGraph(0));
        System.out.println(graph.getResult());
    }

}
```



## 5. 例题

[连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/description/)