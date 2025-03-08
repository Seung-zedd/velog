<h1 id="📜문제-해석">📜문제 해석</h1>
<p>점 네 개의 좌표를 담은 이차원 배열  dots가 다음과 같이 매개변수로 주어집니다.
<code>[[x1, y1], [x2, y2], [x3, y3], [x4, y4]]</code>
주어진 네 개의 점을 두 개씩 이었을 때, 두 직선이 평행이 되는 경우가 있으면 1을 없으면 0을 return 하도록 solution 함수를 만드시오.</p>
<p>🚧제약 조건
(⭐⭐⭐⭐⭐)1. 서로 다른 두 개 이상의 점이 겹치는 경우는 없습니다.
2. 두 직선이 겹치는 경우(일치하는 경우)에도 1을 return 해주세요.
s1: mx + n, s2: m'x + n'
=&gt; 두 직선이 서로 평행하면 m=m', n≠n', 두 직선이 서로 일치하면 m=m' and n=n'</p>
<ol start="3">
<li>임의의 두 점을 이은 직선이 x축 또는 y축과 평행한 경우는 주어지지 않습니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>itertools의 combinations를 이용하여 dots 중 임의의 2개를 선택하는 조합을 dot_ls에 저장한다.</li>
<li>임의의 두 직선의 기울기와 y절편을 저장하기 위한 딕셔너리를 초기화한다.</li>
</ol>
<ul>
<li><code>line_dict = {(x1, y1), (x2, y2): (gradient, y_intercept)}</code>
📢두 직선이 평행할 조건은 직선의 스칼라량과 아무 관련이 없음!</li>
</ul>
<ol start="3">
<li>조합의 반복문이 끝나면 저장했던 딕셔너리의 values()들을 순회하는데 여기서 두 직선이 평행할 조건(s1: mx + n, s2: m'x + n'라고 할 때, m=m', n≠n') 또는 두 직선이 일치할 조건(s1: mx + n, s2: m'x + n'라고 할 때, m=m', n=n')이 참이면 1을 리턴하게 한다.</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">from itertools import combinations

def solution(dots):
    dot_ls = list(combinations(dots, 2))
    line_dict = {} # 라인값을 일단 모두 저장

    for i in dot_ls:
        x1 = i[0][0]
        y1 = i[0][1]
        for j in i:
            x2, y2 = i[1][0], i[1][1]
            if x2 and y2:
                break
        gradient = (y2 - y1) / (x2 - x1)
        # y절편을 구함
        y_intercept = y1 - gradient * x1
        line_dict[(x1,y1), (x2, y2)] = gradient, y_intercept

    values = list(line_dict.values())
    for i in range(len(values)):
        for j in range(i + 1, len(values)):
            if values[i][0] == values[j][0] and (values[i][1] == values[j][1] or values[i][1] != values[j][1]): # 일치하거나 평행하는 경우
                return 1
    return 0</code></pre>
<p>🚩이 코드의 문제점은 combinations을 사용해서 모든 가능한 점 쌍을 생성하지만, 서로 점이 겹치는 직선들도 비교하게 되어서 &quot;서로 다른 4개의 점&quot;으로 이루어진 2개의 직선이 평행한지 여부를 알 수 없다는 것이다.</p>
<p>🤔어짜피 문제에서는 서로 다른 4개의 점을 이어서 2개의 직선이 평행하거나 일치하기만 하면 되니까, <strong>분할</strong> 방법을 사용해도 되지 않을까? 해서 Copilot한테 도움을 받아 다음과 같이 코드를 수정하였다.</p>
<h1 id="⚙️수정된-코드-설계">⚙️수정된 코드 설계</h1>
<ol>
<li>4개의 점으로 만들 수 있는 서로 다른 두 직선의 모든 가능한 조합을 미리 정의한다. (총 3가지 조합)
👉이때, itertools의 combinations 모듈을 사용하는 것이 아니라, <strong>수동으로 dots들을 분할</strong>한다.</li>
<li>각 조합에 대해 점들의 <strong>x, y 좌표를 언패킹</strong>한다.</li>
<li>각 직선의 <strong>기울기</strong>를 계산합니다.</li>
<li>선의 기울기가 같으면 평행하거나 일치하므로 1을 반환하고, 모든 조합을 확인했는데도 평행한 직선이 없으면 0을 반환합니다.</li>
</ol>
<h1 id="🤖코드-구현">🤖코드 구현</h1>
<pre><code class="language-python">def solution(dots):
    # 4개의 점으로 만들 수 있는 서로 다른 두 직선의 모든 가능한 조합
    line_pairs = [
    # 각 쌍은 ((첫번째 직선의 두 점 인덱스), (두번째 직선의 두 점 인덱스))를 나타냄
        ((0, 1), (2, 3)),  # (dots[0], dots[1])와 (dots[2], dots[3])
        ((0, 2), (1, 3)),  # (dots[0], dots[2])와 (dots[1], dots[3])
        ((0, 3), (1, 2)),  # (dots[0], dots[3])와 (dots[1], dots[2])
    ]

    for (a, b), (c, d) in line_pairs:
        x1, y1 = dots[a]
        x2, y2 = dots[b]
        x3, y3 = dots[c]
        x4, y4 = dots[d]

        # 기울기 계산
        gradient1 = (y2 - y1) / (x2 - x1)
        gradient2 = (y4 - y3) / (x4 - x3)

        # 기울기가 같으면 평행 또는 일치
        if gradient1 == gradient2:
            return 1
    return 0</code></pre>
<h1 id="📝이-문제의-시사점">📝이 문제의 시사점</h1>
<p>&quot;2개의 점들로 조합한 직선들의 기울기를 모두 DP를 사용해서 저장한 후, 나중에 비교해야지!&quot; 라고 해서 로직을 더욱 복잡하게 꼬았는데, 문제 요구사항을 자세히 보면, &quot;<strong>두 직선이 평행이 되는 경우</strong>가 뭐지? 아! L1: y=mx+n와 L2: y=m'x+n'일 때, <strong>두 직선의 기울기가 같은 m=m'</strong>이면 되겠구나!&quot;로 치환해서 생각하면 단순하게 풀리는 문제이기 때문에 문제를 제발 꼼꼼히 분석하자!</p>