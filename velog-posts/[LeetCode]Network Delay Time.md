<p><a href="https://leetcode.com/problems/network-delay-time/description/">https://leetcode.com/problems/network-delay-time/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>1~N번 까지의 N개의 노드가 주어졌다.
-&gt; dist를 n+1 크기로 초기화해야겠구나?</p>
<p>유향 그래프를 가진 <code>times[i] = (u, v, w)</code>: u는 source 노드, v는 target 노드, w(간선, 비용)는 source부터 target까지 가는데 걸리는 신호의 시간</p>
<p>우리는 주어진 k 노드로부터 신호를 보낼 것이다. 이때, 모든 N개의 노드가 신호를 받는데 걸리는 최소 시간을 리턴하라.(만약 모든 N개의 노드가 신호를 받지 못하면 -1을 리턴하라.)</p>
<p>🚧제약 조건</p>
<ol>
<li>1 &lt;= k &lt;= n &lt;= 100</li>
<li>1 &lt;= times.length &lt;= 6000</li>
<li>times[i].length == 3</li>
<li>1 &lt;= ui, vi &lt;= n</li>
<li>ui != vi</li>
<li>0 &lt;= wi &lt;= 100</li>
<li>모든 source 노드와 target 노드는 유일하다. (i.e., 다중 간선이 없다는 의미)</li>
</ol>
<p>⏰다익스트라 알고리즘은 힙 자료구조를 사용하기 때문에 $$O(ElogV)$$가 걸리는데, 노드의 개수인 N이 최대 100개고, 간선의 개수인 times.length의 최대가 6000이기 때문에, $$O(6000 * log100) &lt; O(10^8)$$ 보다는 작기 떄문에 사용할 수 있다.
🧐문제가 <strong>가중치가 있는 BFS</strong>라서 대놓고 다익스트라 알고리즘을 사용하라고 힌트를 주고 있다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>기존의 다익스트라 템플릿 코드를 사용하되, 약간의 응용이 필요한 문제다.</p>
<ol>
<li><p>1~N번까지의 N개의 노드가 주어졌다: dist 1차원 배열은 인덱스가 0번부터 시작하기 때문에 1번 노드를 1번 인덱스로 사용하기 위해서는 <strong><code>dist[n+1]</code>로 초기화</strong>해줘야 한다.</p>
</li>
<li><p>커스텀 클래스 대신 1차원 배열로 간단하게 노드와 비용을 나타낼 수 있다.</p>
</li>
<li><p>times가 2차원 배열이라는 것에 감안하여, 기존의 인접리스트를 <strong>times 배열로 for문을 돌리면서 source 노드와 target 노드를 설정</strong>하는 것으로 응용해야 한다.</p>
</li>
<li><ol>
<li>times에서 다음 노드의 비용을 설정해서 비용을 업데이트 해줘야 한다.</li>
</ol>
</li>
<li><p>기존의 <code>dist[end]</code>를 리턴하는 대신에, 1~N번까지의 최소 비용을 리턴해야 하기 때문에 <strong>dist의 1번 인덱스부터 N번까지의 최댓값을 리턴</strong>하면 된다.
👉왜냐하면 다익스트라 알고리즘 자체가 min heap 자료구조를 사용해서 더 싼 비용이 있는 경로만을 탐색하기 때문</p>
</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class NetworkDelayTImeSolution {
    public int networkDelayTime(int[][] times, int n, int k) {
        // 사전 세팅
        int[] dist = new int[n + 1];
        PriorityQueue&lt;int[]&gt; pq = new PriorityQueue&lt;&gt;((a, b) -&gt; Integer.compare(a[1], b[1]));
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[k] = 0;
        pq.offer(new int[]{k, 0});

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int curNode = cur[0];
            int curCost = cur[1];

            if (curCost &gt; dist[curNode]) continue;

            for (int i = 0; i &lt; times.length; i++) {
                // 시작 노드가 curNode일 때만 인접 노드 탐색
                if (times[i][0] == curNode) {
                    int nextNode = times[i][1];
                    int cost = times[i][2];
                    // 일단 비용 업데이트
                    int nextCost = curCost + cost;

                    if (nextCost &lt; dist[nextNode]) {
                        dist[nextNode] = nextCost;
                        pq.offer(new int[]{nextNode, nextCost});
                    }
                }

            }
        }
        // dist에서 최댓값을 구한다
        int max_val = 0;
        for (int i = 1; i &lt; dist.length; i++) {
            max_val = Math.max(max_val, dist[i]);
        }

        return max_val == Integer.MAX_VALUE ? -1 : max_val;

    }

}</code></pre>
<ul>
<li><code>pq.offer(new NodeCost(nextNode.node, nextCost)</code>대신 1차원 정적 배열을 사용해서 <code>pq.offer(new int[] {nextNode, nextCost})</code>로 간단하게 삽입했다.</li>
</ul>
<h2 id="🤔그냥-n번-노드에-최소-비용이-있으니까-distn을-리턴하면-안-되나요">🤔그냥 N번 노드에 최소 비용이 있으니까 <code>dist[n]</code>을 리턴하면 안 되나요?</h2>
<p>👉ㄴㄴ 안됨. 
기존의 다익스트라 알고리즘에서 요구하는 사항: N번 노드(종점)까지 신호를 받는데 걸리는 최소 시간</p>
<p>이번 문제에서 요구하는 사항: <strong>모든 N개의 노드가 신호를 받는데 걸리는 최소 시간</strong>이라서 </p>
<pre><code class="language-txt">1 -&gt; 2 (비용: 1), 시작 노드 = 2
</code></pre>
<p>로 테스트 케이스가 주어졌을 경우에는 2번 노드가 시작 노드가 되서 비용이 0으로 되는데, 인접 노드를 탐색할 때 <code>if (times[i][0] == curNode)</code>조건이 거짓이 되기 때문에 <code>dist = [Integer.MAX_VALUE, Integer.MAX_VALUE, 0]</code>가 된다. 따라서 단순히 <code>dist[2]</code>를 리턴하면 -1이 아닌 0이 된다.</p>
<p>또다른 예시: </p>
<pre><code class="language-txt">    2 (비용: 100)
   ↗
1 
   ↘
    3 → 4 (비용: 1 + 1 = 2)</code></pre>
<p>위의 경우에는 <code>dist = {MAX(고려 x), 0, 100, 1, 2}</code>라서 <strong>모든 노드까지 걸리는 최소 시간은 dist의 최댓값</strong>인 100이어야 하는데, 단순히 N번 노드(종점)까지의 최소 시간인 <code>dist[n]</code>을 리턴하면 2가 나와서 문제의 조건에 위배된다.</p>
<p>🧐이제보니, 이 문제는 <strong>컴퓨터 네트워크의 Distance Vector 알고리즘</strong>을 그대로 사용하라는 건데, 나중에 컴퓨터 네트워크를 다시 복습하면 이 문제가 자동으로 떠오를 것 같다.</p>