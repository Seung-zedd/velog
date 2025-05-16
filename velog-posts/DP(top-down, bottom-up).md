<p>📢이전에 파이썬 버전으로 정리한 것이 있으므로, 자세한 내용은 <a href="https://velog.io/@csj0209/%ED%94%BC%EB%B3%B4%EB%82%98%EC%B9%98-DPtop-down-bottom-up">코딩테스트-올인원[파이썬 버전]</a>을 참고하시기 바랍니다.</p>
<p>🤩기존의 피보나치 top-down, bottom-up을 자바 버전으로 구현해보았다👇</p>
<h1 id="💻피보나치-top-down-코드-구현">💻피보나치 Top-down 코드 구현</h1>
<pre><code class="language-java">public class Fibonacci {
    public int fib(int n) {
        HashMap&lt;Integer, Integer&gt; memo = new HashMap&lt;&gt;();
        return dp(n, memo);
    }

    private static int dp(int n, HashMap&lt;Integer, Integer&gt; memo) {
        // base condition
        if (n &lt;= 1) {
            return n;
        }
        // n이 메모리에 없으면 재귀 함수를 호출
        if (!memo.containsKey(n)) {
            memo.put(n, dp(n - 1, memo) + dp(n - 2, memo));
        }
        // n이 메모리에 있으면 메모리에 있는 value를 리턴
        return memo.get(n);
    }</code></pre>
<ul>
<li>대부분 코테 문제에서는 메서드 시그니처가 문제 그대로 주어지기 때문에 <strong>dp 재귀 함수를 따로 구현</strong>해야 한다.<ul>
<li>파이썬에서는 closure 기능이 있어서 부모 메서드의 지역 변수를 그대로 사용할 수 있지만, 자바는 type-strict한 컴파일러 언어라 <strong>해시맵을 재귀 함수 파라미터로 전달</strong>하였다.<ul>
<li>해시맵으로 초기화한 이유는 n에 따라 value가 달라지기 때문</li>
</ul>
</li>
<li>내부 헬퍼 메서드는 인스턴스 변수에 의존하지 않으므로 <code>static</code>으로 선언하였다.</li>
</ul>
</li>
</ul>
<p>🔎참고로, dp를 <code>int[] dp = new int[n + 1];</code>과 같이 배열로 선언할 수도 있지만, <strong>value가 음수값</strong>으로 주어질 수도 있으므로 <strong>해시맵</strong>으로 초기화하는 것이 더 효율적이라고 한다.</p>
<h1 id="💻피보나치-bottom-up-코드-구현">💻피보나치 Bottom-Up 코드 구현</h1>
<pre><code class="language-java">public class Fibonacci {
    public int fib(int n) {
        int[] table = new int[n + 1]; // 배열은 0부터 시작하므로 1을 더해줘야함
        return bottomUp(n, table);
    }

    private static int bottomUp(int n, int[] table) {
        // base condition
        table[0] = 0;
        table[1] = 1;

        for (int i = 2; i &lt; n + 1; i++) {
            table[i] = table[i - 1] + table[i - 2];
        }

        return table[n];
    }</code></pre>
<ul>
<li>배열은 0부터 시작하기 때문에 테이블을 n+1의 크기로 할당하였다.</li>
<li>$$F(n) = F(n-1) + F(n-2) (n &gt;= 2)$$의 점화식을 따르므로, 시작은 2부터 하였다.</li>
<li>for문에서 조건식을 n + 1로 한 이유는, 결국에는 테이블을 n까지 채워야 하기 때문이다.</li>
</ul>