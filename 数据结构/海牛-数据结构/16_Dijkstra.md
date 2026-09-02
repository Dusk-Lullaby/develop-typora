# Dijkstra 算法

## 1. Dijkstra

<font color = "blue"> C 实现：</font>

```c
/*
	1. 初始化有向图， 没有路径的节点的节点dist更新为INF
	2. 将激活start编号到其他各个节点的路径进行更新，再更新path
	3. 循环，将所有节点都激活
		每激活一个节点，从原点开始到这个激活点，是目前认为的最优解，再最优解的情况下，增加相关的边，会不会影响其他未激活点的距离
		一旦发现更小值，更新path
*/
void DijkstraMatrixGraph(const MatrixGraph* graph, int start, int dist[], int path[]) {
	// 节点访问记录
	int* mark = malloc(sizeof(int) * graph->nodeNum);
	if (mark == NULL || dist == NULL) return;
	
	// 激活start后，更新dist表，path中的start编号设置为-1，作为路径打印时的结束标准
	for (int i = 0; i < graph->nodeNum; i++) {
		dist[i] = graph->edges[start][i];
		mark[i] = 0;
		if (dist[i] < INF) {
			path[i] = start;
		}
		else {
			path[i] = INF;
		}
	}
	mark[start] = 1;
	path[start] = -1;

	// 从dist里查找最小值
	// 除了start，还需要激活的点
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		int min = INF;
		int tmpIndex = 0;
		// 从未激活节点中，找到一个源点到其的最短距离
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && dist[j] < min) {
				min = dist[j];
				tmpIndex = j;
			}
		}

		mark[tmpIndex] = 1;

		// 以刚刚激活的节点，更新源点到其他顶点的距离
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && dist[j] > graph->edges[tmpIndex][j] + dist[tmpIndex]) {
				dist[j] = graph->edges[tmpIndex][j] + dist[tmpIndex];
				path[j] = tmpIndex;
			}
		}
	}

	free(mark);
}

```



<font color = "blue"> Java 实现：</font>

```java
    public void getDijkstraGraph(int start) {
        MatrixGraph.MatrixEdge[][] edges = this.graph.getEdges();
        // 初始化
        for (int i = 0; i < this.graph.getVertexNum(); i++) {
            dist[i] = edges[start][i].getMatrixEdge();
            mark[i] = false;
            if (dist[i] < this.graph.getINF()) {
                this.path[i] = start;
            } else {
                this.path[i] = this.graph.getINF();
            }
        }
        mark[start] = true;
        path[start] = -1;

        // 还需要激活 vertexNum - 1 个节点
        for (int i = 0; i < this.graph.getVertexNum() - 1; i++) {
            // 获取最小值
            int min = this.graph.getINF();
            int tmpIndex = -1;
            for (int j = 0; j < this.graph.getVertexNum(); j++) {
                if (!mark[j] && min > this.dist[j]) {
                    min = this.dist[j];
                    tmpIndex = j;
                }
            }
            if (tmpIndex == -1) break;
            mark[tmpIndex] = true;

            for (int j = 0; j < this.graph.getVertexNum(); j++) {
                if (!this.mark[j] && this.dist[j] > this.dist[tmpIndex] + edges[tmpIndex][j].getMatrixEdge()) {
                    this.dist[j] = this.dist[tmpIndex] + edges[tmpIndex][j].getMatrixEdge();
                    this.path[j] = tmpIndex;
                }
            }
        }
    }

```



## 2. show

<font color = "blue"> C 实现：</font>

```c
void showShortPath(const int path[], int num, int pos) {
	int* stack = malloc(sizeof(int) * num);
	if (stack == NULL) return;
	int top = -1;

	while (path[pos] != -1) {
		stack[++top] = pos;
		pos = path[pos];
	}

	stack[++top] = pos;

	while (top != -1) {
		printf("%d\t", stack[top--]);
	}
	printf("\n");
}
```



<font color = "blue"> Java 实现：</font>

```java
    public void show(int pos) {
        Deque<Integer> stack = new ArrayDeque<>();
        while (this.path[pos] != -1) {
            stack.push(pos);
            pos = path[pos];
        }
        stack.push(pos);
        while (!stack.isEmpty()) {
            System.out.print(stack.pop() + "\t");
        }
        System.out.println();
    }

```



## 3. 完整实现

<font color = "blue"> C 实现：</font>

`Dijkstra.h`

```c
#pragma once
#include "MatrixGraph.h"

void DijkstraMatrixGraph(const MatrixGraph* graph, int start, int dist[], int path[]);

void showShortPath(const int path[], int num, int pos);
```

`Dijkstra.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include "Dijkstra.h"

/*
	1. 初始化有向图， 没有路径的节点的节点dist更新为INF
	2. 将激活start编号到其他各个节点的路径进行更新，再更新path
	3. 循环，将所有节点都激活
		每激活一个节点，从原点开始到这个激活点，是目前认为的最优解，再最优解的情况下，增加相关的边，会不会影响其他未激活点的距离
		一旦发现更小值，更新path
*/
void DijkstraMatrixGraph(const MatrixGraph* graph, int start, int dist[], int path[]) {
	// 节点访问记录
	int* mark = malloc(sizeof(int) * graph->nodeNum);
	if (mark == NULL) return;

	// 激活start后，更新dist表，path中的start编号设置为-1，作为路径打印时的结束标准
	for (int i = 0; i < graph->nodeNum; i++) {
		dist[i] = graph->edges[start][i];
		mark[i] = 0;
		if (dist[i] < INF) {
			path[i] = start;
		}
		else {
			path[i] = -1;
		}
	}
	mark[start] = 1;
	path[start] = -1;
	dist[start] = 0;

	// 从dist里查找最小值
	// 除了start，还需要激活的点
	for (int i = 0; i < graph->nodeNum - 1; i++) {
		int min = INF;
		int tmpIndex = 0;
		// 从未激活节点中，找到一个源点到其的最短距离
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && dist[j] < min) {
				min = dist[j];
				tmpIndex = j;
			}
		}
		mark[tmpIndex] = 1;
		// 以刚刚激活的节点，更新源点到其他顶点的距离
		for (int j = 0; j < graph->nodeNum; j++) {
			if (mark[j] == 0 && dist[tmpIndex] + graph->edges[tmpIndex][j] < dist[j]) {
				path[j] = tmpIndex;
				dist[j] = dist[tmpIndex] + graph->edges[tmpIndex][j];
			}
		}
	}

	free(mark);
}


void showShortPath(const int path[], int num, int pos) {
	int* stack;		// 栈结构
	int top = -1;	// 指向有效位置

	stack = malloc(sizeof(int) * num);
	if (stack == NULL) return;

	// 将上一个状态压入栈
	while (path[pos] != -1) {
		stack[++top] = pos;
		pos = path[pos];
	}
	stack[++top] = pos;
	
	// 弹栈，直到top = -1
	while (top != -1) {
		printf("%d\t", stack[top--]);
	}
	printf("\n");
	free(stack);
}
```

`main.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include "Dijkstra.h"

void setupMatrixGraph(MatrixGraph* graph, int edgeValue) {
	char* names[] = { "0", "1", "2", "3", "4", "5", "6"};
	initGraph(graph, names, sizeof(names) / sizeof(names[0]), 1, edgeValue);
	addMatrixGraph(graph, 0, 1, 4);
	addMatrixGraph(graph, 0, 2, 6);
	addMatrixGraph(graph, 0, 3, 6);
	addMatrixGraph(graph, 1, 4, 7);
	addMatrixGraph(graph, 1, 2, 1);
	addMatrixGraph(graph, 2, 4, 6);
	addMatrixGraph(graph, 2, 5, 4);
	addMatrixGraph(graph, 3, 2, 2);
	addMatrixGraph(graph, 3, 5, 5);
	addMatrixGraph(graph, 4, 6, 6);
	addMatrixGraph(graph, 5, 4, 1);
	addMatrixGraph(graph, 5, 6, 8);
}

int main() {
	MatrixGraph* graph = malloc(sizeof(MatrixGraph));
	setupMatrixGraph(graph, INF);
	if (graph == NULL) return;
	int* dist = malloc(sizeof(int) * graph->nodeNum);			// 存储 源点x到其他节点的最短路径
	int* path = malloc(sizeof(int) * graph->nodeNum);			// 存储源点到每个最短路径的前一个节点信息
	if (dist == NULL || path == NULL) {
		printf("malloc failed\n");
		return;
	}

	setupMatrixGraph(graph, INF);
	DijkstraMatrixGraph(graph, 0, dist, path);
	printf("0 node to 5 node\n");
	showShortPath(path, 10, 5);
	printf("0 node to 6 node\n");
	showShortPath(path, 10, 6);
}
```



<font color = "blue"> Java 实现：</font>

`DijsktraGraph`

```java
package com.sonnet.dijkstra;

import com.Sonnet.matrix_graph.MatrixGraph;

import java.util.ArrayDeque;
import java.util.Deque;

public class DijkstraGraph<T> {
    // 邻接矩阵
    final private MatrixGraph<T> graph;

    // 路径数组
    private int[] path;

    // 是否被激活
    private boolean[] mark;

    // 需要的距离
    private int[] dist;

    public DijkstraGraph(MatrixGraph<T> graph) {
        this.graph = graph;
        this.dist = new int[this.graph.getVertexNum()];
        this.path = new int[this.graph.getVertexNum()];
        this.mark = new boolean[this.graph.getVertexNum()];
    }

    public void getDijkstraGraph(int start) {
        MatrixGraph.MatrixEdge[][] edges = this.graph.getEdges();
        // 初始化
        for (int i = 0; i < this.graph.getVertexNum(); i++) {
            dist[i] = edges[start][i].getMatrixEdge();
            mark[i] = false;
            if (dist[i] < this.graph.getINF()) {
                this.path[i] = start;
            } else {
                this.path[i] = this.graph.getINF();
            }
        }
        mark[start] = true;
        path[start] = -1;

        // 还需要激活 vertexNum - 1 个节点
        for (int i = 0; i < this.graph.getVertexNum() - 1; i++) {
            // 获取最小值
            int min = this.graph.getINF();
            int tmpIndex = -1;
            for (int j = 0; j < this.graph.getVertexNum(); j++) {
                if (!mark[j] && min > this.dist[j]) {
                    min = this.dist[j];
                    tmpIndex = j;
                }
            }
            if (tmpIndex == -1) break;
            mark[tmpIndex] = true;

            for (int j = 0; j < this.graph.getVertexNum(); j++) {
                if (!this.mark[j] && this.dist[j] > this.dist[tmpIndex] + edges[tmpIndex][j].getMatrixEdge()) {
                    this.dist[j] = this.dist[tmpIndex] + edges[tmpIndex][j].getMatrixEdge();
                    this.path[j] = tmpIndex;
                }
            }
        }
    }

    public void show(int pos) {
        Deque<Integer> stack = new ArrayDeque<>();
        while (this.path[pos] != -1) {
            stack.push(pos);
            pos = path[pos];
        }
        stack.push(pos);
        while (!stack.isEmpty()) {
            System.out.print(stack.pop() + "\t");
        }
        System.out.println();
    }
}

```

`DijkstraTest`

```java
package com.sonnet.dijkstra;

import com.Sonnet.matrix_graph.MatrixGraph;

public class DijkstraTest {

    public static void main(String[] args) {
        Integer[] show = {0, 1, 2, 3, 4, 5, 6};
        MatrixGraph<Integer> matrixGraph = new MatrixGraph<>(show, show.length, true, 999999);
        matrixGraph.addMatrixGraph(0, 1, 4);
        matrixGraph.addMatrixGraph(0, 2, 6);
        matrixGraph.addMatrixGraph(0, 3, 6);
        matrixGraph.addMatrixGraph(1, 4, 7);
        matrixGraph.addMatrixGraph(1, 2, 1);
        matrixGraph.addMatrixGraph(2, 4, 6);
        matrixGraph.addMatrixGraph(2, 5, 4);
        matrixGraph.addMatrixGraph(3, 2, 2);
        matrixGraph.addMatrixGraph(3, 5, 5);
        matrixGraph.addMatrixGraph(4, 6, 6);
        matrixGraph.addMatrixGraph(5, 4, 1);
        matrixGraph.addMatrixGraph(5, 6, 8);

        DijkstraGraph<Integer> graph = new DijkstraGraph<>(matrixGraph);
        graph.getDijkstraGraph(0);
        graph.show(5);
        graph.show(6);
    }
}

```

