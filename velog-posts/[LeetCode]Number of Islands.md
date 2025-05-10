<p><a href="https://leetcode.com/problems/number-of-islands/description/">https://leetcode.com/problems/number-of-islands/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>m x n 2차원 binary grid가 주어졌을 때, 섬들의 수를 리턴하라.(grid의 '1'은 land를, '0'은 물을 의미한다.)</p>
<ul>
<li>섬들은 물로 둘러쌓여 있고, 연결된 인접의 섬들이 수평적으로 또는 수직적으로 형성되어 있다.</li>
<li>당신은 grid의 모든 4개의 간선들이 물로 둘러쌓여있다고 가정해도 된다.</li>
</ul>
<p>🚧제약 조건</p>
<ol>
<li>m == grid.length (row), n == grid[i].length (col)</li>
<li>1 &lt;= m, n &lt;= 300</li>
<li>grid[i][j]는 '0' 또는 '1'이다.</li>
</ol>
<p>위 문제도 마찬가지로 bfs 또는 dfs로 풀 수 있다.
⏰참고로, grid의 모든 원소를 탐색하기 때문에 시간복잡도는 bfs, dfs 둘다 $$O(M*N)$$이다.</p>
<h1 id="bfs-풀이">bfs 풀이</h1>
<h2 id="⚙️코드-설계">⚙️코드 설계</h2>
<p>원본 grid 데이터를 변경하지 않게 하기 위해 <strong>boolean 타입의 2차원 배열 visited로 grid의 크기만큼 설정</strong>하였고 섬들의 수를 카운트 하기 위해 island를 0으로 초기화하였다.</p>
<p>🤔암시적 그래프로 주어진 grid를 bfs에서 순회하려면 어떻게 해야 하지? 즉, HashMap 타입의 인접 리스트가 아닐 때, for문을 어떻게 사용하지?
🔨int타입의 dr, dc로 상, 하, 좌, 우를 설정한 후, for문으로 순회시키면 된다.</p>
<p>그리고 bfs 함수 템플릿은 동일하게 사용하였다.</p>
<h2 id="💻코드-구현">💻코드 구현</h2>
<pre><code class="language-java">public class IslandsSolution {
    public int numIslands(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        int island = 0;

        // dr, dc 세팅
        int[] dr = {-1, 1, 0, 0};
        int[] dc = {0, 0, -1, 1};

        // 완전 탐색
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                // 섬이고 현재 방문을 하지 않았으면
                if (grid[i][j] == '1' &amp;&amp; !visited[i][j]) {
                    // bfs 탐색 시작 and 사전 세팅
                    Queue&lt;Pair&gt; q = new LinkedList&lt;&gt;();
                    q.offer(new Pair(i, j));
                    visited[i][j] = true;

                    while (!q.isEmpty()) {
                        Pair cur = q.poll();
                        int startR = cur.r;
                        int startC = cur.c;

                        for (int k = 0; k &lt; 4; k++) {
                            int nextR = startR + dr[k];
                            int nextC = startC + dc[k];

                            if ((0 &lt;= nextR &amp;&amp; nextR &lt; m) &amp;&amp; (0 &lt;= nextC &amp;&amp; nextC &lt; n)) {
                                if (!visited[nextR][nextC] &amp;&amp; grid[nextR][nextC] == '1') {
                                    visited[nextR][nextC] = true;
                                    q.offer(new Pair(nextR, nextC));
                                }

                            }
                        }
                    }
                    island++;

                }

            }
        }
        return island;
    }

    private static class Pair {
        int r;
        int c;

        public Pair(int r, int c) {
            this.r = r;
            this.c = c;
        }
    }

}</code></pre>
<p>파이썬과 다르게 bfs 함수를 캡슐화하는데 dr, dc를 클래스 레벨로 선언하는 것과 같이 처리가 까다롭기 때문에 그냥 메서드 안에 필요한 로직들을 전부 넣었다.</p>
<p>📖스토리는 다음과 같다:</p>
<ol>
<li>bfs에서 <strong>인접 리스트를 모방하기 위해 dr, dc를 설정</strong>하였다.</li>
<li>2중 for문으로 grid를 완전 탐색할 때 grid가 '1'이고 현재 방문하지 않았으면 bfs 탐색을 시작한다.</li>
<li>현재 좌표를 bfs 함수 템플릿에서 사전 세팅에 할당하였고, 이때 현재 좌표를 queue에 추가하기 위해 <strong>커스텀 클래스</strong>를 따로 만들었다.</li>
<li>for문으로 dr, dc의 크기만큼 순회하는데, grid의 범위 내에 있고 grid가 '1'이며 현재 방문하지 않았으면 방문했다고 표시하고 큐에 추가를 한다.</li>
<li>큐가 비었으면 bfs가 끝난거니까 island를 1만큼 증가시킨다.</li>
<li>완전 탐색을 끝내면 island를 리턴한다.</li>
</ol>
<h1 id="dfs-풀이">dfs 풀이</h1>
<h2 id="⚙️-코드설계">⚙️ 코드설계</h2>
<p>dfs 함수 템플릿을 그대로 사용했고, bfs의 암시적 그래프 활용도 그대로 사용했기 때문에 따로 설계는 하지 않았다.</p>
<h2 id="💻코드-구현-1">💻코드 구현</h2>
<pre><code class="language-java">public class IslandsSolution {
    public int numIslands(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        int island = 0;

        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                if (grid[i][j] == '1' &amp;&amp; !visited[i][j]) {
                    island += dfs(grid, visited, i, j, m, n);
                }
            }
        }
        return island;
    }

    private int dfs(char[][] grid, boolean[][] visited, int startR, int startC, int m, int n) {
        int[] dr = {1, -1, 0, 0};
        int[] dc = {0, 0, -1, 1};
        visited[startR][startC] = true;

        for (int k = 0; k &lt; 4; k++) {
            int nextR = startR + dr[k];
            int nextC = startC + dc[k];

            if ((0 &lt;= nextR) &amp;&amp; (nextR &lt; m) &amp;&amp; (0 &lt;= nextC) &amp;&amp; (nextC &lt; n) &amp;&amp; grid[nextR][nextC] == '1' &amp;&amp; !visited[nextR][nextC]) {
                dfs(grid, visited, nextR, nextC, m, n);
            }
        }
        return 1;
    }

}</code></pre>
<ul>
<li>dfs 재귀 함수 인자에 nextR, nextC를 전달해서 인접 리스트를 모방하였다.</li>
</ul>
<p>📝bfs는 큐에 좌표를 추가하기 위해 커스텀 클래스를 사용했지만, dfs는 함수 부분에 좌표 파라미터를 그대로 전달만 하면 되기 때문에 따로 커스텀 클래스를 만들 필요가 없었다는 차이점을 깨달았다.</p>