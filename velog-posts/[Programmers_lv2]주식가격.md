<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42584">https://school.programmers.co.kr/learn/courses/30/lessons/42584</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>param: &quot;초 단위&quot;로 기록된 &quot;주식가격&quot;이 담긴 배열
요구사항: &quot;가격이 떨어지지 않은&quot; &quot;기간&quot;은 몇 초?</p>
<p>🚧제약 조건 </p>
<ol>
<li>1 &lt;= <code>prices[i]</code> &lt;= 10,000</li>
<li>2 &lt;= <code>prices.length</code> &lt;= 100,000
-&gt; $$10^5$$이 최대이므로, $$O(NlogN)$$ 이하에서 해결하라는 의미</li>
</ol>
<p>입출력 예 설명을 보니, 1초 시점, 2초 시점, ... 은 prices의 idx + 1을 의미</p>
<p>❓그렇다면 가격이 떨어졌다는 것은 무엇을 기준?
👉미래의 시점을 현재로 가져와야됨. 즉, 판단 기준은 &quot;가격이 떨어졌느냐&quot;가 핵심임.</p>
<p>❓그럼 어떻게 미래의 시점을 기록해야 하느냐?
👉이거 보고 딱 떠오르는 문제 없음? 바로 특정 조건에 반응하는 <strong>리트코드의 Daily Temperatures</strong> 문제.
즉, 헬퍼 클래스로 인덱스와 가격을 세팅한 뒤, &quot;가격이 떨어지지 않을 때&quot; 를 특정 조건으로 설정한다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>&quot;가격이 떨어지지 않을 때&quot;를 조건에 반응하는 if문으로 설정.
prices를 for문으로 순회하는 것을 현재 시점으로 설정.
순회할 때마다 stack에 푸시하고, 그 가격을 기록하는 용도로 사용.</p>
<p>⚠️가격이 떨어지지 않았다 == 원소가 stable하게 증가한다 == 조건을 <code>stack.peek()</code>의 가격이 현재 가격보다 작거나 같으면 idx++시킨다.</p>
<p>그런데, Daily Temperatures와 다른 점은, <strong>&quot;기간&quot;</strong>이기 때문에, 배열의 끝까지 순회해서 해당 주식의 가격을 추적해야 된다. 즉, 스택에 남아있을 때도 조건으로 생각해야 한다는 것이다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class StockSolution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];

        // 스택 초기화
        Stack&lt;Stock&gt; s = new Stack&lt;&gt;();

        for (int i = 0; i &lt; prices.length; i++) {
            // 과거 가격 &gt; 현재 가격이면 stack.pop()
            while (!s.isEmpty() &amp;&amp; (s.peek().stockPrice &gt; prices[i])) {
                Stock stock = s.pop();
                int idx = stock.idx;
                answer[idx] = i - idx; // 1초간 가격이 떨어지지 않음
            }

            s.push(new Stock(prices[i], i));

        }

        Iterator&lt;Stock&gt; iter = s.iterator();
        while (iter.hasNext()) {
            Stock next = iter.next();
            int idx = next.idx;
            answer[idx] = (prices.length - 1) - idx;
        }

        return answer;
    }

    private static class Stock {
        int stockPrice;
        int idx;

        public Stock(int stockPrice, int idx) {
            this.stockPrice = stockPrice;
            this.idx = idx;
        }
    }
}</code></pre>
<ul>
<li>역시 Daily Temperatures와 마찬가지로, <strong>반응하는 조건을 계속해서 추적</strong>해야 하기 때문에 while문 내에서 <code>pop()</code>을 먼저 구현하였고, 그렇지 않으면 <code>push()</code>를 하게 하였다.</li>
<li>기간은 그냥 배열의 길이에서 현재 주식의 인덱스를 빼기만 하면 된다.</li>
</ul>
<h1 id="🤖개선된-코드-제안">🤖개선된 코드 제안</h1>
<pre><code class="language-java">public class StockSolution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];

        // 스택 초기화
        Stack&lt;Stock&gt; s = new Stack&lt;&gt;();

        for (int i = 0; i &lt; prices.length; i++) {
            // 과거 가격 &gt; 현재 가격이면 stack.pop()
            while (!s.isEmpty() &amp;&amp; (s.peek().stockPrice &gt; prices[i])) {
                Stock stock = s.pop();
                int idx = stock.idx;
                answer[idx] = i - idx; // 1초간 가격이 떨어지지 않음
            }

            s.push(new Stock(prices[i], i));

        }

        // 스택이 빌 때까지 pop하며 남은 요소들을 처리
        while (!s.isEmpty()) {
            Stock stock = s.pop();
            int idx = stock.idx;
            answer[idx] = (prices.length - 1) - idx;
        }

        return answer;
    }

    private static class Stock {
        int stockPrice;
        int idx;

        public Stock(int stockPrice, int idx) {
            this.stockPrice = stockPrice;
            this.idx = idx;
        }
    }
}</code></pre>
<h2 id="🔎스택의-pop-연산과-iterator의-차이점">🔎스택의 pop() 연산과 Iterator의 차이점</h2>
<p>공통점: Stack 자료구조에서 <code>pop()</code> 연산과 Iterator의 <code>next()</code> 연산은 모두 $$O(1)$$ 입니다.</p>
<ol>
<li>Iterator: <code>iter.hasNext()</code>와 <code>iter.next()</code>를 호출할 때마다 <strong>내부적으로 상태를 확인</strong>하는 과정이 있고, Iterator 객체 자체가 새로 생성되는 <span style="color: red;"><strong>오버헤드</strong></span>가 있기 때문에 성능면에서 비효율적이다.</li>
</ol>
<ul>
<li>다만, <code>pop()</code>과의 연산 속도 차이는 미미하다고 한다.</li>
</ul>
<ol start="2">
<li>while/pop: 스택의 <code>pop()</code> <strong>메서드를 직접 호출</strong>하므로 중간 단계 없이 더 직접적입니다.</li>
</ol>
<ul>
<li>심지어, 코드 면에서도 더 간결하고 직관적이다.</li>
</ul>