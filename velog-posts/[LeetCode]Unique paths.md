<p><a href="https://leetcode.com/problems/unique-paths/description/">https://leetcode.com/problems/unique-paths/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>m x n grid에 로봇이 있다. 로봇은 처음에 <code>grid[0][0]</code>에 위치해 있다. 로봇은 <code>grid[m-1][n-1]</code>로 이동하려고 한다. 로봇은 한번 움직일 때 <strong>아래 또는 오른쪽</strong>으로 밖에 이동하지 못한다.</p>
<p>이때 int형 m, n이 주어졌을 때, 로봇이 <code>grid[m-1][n-1]</code>로 도착할 수 있는 가능한 unique paths의 수를 리턴하라.
(단, 답은 2 * 10^9 이하의 시간복잡도를 가져야 한다.)</p>
<p>🚧제약 조건
1 &lt;= m, n &lt;= 10^2 
=&gt; 만약 완전탐색할 경우, O(N^2) = 10^4이므로 가능!
But recursive function으로 구현할 경우 -&gt; O(2^N) = 2^100은 불가!</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/fb93076f-1260-431d-834a-64653274806c/image.png" /></p>
<p>처음에는 바로 mississippi와 같이 순열이 떠올라서 중복된 원소를 나누는 것으로 접근했다. 즉, 위와 같은 테스트 케이스가 있을 때, $$8! / (6!<em>2!)$$가 나온다.
이것을 일반화시키면, $$(m+n-2)! / ((n-1)!</em>(m-1)!)$$로 나타낼 수 있다. </p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class UniquePathSolution {
    public int uniquePaths(int m, int n) {
        HashMap&lt;Integer, Integer&gt; memo = new HashMap&lt;&gt;();
        return fact(n + m - 2, memo) / (fact(n - 1, memo) * fact(m - 1, memo));
    }

    private static int fact(int x, HashMap&lt;Integer, Integer&gt; memo) {
        // base condition
        if (x &lt;= 1) {
            return 1;
        }

        if (!memo.containsKey(x)) {
            memo.put(x, fact(x - 1, memo) * x);
        }
        return memo.get(x);
    }
}</code></pre>
<ul>
<li>🚩fact() 함수를 중복 호출하는 경우가 있어서 DP를 부분적으로 적용해봤다. 그런데, 이렇게 하면 다음과 같은 문제 때문에 성공적으로 제출이 안된다:</li>
</ul>
<ol>
<li>m = n일 경우(n &gt; 3), $$2! * (n-1)! / ((n-1)! * (n-1)!)$$ 
= $$2 / (n-1)!$$ 이 되서 int 타입으로 리턴하면 당연히 0이 나올 수 밖에 없다.</li>
<li>최빈값인 m = 100, n = 99와 같은 값을 input으로 넣을 경우, <strong><span style="color: red;">signed int의 최대값인 21억이 넘어서 overflow가 발생</span></strong>하기 때문에 예상하지 못한 output을 초래한다.</li>
</ol>
<p>🤔따라서 조합론으로 접근하는 대신, 직접 그림을 그려가면서 적절한 알고리즘을 찾는 수 밖에 없다.</p>
<h2 id="🚩커스텀-클래스를-map에-넣을-수-없는-이유">🚩커스텀 클래스를 map에 넣을 수 없는 이유</h2>
<p><code>memo.put(new Point(0, 0))</code>과 같이 커스텀 클래스를 HashMap의 키(key)로 사용할 때, dp에서 재귀적으로 호출하다가 <strong>동일한 의미의 좌표가 전달되더라도 <code>new Point(0, 0)</code>처럼 새 인스턴스를 만들면 서로 다른 객체로 간주</strong>된다.
이를 해결하기 위해 번거롭게 <code>hashCode()</code>와 <code>equals()</code>를 오버라이딩 해야 하는데 코드가 매우 지저분하므로, 
🔨배열 타입의 dp를 사용하자.</p>
<h1 id="⚙️수정된-코드-설계">⚙️수정된 코드 설계</h1>
<p>정말 간단하게 노트에 grid 그림 그리고 중고딩 시절때 했던 방법으로 unique paths를 다음과 같이 구해보았다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/3cf73589-3081-4183-b4c2-2c4c386ef930/image.jpg" /></p>
<ol>
<li>각 모서리로 가는 방법은 1가지 밖에 없기 때문에 모서리를 모두 1로 <strong>채운다</strong>.</li>
<li>각 셀들의 방법 수를 더한 다음, 종점에 도달할 때까지 더하는 과정을 반복한다.</li>
</ol>
<p>...채운다, 채운다, 바로 tabulation 기법이 떠오르지 않은가!?? 그렇다. 이 문제는 자연스럽게 Bottom-up으로 풀 수가 있는 것이다.</p>
<p>🤔아니 grid는 암시적 그래프로 표현할 수 있으니까 bfs나 dfs로도 풀 수 있는거 아닌가요?
👉ㄴㄴ. 이전에 풀었던 The shortest path in a binary matrix에서는 시점에서 (dr, dc 세팅해서)bfs로 가장 짧은 경로를 구했지만, 이 문제는 <strong>경로(방법)의 개수</strong>를 구하는 것이 목적이므로 dp가 가장 적절하다.</p>
<p><a href="https://velog.io/@csj0209/DPtop-down-bottom-up#%EB%AC%B8%EC%A0%9C%EC%97%90%EC%84%9C-dp%EB%A1%9C-%ED%92%80%EB%9D%BC%EB%8A%94-%ED%9E%8C%ED%8A%B8%EB%93%A4">🍯문제에서 dp로 풀라는 힌트들</a> 참고 할 것!</p>
<p>따라서 tabulation 기법을 활용해서 다음과 같이 코드를 구현할 수 있다.</p>
<h1 id="💻코드-구현-1">💻코드 구현</h1>
<pre><code class="language-java">public class UniquePathSolution {
    public int uniquePaths(int m, int n) {
        // m x n 크기의 dp를 초기화
        int[][] dp = new int[m][n];

        // 모서리를 1로 모두 채운다.
        for (int i = 0; i &lt; m; i++) {
            dp[i][0] = 1;
        }
        for (int j = 0; j &lt; n; j++) {
            dp[0][j] = 1;
        }

        // 모서리의 합으로 종점의 값을 리턴
        for (int i = 1; i &lt; m; i++) {
            for (int j = 1; j &lt; n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}</code></pre>
<ul>
<li>m = 1, n = 1일 때와 같이 시점과 종점이 같을 때의 output이 1이므로 1로 채우는 로직에서 i, j의 시작점을 0으로 설정하였다.</li>
<li>각 모서리의 합으로 경로를 갱신하기 위해 <code>dp[i][j] = dp[i - 1][j] + dp[i][j - 1]</code>로 계속해서 grid의 값을 채워나갔다.</li>
</ul>
<p>😮그런데, LeetCode의 한 천재적인 사용자가 아래 그림과 같이 grid를 시계 방향으로 45도 돌리면 정확히 파스칼의 삼각형이 나온다고 하였다!!
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/50ee07d7-0b4d-4059-92e0-8c95e39f6fe5/image.png" /></p>
<p>그렇다. 경로를 갱신하기 위한 저 수식은 사실은 파스칼의 삼각형 법칙인 $$\binom{n}{r} = \binom{n-1}{r-1} + \binom{n-1}{r}$$에서 나온 것이였다.</p>
<h1 id="⏰코드의-시간복잡도">⏰코드의 시간복잡도</h1>
<p>모든 grid를 순회해서 값으로 채우기 때문에 $$O(M*N)$$이 걸린다.</p>
<p>🚧노트에 grid를 그려가면서 top-down 접근법을 떠올려보자(점화식, dp를 사용하기 위한 자료구조, base condition, 언제 재귀 함수를 호출해야 하는가?)</p>
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>grid가 보인다고 무지성으로 bfs 또는 dfs를 쓸 것이 아니라, 반드시 자료구조 및 알고리즘을 선택하기 전에 <strong>naive하게 문제를 먼저 풀어보고 선택</strong>할 것!</p>