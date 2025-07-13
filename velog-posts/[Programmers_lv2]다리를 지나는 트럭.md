<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42583">https://school.programmers.co.kr/learn/courses/30/lessons/42583</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>📌요구사항: <strong>트럭 여러 대</strong>가 강을 가로지르는 <strong>일차선 다리</strong>를 <strong>정해진 순</strong>으로 건너려 합니다.</p>
<ol>
<li><p>트럭 여러 대: 트럭 여러 대를 어떻게 표현?
👉트럭은 최대 <code>bridge_length</code>까지 올라갈 수 있음 &amp;&amp; 다리는 <code>weight</code> 이하까지의 무게를 견딜 수 있음(단, 다리에 완전히 오르지 않은 트럭의 무게는 무시한다.)
👉다리의 길이와 무게가 중요
😵Pain point: <code>bridge_length</code>가 트럭이 올라가기 위한 조건으로만 이해를 했지, <span style="color: red;"><strong>트럭이 <code>bridge_length</code>를 건너는데 시간이 걸린다는 것을 파악하지 못함.</strong></span></p>
</li>
<li><p>일차선 다리: 한번 다리에 올라서면 후진할 수 없음? ㅇㅇ</p>
</li>
<li><p>정해진 순으로 건넌다: 정해진 순으로 건넌다는게 뭔말?
👉 &quot;순서대로&quot; 최단 시간 안에 다리를 건넌다 == <strong>큐 자료구조</strong>를 사용한다</p>
</li>
</ol>
<p>모든 트럭이 다리를 건너려면 &quot;최소 몇 초&quot;가 걸리는지 알아내야 합니다.
  👉 if 모든 트럭 pass: need min sec;
  👉 🤔왜 모든 트럭은 최소 몇 초가 필요한걸까?
    👉 💰이것이 추후에 컴퓨터 네트워크의 <strong>L-bit 패킷이 다른 노드로 전송되는데 걸리는 시간을 파악</strong>해야 하기 때문에 최소 몇 초가 필요하다는 것을 깨달음.</p>
<p>다리: 예시 케이스를 토대로 그림을 그린 결과, 모든 트럭은 다리를 건너는데 &quot;2초&quot;가 걸린다.
👉이것도 마찬가지로 Pain point에 해당😵(즉, 모든 트럭이 사실은 다리를 건너는데 <span style="color: red;"><strong>동일한 시간</strong></span>이 걸리는게 아니라, <strong>다리의 길이에 따라 트럭이 건너는 시간이 달라짐</strong>)</p>
<h2 id="🚧제약-조건">🚧제약 조건</h2>
<ol>
<li><p><code>bridge_length</code>는 1 이상 10,000 이하입니다.</p>
</li>
<li><p><code>weight</code>는 1 이상 10,000 이하입니다.
👉 위의 둘은 데이터 크기에 영향 x</p>
</li>
<li><p><code>truck_weights</code>의 길이는 1 이상 10,000 이하입니다.
👉 최대 $$10^4$$ 인데, 어짜피 큐 사용할꺼라서 $$O(N)$$ 걸림</p>
</li>
<li><p>모든 트럭의 무게는 1 이상 <code>weight</code> 이하입니다.
👉 최소 1 이상이니까 <strong>엣지 케이스</strong> 생각하라는 의미</p>
</li>
</ol>
<p>&lt;엣지 케이스&gt;
다리 길이 = 1, 다리 무게 = 1, 트럭 별 무게 = 1일 경우:
  -&gt; 최소 2초</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>문제에서 설명해주는 예시를 아래와 같이 그림으로 표현해봤다.👇
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/320cc7ac-d5d4-400c-bc79-c082b4a986d7/image.jpg" /></p>
<ul>
<li><p>나는 요구사항을 제대로 이해하지 못한 채, 대표적인 케이스만 보고 &quot;모든 트럭은 다리를 건너는데 동일한 시간이 걸린다&quot;라는 잘못된 해석을 하는 우를 범했다.</p>
</li>
<li><p>아무튼, 위의 그림을 보면, 트럭이 순서대로 건너야하기 때문에 트럭 배열을 대기 큐로 초기화시켰고, 트럭이 큐를 빠져나가서 다리 위로 올라가는 시간을 모두 1초 걸리게 하였다.</p>
<ul>
<li>마찬가지로, 다리를 지난 트럭도 1초를 걸리게 하였다.</li>
</ul>
<hr />
<p>알고리즘 설계는 다리를 건너기 전, 다리를 건너는 중, 다리를 건넌 후와 같이 3단계로 나뉜다. 따라서 구체적인 로직 과정은 다음과 같다:</p>
</li>
</ul>
<ol>
<li><p>다리를 건너기 전에(<code>q.poll()</code>을 하기 전에) 
트럭의 무게(<code>q.peek()</code>으로 맨 앞의 트럭의 무게를 측정) &lt;= <code>weight</code>이면 다리를 건너게 한다.
👉다리를 건너는 트럭은 <code>bridth_length</code> 이하까지 가능하기 때문에 총 트럭의 무게를 합산하기 위해 <code>sumTruckWeight</code>이라는 변수를 추가적으로 도입한다.</p>
</li>
<li><p>다리를 건너는 중에는 Deque로 트럭을 건너게 한다. 
👉다리를 건너는 트럭을 마킹하기 위해 Stack 자료구조를 사용하는데, 트럭을 추가하고 트럭의 크기가 다리 길이 이하가 되는지 검사를 한다.</p>
</li>
</ol>
<ol start="3">
<li>다리를 건넜으면, <code>sumTruckWeight</code>에서 다리를 건넌 트럭의 무게를 뺀다.
👉역시 시간을 1초 증가시킨다.
👉스택 자료구조를 사용한 이유는 if 중괄호에서 Deque를 해서 curTruck 변수를 사용 못하기 때문에 다른 자료구조를 도입하였다.</li>
</ol>
<p>위의 코드 설계를 아래와 같이 코드 구현으로 녹여봤다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">
import java.util.*;
import java.util.stream.*;

public class TruckSolution {
    public int solution(int bridge_length, int weight, int[] truck_weights) {
        // 큐로 트럭 초기화
        Queue&lt;Integer&gt; q = new LinkedList&lt;&gt;(Arrays.stream(truck_weights).boxed().collect(Collectors.toList()));
        // 다리를 지나고 있는 트럭 마킹용
        Stack&lt;Integer&gt; truckStack = new Stack&lt;&gt;();
        int second = 0; // 최소 몇 초가 걸리는지 파악하기 위함
        int sumTruckWeight = 0; // 트럭의 무게는 최소 1

        // 엣지 케이스 먼저 고려
        if (truck_weights.length == 1 &amp;&amp; bridge_length == 1 &amp;&amp; weight == 1) {
            return 2;
        }


        // 다리를 건너기 전의 트럭들 == 다리를 건너는 시점
        while (!q.isEmpty()) {
            // 시간을 계속 흐르게 함
            second++;
            // 다리를 건너는 시점 == Deque
            // 검문소: 다리 무게 및 길이 체크
            Integer waitingTruck = q.peek();
            sumTruckWeight += waitingTruck;
            if (sumTruckWeight &lt;= weight &amp;&amp; truckStack.size() &lt;= bridge_length) {
                Integer curTruck = q.poll();
                sumTruckWeight -= waitingTruck;
                sumTruckWeight += curTruck; // 트럭 무게 증가
                truckStack.add(curTruck);

            }

            // 다리위에 건너는 트럭이 있으면
            if (!truckStack.empty()) {
                // 다리를 지난 시점
                Integer crossTruck = truckStack.pop();
                sumTruckWeight -= crossTruck; // 지난 트럭의 무게를 빼줘야함
                second++;

            }
        }

        return second;
    }
}</code></pre>
<h2 id="🚩위-코드의-pain-point">🚩위 코드의 Pain Point</h2>
<p>다음과 같은 3가지를 파악하지 못했기 때문에 다른 테케를 통과하지 못하였다:</p>
<ol>
<li><code>bridge_length == Propagation delay</code>: 트럭 한 대가 <strong>다리의 한 쪽 끝에서 다른 쪽 끝까지 가는데 걸리는 시간</strong></li>
<li><code>1초(트럭 한 대가 다리에 올라가는 행위) == Transmission delay</code>: 트럭 한 대를 <strong>다리 위로 완전히 밀어넣는데</strong> 걸리는 시간</li>
<li>while문 안에서의 second++: 시뮬레이션의 시간 흐름</li>
<li>대기 큐(트럭이 순서대로 다리를 건너려면~) / 다리 큐(다리 무게와 비교하기 위함)
💰트럭을 커스텀 클래스로 만들고, <code>entryTime</code>이라는 추가적인 필드를 도입해야 함.</li>
</ol>
<hr />
<p>위의 4가지를 고려해보고 코드를 다시 짜보려고 했지만, 결국 어디서 실패하는지 몰라서 AI의 도움을 받았는데 <strong>'정산 후 행동(Settle then Act)'</strong>라는 숨겨진 규칙 때문에 결국에는 이 문제를 푸는데 실패했던 것이다.</p>
<ul>
<li>나의 언어로 표현하자면, <strong>(동일 시간 내에)선 제거, 후 삽입</strong>이 숨겨진 규칙이였음.</li>
</ul>
<p>핵심은 '같은 시간(1초)에 일어나는 일'의 처리 순서
시뮬레이션을 <strong>'매초 정각에 상태가 업데이트된다'</strong>고 생각하면 이해하기 쉽습니다.</p>
<p>t초가 되는 순간:</p>
<ol>
<li><p>선 제거: 먼저, 다리 위에서 <code>bridge_length</code>만큼의 시간을 모두 채워 t초에 도착한 트럭을 다리에서 내립니다. (다리 위 무게와 공간에 여유가 생김)</p>
</li>
<li><p>후 삽입: 그 다음, 방금 생긴 여유 공간(bridgeQ를 말함)을 고려하여 대기 중인 새 트럭이 t초에 다리로 진입할 수 있는지 결정합니다.</p>
</li>
</ol>
<p>📢이 '정산 후 행동(Settle then Act)' 순서를 지키지 않고 삽입을 먼저 고려하면, 실제로는 진입할 수 있는 트럭이 막혀버리는 논리적 오류가 발생합니다.</p>
<ol start="3">
<li>최종 설계: 두 개의 큐와 헬퍼 클래스
이 로직을 효율적으로 구현하기 위해 다음과 같이 자료구조를 설계합니다.</li>
</ol>
<p>1) 대기 큐 (<code>Queue&lt;Truck&gt; waitingQ</code>)</p>
<ul>
<li>역할: 아직 다리에 올라가지 못한 트럭들을 원래 <strong>순서대로 보관</strong>합니다.</li>
</ul>
<p>2) 다리 큐 (<code>Queue&lt;Truck&gt; bridgeQ</code>)</p>
<ul>
<li>역할: 현재 다리 위를 건너는 트럭들만 관리합니다. 이 큐의 상태를 통해 다리 위의 총 무게와, 맨 앞 트럭의 도착 여부를 판단합니다.</li>
</ul>
<h4 id="🤔대기-큐-이외에-별도로-다리-큐를-생성해야-하는-이유">🤔대기 큐 이외에 별도로 다리 큐를 생성해야 하는 이유?</h4>
<p>👉문제 요구사항에서 &quot;일차선 다리&quot;가 뒤늦게 진입한 트럭이 먼저 진입한 트럭에 대해 <strong>추월이 불가능하고 순서가 보장</strong>되는 것을 의미하기 때문이다.</p>
<p>3) 헬퍼 클래스 (Truck)</p>
<ul>
<li>역할: 트럭의 <code>weight</code>와 다리에 진입한 시간(<code>entryTime</code>)을 함께 묶어서 관리합니다. entryTime 정보가 있어야만 <code>time(현재시간) - entryTime == bridge_length</code> 공식을 통해 <strong>트럭이 다리를 빠져나갈 시간을 계산</strong>할 수 있습니다.</li>
</ul>
<h1 id="💻pain-point를-반영한-코드-구현">💻Pain Point를 반영한 코드 구현</h1>
<pre><code class="language-java">import java.util.*;

public class TruckSolution {
    public int solution(int bridge_length, int weight, int[] truck_weights) {
        // 대기 큐 초기화
        Queue&lt;Truck&gt; waitingQ = new LinkedList&lt;&gt;();
        // 트럭 무게를 대기 큐에 삽입
        for (int w : truck_weights) {
            waitingQ.offer(new Truck(w, 0)); // 아직 다리에 진입하지 않았으므로 0으로 초기화
        }

        // 다리 큐 초기화
        // 다리 길이 및 무게 비교하기 위함
        Queue&lt;Truck&gt; bridgeQ = new LinkedList&lt;&gt;();
        int time = 0; // 시뮬레이션을 위한 전체 시간
        int totalWeightOnBridge = 0; // 다리 위의 트럭 무게 합산


        // 대기 큐와 다리 큐가 비어있지 않을 때까지 시뮬레이션 실행
        while (!waitingQ.isEmpty() || !bridgeQ.isEmpty()) {
            time++;
            // 정산 후 행동, 즉 선 제거 후 삽입을 구현한다
            // 동일한 시간에서 하나의 행동을 완수해야 하기 때문에 while문 대신 if문으로 구현
            if(!bridgeQ.isEmpty()) {
                // 현재 시간 - 트럭의 진입시간 == 다리 길이 =&gt; 다리를 다 건넜음
                if (time - bridgeQ.peek().entryTime == bridge_length) {
                    Truck rearTruck = bridgeQ.poll();
                    totalWeightOnBridge -= rearTruck.truckWeight;
                }
            }


            // 후 삽입
            // 위와 동문
            if (!waitingQ.isEmpty()) {
                // 진입하기 전의 트럭 무게 + 총 트럭의 무게 &lt;= 다리 무게면 진입 허용
                if (waitingQ.peek().truckWeight + totalWeightOnBridge &lt;= weight &amp;&amp; bridgeQ.size() &lt; bridge_length) {
                    Truck frontTruck = waitingQ.poll();
                    frontTruck.entryTime = time;
                    bridgeQ.offer(new Truck(frontTruck.truckWeight, frontTruck.entryTime));
                    totalWeightOnBridge += frontTruck.truckWeight; // 다리에 진입했으므로 무게를 합산
                }
            }
        }

        return time;
    }

    private static class Truck {
        int truckWeight;
        int entryTime;

        public Truck(int truckWeight, int entryTime) {
            this.truckWeight = truckWeight;
            this.entryTime = entryTime;
        }
    }
}</code></pre>
<h2 id="⏰시간복잡도">⏰시간복잡도</h2>
<p>최종 시간 T에 비례하는 $$O(T)$$ 복잡도 (단, time T는 알고리즘의 연산을 결정하는 트럭 무게(<code>truck_weights</code>), 다리 길이(<code>bridge_length</code>) 및 다리 무게(<code>weight</code>)와 같은 여러가지 변수에 영향을 받는다.)</p>
<h1 id="✍️깨달은-점">✍️깨달은 점</h1>
<p>위와 같이 시뮬레이션을 코드로 구현하기 위해서는 직관적으로 &quot;선 삽입, 후 제거&quot;가 아니라, Settle and Act에 따라 선 제거, 후 삽입이라는 규칙을 배웠고, 단순하게 bfs, dfs 문제의 <code>while (!q.isEmpty()) {}</code>와 같이 무지성으로 사용하는 것이 아니라, 동일한 시간 내에서 제거, 삽입과 같이 다른 행동을 완료해야 하기 때문에 if문으로 구현해야 한다는 것을 깨달았다.</p>
<p>즉, <strong>상황에 따라 조건문같이 사소한 디테일도 챙겨야 한다</strong>는 것을 깨달았다.</p>