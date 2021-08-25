# [JAVA] 프로그래머스-멀쩡한_사각형



## 1. 문제

- https://programmers.co.kr/learn/courses/30/lessons/62048



## 2. 접근

1. 패턴 찾기(최대공약수)
   - 가로, 세로 길이에 따라 그어지는 대각선 모양이 다를텐데 어떻게 처리해야 될지 막막했다. 하지만 그림을 계속 보면 일정한 패턴으로 잘려진 사각형들을 볼 수 있다.
   - 예제에서 나온 가로 길이 8과 세로 길이 12, 그리고 그 안에 잘려진 사각형 패턴들을 계속해서 연관성을 찾다보니 **최대공약수** 라는 해답이 나왔다. 일정한 패턴이 가로 길이와 세로길이의 최대공약수 만큼 생성이 되고 반복이 된다는 걸 볼 수 있다.
2. 내부 사각형 안에서 잘려진 구간 찾기
   - 패턴을 찾아냈으면 그 내부 사각형에서 어떻게 잘려진 구간을 구해야 되는지 고민을 했다. 대각선 모양이 케이스마다 달라지니 어떻게 구해야될지 몰라 결국 검색을 해서 알아냈다. 
   - 대각선 방향이기 때문에 N+M-1 만큼 사각형이 잘려진다.



## 3. 풀이

```java
class Solution {
    public long solution(long w, long h) {
        long gcd_num;
        // 최대 공약수 구하기
        if(w >= h) gcd_num = gcd(w,h);
        else gcd_num = gcd(h,w);

        long N = w/gcd_num;
        long M = h/gcd_num;
        long cutting_part = (N+M-1)*gcd_num;
        long answer = w*h-cutting_part;
        return answer;
    }
  
    public long gcd(long a, long b) {
        while(b != 0) {
          long r = a%b;
          a = b;
          b = r;
        }
        return a;
		}
}
```



## 4. 정리

- 그림이나 도형, 새로운 유형이라고 느껴진다면 패턴이 있는지 찾아보자.
- 가끔 코테에서 최대공약수를 사용하는 문제가 나오는데 최대공약수를 구현할 수 있도록 하자!(이왕 최소공배수까지..)