<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/49994">https://school.programmers.co.kr/learn/courses/30/lessons/49994</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>게임 캐릭터를 4가지 명령어를 통해 움직이려 합니다. 명령어는 다음과 같습니다.</p>
<pre><code class="language-txt">U: 위쪽으로 한 칸 가기

D: 아래쪽으로 한 칸 가기

R: 오른쪽으로 한 칸 가기

L: 왼쪽으로 한 칸 가기</code></pre>
<p>캐릭터는 좌표평면의 (0, 0) 위치에서 시작합니다. 좌표평면의 경계는 왼쪽 위(-5, 5), 왼쪽 아래(-5, -5), 오른쪽 위(5, 5), 오른쪽 아래(5, -5)로 이루어져 있습니다.</p>
<p>요구사항: 이때, 우리는 게임 캐릭터가 지나간 길 중 <strong>캐릭터가 처음 걸어본 길의 길이를 구하려고 합니다.</strong>
(단, 좌표평면의 경계를 넘어가는 명령어는 무시합니다.</p>
<p>명령어가 매개변수 dirs로 주어질 때, 게임 캐릭터가 처음 걸어본 길의 길이를 구하여 return하라. </p>
<h2 id="🚧제약조건">🚧제약조건</h2>
<p>dirs는 string형으로 주어지며, 'U', 'D', 'R', 'L' 이외에 문자는 주어지지 않습니다.
dirs의 길이는 500 이하의 자연수입니다.</p>
<p>먼저 문제 요구사항의 &quot;길&quot;이 무엇을 의미하는지 알아야 한다. 나는 문제의 예시대로 노트에 좌표 평면을 그리고 명령어에 매핑된 위치로 이동시켰는데, 길이 중복되면 안되는 것을 파악했더니, <strong>좌표평면 -&gt; 그래프 모델링의 문제</strong>로 치환되었다.</p>
<p>🔖Path: 그래프의 꼭짓점(노드)들을 연결하는 간선(엣지)들의 순서 있는 열을 의미하며, 일반적으로 <strong><span style="color: red;">같은 꼭짓점을 두 번 이상 방문하지 않는 단순 경로(Simple path)</span></strong>를 가리킵니다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>String 타입의 dirs를 for문으로 순회하는데, 특정 명령어를 좌표에 매핑한다.
1.1. if 경계에 포함된 다음 이동 좌표에 visted 값이 true면 dist 증가 x</li>
<li>dist를 리턴</li>
</ol>
<p>💡디버깅을 하면서 visited는 정점만 체크하기 때문에 가지 않은 path라고 하더라도 무시해서 예상했던 결과가 나오지 않았다. 
따라서 visited 대신에 <strong>커스텀 클래스 Coordinate를 도입</strong>해서 다음과 같이 코드를 구현하였다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class VisitedLenSolution {
    public int solution(String dirs) {
        // 원점 초기화 &amp;&amp; 경계값 먼저 설정
        // 원점 -&gt; (5, 5); 어짜피 중복된 좌표만 제외하면 되니까
        int curR = 5;
        int curC = 5;
        int dist = 0;
        // key: Vertex, value: outgoing Vertex
        Map&lt;Coordinate, Coordinate&gt; map = new HashMap&lt;&gt;();

        for (int i = 0; i &lt; dirs.length(); i++) {
            switch (dirs.charAt(i)) {
                case 'U':
                    int nextR = curR - 1;
                    if (!map.containsValue(new Coordinate(nextR, curC)) &amp;&amp; 0 &lt;= nextR &amp;&amp; nextR &lt;= 10 &amp;&amp; 0 &lt;= curC &amp;&amp; curC &lt;= 10) {
                        map.put(new Coordinate(curR, curC), new Coordinate(nextR, curC));
                        dist += 1;
                        curR = nextR;
                    }
                    break;
                case 'D':
                    nextR = curR + 1;
                    if (!map.containsValue(new Coordinate(nextR, curC)) &amp;&amp; 0 &lt;= nextR &amp;&amp; nextR &lt;= 10 &amp;&amp; 0 &lt;= curC &amp;&amp; curC &lt;= 10) {
                        map.put(new Coordinate(curR, curC), new Coordinate(nextR, curC));
                        dist += 1;
                        curR = nextR;
                    }
                    break;
                case 'L':
                    int nextC = curC - 1;
                    if (!map.containsValue(new Coordinate(curR, curC)) &amp;&amp; 0 &lt;= curR &amp;&amp; curR &lt;= 10 &amp;&amp; 0 &lt;= nextC &amp;&amp; nextC &lt;= 10) {
                        map.put(new Coordinate(curR, curC), new Coordinate(curR, nextC));
                        dist += 1;
                        curC = nextC;
                    }
                    break;
                case 'R':
                    nextC = curC + 1;
                    if (!map.containsValue(new Coordinate(curR, curC)) &amp;&amp; 0 &lt;= curR &amp;&amp; curR &lt;= 10 &amp;&amp; 0 &lt;= nextC &amp;&amp; nextC &lt;= 10) {
                        map.put(new Coordinate(curR, curC), new Coordinate(curR, nextC));
                        dist += 1;
                        curC = nextC;
                    }
            }
        }
        return dist;
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
<h2 id="🚩pain-point">🚩Pain point</h2>
<ol>
<li>new로 만든 <strong>Coordinate(커스텀) 객체의 참조값은 value가 같더라도 항상 힙 영역에 저장</strong>되기 때문에 equals/hashcode를 오버라이딩하지 않으면 중복 검사를 할 수 없다.
👉간선 중복 검사를 하기 위한 다른 솔루션이 필요</li>
</ol>
<hr />
<p>👨‍🏫위의 코드가 더럽기 때문에 <span style="color: red;"><strong>패턴 미학습</strong></span>으로 간주하고, 책으로 코드 설계~코드 구현까지 다시 하겠습니다!</p>
<h1 id="💻모범-코드-답안">💻모범 코드 답안</h1>
<pre><code class="language-java">public class VisitedLenSolution {
    // 좌표평면을 벗어나는지 체크하는 메서드
    private static boolean isValidMove(int nx, int ny) {
        return -5 &lt;= nx &amp;&amp; nx &lt;= 5 &amp;&amp; -5 &lt;= ny &amp;&amp; ny &lt;= 5;
    }

    // 다음 좌표 결정을 위한 해시맵 생성
    private static final Map&lt;Character, int[]&gt; location = new HashMap&lt;&gt;();

    private static void initLocation() {
        location.put('U', new int[]{0, 1});
        location.put('D', new int[]{0, -1});
        location.put('L', new int[]{-1, 0});
        location.put('R', new int[]{1, 0});
    }

    public int solution(String dirs) {
        initLocation();
        int x = 0, y = 0;

        // 겹치는 좌표는 1개로 처리하기 위해 해시셋으로 초기화
        // edge 카운팅 용도
        Set&lt;String&gt; edges = new HashSet&lt;&gt;();

        for (int i = 0; i &lt; dirs.length(); i++) {
            int[] offset = location.get(dirs.charAt(i));
            int nx = x + offset[0];
            int ny = y + offset[1];

            // 좌표평면을 벗어나면 아래의 코드를 스킵
            if (!isValidMove(nx, ny)) {
                continue;
            }

            // A -&gt; B와 B -&gt; A는 같은 경우로 취급
            edges.add(x + &quot; &quot; + y + nx + &quot; &quot; + ny);
            edges.add(nx + &quot; &quot; + ny + x + &quot; &quot; + y);

            // 좌표를 이동했으므로 업데이트
            x = nx;
            y = ny;
        }

        return edges.size() / 2; // simple path이므로 2를 나눔
    }
}</code></pre>
<ul>
<li>🤔<code>edges.add(x + &quot; &quot; + y + nx + &quot; &quot; + ny);</code>가 되는 이유
👉<code>+</code> 연산을 할 때 <strong>피연산자 중 하나가 String</strong>이면 나머지도 문자열로 변환되는 규칙에 따른다는 것이다.</li>
</ul>
<blockquote>
<p>The compiler internally transforms the”+” operator to a StringBuilder (or StringBuffer) class and its append() method.
Since the “+” operator silently converts the argument to a String (using the toString() method for objects), we avoid the NullPointerException. However, we need to consider if our final string result works for us with the “null” in the string body.</p>
</blockquote>
<h2 id="👨⚖️지켜야-할-행동-강령">👨‍⚖️지켜야 할 행동 강령</h2>
<ol>
<li><strong>중복을 포함하지 않는다</strong>는 문장이 나오면(&quot;캐릭터가 처음 걸어본 길의 길이&quot;) <strong>해시셋</strong>(인터페이스 Set이 보편적)을 생각할 것</li>
<li>좌표평면을 이동하라는 <strong>명령어</strong>가 있으면 switch-case문으로 분류하는 것이 아니라, <strong>HashMap을 생각해서 <code>init()</code> 메서드 안에서 초기화</strong>할 것</li>
<li>좌표평면을 벗어나는지 체크하는 boolean 리턴 타입 메서드는 좌표 문제에 단골로 등장하기 때문에 <strong>solution 메서드와 분리해서 구현</strong>할 것</li>
</ol>
<p>💡문제에서 <strong>좌표평면</strong>이라고 명시했으면, 각 점을 정점으로 보고 연산을 좌표쌍 자체로 처리하면 되므로, <strong><span style="color: red;">반드시 2차원 배열 인덱스(r, c)로 변환할 필요는 없다.</span></strong> (왜냐하면 각 정점이 유일한 위치를 갖기 때문)</p>
<h3 id="좌표평면-vs-그리드">좌표평면 vs 그리드</h3>
<ul>
<li><p>좌표평면 문제는 보통 x, y를 실체 좌표로 쓰며, 이동과 경계 체크를 좌표 비교로 처리한다.</p>
</li>
<li><p>반대로 격자(grid) 탐색(BFS/DFS)은 주로 파라미터가 2차원 배열로 주어주기 때문에 관습적으로 row, col 인덱스로 다루고, 배열 경계와 visited 배열로 관리한다.</p>
</li>
</ul>
<hr />
<p>📚참고자료
🔗문자열 결합: <a href="https://www.baeldung.com/java-string-concatenation">https://www.baeldung.com/java-string-concatenation</a></p>