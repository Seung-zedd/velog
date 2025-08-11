<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/12945">https://school.programmers.co.kr/learn/courses/30/lessons/12945</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>피보나치 수는 $$F(0) = 0, F(1) = 1$$일 때, 1 이상의 n에 대하여 $$F(n) = F(n-1) + F(n-2)$$ 가 적용되는 수 입니다. 
이때, 2 이상의 n이 입력되었을 때, n번째 피보나치 수를 1234567으로 나눈 나머지를 리턴하는 함수, solution을 완성해 주세요.</p>
<p>🚧제약 조건
2 &lt;= n &lt;= 100,000</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>📢well-known인 피보나치 수 문제이므로, 점화식 그대로 이용하면 되기 때문에 생략합니다.
👉하지만, 코드 구현하는 과정에서 몇가지 Pain point가 있기 때문에 이 점은 기록하겠습니다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class FibonacciSolution {
    public int fib(int n) {
        int[] table = new int[1000001];

        // base case
        table[0] = 0;
        table[1] = 1;

        for (int i = 2; i &lt; n + 1; i++) {
            table[i] = table[i - 1] + table[i - 2];
        }
        return table[n] % 1234567;
     }
}</code></pre>
<ul>
<li>🚩원래는 재귀의 map을 활용한 memoization기법으로 풀려고 했으나, 무려 콜 스택을 100,000이나 호출하기 때문에 <span style="color: red;"><strong>스택오버플로우가 발생했다.</strong></span></li>
<li>for문을 이용한 bottom-up 방식으로 아래부터 채워나가는 tabulation 방식이다. 그런데 이렇게 제출하면 딱! 절반만 통과가 된다. 
🚩⛲왜냐하면 47번째 피보나치 수는 2,971,215,073이고, 이 수는 32비트 정수(ex. int) 범위를 넘어 오버플로우가 발생하기 때문이다.</li>
</ul>
<p>그래서 코딩테스트 연습 힌트 모음집에서는 다음과 같은 풀이를 제안했다.</p>
<blockquote>
<p>F(n) % m = (F(n-1) + F(n-2)) % m</p>
</blockquote>
<p>그러면 다음과 같은 코드 수정으로 성공적으로 제출을 할 수 있다.</p>
<pre><code class="language-java">public class FibonacciSolution {
    public int fib(int n) {
        int[] table = new int[1000001];

        // base case
        table[0] = 0;
        table[1] = 1;

        for (int i = 2; i &lt; n + 1; i++) {
            table[i] = (table[i - 1] + table[i - 2]) % 1234567;
        }
        return table[n] % 1234567;
     }
}</code></pre>
<h1 id="✍️정리">✍️정리</h1>
<ol>
<li>DP 문제는 먼저 top-down으로 푼다.(∵점화식을 그대로 함수로 옮긴 것이라 구현이 쉬움)
if StackoverFlow:</li>
<li>bottom-up으로 변환한다.
if 오버플로우 발생:
👉점화식에 나머지 연산('%')을 적용한다.</li>
</ol>