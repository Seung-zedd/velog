<p><a href="https://leetcode.com/problems/keys-and-rooms/description/">https://leetcode.com/problems/keys-and-rooms/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>0부터 n-1까지의 번호가 있는 n개의 방들이 있고, <strong>모든 방들은 0번 방을 제외하고 모두 잠겨있다.</strong>
너의 목표는 모든 방들을 방문하는 것이다. 하지만, 당신은 방에 해당되는 열쇠 없이는 잠긴 방에 들어갈 수 없다.</p>
<p>방에 방문할 때, 당신은 그곳에서 구별되는 열쇠들을 찾을 수 있다. <strong>각각의 열쇠들은 방을 열 수 있는 번호</strong>들이 적혀있고, 당신은 다른 방들을 열 수 있는 그것들을 모두 가져갈 수도 있다.</p>
<p>i번째 방에 방문하면 열쇠 세트를 얻을 수 있는 방들이 배열로 주어졌을 때, 모든 방들을 방문할 수 있으면 true를, 그렇지 않으면 false를 리턴하라.</p>
<p>🚧제약 조건 </p>
<ol>
<li><code>n == rooms.length</code></li>
<li><code>2 &lt;= n &lt;= 1000</code></li>
<li><code>0 &lt;= rooms[i].length &lt;= 1000</code>
=&gt; 1000 * 1000 = 10^6 &lt; 10^8이므로 brute-force 가능</li>
<li><code>1 &lt;= sum(rooms[i].length) &lt;= 3000</code>
=&gt; 적어도 1번 이상의 열쇠는 있다는 의미</li>
<li><code>0 &lt;= rooms[i][j] &lt; n</code>
=&gt; 0부터 n-1까지의 번호</li>
<li>rooms[i]의 모든 값들은 unique하다.(중복되는 value가 없다는 의미)</li>
</ol>
<hr />
<p>0번방을 제외하고 모두 잠겨있다 -&gt; rooms[0][0]부터 방문하라는 의미
모든 방들을 방문 -&gt; bfs 또는 dfs로 모든 정점을 방문해라
방들을 방문할 때 distinct keys들을 얻을 수 있음 -&gt; 번호가 적혀있는 해당 정점에 방문할 수 있음</p>
<p>🔑Rooms는 Vertex를, Keys는 Edge를 의미한다.
👉따라서 이 문제는 &quot;<strong>인접 리스트</strong>로 주어졌을 때, 해당 input을 bfs 또는 dfs로 모두 방문할 수 있는지&quot;를 묻는 문제로 해석될 수 있다.</p>
<h1 id="bfs-풀이">bfs 풀이</h1>
<h2 id="⚙️코드-설계">⚙️코드 설계</h2>
<p>별다른 건 없고, 인접 리스트의 코드 템플릿을 그대로 적용한다.</p>
<h2 id="💻코드-구현">💻코드 구현</h2>
<pre><code class="language-java">public class KeysAndRoomsSolution {
    public boolean canVisitAllRooms(List&lt;List&lt;Integer&gt;&gt; rooms) {
        int n = rooms.size();
        boolean[] visited = new boolean[n];

        // bfs 탐색
        // queue: keys(Edge)들을 추가하기 위한 자료구조
        Queue&lt;List&lt;Integer&gt;&gt; q = new LinkedList&lt;&gt;();
        visited[0] = true; // 0번방은 무조건 방문
        q.offer(rooms.getFirst());

        while (!q.isEmpty()) {
            List&lt;Integer&gt; keys = q.poll();

            for (Integer key : keys) {
                if (!visited[key]) {
                    visited[key] = true;
                    q.offer(rooms.get(key)); // key에 해당하는 room에 들어가서 distinct keys를 얻음

                }
            }
        }

        // 하나라도 방문을 안 했으면 false를 리턴
        for (boolean v : visited) {
            if (!v) {
                return false;
            }
        }

        return true;

    }

}</code></pre>
<ul>
<li>💡원래 bfs 코드에는 <code>q.offer(v)</code>로 String 또는 Integer 타입만 추가를 했었는데, 이 문제에서는 큐에 추가되는 keys들이 <code>List&lt;Integer&gt;</code> 타입이므로, 2차원 리스트의 rooms에서 key(index)에 해당하는 keys를 큐에 추가하면 된다.</li>
</ul>
<h1 id="dfs-풀이">dfs 풀이</h1>
<h2 id="💻코드-구현-1">💻코드 구현</h2>
<pre><code class="language-java">public class KeysAndRoomsSolution {

    public boolean canVisitAllRooms(List&lt;List&lt;Integer&gt;&gt; rooms) {
        int n = rooms.size();
        boolean[] visited = new boolean[n];

        // 0번방을 무조건 먼저 방문해야 하기 때문에 사전 세팅
        // dfs 탐색
        visited[0] = true;
        List&lt;Integer&gt; keys = rooms.get(0);

        for (Integer key : keys) {
            if (!visited[key]) {
                dfs(key, visited, rooms);
            }
        }

        // 하나라도 방문을 안 했으면 false를 리턴
        for (boolean v : visited) {
            if (!v) {
                return false;
            }
        }

        return true;

    }

    private static void dfs(Integer key, boolean[] visited, List&lt;List&lt;Integer&gt;&gt; rooms) {
        visited[key] = true;
        List&lt;Integer&gt; keys = rooms.get(key);

        for (Integer k : keys) {
            if (!visited[k]) {
                dfs(k, visited, rooms);
            }
        }

    }

}</code></pre>
<ul>
<li>0번방으로 가서 key들을 얻고, 해당 key에 해당하는 방을 방문하지 않았으면 dfs를 호출한다.<ul>
<li>🤩dfs 함수 정의 부분은 dfs 함수 템플릿을 그대로 사용했는데, <strong>keys를 순회하기 위해 rooms를 dfs의 파라미터로 전달해서 <code>List&lt;Integer&gt; keys = rooms.get(key);</code></strong>로 가져온 생각을 위의 bfs 코드 중 큐에 추가하는 아이디어를 활용해서 구현하였다.</li>
</ul>
</li>
</ul>
<p>⏰rooms는 정점이고, keys는 간선이므로, bfs, dfs 모두 $$O(V+E)$$의 시간복잡도를 가진다. </p>