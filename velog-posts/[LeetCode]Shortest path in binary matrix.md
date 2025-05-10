<p><a href="https://leetcode.com/problems/shortest-path-in-binary-matrix/description/">https://leetcode.com/problems/shortest-path-in-binary-matrix/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>n x n 이진 행렬 grid가 주어졌을 때, 행렬에서 가장 짧은 clear path를 리턴하라.(단, clear path가 없으면 -1을 리턴하라)</p>
<p>clear path: (0,0) -&gt; (n-1, n-1)로 가는 path를 말함:</p>
<ol>
<li>path의 모든 방문한 셀들은 0이다.</li>
<li>path의 인접한 셀들은 8-directionally로 연결되어 있다.(그것들은 모두 다르고, 그것들은 간선 또는 구석을 공유한다.)</li>
<li>clear path의 길이는 방문한 셀들의 수이다.</li>
</ol>
<p>🚧제약 조건 </p>
<ol>
<li>m = row, n = col</li>
<li>1 &lt;= m, n &lt;= 100
=&gt; m*n = 10000 &lt; 10^8 이므로 brute-force 가능</li>
<li><code>grid[i][j]</code>는 0 또는 1이다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>(0,0) 또는 (n-1, n-1)이 1이면 clear path가 아니므로 -1을 리턴</li>
<li>dfs로 하면 0이 표시된 셀들은 무조건 방문하기 때문에 shorest path가 나올 수 x =&gt; <strong>bfs 알고리즘 선택</strong></li>
<li>bfs 함수 템플릿 그대로 사용하는데, Tree에서 했던 커스텀 클래스를 사용해서 셀마다 방문한 횟수를 업데이트한다.</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class ShortestPathSolution {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        final int[] dr = {-1, 1, 0, 0, -1, 1, 1, -1};
        final int[] dc = {0, 0, -1, 1, -1, 1, -1, 1};
        boolean[][] visited = new boolean[m][n];
        int count = 0;

        // 시점 또는 종점이 1이면 clear path가 아니므로 -1을 리턴
        if (grid[0][0] == 1 || grid[m - 1][n - 1] == 1) {
            return -1;
        }

        // 완전 탐색
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                if (grid[i][j] == 0 &amp;&amp; !visited[i][j]) {
                    // bfs 탐색 시작
                    visited[i][j] = true;
                    Queue&lt;Point&gt; q = new LinkedList&lt;&gt;();
                    q.offer(new Point(i, j, 1));

                    while (!q.isEmpty()) {
                        Point cur = q.poll();
                        int startR = cur.r;
                        int startC = cur.c;
                        int curCount = cur.count;

                        // 업데이트
                        count = Math.max(count, curCount);

                        // 현재 좌표가 종점이면 count를 리턴
                        if ((startR == m - 1) &amp;&amp; (startC == n - 1)) {
                            return count;
                        }

                        for (int k = 0; k &lt; 8; k++) {
                            int nextR = startR + dr[k];
                            int nextC = startC + dc[k];

                            if ((nextR &gt;= 0 &amp;&amp; nextR &lt; m) &amp;&amp; (nextC &gt;= 0 &amp;&amp; nextC &lt; n) &amp;&amp; grid[nextR][nextC] == 0 &amp;&amp; !visited[nextR][nextC]) {
                                visited[nextR][nextC] = true;
                                q.offer(new Point(nextR, nextC, curCount + 1));
                            }
                        }
                    }
                    return -1;

                }
            }
        }
        return count;
    }

    private static class Point {
        int r;
        int c;
        int count;

        public Point(int r, int c, int count) {
            this.r = r;
            this.c = c;
            this.count = count;
        }
    }
}</code></pre>
<p>📖스토리는 다음과 같다:</p>
<ol>
<li>시점 또는 종점이 1이면 clear path가 아니므로 -1을 리턴</li>
<li>완전 탐색을 하는데 현재 좌표가 (0,0)이면 bfs 탐색 시작
2-1) 셀을 방문할 때마다 count 1씩 증가시킨다.
📢이때 주의할 점이 8-directionally로 탐색할 때 (nextR, nextC)을 sibling Node처럼 취급을 해서 방문하려고 하는 셀에 <code>curCount + 1</code>로 <strong>따로따로 처리</strong>해줘야 한다.
2-2) 현재 좌표가 종점이면 업데이트시킨 count를 리턴</li>
</ol>
<hr />
<p>🤖<strong>clear path처럼 경로가 정해진 grid</strong>는 위와 같이 완전 탐색을 할 필요가 없고 <strong><code>grid[0][0]</code>일 때 bfs 탐색을 바로 시작</strong>하는 것이 좋습니다.</p>
<ul>
<li>즉, 이전의 num of islands 문제는 경로가 정해지지 않았기 때문에 섬들을 탐색하기 위해서 완전 탐색을 할 수 밖에 없다는 것에서 차이가 있다.</li>
</ul>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public class ShortestPathSolution {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        final int[] dr = {-1, 1, 0, 0, -1, 1, 1, -1};
        final int[] dc = {0, 0, -1, 1, -1, 1, -1, 1};
        boolean[][] visited = new boolean[m][n];

        // 시점 또는 종점이 1이면 clear path가 아니므로 -1을 리턴
        if (grid[0][0] == 1 || grid[m - 1][n - 1] == 1) {
            return -1;
        }

        // (0,0)이 0이면 bfs 탐색 시작
        if (grid[0][0] == 0) {
            // bfs 탐색 시작
            visited[0][0] = true;
            Queue&lt;Point&gt; q = new LinkedList&lt;&gt;();
            q.offer(new Point(0, 0, 1));

            while (!q.isEmpty()) {
                Point cur = q.poll();
                int startR = cur.r;
                int startC = cur.c;
                int curCount = cur.count;

                // 현재 좌표가 종점이면 count를 리턴
                if ((startR == m - 1) &amp;&amp; (startC == n - 1)) {
                    return curCount;
                }

                for (int k = 0; k &lt; 8; k++) {
                    int nextR = startR + dr[k];
                    int nextC = startC + dc[k];

                    if ((nextR &gt;= 0 &amp;&amp; nextR &lt; m) &amp;&amp; (nextC &gt;= 0 &amp;&amp; nextC &lt; n) &amp;&amp; grid[nextR][nextC] == 0 &amp;&amp; !visited[nextR][nextC]) {
                        visited[nextR][nextC] = true;
                        q.offer(new Point(nextR, nextC, curCount + 1));
                    }
                }
            }

        }
        return -1;
    }

    private static class Point {
        int r;
        int c;
        int count;

        public Point(int r, int c, int count) {
            this.r = r;
            this.c = c;
            this.count = count;
        }
    }
}
</code></pre>
<ul>
<li>💡max depth in a binary tree문제는 <strong>&quot;모든 노드를 방문&quot;</strong>하는 과정에서 가장 깊은 레벨을 추적해야 하므로, <strong>전역 변수(혹은 메서드 스코프 변수)로 최대값을 계속 업데이트</strong>해줘야 한다.<ul>
<li>반면에 이번 문제는 <strong>목적지에만 도달하면 증가시킨 count만 리턴</strong>하면 되기 때문에 굳이 메서드 레벨 변수를 만들어서 <code>Math.max(count, curCount)</code>로 업데이트시킬 필요가 없다.</li>
</ul>
</li>
</ul>