<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42587">https://school.programmers.co.kr/learn/courses/30/lessons/42587</a>
🤗2일이나 걸려서 스스로의 힘으로 문제 푸는데 성공! 역시 코테 공부는 <strong>&quot;설계 양치기&quot;</strong>를 통해 비슷한 상황에서의 문제 해결 능력을 길러야 된다는 것 다시 한번 체감했다.</p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<blockquote>
<p>규칙</p>
</blockquote>
<ol>
<li>실행 대기 큐(Queue)에서 대기중인 프로세스 하나를 꺼냅니다.</li>
<li>큐에 대기중인 프로세스 중 우선순위가 더 높은 프로세스가 있다면 방금 꺼낸 프로세스를 다시 큐에 넣습니다.</li>
<li>만약 그런 프로세스가 없다면 방금 꺼낸 프로세스를 실행합니다.<ul>
<li>3.1 한 번 실행한 프로세스는 다시 큐에 넣지 않고 그대로 종료됩니다.</li>
</ul>
</li>
</ol>
<p>🔎자, 아래의 제약조건 및 입출력 예시를 살펴보기 전에, 위의 규칙을 한 문장씩 해석하거나 수도 코드로 작성해보자.</p>
<ol>
<li><p>실행 대기 큐(Queue)에서 대기중인 프로세스 하나를 꺼냅니다.
👉대기 큐(FIFO 구조)에서 대기중인 프로세스 하나를 <strong>Deque(프론트에서 삭제) 작업</strong>을 <code>q.poll()</code>로 진행한다.</p>
</li>
<li><p>큐에 대기중인 프로세스 중 <strong>우선순위가 더 높은 프로세스가 있다면</strong> 방금 꺼낸 프로세스를 다시 큐에 넣습니다.
👉if (대기 큐에 &quot;하나라도 우선순위가 더 높은 프로세스 존재): <code>q.poll()</code> 한 것을 <strong>Enqueue(rear로 삽입) 작업을</strong> <code>q.offer()</code>로 진행한다.</p>
</li>
<li><p>만약 그런 프로세스가 없다면 <strong>방금 꺼낸 프로세스를 실행</strong>합니다.
3.1 한 번 실행한 프로세스는 다시 큐에 넣지 않고 그대로 종료됩니다.
👉<code>else {}</code> // 일단 &quot;실행&quot;을 무엇으로 해야 할 지 몰라서 보류 </p>
</li>
</ol>
<p>🔢param: 프로세스의 중요도가 순서대로 담긴 배열 <code>priorities</code>, 몇 번째로 실행되는지 알고싶은 프로세스의 위치를 알려주는 <code>location</code>
  👉얘네가 현재 실행 대기 큐에 담겨있다고 함.</p>
<p>📌문제 요구사항: 특정 프로세스가 몇 번째로 실행?</p>
<p>  👉위의 요구사항을 봤을 때 당연히 아래와 같은 의문들이 들어야 한다:</p>
<ol>
<li>특정 프로세스를 뭘로 가리켜야 하나?</li>
<li>몇 번째를 뭘로 나타내야됨?</li>
<li>실행은 또 무슨 의미야?</li>
</ol>
<p>뿐만 아니라, 우선순위가 더 높은 것을 뭘로 표현하는지에 대한 의문도 들어야 한다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li><code>Queue&lt;Process&gt; q = new LinkedList&lt;&gt;();</code>로 큐를 초기화한다.</li>
</ol>
<p>...아래의 수도코드는 프로세스 문제를 바로 코드 구현하지 않고 naive하게 의식의 흐름대로 작성한 과정이다.</p>
<pre><code class="language-java">while (!q.isEmpty() {
    Prcess curProc = q.poll();

    // 큐를 iterate
    Iterator&lt;Process&gt; iter = q.iterator();
     while(iter.hasNext()) {
         Process nextProc = iter.next();
        int nextVal = nextProc.priority;

        if (curVal &lt; nextVal) {
            q.offer(new Process(curVal, curIdx));
        } else(그러니까 curVal &gt;= nextVal일 때) {
            curProc.execute(); -&gt; curProc.terminate();
            // GC에 의해 참고되지 않은 객체는 알아서 수거해감
        }
            // 종료 조건으로 격상
            if (curProc.idx == location) {
                return count;
            }</code></pre>
<p>위의 규칙을 따랐을 때, 문제는 아래와 같이 대기 큐에 있는 원소들을 순회할 때 Conflict가 일어난다는 것이다.</p>
<pre><code class="language-python">case 1)
curProc = (B, 2) # q = [(C, 3), (D, 2), (A, 2)]
    -&gt; 대기 큐에 있는 모든 프로세스들의 우선순위보다 &quot;낮기&quot; 때문에 curProc을 다시 대기 큐에 삽입

case 2)
curProc = (C, 3) # q = [(D, 2), (A, 2), (B, 1)]
    -&gt; 대기 큐에 있는 모든 프로세스들의 우선순위보다 &quot;높기&quot; 때문에 curProc을 실행시켜서 대기 큐에 삽입을 하지 x</code></pre>
<br />

<p>🔨위의 Conflict를 해결하기 위해서는 결국 <strong>실행횟수인 <code>int execCount</code> 변수를 도입</strong>해야 한다는 생각이 났다. 
즉, case 1에 해당하면 큐에서 꺼낸 프로세스를 다시 큐에 삽입하는 동작만 하고, <strong>case 2</strong>에 해당하면 큐에서 꺼낸 프로세스를 &quot;실행&quot;시켜야 하기 때문에 <strong>실행횟수를 증가</strong>시키는 방향으로 로직을 구현하면 된다는 것이다.</p>
<hr />
<p>그런데, 나는 이런 생각도 해봤다. 굳이 <code>Math.max()</code>로 우선순위 최댓값을 찾는 것이 아니라, <code>boolean isCurProcMax</code>라는 변수를 도입해서 <code>while (!q.isEmpty())</code> 루프 안에서 불리언 값을 규칙에 맞게 갱신한다.</p>
<p>😵문제는 어떻게 로직으로 녹여낼 지 몰라서 큐 안에서 그냥 간단하게 <code>Math.max()</code> 메서드를 써서 최댓값을 먼저 구하고 반복자를 통해 curProc와 큐에 있는 원소들과 비교하였다.</p>
<p>🗝️핵심 키워드: 큐, <strong>반복자</strong>를 통한 큐 순회, 큐 안에서 <code>Math.max()</code>를 통해 빠르게 <strong>우선순위 최대값인 프로세스 탐색</strong></p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class ProcessSolution {
    public int solution(int[] priorities, int location) {
        // 큐로 초기화
        Queue&lt;Process&gt; q = new LinkedList&lt;&gt;();
        int execCount = 0; // 프로세스 실행 횟수

        // 엣지 케이스(우선순위 배열 길이가 1일 때는 count + 1을 리턴)
        if (priorities.length == 1) {
            return execCount + 1;
        }

        // 큐에 우선순위 원소 삽입(우선순위, 인덱스)
        for (int i = 0; i &lt; priorities.length; i++) {
            q.offer(new Process(priorities[i], i));
        }

        // 규칙 설정
        while (!q.isEmpty()) {
            // 1. 실행 대기 큐(Queue)에서 대기중인 프로세스 하나를 꺼냅니다.
            Process curProc = q.poll();
            int curVal = curProc.priority;
            int curIdx = curProc.idx;

            // 반복자를 통해 우선순위 최댓값을 큐의 원소를 제거할 때마다 갱신
            Iterator&lt;Process&gt; iterMax = q.iterator();
            int maxVal = 0;

            while (iterMax.hasNext()) {
                Process iterCurProc = iterMax.next();
                int iterCurVal = iterCurProc.priority;

                maxVal = Math.max(maxVal, iterCurVal); // 최댓값 업데이트
            }

            // 2. 큐에 대기중인 프로세스 중 &quot;우선순위가 더 높은 프로세스&quot;가 있다면 방금 꺼낸 프로세스를 다시 큐에 넣습니다.
            if (curVal &lt; maxVal) {
                q.offer(new Process(curVal, curIdx));
            } else {
                //  3. 만약 그런 프로세스가 없다면 방금 꺼낸 프로세스를 실행합니다.
                execCount++;
                // 해당 프로세스를 실행시키고 나서
                // 요구사항: 해당 프로세스가 몇 번째로 실행되는지 return을 수행
                // 현재 프로세스의 인덱스가 location과 같다면 실행횟수를 리턴
                //! 이미 큐 내에서 현재 인덱스와 파라미터의 location을 비교하고 있기 때문에 targetProc은 불필요
                if (curIdx == location) {
                    return execCount;
                }
            }
        }

        return 0;
    }

    private static class Process {
        int priority;
        int idx;

        public Process(int priority, int idx) {
            this.priority = priority;
            this.idx = idx;
        }
    }
}</code></pre>
<h2 id="🚩스스로의-힘으로-구현한-코드의-문제점">🚩스스로의 힘으로 구현한 코드의 문제점</h2>
<ol>
<li><span style="color: red;"><strong>while 루프 안에서 매번 Iterator를 사용해 큐 전체를 스캔</strong></span>하며 최댓값(maxVal)을 찾고 있습니다. 이는 <span style="color: red;"><strong>$$O(N²)$$</strong> </span>의 시간 복잡도를 유발하여 비효율적입니다.
👉왜냐하면, 큐에서 삭제하는 행위가 전체 데이터인 O(N)이고, 내부 루프에서도 마찬가지로 동일한 큐 크기에서 반복자로 순회하기 때문에 $$O(N * N) = O(N^2)$$이 걸리는 것이다.</li>
</ol>
<h1 id="🤖코드-개선-제안">🤖코드 개선 제안</h1>
<p>📢제가 제안해 드린 <em>boolean 플래그를 사용하는 방식</em>(위의 코드 설계에서 큐 내부에서 반복자를 활용해 <code>Math.max()</code>를 사용하는 로직의 대안책)이나, 사용자님이 구현한 코드 둘 다 시간 복잡도는 $$O(N²)$$으로 동일하기 때문에, 구체적인 코드 개선 구현은 생략하였습니다.</p>
<hr />
<p>🔨결국, 우선순위 큐를 통해 $$O(NlogN)$$의 시간복잡도로 최적화를 달성할 수 있다.</p>
<h2 id="💻우선순위-큐를-활용한-코드-구현">💻우선순위 큐를 활용한 코드 구현</h2>
<pre><code class="language-java">public class ProcessPriorityQueueSolution {
    public int solution(int[] priorities, int location) {
        // 1. 우선순위 큐에는 우선순위 값만 내림차순으로 저장
        //? 가장 높은 값을 빠르게 peek 하기 위해
        PriorityQueue&lt;Integer&gt; pq = new PriorityQueue&lt;&gt;(Comparator.reverseOrder());

        for (int priority : priorities) {
            pq.offer(priority);
        }

        // 2. 일반 큐에는 원래 순서를 지키기 위해 프로세스(우선순위, 인덱스)를 삽입
        Queue&lt;Process&gt; q = new LinkedList&lt;&gt;();

        for (int i = 0; i &lt; priorities.length; i++) {
            q.offer(new Process(priorities[i], i));
        }

        int execCount = 0; // 실행횟수

        // 규칙 적용 시작
        while (!q.isEmpty()) {
            // (규칙1) 대기 큐(Queue)에서 대기중인 프로세스 하나를 꺼냅니다.
            Process curProc = q.poll();

            // (규칙3)현재 남아있는 가장 높은 우선순위(pq.peek())와 비교
            if (curProc.priority == pq.peek()) {
                execCount++;
                pq.poll(); // 가장 높은 우선순위가 실행되었으므로 우선순위 큐에서도 제거
                if (curProc.idx == location) {
                    return execCount;
                }
            } else {
                // (규칙2)대기 큐에 더 높은 우선순위가 존재한다면 대기 큐에 다시 삽입
                q.offer(curProc);
            }
        }

        return execCount;
    }

    private static class Process {
        int priority;
        int idx;

        public Process(int priority, int idx) {
            this.priority = priority;
            this.idx = idx;
        }
    }
}</code></pre>
<ul>
<li>우선순위 큐: 프로세스 <strong>우선순위</strong>를 max heap(내림차순 정렬)으로 정렬하기 위해 만듦 
큐: <strong>프로세스(우선순위,인덱스)를 규칙에 적용</strong>하기 위해 만듦<ul>
<li>즉, while 루프 내에서 반복자를 사용해서 우선순위 최댓값을 계속해서 구하는 대신, 
1) <code>pq.peek()</code>으로 최댓값을 확인하고 실행 횟수를 증가시켜서 <code>exec()</code>을, 
2) <code>pq.poll()</code>로 <code>terminate()</code>를 대신하였다.</li>
</ul>
</li>
</ul>
<br />

<p>⏰PriorityQueue를 생성하기 위해(<em>heapify</em>) $$O(N)$$이 걸리고, while 루프 안에서 poll 또는 offer 연산(<em>이 연산이 sift-up 또는 sift-down에 해당됨</em>)이 최대 N번 반복하기 때문에 $$N * O(log N) = O(NlogN)$$이 걸린다. 
따라서 이 둘을 합치면 $$O(N + NlogN) ≒ O(NlogN)$$ 이 걸린다. (단순하게 큐로 구현한 $$O(N^2)$$보다 훨씬 낮음)</p>
<hr />
<p>📚참고자료
🔗 iterator 초기화: <a href="https://www.clien.net/service/board/kin/3590032">https://www.clien.net/service/board/kin/3590032</a></p>