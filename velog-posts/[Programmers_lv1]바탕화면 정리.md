<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/161990">https://school.programmers.co.kr/learn/courses/30/lessons/161990</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<ol>
<li>파일들은 바탕화면의 &quot;격자칸&quot;에 위치 </li>
</ol>
<p>-&gt; 격자칸을 어떻게 방문해야 하지?</p>
<ol start="2">
<li>바탕화면의 &quot;격자점&quot;들은 바탕화면의 가장 왼쪽 위를 <code>(0, 0)</code>으로 시작해 (세로 좌표, 가로 좌표)로 표현하고, 빈칸은 <code>&quot;.&quot;</code>, 파일이 있는 칸은 <code>&quot;#&quot;</code>의 값을 가집니다.</li>
</ol>
<p>요구사항: 바탕화면의 파일(완전탐색으로 파일 개수 파악 먼저 필요)들을 한 번에 삭제하기 위해(<del>bfs가 유리, 왜냐? 최단 거리이기 때문</del>)
<strong>&quot;최소한의 이동거리&quot;를 갖는 &quot;드래그&quot;의 시작점과 끝점을 담은 정수 배열을 리턴</strong>
    -&gt; 드래그의 시작점이 (lux, luy), 끝점이 (rdx, rdy)라면 정수 배열 <code>[lux, luy, rdx, rdy]</code>를 return</p>
<h2 id="🚧제약-조건">🚧제약 조건</h2>
<ol>
<li><code>wallpaper[i][j]</code>는 &quot;#&quot; 또는 &quot;.&quot;의 값만 가집니다.</li>
<li>바탕화면에는 적어도 하나의 파일이 있습니다.</li>
<li>드래그 시작점 (lux, luy)와 끝점 (rdx, rdy)는 <code>lux &lt; rdx, luy &lt; rdy</code>를 만족해야 합니다.</li>
<li>wallpaper의 모든 원소의 길이는 동일합니다. == <code>wallpaper[i]</code>의 원소 길이는 모두 같다. e.g. <code>[5,5,5]</code>, <code>[10,10,10,10,10]</code></li>
<li><code>wallpaper[i][j]</code>는 바탕화면에서 <code>i + 1</code>행 <code>j + 1</code>열에 해당하는 <strong>칸의 상태</strong>를 나타냅니다.
-&gt; 파일은 격자칸에 존재하기 때문에, 대각선으로 드래그한 좌표도 생각하라는 의미</li>
</ol>
<h3 id="🔖용어-정리">🔖용어 정리</h3>
<p>격자칸: 컴퓨터 바탕화면 == 각 칸이 정사각형인 격자판 == 문자열 배열 wallpaper == 파일들은 바탕화면의 격자칸에 위치</p>
<p>격자점: 바탕화면(격자칸)의 가장 왼쪽 위를 <code>(0, 0)</code>으로 시작해 (세로 좌표, 가로 좌표)로 표현합니다.</p>
<p>드래그: 바탕화면의 <strong>격자점</strong> <code>S(lux, luy)</code>를 마우스 왼쪽 버튼으로 클릭한 상태로 <strong>격자점</strong> <code>E(rdx, rdy)</code>로 이동한 뒤 마우스 왼쪽 버튼을 떼는 행동을 말함. </p>
<ul>
<li>이때, &quot;시점 S에서 종점 E로 드래그한다&quot;고 표현하고 점 S와 점 E를 각각 드래그의 시작점, 끝점이라고 표현한다.</li>
<li>시점 S에서 종점 E로 드래그를 하면 바탕화면에서 두 격자점을 각각 왼쪽 위, 오른쪽 아래로 하는 직사각형 내부에 있는 모든 파일이 선택됨</li>
</ul>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p><del>파라미터로 주어지는 격자칸에 있는 wallpaper와 격자점 형태로 주어진 grid를 따로 만들어야 한다.</del>
문자열 배열 wallpaper를 grid 형태로 만들되, <strong>종점의 좌표에 약간 변형</strong>을 주어야 한다. 
즉, 격자칸이 정사각형이라는 점을 이용해서 시점은 처음 파일이 있는 좌표를 사용하고, 종점은 <strong>마지막 파일을 만나는 좌표에서 각각 1을 더한다.</strong> (e.g. 종점이 (2,3) -&gt; (3, 4)), (∵🚧제약조건 5와 매핑)</p>
<ol>
<li>이중 for문으로 완전 탐색을 한다.</li>
<li><code>if grid[i][j] == '#'</code>: 그 좌표를 리스트에 저장</li>
</ol>
<ul>
<li>이때, 좌표는 커스텀 클래스 Coordinate로 생성해서 리스트에 저장</li>
</ul>
<ol start="3">
<li>정사각형 칸에 있는 파일들을 만나면: 그 좌표들을 리스트에 모두 저장한다.</li>
<li><code>[r의 최솟값, c의 최솟값, r의 최댓값, c의 최댓값]</code>을 리턴하기 위해 리스트에 있는 좌표들을 r의 집합과 c의 집합으로 나누고, 각각 최댓값, 최솟값을 구해서 배열을 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class DesktopDragSolution {
    public int[] solution(String[] wallpaper) {
        int m = wallpaper.length;
        int n = wallpaper[0].length();

        // 먼저 완전 탐색으로 파일 개수 파악(어짜피 최댓값은 50이라 10^2이므로 쌉가능)
        //* 수정: 파일의 좌표를 리스트에 추가

        char[][] grid = new char[m][n];
        // String 1차원 배열 -&gt; char[] 2차원 배열(String은 객체 타입이라 인덱싱 접근이 안되므로)
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                char c = wallpaper[i].charAt(j);
                grid[i][j] = c;
            }
        }

        // 완전 탐색으로 totalFile의 좌표를 일단 모음
        //! totalFile로 파일 개수 파악 필요
        int totalFile = 0;
        List&lt;Coordinate&gt; list = new ArrayList&lt;&gt;();
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                if (grid[i][j] == '#') {
                    list.add(new Coordinate(i, j));
                    totalFile++;
                }
            }
        }


        List&lt;Integer&gt; rList = new ArrayList&lt;&gt;();
        List&lt;Integer&gt; cList = new ArrayList&lt;&gt;();

        // r, c 좌표 각각 저장
        for (Coordinate coordinate : list) {
            int r = coordinate.r;
            int c = coordinate.c;
            rList.add(r);
            cList.add(c);
        }

        // 최솟값, 최댓값 추출
        // 종점에 각각 1을 증가
        Integer rMin = rList.get(0);
        Integer rMax = rList.get(rList.size() - 1) + 1;
        Integer cMin = cList.get(0);
        Integer cMax = cList.get(cList.size() - 1) + 1;

        return new int[]{rMin, cMin, rMax, cMax};
    }

    private static class Coordinate {
        int r;
        int c;

        public Coordinate(int r, int c) {
            this.r = r;
            this.c = c;
        }
    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/986fc3cc-75a2-4967-8341-df46c3ad1a14/image.png" /></p>
<p>뭔가 이상하다...? 극단값을 구해서 배열로 리턴했는데 내가 원했던 값이 나오지 않았다. 그래서 테스트 케이스의 그림을 다시 한번 살펴봤다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/dc9de3ae-4933-4f90-889d-b64a6a8aec42/image.png" /></p>
<h2 id="🚩pain-point">🚩Pain Point</h2>
<ol>
<li>배열 리스트는 좌표를 그대로 저장하는데, row와 col의 위치가 뒤죽박죽이기 때문에 <strong>정렬 알고리즘</strong>이 필요함.</li>
<li>완전 탐색으로 파일을 포함한 좌표를 저장하기 때문에 굳이 파일 카운팅할 필요가 없음.</li>
<li>행과 열을 리스트에 모아서 저장하기 때문에 굳이 커스텀 클래스를 만들 필요도 없음.</li>
</ol>
<p>위의 Pain Point를 반영해서 코드를 새로 구현하였더니 성공적으로 제출을 하였다.</p>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public class DesktopDragSolution {
    public int[] solution(String[] wallpaper) {
        int m = wallpaper.length;
        int n = wallpaper[0].length();

        // 먼저 완전 탐색으로 파일 개수 파악(어짜피 최댓값은 50이라 10^2이므로 쌉가능)
        //* 수정: 파일의 좌표를 리스트에 추가

        char[][] grid = new char[m][n];
        // String 1차원 배열 -&gt; char[] 2차원 배열(String은 객체 타입이라 인덱싱 접근이 안되므로)
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                char c = wallpaper[i].charAt(j);
                grid[i][j] = c;
            }
        }

        // 완전 탐색으로 totalFile의 좌표를 일단 모음
        List&lt;Integer&gt; rList = new ArrayList&lt;&gt;();
        List&lt;Integer&gt; cList = new ArrayList&lt;&gt;();
        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                if (grid[i][j] == '#') {
                    rList.add(i);
                    cList.add(j);
                }
            }
        }

        // 좌표 오름차순으로 정렬
        rList.sort(Integer::compare);
        cList.sort(Integer::compare);


        // 최솟값, 최댓값 추출
        Integer minR = rList.get(0);
        Integer minC = cList.get(0);
        Integer maxR = rList.get(rList.size() - 1) + 1;
        Integer maxC = cList.get(cList.size() - 1) + 1;

        return new int[]{minR, minC, maxR, maxC};
    }
}</code></pre>
<p>⏰앗! 정렬 알고리즘은 $$O(nlogn)$$을 차지하기 때문에 정렬 알고리즘 없이 <strong>최소, 최대 라이브러리</strong>를 사용하면 깔끔하게 $$O(n)$$으로 처리할 수 있다.</p>
<h1 id="🤖표준-코드-구현">🤖표준 코드 구현</h1>
<pre><code class="language-java">int minR = Integer.MAX_VALUE;
int minC = Integer.MAX_VALUE;
int maxR = Integer.MIN_VALUE;
int maxC = Integer.MIN_VALUE;

for (int i = 0; i &lt; m; i++) {
    for (int j = 0; j &lt; n; j++) {
        if (wallpaper[i].charAt(j) == '#') {  // grid 없이 직접 접근
            minR = Math.min(minR, i);
            minC = Math.min(minC, j);
            maxR = Math.max(maxR, i);
            maxC = Math.max(maxC, j);
            totalFile++;
        }
    }
}
return new int[]{minR, minC, maxR + 1, maxC + 1};</code></pre>
<ul>
<li><code>wallpaper[i].charAt(j)</code>로 <strong>따로 grid를 만들 필요없이</strong> 파라미터로 준 문자열 배열 wallpaper로 <strong>2차원으로 직접 접근</strong>이 가능하다.</li>
<li><strong>min/max 메서드</strong>를 사용하면 rList, cList 리스트 초기화 및 정렬 없이 <strong>2D 그리드에서 극단값을 찾는 데 표준 기법</strong>이라고 한다.  </li>
</ul>
<h1 id="✍️교훈">✍️교훈</h1>
<ol>
<li><p>이 문제의 핵심은 &quot;최소한의 이동거리&quot;를 <strong>좌표의 극단값으로 치환해서 생각</strong>할 수 있느냐를 물어본 거였음.</p>
</li>
<li><p>⚠️<span style="color: red;"><strong>2d grid라고 해서 무지성으로 bfs 또는 dfs를 사용하면 안 된다!</strong></span> </p>
</li>
</ol>
<ul>
<li>bfs 또는 dfs를 사용하기 위해서는 자료구조가 <code>G=(V,E)</code>인 그래프 형태여야함. 그런데 코테에서는 보통 그래프를 _암시적 그래프_로 주기 때문에 이 암시적 그래프가 무엇인지를 파악해야함.</li>
</ul>
<p>그런데, 문제에서 &quot;코딩테스트를 준비하는 머쓱이는 프로그래머스에서 문제를 풀고 나중에 다시 코드를 보면서 공부하려고 작성한 코드를 <strong>컴퓨터 바탕화면(격자칸)에 아무 위치에나 저장</strong>해 둡니다.&quot;
👉아무 위치에나 저장한다 == 파일을 노드로 생각했을 때, 간선으로 연결된 그래프가 아님!</p>
<h2 id="💡어디서-암시적-그래프를-사용하라는-키워드를-발견할-수-있는가">💡어디서 암시적 그래프를 사용하라는 키워드를 발견할 수 있는가?</h2>
<p>👉문제에서 &quot;인접한&quot;, &quot;연결된&quot;, &quot;그룹&quot;, &quot;클러스터&quot;, &quot;영역&quot; 같은 단어가 나오면 연결성을 의심하여 그래프 자료구조를 떠올리자. &gt; 그래프 자료구조와 관련된 well-known 알고리즘은 BFS 또는 DFS를 사용할 것.</p>