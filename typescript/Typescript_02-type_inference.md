# TypeScript 02 - 타입 추론(TypeInference)



## 1. Static Typing

- 타입을 선언하고 선언된 타입에 맞는 값만이 할당 또는 반환되어야 한다.



```python
# 며칠이 지나면 토마토들이 모두 익는지 최소 일수를 구하기
# 정수 1은 익은 토마토, 정수 0은 익지 않은 토마토, -1은 토마토가 들어있지 않은 칸

# 1과 인접한 노드가 0이라면 1로 바꾸고 익는 일수 더하기
# -1이라면 pass하기
from collections import deque

m, n = map(int, input().split()) # 상자의 크기 m = 가로칸의 수, n = 세로 칸의 수

data = []
for i in range(n):
    data.append(list(map(int, input().split())))

# print(data)
# 상, 하, 좌, 우
dx = [-1, 1, 0, 0]
dy = [0, 0, -1, 1]

def bfs(x, y):
    queue = deque()
    queue.append((x, y))

    while queue:
        x, y = queue.popleft()
        
        # 현재 좌표의 값이 1이상이라면 주변 토마토를 익은 토마토로 만들기
        if data[x][y] >= 1:
            for i in range(4):
                nx = x + dx[i]
                ny = y + dy[i]
                # 현재 좌표값이 범위에 들어오지 않는다면 패스
                if nx < 0 or ny < 0 or nx > n-1 or ny > m-1:
                    continue
                # 만약 좌표값이 0이라면 익은 토마토로 만들기
                elif data[nx][ny] == 0:
                    data[nx][ny] = 1

        # 익은 토마토를 탐색하기
        for i in range(4):
            nx = x + dx[i]
            ny = y + dy[i]

            # 탐색 좌표가 범위에 포함되는지 확인하기
            if nx < 0 or ny < 0 or nx > n-1 or ny > m-1:
                continue
            elif data[nx][ny] == 0:
                continue
            elif data[nx][ny] == 1:
                data[nx][ny] = data[x][y] + 1
                queue.append((nx, ny))

    # return result

for i in range(n):
    for j in range(m):
        x, y = i, j
        bfs(x, y)

for i in range(n):
    print(data[i])
```



