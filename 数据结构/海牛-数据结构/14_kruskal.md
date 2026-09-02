# kruskal算法

## 1. 概念

**Kruskal 算法**（克鲁斯卡尔算法）是一种用于寻找加权无向图的**最小生成树 (Minimum Spanning Tree, MST)** 的贪心算法。

它的核心目标是：在一个包含 V 个顶点和 E 条边的连通图中，找到一棵包含所有顶点的树，使得这棵树的所有边权重之和最小。

> kruskal：m 条边，n 个顶点，m 条边中找 n - 1 条边， n - 1 条边之和是最小，且他们会构成最小生成树。
>
> 约束：必须保证这条边的加入，不会产生环
>
> 添加一条边，<a, b> 知道两个顶点，不在一个集合里，采用并查集



## 2. 申明

<font color = "blue"> C 实现：</font>

```c
// 从邻接矩阵中初始化边集数组

// 定义一个边集结构 边集数组
typedef struct {
	int head;		// 头
	int end;		// 尾
	int weight;		// 权重
}EdgeSet;

// 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
#include "MatrixGraph.h"

```



<font color = "blue"> Java 实现：</font>

```java
public class Graph<T> {
    // 边集结构
    public static class EdgeSet {
        private int head;       // 头
        private int tail;       // 尾
        private int weight;     // 权重

        public EdgeSet(int head, int tail, int weight) {
            this.head = head;
            this.tail = tail;
            this.weight = weight;
        }
    }

    // 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
    private EdgeSet[] edges;
    private int edgeNum;
    private int vertexNum;
}
```



## 3. 初始化

<font color = "blue"> C 实现：</font>

```c
// 初始化
int initEdgeSet(MatrixGraph* graph, EdgeSet* edges) {
	int sum = 0;
	for (int i = 0; i < graph->nodeNum; i++) {
		// 由于是无向图因此只需要考虑对角线之上的边
		for (int j = i; j < graph->nodeNum; j++) {
			// 是边
			if (graph->edges[i][j] > 0 && graph->edges[i][j] < INF) {
				edges[sum].head = i;
				edges[sum].end = j;
				edges[sum].weight = graph->edges[i][j];
				sum++;
			}
		}
	}

	return sum;
}
```



<font color = "blue"> Java 实现：</font>

```java
    // 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
    public Graph(MatrixGraph<T> matrixGraph) {
        this.edgeNum = matrixGraph.getEdgeNum();
        this.vertexNum = matrixGraph.getVertexNum();
        this.edges = new EdgeSet[this.edgeNum];
        MatrixGraph.MatrixEdge[][] matrixEdges = matrixGraph.getEdges();

        int cnt = 0;
        for (int i = 0; i < this.vertexNum; i++) {
            for (int j = i; j < vertexNum; j++) {
                if (matrixEdges[i][j].getMatrixEdge() > 0) {
                    this.edges[cnt++] = new EdgeSet(i, j , matrixEdges[i][j].getMatrixEdge());
                }
            }
        }
    }

```



## 4. 排序

<font color ="blue"> C 实现：</font>

```c
// 排序
void sortEdgeSet(EdgeSet* edges, int num) {
	for (int i = 0; i < num; i++) {
		for (int j = i + 1; j < num; j++) {
			if (edges[j].weight < edges[i].weight) {
				EdgeSet tmp = edges[i];
				edges[i] = edges[j];
				edges[j] = tmp;
			}
		}
	}
}
```



<font color = "blue"> Java 实现：</font>

```java
    /*
        功能：排序
        参数：无
        返回值：无
     */
    public void sortEdgeSet() {
        for (int i = 0; i < this.edgeNum; i++) {
            for (int j = i; j < this.edgeNum; j++) {
                if (edges[i].weight > edges[j].weight) {
                    EdgeSet tmp = edges[i];
                    edges[i] = edges[j];
                    edges[j] = tmp;
                }
            }
        }
    }

```



## 5. `kruskal`

<font color = "blue"> C 实现：</font>

```c
// 路径压缩寻找根节点
static int findRoot(int* uSet, int a) {
	// 父亲节点是自己本身时，则为根节点
	if (uSet[a] != a) {
		uSet[a] = findRoot(uSet, uSet[a]);
	}


	return uSet[a];
}

// kruskal
int kruskalMGraph(const MatrixGraph* graph, const EdgeSet* edges, int num, EdgeSet* result) {
	int* uSet = malloc(sizeof(int) * num);
	if (uSet == NULL) return 0;
	for (int i = 0; i < graph->nodeNum; i++) {
		uSet[i] = i;
	}

	int count = 0;
	int sum = 0;
	for (int i = 0; i < num; i++) {
		int a = findRoot(uSet, edges[i].head);
		int b = findRoot(uSet, edges[i].end);
		if (a == b) continue;
		uSet[a] = b;
		result[count].head = edges[i].head;
		result[count].end = edges[i].end;
		result[count].weight = edges[i].weight;
		sum += edges[i].weight;
		count++;
		if (count == graph->nodeNum - 1) break;
	}

	free(uSet);
	return sum;
}
```



<font color = "blue"> Java 实现：</font>

```c
    /*
        功能：kruskal
        参数：最短路径数组
        返回值：最短路径
     */
    public int kruskalEdgeSet(EdgeSet[] result) {
        int[] uSet = new int[this.vertexNum];
        for (int i = 0; i < this.vertexNum; i++) {
            uSet[i] = i;
        }

        int count = 0;
        int sum = 0;
        for (int i = 0; i < this.edgeNum; i++) {
            int a = findRoot(uSet, this.edges[i].head);
            int b = findRoot(uSet, this.edges[i].tail);
            if (a != b) {
                uSet[a] = b;
                result[count] = new EdgeSet(this.edges[i].head, this.edges[i].tail, this.edges[i].weight);
                count++;
                sum += this.edges[i].weight;
                if (count == this.vertexNum - 1) break;
            }
        }

        return sum;
    }

    /*
        功能：压缩路径寻找根节点
        参数：并查集 需要查找的节点
        返回值：根节点
     */
    private int findRoot(int[] uSet, int a) {
        // 当父亲节点为自己时，则为根节点
        if (uSet[a] != a) {
            uSet[a] = findRoot(uSet, uSet[a]);
        }

        return uSet[a];
    }

```



## 6. 完整实现

<font color = "blue"> C 实现：</font>

`kruskal.h`

```c
#pragma once
// 从邻接矩阵中初始化边集数组

// 定义一个边集结构， 边集数组
typedef struct {
	int begin;		// 边的起点
	int end;		// 边的终点
	int weight;		// 边的权重
}EdgeSet;

// 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
#include "MatrixGraph.h"

/*
	功能：初始化边集数组
	参数：邻接矩阵 边集数组
	返回值：边的个数
*/
int initEdgeSet(const MatrixGraph* graph, EdgeSet* edges);

/*
	功能：排序边集数组
	参数：边集数组 顶点个数
	返回值：无
*/
void sortEdgeSet(EdgeSet* edges, int num);

/*
	功能：kruskal最小生成树
	参数：邻接矩阵 边集数组 边数 最短路径数组
	返回值：最短路径
*/
int kruskalMGraph(const MatrixGraph* graph, const EdgeSet* edges, int num, EdgeSet* result);

```

`kruskal.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "kruskal.h"

int initEdgeSet(const MatrixGraph* graph, EdgeSet* edges) {
	int k = 0;
	// 遍历每个节点
	for (int i = 0; i < graph->nodeNum; i++) {
		for (int j = i + 1; j < graph->nodeNum; j++) {
			// 有边
			if (graph->edges[i][j] > 0 && graph->edges[i][j] < INF) {
				edges[k].begin = i;
				edges[k].end = j;
				edges[k].weight = graph->edges[i][j];
				k++;
			}
		}
	}

	return k;
}

void sortEdgeSet(EdgeSet* edges, int num) {
	EdgeSet tmp;
	for (int i = 0; i < num; i++) {
		for (int j = i + 1; j < num; j++) {
			if (edges[j].weight < edges[i].weight) {
				memcpy(&tmp, &edges[i], sizeof(EdgeSet));
				memcpy(&edges[i], &edges[j], sizeof(EdgeSet));
				memcpy(&edges[j], &tmp, sizeof(EdgeSet));
			}
		}
	}
}

// 并查集 quickunion，找a的根节点
static int getRoot(int* uSet, int a) {
	// 当父节点是自己时，是根节点
	if (uSet[a] != a) {
		uSet[a] = getRoot(uSet, uSet[a]);
	}

	return uSet[a];
}

int kruskalMGraph(const MatrixGraph* graph, const EdgeSet* edges, int num, EdgeSet* result) {
	int sum = 0;
	int count = 0;
	// 1. 初始化并查集， 每一个节点的编号都是自己
	int* uSet = malloc(sizeof(int) * graph->nodeNum);
	if (uSet == NULL) return 0;

	for (int i = 0; i < graph->nodeNum; i++) {
		uSet[i] = i;
	}

	// 2. 从已经排好序的边集中，找到最小的边，当这个边加入后，不构成闭环
	for (int i = 0; i < num; i++) {
		int a = getRoot(uSet, edges[i].begin);
		int b = getRoot(uSet, edges[i].end);
		if (a != b) {
			uSet[a] = b;
			result[count].begin = edges[i].begin;
			result[count].end = edges[i].end;
			result[count].weight = edges[i].weight;
			sum += edges[i].weight;
			count++;
			if (count == graph->nodeNum - 1) 
				break;
		}
	}
	free(uSet);
	return sum;
}
```

`main.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include "kruskal.h"

void setupMatrixGraph(MatrixGraph* graph, int edgeValue) {
	char* names[] = { "A", "B", "C", "D", "E", "F", "G"};
	initGraph(graph, names, sizeof(names)/sizeof(names[0]), 0, edgeValue);
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
	setupMatrixGraph(graph, 0);
	EdgeSet* edges = malloc(sizeof(EdgeSet) * graph->edgeNum);
	if (edges == NULL) return;

	int num = initEdgeSet(graph, edges);
	sortEdgeSet(edges, num);

	EdgeSet* result = malloc(sizeof(EdgeSet) * (graph->nodeNum - 1));
	if (result == NULL) return;
	int sumWeight = kruskalMGraph(graph, edges, num, result);
	printf("kruskal sum of weight: %d\n", sumWeight);
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		printf("edges: %d: [%s] --- <%d> --- [%s]\n",
			i + 1, graph->vex[result[i].begin].show, result[i].weight, graph->vex[result[i].end].show);
	}
	free(edges);
	free(result);
	free(graph);
}

int main() {
	test01();
}
```



<font color = "blue"> Java 实现：</font>

`Graph`

```java
package com.sonnet.kruskal;

import com.Sonnet.matrix_graph.MatrixGraph;

import java.util.Arrays;

public class Graph<T> {
    // 边集结构
    public static class EdgeSet {
        private int head;       // 头
        private int tail;       // 尾
        private int weight;     // 权重

        public EdgeSet(int head, int tail, int weight) {
            this.head = head;
            this.tail = tail;
            this.weight = weight;
        }

        public int getHead() {
            return head;
        }

        public int getTail() {
            return tail;
        }

        public int getWeight() {
            return weight;
        }
    }

    // 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
    private EdgeSet[] edges;
    private int edgeNum;
    private int vertexNum;

    // 使用邻接矩阵来表示无向图，再从邻接矩阵中初始化边集数组
    public Graph(MatrixGraph<T> matrixGraph) {
        this.edgeNum = matrixGraph.getEdgeNum();
        this.vertexNum = matrixGraph.getVertexNum();
        this.edges = new EdgeSet[this.edgeNum];
        MatrixGraph.MatrixEdge[][] matrixEdges = matrixGraph.getEdges();

        int cnt = 0;
        for (int i = 0; i < this.vertexNum; i++) {
            for (int j = i; j < vertexNum; j++) {
                if (matrixEdges[i][j].getMatrixEdge() > 0) {
                    this.edges[cnt++] = new EdgeSet(i, j , matrixEdges[i][j].getMatrixEdge());
                }
            }
        }
    }

    /*
        功能：排序
        参数：无
        返回值：无
     */
    public void sortEdgeSet() {
        for (int i = 0; i < this.edgeNum; i++) {
            for (int j = i; j < this.edgeNum; j++) {
                if (edges[i].weight > edges[j].weight) {
                    EdgeSet tmp = edges[i];
                    edges[i] = edges[j];
                    edges[j] = tmp;
                }
            }
        }
    }

    /*
        功能：kruskal
        参数：最短路径数组
        返回值：最短路径
     */
    public int kruskalEdgeSet(EdgeSet[] result) {
        int[] uSet = new int[this.vertexNum];
        for (int i = 0; i < this.vertexNum; i++) {
            uSet[i] = i;
        }

        int count = 0;
        int sum = 0;
        for (int i = 0; i < this.edgeNum; i++) {
            int a = findRoot(uSet, this.edges[i].head);
            int b = findRoot(uSet, this.edges[i].tail);
            if (a != b) {
                uSet[a] = b;
                result[count] = new EdgeSet(this.edges[i].head, this.edges[i].tail, this.edges[i].weight);
                count++;
                sum += this.edges[i].weight;
                if (count == this.vertexNum - 1) break;
            }
        }

        return sum;
    }

    /*
        功能：压缩路径寻找根节点
        参数：并查集 需要查找的节点
        返回值：根节点
     */
    private int findRoot(int[] uSet, int a) {
        // 当父亲节点为自己时，则为根节点
        if (uSet[a] != a) {
            uSet[a] = findRoot(uSet, uSet[a]);
        }

        return uSet[a];
    }
}

```

`GraphTest`

```java
package com.sonnet.kruskal;

import com.Sonnet.matrix_graph.MatrixGraph;

public class GraphTest {

    public static void main(String[] args) {
        test();
    }

    public static MatrixGraph<String> toMatrixGraph() {
        String[] show = {"A", "B", "C", "D", "E", "F", "G"};
        MatrixGraph<String> matrixGraph = new MatrixGraph<>(show, show.length, false, 0);
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
        MatrixGraph<String> matrixGraph = toMatrixGraph();
        Graph<String> graph = new Graph<>(matrixGraph);
        graph.sortEdgeSet();
        Graph.EdgeSet[] result = new Graph.EdgeSet[matrixGraph.getVertexNum() - 1];
        int sum = graph.kruskalEdgeSet(result);
        System.out.println("kruskal sum of weight: " + sum);

        MatrixGraph.MatrixVertex<String>[] vertices =  matrixGraph.getVertices();
        for (Graph.EdgeSet edge : result) {
            System.out.println("head:" + vertices[edge.getHead()].getShow() + " " +
                    "tail:" + vertices[edge.getTail()].getShow() + " " + "weight:" + edge.getWeight());
        }

    }
}

```



## 7. 贪心

从`kruskal`算法中，可以得出一个专门的算法思想，那就是贪心/贪婪算法。

贪心算法（Greedy Algorithm）是一种在每一步选择中都采取当前状态下最好或最优（即最有利）的选择，从而希望最终能够导致全局最优解的算法策略。

它的核心思想非常直接：“目光短浅”地只看眼前利益，不考虑长远后果，且做出的选择不能回退。



贪心算法成立的两个核心前提：

要使用贪心算法求得全局最优解，问题必须满足以下两个性质：

- **贪心选择性质（Greedy Choice Property）：** 问题的全局最优解可以通过一系列局部最优的选择来达到。每次做出的选择只依赖于当前已有的信息，不依赖于未来的选择或子问题的解。
- **最优子结构（Optimal Substructure）：** 问题的最优解包含其子问题的最优解。也就是说，如果把原问题拆分成小问题，小问题的最优解组合起来就是原问题的最优解。



贪心算法的局限性：

贪心算法最大的缺点是**不保证总能得到全局最优解**。因为它没有从整体最优上进行考虑，一旦前面的选择导致后续陷入死胡同，它也无法回溯（撤销选择）。



## 8. 例题

[连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/description/)
