<p>🔖그래프: 정점(vertex)들의 집합 V와 이들을 연결 하는 간선(edge)들의 집합 E로 구성된 자료구조를 말하며, $$G = (V, E)$$로 표현한다.</p>
<p>📢여기서 잠깐! 그래프의 종류를 학습하기 전에, 이전 시간에 배운 트리와 그래프의 차이점을 알아보자.</p>
<blockquote>
<p><strong>트리</strong>도 그래프와 마찬가지로 정점(Node)와 간선(Edge)가 있지만, <strong>깊이(depth)가 있어 계층 구조</strong>를 가진다는 점에서 그래프와 차이가 있다.
base condition의 역할: 트리는 구조적으로 사이클이 없기 때문에 <code>if (root == null) return null;</code>과 같은 체크만으로 충분하지만, <strong>그래프</strong>는 보통 무향 그래프(undirected graph)로 주어져 <strong>사이클이 존재</strong>할 수 있기 때문에 <strong>방문 여부 확인이 base condition의 역할</strong>을 한다.</p>
</blockquote>
<h1 id="그래프의-종류">그래프의 종류</h1>
<p>🔖방향 그래프(Directed graph): 들어오는 edge와 나가는 edge가 따로 정해져있는 그래프를 의미함</p>
<p>🔖indegree: 하나의 정점에 들어오는 edge
🔖outdegree: 하나의 정점에서 나가는 edge</p>
<p>🔖무향 그래프(Undirected graph): 방향이 안 정해져 있어 두 정점 간에 edge로 연결이 되어있으면 V -&gt; W 또는 W -&gt; V로 갈 수 있는 그래프를 의미함</p>
<p>🔖simple graph: 두 정점간에 edge가 1개로만 이루어져 있는 그래프
🔖다중그래프: 인접해있는 두 정점 사이에 outdegree와 indegree가 2개 이상인 그래프</p>
<blockquote>
<p>📢코테에서는 Undirected graph와 simple graph를 주로 다룸!</p>
</blockquote>
<p>🔖가중치 그래프: 각 경로(edge)마다 가중치(비용 또는 시간)가 다른 그래프</p>
<blockquote>
<p>📢가중치 그래프는 다익스트라 알고리즘에서 주로 쓰이기 때문에 추후 심화편에서 다룰 예정임</p>
</blockquote>
<h1 id="그래프의-구현">그래프의 구현</h1>
<p>그래프를 구현하는 방법으로는 인접 행렬, 인접 리스트, 그리고 암시적 그래프로 총 3가지가 있다.</p>
<h2 id="인접-행렬">인접 행렬</h2>
<p>row와 column에 따라, 즉 2차원 배열로 그래프를 구현한 것을 말한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/614c7eeb-f892-4ed2-9256-915cfbbb66c5/image.png" /></p>
<p>위와 같은 그래프를 행렬로 나타내면 다음과 같다👇</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/1f7b1137-5776-4f39-abcd-517effecb0c3/image.png" /></p>
<ul>
<li>인접 행렬의 특징은 대각 성분을 기준으로 <strong>대칭성</strong>을 갖고 있다는 것이다.(∵undirected graph)<ul>
<li>자기 자신을 연결하는 edge는 존재하지 않아서 대각 성분은 모두 0이다.</li>
</ul>
</li>
<li>각 정점들을 row와 column으로 나타낼 수 있고, edge로 연결되어 있으면 1, 그렇지 않으면 0으로 표현할 수 있다.</li>
</ul>
<p>🚩그런데, 인접 행렬은 위와 같이 정점은 많은데 간선의 개수가 적을 때는 0의 값까지 메모리에 저장해야 하기 때문에 비효율적이다.</p>
<p>🔨따라서 인접 리스트로 위의 문제를 해결할 수 있다.</p>
<h2 id="인접-리스트">인접 리스트</h2>
<p>해시맵의 형태로 정의되고, <strong>Key는 정점들이 들어가고, Value에는 list 형태로 연결 관계를 표시</strong>해준다.</p>
<p>위의 그래프를 인접 리스트로 표현하면 다음과 같다.</p>
<pre><code class="language-java">public class GraphSort {
    public static void main(String[] args) {
        HashMap&lt;String, List&lt;String&gt;&gt; graph = new HashMap&lt;&gt;();
        graph.put(&quot;A&quot;, List.of(&quot;B&quot;));
        graph.put(&quot;B&quot;, List.of(&quot;A&quot;, &quot;C&quot;, &quot;E&quot;));
        graph.put(&quot;C&quot;, List.of(&quot;B&quot;, &quot;D&quot;));
        graph.put(&quot;D&quot;, List.of(&quot;C&quot;, &quot;E&quot;, &quot;F&quot;));
        graph.put(&quot;E&quot;, List.of(&quot;B&quot;, &quot;D&quot;, &quot;F&quot;));
        graph.put(&quot;F&quot;, List.of(&quot;D&quot;, &quot;E&quot;));

        System.out.println(graph);
    }
}</code></pre>
<ul>
<li>💡자바9 이상부터 사용되는 <code>List.of()</code>는 하나 이상의 원소를 포함하는 불변 리스트 객체를 리턴한다.<ul>
<li>얘는 <code>ImmutableCollections</code> 클래스의 인스턴스를 주입해서 사용하기 때문에 불변 객체를 리턴함</li>
</ul>
</li>
</ul>
<h2 id="암시적-그래프">암시적 그래프</h2>
<p>보통, 미로 문제가 나오면 다음과 같이 그래프를 암시적으로 표현할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/caa54f2a-af25-4217-875e-d6ab80a3ffc4/image.png" />
검은색이 벽이고 흰 색이 길인데, 벽을 1로 길을 0으로 표현함으로써 구분할 수가 있다.</p>
<p>뿐만 아니라, 각 영역에 좌표 개념을 도입하여 row와 col로 인접 행렬과 비슷하게 표현할 수도 있다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/e72898b0-568e-4417-ab1d-851b01f98169/image.png" /></p>
<pre><code class="language-java">public class GraphSort {
    public static void main(String[] args) {
        int[][] maze = {
                {1, 1, 1, 1, 1},
                {0, 0, 0, 1, 1},
                {1, 1, 0, 1, 1},
                {1, 0, 0, 0, 0},
                {1, 1, 1, 1, 1}
        };

        System.out.println(maze[2][1]);
    }
}</code></pre>
<p>만약, (2, 1)에 접근하고 싶으면, maze[2][1]를 통해 값을 알아낼 수 있다.</p>