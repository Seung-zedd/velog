<p><a href="https://leetcode.com/problems/daily-temperatures/description/">https://leetcode.com/problems/daily-temperatures/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>일상 온도를 나타내는 int형 temperatures 배열이 주어졌을 때, answer[i]는 i번째 날에서 더 따뜻해지기를 기다려야 하는 날을 의미한다. 이때 answer 배열을 리턴해라.
만약 이것이 가능한 미래의 날이 없다면, 0을 리턴해라.</p>
<p>🚧제약 조건
<code>1 &lt;= temperatures.length &lt;= 10^5</code>
-&gt; O(N^2)는 불가! 이중 for문으로 brute force 불가!</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>🚩for문을 순회해야 하는데, 문제는 <span style="color: red;"><strong>현재의 index에서는 과거의 온도와 날짜를 기억할 수 없다는 것이다.</strong></span>
👉따라서 과거의 온도, 날짜를 저장해서 기억할 수 있는 메모리가 필요해서 스택을 도입하였는데, Output을 보면 <strong>현재보다 높은 온도가 몇 일 뒤에 나타나는지를 answer[i]로 저장</strong>해야 하기 때문에 온도를 비교하기 위한 tempStack과 날짜를 리턴하기 위한 dayStack을 별도로 두었다.</p>
<ol>
<li>일단 먼저 stack.push()를 한다.</li>
<li>tempStack의 top 원소보다 현재 온도가 높으면 stack.pop()을 하고, answer[i] = 미래 날짜 - 현재 날짜로 i번째에 day를 저장한다.</li>
<li>answer를 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class DailyTemSolution {
    public int[] dailyTemperatures(int[] temperatures) {
        Stack&lt;Integer&gt; tempStack = new Stack&lt;&gt;();
        Stack&lt;Integer&gt; dayStack = new Stack&lt;&gt;();
        int[] answer = new int[temperatures.length];

        for (int i = 0; i &lt; temperatures.length; i++) {

            while (!tempStack.isEmpty() &amp;&amp; (tempStack.peek() &lt; temperatures[i])) {
                tempStack.pop();
                Integer prev_day = dayStack.pop();
                answer[prev_day] = i - prev_day; // 현재 날짜 - 과거 날짜
            }

            tempStack.push(temperatures[i]);
            dayStack.push(i);
        }
        return answer;

    }
}</code></pre>
<h2 id="☝코드-설명">☝코드 설명</h2>
<p>🤔이전에 풀었던 <a href="https://velog.io/@csj0209/LeetcodeValid-Parentheses">Valid Parentheses</a>와 어떤 차이점이 있나?</p>
<p>괄호 문제는 원소가 열린 괄호면 <code>stack.push()</code>하라는 조건이 있는 반면, 이번 문제의 비교 대상은 <strong>오직 스택에 있는 과거 온도와 for문으로 순회하는 현재 온도</strong>밖에 없기 때문에 <code>stack.push()</code>를 맨 아래로 뒀다.</p>
<p>💡만약에 for문으로 순회하는 현재와 <code>temp[i+x]</code>같이 미래에 있는 온도와 비교를 하면 <span style="color: red;"><strong>미래의 어느 인덱스에서 더 따뜻해지는지 모르기 때문에 ArrayList는 사용할 수 x</strong></span></p>
<p>또한, stack이 비어있는 상태에서 peek()을 할 수는 없어서 스택이 비어있지 않을 때만으로 조건을 추가했다.</p>
<ul>
<li>테케 <code>temperatures = [73,74,75,71,69,72,76,73]</code>을 보면 71에서 while문을 충족하지 못하기 때문에 계속해서 push를 한다. 그러다가 72에서 검사를 하면 현재의 stack top원소는 69이기 때문에 충족해서 pop을 하고, 현재의 인덱스에서 이전에 저장했던 인덱스를 뺀 값을 answer[i]에 저장한다.
👉이것은 top의 원소를 기준으로 answer[i]에 저장되기 때문에 인덱스의 위치에 의존을 하지 않는다.</li>
</ul>
<p>🤔그런데 불현듯 이런 생각이 들었다. &quot;stack의 top 원소를 기준으로 answer의 배열을 갱신하니까, Queue의 FIFO로도 가능하지 않을까?&quot;
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/8c7d163c-0bfd-4ffb-95ef-6f9ec51fcec2/image.jpg" /></p>
<p>그래서 Deque로 구현해서 submit을 했는데 성공했다.</p>
<h1 id="💻deque로-코드-구현">💻Deque로 코드 구현</h1>
<pre><code class="language-java">public class DailyTempQueueSolution {
    public int[] dailyTemperatures(int[] temperatures) {
        Deque&lt;Integer&gt; tempQueue = new LinkedList&lt;&gt;();
        Deque&lt;Integer&gt; dayQueue = new LinkedList&lt;&gt;();
        int[] answer = new int[temperatures.length];

        for (int i = 0; i &lt; temperatures.length; i++) {

            while (!tempQueue.isEmpty() &amp;&amp; tempQueue.peek() &lt; temperatures[i]) {
                tempQueue.remove();
                Integer prev_day = dayQueue.remove();
                answer[prev_day] = i - prev_day;
            }

            tempQueue.addFirst(temperatures[i]);
            dayQueue.addFirst(i);
        }
        return answer;

    }
}</code></pre>
<p>💡참고로, Deque(Queue)는 Stack과 연산이 완전히 정반대이기 때문에, peek()은 큐의 head의 value를 반환하고, remove() 역시 프론트부터 제거한다.
🤦‍♂️큐의 연산을 착각했다! enqueue는 무조건 rear에서 삽입을 하고, dequeue는 front에서 제거하는 구조인데, 나도 모르게 스택의 동작 과정과 비슷하게 봐서 add()를 addFirst()로 해서 heuristic하게 때려맞췄다.</p>
<h1 id="⏰stack과-deque의-런타임-비교">⏰Stack과 Deque의 런타임 비교</h1>
<p>💡참고로, 시간복잡도는 하나의 for문으로만 구현했기 때문에 O(N)으로 동일하다.</p>
<ol>
<li><p>Stack으로 구현했을 때
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/8896fbd2-52bd-468d-83bc-7c86181e70eb/image.png" /></p>
</li>
<li><p>Deque로 구현했을 때
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/c4394d04-c90a-492c-8fd4-d003eb12e78d/image.png" /></p>
</li>
</ol>
<p>📝Stack은 Vector 기반으로 동작하기 때문에 동기화 처리를 해야되서 오버헤드가 있지만, Deque는 Doubly LinkedList 기반으로 동작해서 삽입/삭제가 O(1)만 걸리기 때문에 구현체 차이로 성능 차이가 발생한 것이다.</p>