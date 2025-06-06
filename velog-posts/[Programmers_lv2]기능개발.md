<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42586">https://school.programmers.co.kr/learn/courses/30/lessons/42586</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>각 기능은 진도(progresses)가 100%일 때 서비스에 반영할 수 있습니다.
뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발(speeds)될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.
=&gt; 기능에 순서가 있음</p>
<p>먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때, 이 배포되는지를 return하라.
return: 각 배포마다 몇 개의 기능? =&gt; 각 arr[i]의 progresses와 speeds를 고려해야함(단, 며칠 걸리는지는 상관없고, 앞 기능 -&gt; 뒤 기능 배포 순서는 필수임)</p>
<ul>
<li>제약 조건</li>
</ul>
<ol>
<li>작업의 개수(progresses, speeds배열의 길이)는 100개 이하입니다.</li>
<li>작업 진도는 100 미만의 자연수입니다, 작업 속도는 100 이하의 자연수입니다.</li>
<li><strong>배포는 하루에 한 번</strong>만 할 수 있으며, <strong>하루의 끝</strong>에 이루어진다고 가정합니다. 예를 들어 진도율이 95%인 작업의 개발 속도가 하루에 4%라면 배포는 2일 뒤에 이루어집니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>remainedProgresses = 100 - progresses[i] 계산 필요</li>
<li>workTime = remainedProgress / speeds[i]</li>
<li>앞 기능 -&gt; 뒤 기능 배포 순서는 필수임 =&gt; Queue 자료구조</li>
<li><ol>
<li>workTimeArr를 Queue에 push(첫번째가 중요!!)</li>
</ol>
</li>
<li><ol start="2">
<li>if i번째 작업날짜 &gt; (i + 1)의 작업날짜: (i + 1)는 1번째 작업날짜에 같이 배포</li>
</ol>
</li>
<li><ol start="3">
<li>else if i번째 작업날짜 &lt; (i + 1)의 작업날짜: 따로 기능을 배포</li>
</ol>
</li>
<li>answer에 배포 개수를 삽입 후 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">import java.util.*;

public class DevelopmentSolution {
    public int[] solution(int[] progresses, int[] speeds) {
        int[] workTimeArr = new int[speeds.length];
        Queue&lt;Integer&gt; q = new LinkedList&lt;&gt;();

        for (int i = 0; i &lt; progresses.length; i++) {
            int remained = 100 - progresses[i];
            workTimeArr[i] = (int) Math.ceil((double) remained / speeds[i]); // 나누기 연산자는 소수점을 truncate하기 때문에 나누기 결과를 double로 바꾸고 소수점을 올려서 int로 계산함
        }

        // 작업 가능한 날짜를 큐에 삽입
        for (int i = 0; i &lt; workTimeArr.length; i++) {
            q.offer(workTimeArr[i]);
        }

        List&lt;Integer&gt; deployDay = new ArrayList&lt;&gt;();

        while (!q.isEmpty()) {
            int count = 0; // 큐를 꺼낼때마다 개수 세기 위함
            Integer firstWorkTime = q.poll();
            count++;
            while (!q.isEmpty() &amp;&amp; (firstWorkTime &gt;= q.peek())) {
                q.poll();
                count++;
            }
            deployDay.add(count);
        }

        int[] answer = new int[deployDay.size()];
        for (int i = 0; i &lt; answer.length; i++) {
            answer[i] = deployDay.get(i);
        }

        return answer;

    }

}</code></pre>
<ul>
<li>굳이 remainedProgresses[i]로 순회하면서 삽입하는 것이 아니라, progresses 배열 안에서 순회하면서 남은 작업 진도를 계산하였다.</li>
<li>⚠️프로그래머스 에디터에서 <code>Math.ceilDiv</code>를 지원하지 않아서 <code>(int) Math.ceil((double) remained / speeds[i]);</code>를 workTimeArr[i]에 삽입하였다.<ul>
<li>나누기 연산자는 소수점을 truncate하기 때문에 나누기 결과를 double로 바꾸고 <code>Math.ceil</code>로 소수점을 올려서 최종적으로 int를 리턴하였다.</li>
</ul>
</li>
</ul>
<hr />
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>나는 문제 요구사항에서 &quot;뒤에 있는 기능은 <strong>앞에 있는 기능이 배포될 때 함께 배포</strong>됩니다.&quot;를 보자마자 이전에 리트코드에서 푼 Daily Temperatures 문제가 바로 떠올랐다. 
왜냐하면 뒤에 있는 기능들이 앞에 있는 기능의 작업 날짜에 반응하기 때문에,
이것을 <code>while (!q.isEmpty() &amp;&amp; (firstWorkTime &gt;= q.peek()))</code>로 구현하였다.
👉<code>q.peek()</code>을 호출할 때 <span style="color: red;"><strong>큐가 비어있으면 NullPointerException을 발생</strong></span>시키기 때문에 안전하게 큐가 비어있지 않을 때라는 조건을 추가하였다.)</p>