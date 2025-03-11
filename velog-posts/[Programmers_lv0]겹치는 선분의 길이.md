<h1 id="📜문제-해석">📜문제 해석</h1>
<p>수직선 상의 선분 3개가 평행하게 놓여 있습니다. 세 선분의 시작과 끝 좌표가 <code>[[start, end], [start, end], [start, end]]</code> 형태로 들어있는 2차원 배열 lines가 매개변수로 주어질 때,
<strong>&quot;두 개 이상의 선분이 겹치는 부분의 길이&quot;</strong>를 return하라.(만약 겹치는 부분이 없으면 0을 리턴)</p>
<p>🚧제약 조건</p>
<ol>
<li>모든 선분은 길이가 1 이상입니다.</li>
<li>lines의 원소는 [a, b] 형태이며, a, b는 각각 선분의 양 끝점 입니다.</li>
</ol>
<ul>
<li>-100 ≤ a &lt; b ≤ 100</li>
</ul>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>일단, 2개의 선분이 겹친다는 것을 어떻게 파악할 수 있지?</li>
</ol>
<ul>
<li>먼저 가장 쉬운 2개의 선분만 겹치는 경우를 생각해보자.</li>
<li>노트에 수직선을 그려보면, <code>2&lt;=x&lt;=5 and 3&lt;=x&lt;=9</code>의 케이스가 있다. 이것을 집합으로 표현하면, <code>{2,3,4,5} and {3,4,5,6,7,8,9}</code>가 되는데, 이들의 교집합을 찾아보면 <code>{3,4,5}</code> 즉, <code>[3,5]</code>의 구간이 나온다. 따라서 <code>len(intersect_set) - 1</code>로 표현하면 된다.</li>
</ul>
<ol start="2">
<li>두 개 이상의 선분 겹치는 교집합을 하나의 함수로 만든 후, 그것을 리턴함 -&gt; 리턴한 값들을 전부 <strong>합집합 연산</strong>을 통해 리턴, 그러니까 <code>len(Union_result) - 1</code>로 표현하면 된다.</li>
</ol>
<p>💡지난 시간 때 푼 평행에서의 line_pair 아이디어를 활용해서 이번에도 2개의 직선 조합을 찾기 위해 똑같이 구현해 보았다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(lines):
    def find_overlapped_lines():
        line = [(0, 1), (0, 2), (1, 2)]
        overlapped_line = []
        # 임의의 2개 선분을 먼저 추출
        for u, v in line:
            line_a = []
            line_b = []
            a, b = lines[u]
            c, d = lines[v]

            if b - a &lt;= d - c:
                line_a = [i for i in range(a, b + 1)]
                line_b = [j for j in range(c, d + 1)]
                # 일단 겹치는 부분을 모두 저장
                if set(line_a) &amp; set(line_b):
                    overlapped_line.append(set(line_a) &amp; set(line_b))
        return overlapped_line

    intersect_set =  find_overlapped_lines()
    # 3개 선분을 모두 추출
    if len(intersect_set) &gt; 1:
        union_set = intersect_set[0] | intersect_set[1] | intersect_set[2]
        return len(union_set) - 1  
    # 2개 선분이 겹치는 경우
    elif len(intersect_set) == 1:
        return len(intersect_set[0]) - 1
    # 아무 선분도 겹치지 않을 경우
    else:
        return 0</code></pre>
<p>🚩이렇게 작성했더니 list index out of range 런타임 에러가 발생했다. 잘 안 보여서 AI한테 물어봤더니 <code>intersect_set[num]</code>으로 인덱싱 하는 부분에서 원소가 그보다 적으니 없는 부분을 인덱싱해서 그런다고 답변을 해줬다.</p>
<h1 id="🤖수정한-코드-구현">🤖수정한 코드 구현</h1>
<pre><code class="language-python">def solution(lines):
    def find_overlapped_lines():
        line = [(0, 1), (0, 2), (1, 2)]
        overlapped_line = []
        # 임의의 2개 선분을 먼저 추출
        for u, v in line:
            a, b = lines[u]
            c, d = lines[v]

            # 직선을 set으로 표현 (종료점을 포함하지 않도록 수정)
            line_a = set(i for i in range(a, b))
            line_b = set(j for j in range(c, d))

            # 겹치는 부분 저장
            intersection = line_a &amp; line_b
            if intersection:
                overlapped_line.append(intersection)

        return overlapped_line

    intersect_set = find_overlapped_lines()

    # 겹치는 부분이 있을 경우 합집합 계산
    if intersect_set:
        union_set = set()
        for s in intersect_set:
            union_set |= s
        return len(union_set)
    else:
        return 0</code></pre>
<ul>
<li>하나의 선분이 다른 선분보다 긴 경우도 있기 때문에 <strong>end를 포함하지 않게 수정</strong>하였고, 어짜피 길이는 end - start라 <strong>원소의 개수</strong>만 세면 되기 때문에 <code>if b - a &lt;= d - c:</code>부분이 필요가 없다.</li>
<li>intersect_set 부분을 직접 인덱싱 하는 대신, <strong>for문</strong>을 사용해서 순회하게 함으로써 out of range 에러를 해결하였음.</li>
<li>집합 연산자 <code>union_set |= s</code>(union_set = union_set | s과 동일)를 사용해서 <strong>선분이 2개만 겹치는 경우와 선분 3개 모두 겹치는 경우를 모두 처리</strong>하게 하였음.</li>
</ul>