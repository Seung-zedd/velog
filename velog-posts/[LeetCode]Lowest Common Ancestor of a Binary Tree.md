<p><a href="https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/">https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>이진 트리가 주어졌을 때, 트리에서 주어진 두 노드의 가장 낮은 공통 조상 (LCA)을 찾으십시오.
🔖LCA: &quot;가장 낮은 공통 조상은 두 노드 P와 Q 사이에서 P와 Q를 자손으로서 T에서 가장 낮은 노드로 정의됩니다 (노드 자체가 후손이 될 수 있습니다).&quot;</p>
<p>🚧제약 조건</p>
<ol>
<li>노드의 수: <code>[2, 10^5]</code></li>
</ol>
<ul>
<li>bfs 또는 dfs의 노드 탐색 시간복잡도는 O(N)이므로 가능</li>
<li>노드의 최소 개수가 2라는 것은, <strong>부모 노드와 자식 노드만 존재</strong>할 수도 있다는 의미</li>
</ul>
<ol start="2">
<li><code>-10^9 &lt;= Node.val &lt;= 10^9</code></li>
</ol>
<ul>
<li>노드의 val은 int 타입</li>
</ul>
<ol start="3">
<li>모든 노드의 val은 unique하고, p와 q 노드는 다르다.</li>
</ol>
<ul>
<li><del>key값으로 사용하려고 하는 Node.val이 unique 해서 충돌이 없기 때문에 해시맵을 사용해도 된다는 의미</del></li>
</ul>
<ol start="4">
<li>p와 q 노드는 트리 구조에서 존재한다.</li>
</ol>
<ul>
<li><del>굳이 p와 q의 val을 확인하지 않고 레벨만 확인하면 된다는 의미</del></li>
</ul>
<p>3과 4 조건은 p와 q가 같은 노드일 수 없고 p 또는 q 노드만 존재할 수도 없다는 의미다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>📢문제의 조건이 최소 공통 &quot;조상 노드&quot; 찾기 이기 때문에, BFS(levelOrder)보다는 dfs로 재귀적으로 p와 q를 찾은 다음, 리턴해서 부모 노드를 찾고 공통 조상 노드를 찾으면 되기 때문에 dfs가 더 효율적이라고 생각하였다.</p>
<p>😂실제로, level에 따른 케이스 분류를 해서 해시맵을 사용한 levelOrder를 구현해봤는데, 생각보다 예외 케이스들이 많았고 <code>mapVisited&lt;curNode.val, level&gt;</code> 이외에 부모 노드를 저장해야 하는 별도의 로직이 필요했기 때문에 과감하게 포기하였다.</p>
<ol>
<li>preOrder를 재귀적으로 호출해서 p와 q 노드를 먼저 찾는다.</li>
<li>p와 q의 공통 조상 노드를 찾는다.
2.1. p와 q가 다른 subtree에 존재: root 노드가 LCA
2.2. p와 q가 같은 subtree에 존재: depth가 낮은 노드가 LCA</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class LcaSolution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // base condition
        if (root == null) {
            return null;
        }

        // p와 q 노드를 찾는다.
        if (root == p) {
            return p;
        }
        if (root == q) {
            return q;
        }

        // p와 q가 특정 subtree에 존재한다는 보장이 없으므로 변수 추가
        TreeNode findNode1 = lowestCommonAncestor(root.left, p, q);
        TreeNode findNode2 = lowestCommonAncestor(root.right, p, q);

        // p와 q가 다른 subtree에 존재
        if ((findNode1 != null) &amp;&amp; (findNode2 != null)) {
            return root;
        } // p와 q가 같은 subtree에 존재
        else if (findNode1 == null) {
            return findNode2;
        } else {
            return findNode1;
        }

    }
}</code></pre>
<ul>
<li><code>lowestCommonAncestor(root.left, p, q)</code>와 <code>lowestCommonAncestor(root.right, p, q)</code>로 2개의 재귀 함수를 구현한 이유는 문제에서 binary tree, 즉 차수가 2개 이하인 트리라고 명시했기 때문이다.<ul>
<li>🔖차수: 각 노드의 자식 노드의 개수</li>
</ul>
</li>
</ul>
<p>📝이 문제가 묻고 있는 것은:</p>
<ol>
<li><p>p와 q가 다른 subtree에 존재하거나 같은 subtree에 존재할 때, 이를 케이스 분류할 수 있나?
👉직접 그림을 그려가면서 쉽게 찾을 수 있었는데, 다른 subtree에 존재하면 root를, 같은 subtree에 있으면 depth가 낮은 노드를 리턴하면 되니까 preorder를 재귀적으로 호출했을 때에 존재하는 노드를 리턴하면 된다.</p>
</li>
<li><p>preorder traversal을 하면서 노드를 방문할 때, 어떠한 행위를 할 것인가?
👉원래 preorder 함수 템플릿에서는 노드를 방문할 때 출력을 하거나 값을 저장했지만, 여기서는 if문으로 root가 p 또는 q와 같은지로 검사하는 행위로 변환하였다.</p>
</li>
</ol>
<p>로 정리할 수 있다.</p>