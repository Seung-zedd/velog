<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/12906">https://school.programmers.co.kr/learn/courses/30/lessons/12906</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>배열 arr가 주어집니다. 배열 arr의 각 원소는 숫자 0부터 9까지로 이루어져 있습니다. 이때, <strong>배열 arr에서 연속적으로 나타나는 숫자는 하나만 남기고 전부 제거</strong>하려고 합니다.(단, 제거된 후 남은 수들을 반환할 때는 <strong>배열 arr의 원소들의 순서를 유지</strong>해야 합니다.)
=&gt; 순서를 유지해야 하기 때문에 sort 알고리즘 사용 불가!</p>
<p>🚧제약 조건</p>
<ol>
<li><code>1&lt;= arr.length &lt;= 10^6</code>
=&gt; O(N)까지만 가능</li>
<li>배열 arr의 원소의 크기 : 0보다 크거나 같고 9보다 작거나 같은 정수</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>for문으로 일단 arr 순회</li>
<li>stack 자료구조를 사용하는데, <code>peek()</code>해서 같은 원소가 있으면 continue, 그렇지 않으면 <code>stack.push()</code>
💡중복되는 숫자를 필터링하기 위해 peek()를 사용 + 순서를 유지하면서 answer에 원소를 추가 = <strong>Deque 자료구조</strong>가 적절!(∵Doubly-ended-queue라 양쪽에서 삽입/삭제 가능)</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class HateDuplicateNumSolution {
    public int[] solution(int []arr) {
        Deque&lt;Integer&gt; q = new LinkedList&lt;&gt;();
        for (int i = 0; i &lt; arr.length; i++) {
            if (!q.isEmpty() &amp;&amp; (q.peekLast() == arr[i])) {
                continue;
            } else {
                q.offer(arr[i]);
            }
        }
        int[] answer = new int[q.size()];

        for (int i = 0; i &lt; answer.length; i++) {
            answer[i] = q.pollFirst();
        }

        return answer;
    }

}</code></pre>
<p>📝Valid Parenthesis 문제를 잘 풀었으면 어렵지 않게 풀 수 있는 문제였다.</p>