<p><a href="https://leetcode.com/problems/maximum-depth-of-binary-tree/description/">https://leetcode.com/problems/maximum-depth-of-binary-tree/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>이진 트리의 root가 주어졌을 때, 그것의 최대 depth를 리턴하라.
🔖이진트리의 최대 깊이: root 노드로부터 가장 먼 leaf 노드까지의 거리를 말함</p>
<p>🚧제약 조건 </p>
<ol>
<li>노드의 수: <code>[0, 10^4]</code>
=&gt; 어짜피 bfs, dfs의 시간복잡도는 O(N)이므로 O.K</li>
<li>-100 &lt;= Node.val &lt;= 100</li>
</ol>
<p>🌲이 문제는 트리의 최대 깊이만 구하면 되기 때문에 bfs, dfs 2가지 모두로 풀 수 있다. 먼저 bfs(levelorder traversal) 방법으로 문제를 풀어보자.</p>
<h1 id="bfslevelorder-풀이">bfs(levelOrder) 풀이</h1>
<h2 id="⚙️코드-설계">⚙️코드 설계</h2>
<p>딱히 복잡한 코드 설계가 필요없고, 기존의 levelorder traversal 함수 템플릿에서 약간의 응용만 필요로 하면 되는데, 요구사항이 트리의 maxDepth를 리턴하는 것이기 때문에 전체의 트리 높이를 level로 두고 계속해서 업데이트하는 것을 visited 행위로 하였다.</p>
<h2 id="💻코드-구현">💻코드 구현</h2>
<pre><code class="language-java">public class MaxDepthSolution {
    public int maxDepth(TreeNode root) {
        int level = 0; // start from zero
        // if the root is null
        if (root == null) {
            return level;
        }

        Queue&lt;NodeInfo&gt; q = new LinkedList&lt;&gt;();
        q.offer(new NodeInfo(root, ++level));

        while (!q.isEmpty()) {
            NodeInfo nodeInfo = q.poll();
            TreeNode curNode = nodeInfo.node;
            int curLevel = nodeInfo.level;

            level = Math.max(level, curLevel);

            if (curNode.left != null) {
                q.offer(new NodeInfo(curNode.left, curLevel + 1));
            }
            if (curNode.right != null) {
                q.offer(new NodeInfo(curNode.right, curLevel + 1));
            }

        }
        return level;
    }
    // 커스텀 클래스 정의
    static class NodeInfo {
        TreeNode node;
        int level;

        NodeInfo(TreeNode node, int level) {
            this.node = node;
            this.level = level;
        }
    }
}</code></pre>
<ul>
<li>👍커스텀 클래스를 사용하면 필요한 필드(여기서는 level)를 추가해서 함수에 원하는대로 사용할 수 있기 때문에 좋다.<ul>
<li>심지어, <strong>노드에 level을 표시</strong>한다는 직관적인 사고도 반영할 수 있어서 쉽게 써먹을 수 있음  </li>
</ul>
</li>
<li>💡<code>level = Math.max(level, curLevel);</code>로 전역변수의 level을 계속해서 업데이트한 이유는 <strong>레벨별로 노드를 처리함으로써 트리의 최대 높이를 추적</strong>할 수 있기 때문이다.<ul>
<li>뿐만 아니라, <strong>while문에도 중괄호 스코프</strong>가 있기 때문에 while문을 빠져나가면 curLevel 지역변수는 사라져서 값을 기억할 수가 없다.</li>
<li>이 부분이 levelorder traversal의 visited 역할을 대신했다고 보면 된다.</li>
</ul>
</li>
</ul>
<p>📢큐에 <code>curLevel++</code>로 추가를 하면 sibling Node에 영향을 미칠 수 있기 때문에 <code>curLevel + 1</code>로 <strong>따로따로 처리</strong>해줘야 한다.</p>
<hr />
<p>🤔levelorder는 큐로 자식 노드를 순회하고, visited로 노드에 방문해서 특정한 행위(저장, 출력 등)을 하는데, 위의 방식 대신 현재 노드에 방문할 때마다 level을 증가시킬 수는 없을까?
👉<code>queue.poll()</code>을 한 다음에 level++을 하면 레벨별로 처리가 안되고 큐에 저장된 노드 레벨에 영향을 미치기 때문에 AI의 도움을 받아서 아래와 같이 코드를 구현하였다.</p>
<h2 id="💻레벨별로-처리하는-코드-구현">💻레벨별로 처리하는 코드 구현</h2>
<pre><code class="language-java">public class MaxDepthSolution {
    public int maxDepth(TreeNode root) {
        // if the root is null
        if (root == null) {
            return 0;
        }

        // 사전 세팅
        Queue&lt;TreeNode&gt; q = new LinkedList&lt;&gt;();
        q.offer(root);
        int level = 0;

        while (!q.isEmpty()) {
            // 현재 레벨의 모든 노드를 처리
            int size = q.size();
            level++; // 새 레벨로 이동

            for (int i = 0; i &lt; size; i++) {
                TreeNode curNode = q.poll();

                if (curNode.left != null) {
                    q.offer(curNode.left);
                }
                if (curNode.right != null) {
                    q.offer(curNode.right);
                }
            }

        }
        return level;

    }
}</code></pre>
<ul>
<li>👎이 코드의 단점은 큐의 size로 반복하는 방식이 바로 떠오르지 않기 때문에 비추함</li>
</ul>
<p>📝levelOrder 함수 템플릿을 그대로 적용하는 것이 아니라, <strong>문제 요구사항에 따라 적절히 응용하는게 중요</strong>하다는 것을 깨달았다.</p>
<h1 id="dfs-풀이">dfs 풀이</h1>
<h2 id="⚙️코드-설계-1">⚙️코드 설계</h2>
<ol>
<li>노드를 재귀적으로 탐색하면서 트리의 깊이를 증가시켜야 되는데, 이전의 재귀함수에서 <code>return sum(n - 1) + 1</code>처럼 재귀적으로 함수를 호출하고 리턴할 때 1씩 증가시킨다.</li>
<li>여기에 더해서, postorder traversal로 노드를 탐색한 다음, root의 left child subtree와 right child subtree의 최대 깊이를 리턴하면 된다.</li>
</ol>
<h2 id="💻코드-구현-1">💻코드 구현</h2>
<pre><code class="language-java">public class MaxDepthSolution {
    public int maxDepth(TreeNode root) {
        // base condition
        if (root == null) {
            return 0;
        }
        int left = maxDepth(root.left) + 1;
        int right = maxDepth(root.right) + 1;
        return Math.max(left, right);
    }

}</code></pre>
<ul>
<li>재귀 함수는 <strong>base condition</strong>이 필수이기 때문에 위의 levelorder traversal과 비슷하게 주어진 노드가 null이면 0을 리턴하게 하였고, <strong>left -&gt; right</strong> -&gt; 서브트리의 연산 결과를 활용해서 <strong><code>Math.max()</code>로 최댓값을 구한 것이 visited 역할</strong>을 함</li>
</ul>