# [JAVA] 프로그래머스_카카오프렌즈-컬러링북



## 1. 문제

- https://programmers.co.kr/learn/courses/30/lessons/1829



## 2. 접근

- BFS 혹은 DFS로 쉽게 풀 수 있었던 문제였다.
- 맵 전체를 순회하면서 0이 아닌 값이 있으면 그 점을 기점으로 상,하,좌,우 순회하면서 연결되어 있는 부분(DFS, BFS)들을 찾아나가면 된다.
- 나는 개인적으로 BFS 보다 DFS가 구현하기가 쉬워서 DFS로 구현하였다.



## 3. 풀이

```java
class Solution {
    static boolean[][] visited;
		static int[][] pos = {{-1,0},{1,0},{0,-1},{0,1}};	// 상,하,좌,우
		static int M, N;
		static int tmpSize;
    public int[] solution(int m, int n, int[][] picture) {
        int numberOfArea = 0;
        int maxSizeOfOneArea = Integer.MIN_VALUE;
        M = m;
        N = n;
        visited = new boolean[M][N];
 
        for (int i = 0; i < M; i++) {
					for (int j = 0; j < N; j++) {
            if(picture[i][j] != 0 && !visited[i][j]) {
              tmpSize = 0;
              dfs(i,j,picture[i][j], picture);
              numberOfArea++;
              maxSizeOfOneArea = Math.max(maxSizeOfOneArea, tmpSize);
						}
					}
				}
        int[] answer = new int[2];
        answer[0] = numberOfArea;
        answer[1] = maxSizeOfOneArea;
        return answer;
    }
    
    static void dfs(int r, int c, int color, int[][] picture) {
		tmpSize++;
		visited[r][c] = true;
		for (int i = 0; i < pos.length; i++) {
			int nr = r + pos[i][0];
			int nc = c + pos[i][1];
			
			if(nr>=0 && nr<M && nc>=0 && nc<N && picture[nr][nc] == color && !visited[nr][nc]) {
				dfs(nr,nc,color,picture);
			}
		}
	}
    
}
```



## 4. 정리

- BFS, DFS를 구현 연습 해볼만한 좋은 문제였다.