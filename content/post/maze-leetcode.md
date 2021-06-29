+++ 
author = "Xi Jiang"
title = "Maze games and BFS (Leetcode #490 #505)"
date = "2021-06-28"
description = "Leetcode solution for maze problem"
tags = [
    "Algorithm","Leetcode"
]
thumbnail = "images/Maze.png"
+++
<p align="center">
    <img src="../../images/WalkieTalkie.gif" width="600" />
</p>

Recently when I was reviewing *Ludum Dare* game jam competition and those artworks from the past years, some of the witty ideas really inspired me. In LD 37(2016, the theme: one room）, @managore(*Daniel Linssen*) created the coolest chatroom platform game Walkie Talkie with perfect implementation and top entries. Since he is always one of my favourite indie game designers so I'm looking forward to his game this year.

<img src="../../images/adrena.png" width="800" />

This 1-bit style game called adrena-line that made for Ludum Dare 46(Deeper and Deeper) is also published on his [personal website](https://managore.itch.io/). And this maze really reminds me of a classic BFS leetcode problem and its extension.

<p align="center">
  <img src="../../images/adrena.gif" width="400"/>
</p>

## Breadth-First Search

In BFS, we first explores a graph starting at a node and visits all the node's neighbors, then visits all the neighbors's neighbors and continues in this fashion. In pseudocode, we could present a simple template for the similar problems.

{{< highlight java >}}
int BFS(Node Node start, Node target){
    Queue<Node> q; // core datastructure
    Set<Node> visited; // avoid visited nodes to be added

    q.offer(start); // add start point to the queue
    visited.add(start);
    int step = 0; // record radius steps

    while(q not empty){
        int sz = q.size();
        // add one step radius for all the nodes in queue
        for(int i = 0; i < sz; i++){
            Node cur = q.poll();
            // judge: is it the desitination?
            if(cur is target)
                return step;
            // add cur's neighbors to queue
            for(Node x: cur.adj())
                if(x not in visited){
                    q.offer(x);
                    visited.add(x);
                }
        }
        // update steps
        step++
    }
}
{{< /highlight >}}

## Leetcode 490 Discription

<p align="center">
  <img src="../../images/maze_1.png" width="400"/>
</p>

>There is a ball in a maze with empty spaces (represented as 0) and walls (represented as 1). The ball can go through the empty spaces by rolling **up**, **down**, **left** or **right**, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.<br>
<br> Given the m x n maze, the ball's start position and the destination, where start = [start<sub>row</sub>, start<sub>col</sub>] and destination = [destination<sub>row</sub>, destination<sub>col</sub>], return true if the ball can stop at the destination, otherwise return false.

>**Input**: maze = [[0,0,1,0,0],[0,0,0,0,0],[0,0,0,1,0],[1,1,0,1,1],[0,0,0,0,0]], start = [0,4], destination = [4,4]<br>
**Output**: true<br>
**Explanation**: One possible way is : left -> down -> left -> down -> right -> down -> right.<br>

This problem is a classic 2D matrix BFS traversing, we may choose to initialize a direction array first.

{{< highlight java >}}
int[][] dirs = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};
{{< /highlight >}}

The critical part is the right time for conditional checking whether the current node is the destination. We could put this code right after polling elements from the queue.

{{< highlight java >}}
class Solution {
    public boolean hasPath(int[][] maze, int[] start, int[] destination) {
        int[][] dirs = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};
        boolean[][] visited = new boolean[maze.length][maze[0].length];
        Queue<int[]> q = new LinkedList<>();
        q.add(start);
        visited[start[0]][start[1]] = true;
        while (!q.isEmpty()){
            int[] cur = q.poll();
            if (cur[0] == destination[0] && cur[1] == destination[1]){
                return true;
            }
            for(int[] dir : dirs){
                int x = cur[0], y = cur[1];
                while(x >= 0 && y >= 0 && x < maze.length && y < maze[0].length && maze[x][y] != 1){
                    x += dir[0];
                    y += dir[1];
                }
                x -= dir[0];
                y -= dir[1];
                if(!visited[x][y]){
                    q.add(new int[] {x, y});
                    visited[x][y] = true;
                }
            }
        }
        return false;  
        
    }
}
{{< /highlight >}}

## Leetcode 505 Discription

>Almost the same as #490 except for return the **shortest distance** for the ball to stop at the destination. If the ball cannot stop at destination, return -1.

>**Input**: maze = [[0,0,1,0,0],[0,0,0,0,0],[0,0,0,1,0],[1,1,0,1,1],[0,0,0,0,0]], start = [0,4], destination = [4,4]<br>
**Output**: 12<br>
**Explanation**: One possible way is : left -> down -> left -> down -> right -> down -> right.<br>
The length of the path is 1 + 1 + 3 + 1 + 2 + 2 + 2 = 12.<br>

{{< highlight java >}}
class Solution {
    int[][] dirs = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int[][] distance = new int[maze.length][maze[0].length];
        for(int[] row:distance) Arrays.fill(row, Integer.MAX_VALUE);
        distance[start[0]][start[1]] = 0;
        dijkstra(maze, start, distance);
        return distance[destination[0]][destination[1]] == Integer.MAX_VALUE? -1 : distance[destination[0]][destination[1]];
    }
    
    public void dijkstra(int[][] maze, int[] start, int[][] distance){
        PriorityQueue<int[]> q = new PriorityQueue<>((a, b) -> (a[2] - b[2]));
        q.offer(new int[]{start[0], start[1], 0});
        while (!q.isEmpty()){
            int[] cur = q.poll();
            for(int[] dir : dirs){
                int x = cur[0] + dir[0], y = cur[1] + dir[1], count = 0;
                while(x >= 0 && y >= 0 && x < maze.length && y < maze[0].length && maze[x][y] == 0){
                    x += dir[0];
                    y += dir[1];
                    count++;
                }
                x -= dir[0];
                y -= dir[1];
                if(distance[cur[0]][cur[1]] + count < distance[x][y]){
                    distance[x][y] = distance[cur[0]][cur[1]] + count;
                    q.add(new int[]{x, y, distance[x][y]});
                
                }
            }   
        }

    }
}
{{< /highlight >}}