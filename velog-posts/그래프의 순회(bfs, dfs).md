<p>이전 시간에 배운 트리의 순회 방법인 levelorder traversal(bfs), dfs와 그렇게 큰 차이는 없다. 다만, base condition에서 약간의 차이를 보인다.</p>
<h3 id="💻bfs-코드">💻bfs 코드</h3>
<pre><code class="language-java">public static Set&lt;String&gt; bfs() {
        // graph 구조는 이전 것을 그대로 가져왔기 때문에 createGraph()의 바디는 생략하였음.
        HashMap&lt;String, List&lt;String&gt;&gt; graph = createGraph();

        // 첫번째 정점을 시작점으로 설정
        String startNode = &quot;A&quot;;

        // 사전 세팅: 시작 정점을 넣는다
        LinkedHashSet&lt;String&gt; visited = new LinkedHashSet&lt;&gt;();
        Queue&lt;String&gt; q = new LinkedList&lt;&gt;();
        visited.add(startNode);
        q.offer(startNode);

        while (!q.isEmpty()) {
            String curNode = q.poll();

            for (String v : graph.get(curNode)) {
                if (!visited.contains(v)) {
                    visited.add(v);
                    q.offer(v);
                }
            }
        }

        return visited;
    }</code></pre>
<ul>
<li>🚩파이썬에서는 visited를 간단하게 리스트로 표현해서 <code>if v not in visited:</code>로 조건을 검사했지만, 자바에서는 ArrayList 타입의 visited로 v가 있는지 검사하면 순회하면서 모든 요소를 탐색해야 하기 때문에 O(N)이 걸린다. <ul>
<li>🔨따라서 순서가 보장되는 LinkedHashSet으로 visited 객체를 만들면 HashSet의 <code>contains()</code>가 HashMap의 <code>containsKey(Object key)</code> 기반으로 만들어졌기 때문에 O(1)만 걸리게 할 수 있다.</li>
</ul>
</li>
</ul>
<p>잘 기억이 안나면 <a href="https://velog.io/@csj0209/%ED%95%B4%EC%8B%9C%ED%85%8C%EC%9D%B4%EB%B8%94Hash-Table#%ED%95%B4%EC%8B%9C%EB%A7%B5%EC%9D%98-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98">해시맵의 메서드 종류</a>를 참고 할 것!  </p>
<p>⏰따라서, 총 시간복잡도는 모든 정점과 간선을 visited와 queue를 통해서 한 번만 처리할 수 있기 때문에 $$O(V+E)$$만 걸린다.</p>
<h3 id="💻dfs-코드">💻dfs 코드</h3>
<pre><code class="language-java">public static Set&lt;String&gt; dfs(String curNode) {
        HashMap&lt;String, List&lt;String&gt;&gt; graph = createGraph();
        visited.add(curNode); // 노드 방문 후 저장
        // 노드 순회
        for (String v : graph.get(curNode)) {
            if (!visited.contains(v)) {
                dfs(v);
            }
        }
        return visited;
    }</code></pre>
<ul>
<li><strong>트리의 preorder 방식</strong>과 유사하다. 즉, 노드를 방문할 때 visited에 저장하고, 그 다음에 재귀적으로 curNode에 연결된 정점들을 순회한다.</li>
<li>💡dfs를 호출하면 visited도 초기화되기 때문에, <strong>visited를 전역 변수</strong>로 만들었다.</li>
</ul>
<p>⏰마찬가지로 모든 정점과 for문으로 간선으로 연결된 정점들을 탐색하기 때문에 $$O(V+E)$$ 시간복잡도가 걸린다.</p>