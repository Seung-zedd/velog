<p><a href="https://leetcode.com/problems/climbing-stairs/description/">https://leetcode.com/problems/climbing-stairs/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>당신은 계단을 오르고 있다. top까지 도달하는데 n 스텝이 걸린다. 당신은 1 걸음 또는 2걸음씩 올라갈 수 있다. top까지 도달하는데 distinct ways가 얼마나 필요할까?</p>
<p>🚧제약 조건
1 &lt;= n &lt;= 45</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>전형적인 피보나치 수열 문제다. 그런데 이전에 dp를 학습했을 때는 피보나치 수열을 0부터 시작하는 걸로 했기 때문에 이번 문제에서는 제약 조건을 고려해서 base condition을 다르게 설정해야 한다.</p>
<h1 id="💻top-down-코드-구현">💻top-down 코드 구현</h1>
<pre><code class="language-java">class Solution {
    public int climbStairs(int n) {
        HashMap&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();
        return dp(n, map);
    }

    private static int dp(int n, HashMap&lt;Integer, Integer&gt; map) {
        if (n &lt;= 2) {
            return n;
        }

        if (!map.containsKey(n)) {
            map.put(n, dp(n - 1, map) + dp(n - 2, map));
        }

        return map.get(n);
    }
}</code></pre>
<ul>
<li>문제의 예시에서 base condition을 줬기 때문에 위와 같이 세팅할 수 있다.</li>
</ul>
<hr />
<p>😵나는 특히 bottom-up에서 코드를 구현하는데 다음과 같은 난관을 겪었다.</p>
<ol>
<li>dp 배열의 사이즈를 얼마만큼 할당해줘야 하는가?</li>
<li>배열은 항상 0부터 시작하는데, 이것을 고려해서 base condition을 어떻게 설정해야 하는가?</li>
<li>n의 최빈값인 1과 45가 들어갈 때, 어떻게 처리해야 OutofIndex 에러를 해결할 수 있는가?</li>
</ol>
<h1 id="💻bottom-up-코드-구현">💻bottom-up 코드 구현</h1>
<pre><code class="language-java">class Solution {
    public int climbStairs(int n) {
        // input: n steps, output: distinct ways
        int[] dp = new int[n + 1];
        // base condition
        dp[0] = 1; // 1 step
        dp[1] = 2; // 2 step   

        for (int i = 2; i &lt; n + 1; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n - 1];

    }

}</code></pre>
<ul>
<li>위의 코드는 submit에 성공했지만, n step이 아닌 n-1 step을 리턴해서 문제 요구사항에는 맞지 않은 풀이다.</li>
</ul>
<p>따라서 문제 요구사항을 제대로 반영하려면 다음과 같이 코드를 작성해야 한다.</p>
<h1 id="💻수정된-bottom-up-코드">💻수정된 bottom-up 코드</h1>
<pre><code class="language-java">class Solution {
    public int climbStairs(int n) {
    if (n &lt;= 2) return n; // 1계단은 1가지, 2계단은 2가지

    // n &gt;= 3일 때 아래의 값들을 이용
    int[] dp = new int[n + 1];
    dp[1] = 1; // 1계단은 1가지 방법
    dp[2] = 2; // 2계단은 2가지 방법

    for (int i = 3; i &lt; n + 1; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

        return dp[n];
    }

}</code></pre>
<ul>
<li>배열의 크기를 n+1로 해준 이유는 인덱스가 0부터 시작하기 때문<ul>
<li>예를 들어 n = 1이면, dp의 인덱스는 0, 1이 되지만 <code>dp[2] = 2</code>는 크기를 초과하기 때문에 OutofIndex 에러가 발생한다.</li>
</ul>
</li>
<li>💡따라서, 배열의 인덱스가 0부터 시작한다는 것과 문제의 제약조건이 1부터 시작한다는 gap을 채우기 위해 <code>if (n &lt;= 2) return n;</code> 조건을 추가하였다.</li>
</ul>
<p>🤔그런데, 사실상 해시맵을 사용하면 문제 입력조건에 맞춰서 그대로 코드를 구현할 수 있기 때문에 훨씬 편리하다는 장점이 있다.</p>
<h1 id="💻해시맵을-사용한-bottom-up-코드">💻해시맵을 사용한 bottom-up 코드</h1>
<pre><code class="language-java">class Solution {
    public int climbStairs(int n) {
        // input: steps, output: distinct ways
        HashMap&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();

        // base condition
        map.put(1, 1);
        map.put(2, 2);   

        for (int i = 3; i &lt; n + 1; i++) {
           map.put(i, map.get(i - 1) + map.get(i - 2));
        }

        return map.get(n);

    }

}</code></pre>
<p>📝결국 top-down 방식은 배열의 인덱스를 고려하지 않아도 되기 때문에 문제의 입력조건을 그대로 사용해도 되지만, bottom-up의 <strong>table을 배열로 초기화할 경우에는 index를 고려</strong>해야 하기 때문에 더욱 복잡하다는 차이점이 있다는 것을 깨달았다.</p>