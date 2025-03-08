<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/ee7db647-630b-4747-9410-c54137015c03/image.png" />
? 이분들 아니니까 들어가십시오...</p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>지뢰가 있는 지역과 &quot;지뢰에 인접한 위, 아래, 좌, 우 대각선 칸을 모두 위험지역으로 분류&quot;한다고 한다.
지뢰는 2차원 배열 board에 1로 표시되어 있고 board에는 지뢰가 매설 된 지역 1과, 지뢰가 없는 지역 0만 존재한다.
이때, 지뢰가 매설된 지역의 지도 board가 매개변수로 주어질 때, 안전한 지역의 칸 수를 return하라.</p>
<p>🚧제약 조건<br />board에는 지뢰가 있는 지역 1과 지뢰가 없는 지역 0만 존재</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>🔎<a href="https://leetcode.com/problems/shortest-path-in-binary-matrix/description/">https://leetcode.com/problems/shortest-path-in-binary-matrix/description/</a> &lt;- 이 문제와 굉장히 흡사하니 시간남으면 풀어 볼 것!(아이디어도 이하 동일)</p>
<p>문제가 딱봐도 암시적 그래프라 bfs 또는 dfs로 풀면 되는데, &quot;지뢰에 인접한 위, 아래, 좌, 우 대각선 칸을 모두 위험지역으로 분류&quot;하기 때문에 <strong>bfs</strong>로 풀면 될 것 같다.</p>
<ol>
<li>이중 for문으로 완전탐색</li>
<li>지뢰가 발생한 지점을 인자로 해서 bfs 함수를 호출</li>
<li>dr, dc 세팅해서 인접 지역 탐색</li>
<li>최종적으로 r*c - board를 복사한 visited의 True 개수를 리턴한다.</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(board):
    r = len(board)
    c = len(board[0])
    visited = [[False] * c for i in range(r)]

    def danger_search(start_x, start_y):
        # 지뢰 지점을 True로 변경
        visited[start_x][start_y] = True

        # 좌상부터 시계방향으로 세팅
        dr = [-1, -1, -1, 0, 1, 1, 1, 0]
        dc = [-1, 0, 1, 1, 1, 0, -1, -1]

        for i in range(8):
            next_x = start_x + dr[i]
            next_y = start_y + dc[i]

            # 범위 설정
            if 0 &lt;= next_x &lt;= r - 1 and 0 &lt;= next_y &lt;= c - 1:
                if visited[next_x][next_y] == False and board[next_x][next_y] == 0:
                    visited[next_x][next_y] = True # 위험 지대 설정
    # board 순회
    for i in range(r):
        for j in range(c):
            if board[i][j] == 1:
                danger_search(i,j)
    # row의 행에 있는 True값들을 먼저 sum()하고 행들의 합을 sum()
    danger = sum(sum(row) for row in visited)
    return r*c - danger</code></pre>
<ol>
<li>매개변수가 2차원 배열만 주어졌을 때는, <code>r = len(board), c = len(board[0])</code>으로 설정하고 <code>visited = [[False] * c for i in range(r)]</code>로 초기화한다.</li>
</ol>
<ul>
<li>visited는 한번 방문한 곳을 방문하지 않기 위한 장치</li>
</ul>
<ol start="2">
<li>완전 탐색을 시작하는데 <code>board[i][j]</code>의 값이 1이면 함수를 호출한다.(이때, 호출하면서 지뢰 지점은 True로 변경됨)
2-1. 여기가 중요한데, 리트코드 문제는 <code>deque()</code>가 있었지만, 여기서는 <strong>지뢰의 인접 노드만 방문</strong>하면 되기 때문에 굳이 필요가 없어서 <code>deque()</code>를 사용하지 않았다.
2-2. 마찬가지로 11시방향을 기준으로 해서 dr, dc를 세팅
2-3. 가고자 하는 다음 방향을 next_x, next_y로 초기화
2-4. board 범위 내에 있어야 하므로 <code>if 0 &lt;= next_x &lt;= r - 1 and 0 &lt;= next_y &lt;= c - 1:</code>로 범위 세팅</li>
<li>board의 전체 크기인 r * c에서 전역 변수인 visited의 True값들을 뺀 것을 리턴</li>
</ol>
<p>💡<code>sum(sum(row) for row in visited)</code> : visited와 board가 같은 2차원 배열인 것을 고려했을 때, visited를 순회하는 row는 <code>visited[0]</code>, <code>visited[1]</code>, ..., <code>visited[5]</code>가 
제너레이터 표현식을 통해 각 행의 합을 계산하고, 그 합들을 다시 합산하여 최종 결과를 반환한다.</p>