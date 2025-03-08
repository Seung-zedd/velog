<h1 id="📜문제-해석">📜문제 해석</h1>
<p>머쓱이는 RPG게임을 하고 있습니다. 게임에는 up, down, left, right 방향키가 있으며 각 키를 누르면 <strong>위, 아래, 왼쪽, 오른쪽으로 한 칸씩 이동</strong>합니다. 머쓱이가 입력한 방향키의 배열 keyinput와 맵의 크기 board이 매개변수로 주어집니다. <strong>&quot;캐릭터는 항상 [0,0]에서 시작&quot;</strong>할 때 키 입력이 모두 끝난 뒤에 캐릭터의 좌표 [x, y]를 return하라.</p>
<ul>
<li>🚧제약 조건 -</li>
</ul>
<ol>
<li>예를 들어 board의 가로 크기가 9라면 캐릭터는 왼쪽으로 최대 [-4, 0]까지 오른쪽으로 최대 [4, 0]까지 이동</li>
<li>board은 [가로 크기, 세로 크기] 형태로 주어집니다.</li>
<li>board의 가로 크기와 세로 크기는 홀수입니다.</li>
<li>board의 크기를 벗어난 방향키 입력은 무시합니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>board의 범위를 dx, dy로 나눔
1-1. 만약 board의 범위를 넘어가면 입력값을 무시(continue로 사용할 예정)</li>
<li>keyinput은 str타입인데, 이것을 파싱해서 int값으로 변환하기 위한 장치 필요
즉, 현재 상태에서 사칙연산하는 과정이 필요하다.
2-1. keyinput은 순서대로 입력되기 때문에 for문을 사용
2-2. 파싱하는 장치는 딕셔너리를 사용해서 {str_key: int_val}로 초기화를 한다.</li>
</ol>
<p>먼저 절반 테케만 맞은 코드 구현은 다음과 같다.</p>
<h1 id="💻1차-코드-구현">💻1차 코드 구현</h1>
<pre><code class="language-python">def solution(keyinput, board):
    answer = [0, 0] # 캐릭터는 항상 [0,0]에서 시작
    dx = answer[0]
    dy = answer[1]
    range_x = board[0] // 2
    range_y = board[1] // 2

    dict = {&quot;left&quot;: -1, &quot;right&quot;: 1, &quot;up&quot;: 1, &quot;down&quot;: -1}

    for s in keyinput:
        # 전체 범위 설정
        if -range_x &lt; dx &lt; range_x and -range_y &lt; dy &lt; range_y:

            if s == &quot;left&quot; or s == &quot;right&quot;:
                dx += dict[s] # 좌표 업데이트
            else:
                dy += dict[s]
        # 크기를 벗어났으므로 입력값 무시
        else:
            continue
    return [dx, dy]</code></pre>
<p>나는 한참을 고민했다. &quot;왜 절반의 테케만 통과한거지?&quot; 그래서 range_x와 range_y 범위를 AND 연산을 하면 직사각형 넓이가 되는데, 여기서 둘레에 해당하는 케이스가 걸려서 그런 것 같다고 예측했다.
즉, <code>board = [7, 9]</code>가 주어졌을 때, x = 3인데, 이동할 y값이 자유로우면 전체 범위를 벗어나지 않기 때문에 입력값이 허용이 된다.</p>
<p>💡현재 위치가 아니라, 다음 좌표를 범위 내에서 if문으로 검사하면 어떨까?
👉그러니까 입력값을 for문으로 순회할 때, 전체 직사각형 범위를 outer if문으로 두고, 다음 좌표를 검사할 때를 각각 inner if문으로 검사를 하는 것이다.</p>
<p>이렇게 하면 테케를 모두 통과하게 된다.</p>
<h1 id="💻최종-코드-구현">💻최종 코드 구현</h1>
<pre><code class="language-python">def solution(keyinput, board):
    answer = [0, 0] # 캐릭터는 항상 [0,0]에서 시작
    dx = answer[0]
    dy = answer[1]
    range_x = board[0] // 2
    range_y = board[1] // 2

    my_dict = {&quot;left&quot;: -1, &quot;right&quot;: 1, &quot;up&quot;: 1, &quot;down&quot;: -1}

    for s in keyinput:
        # 전체 범위 설정
        if -range_x &lt;= dx &lt;= range_x and -range_y &lt;= dy &lt;= range_y:
            # 다음 이동할 좌표의 범위를 검사
            if (s == &quot;left&quot; and dx - 1 &gt;= -range_x) or (s == &quot;right&quot; and dx + 1 &lt;= range_x):
                dx += my_dict[s] # 좌표 업데이트
            elif (s == &quot;down&quot; and dy - 1 &gt;= -range_y) or (s == &quot;up&quot; and dy + 1 &lt;= range_y):
                dy += my_dict[s]
        # 크기를 벗어났으므로 입력값 무시
        else:
            continue
    return [dx, dy]</code></pre>
<p>🤖파이썬의 내장 함수 이름을 변수로 사용하면, 해당 이름이 내장 함수 대신 변수에 바인딩되어 <span style="color: red;">이후에 내장 함수를 호출할 수 없게 됩니다.</span> 따라서 my_dict와 같이 <strong>내장 함수 이름과 충돌하지 않는 변수 네이밍을 하는 것이 좋습니다.</strong></p>