<h1 id="📜문제-해석">📜문제 해석</h1>
<p>문자열 배열 quiz: 덧셈, 뺄셈 수식들이 'X [연산자] Y = Z' 형태로 들어있음
이때 수식이 옳다면 &quot;O&quot;를 틀리다면 &quot;X&quot;를 순서대로 담은 배열을 return하라.</p>
<p>🚧제약조건</p>
<ol>
<li>1 ≤ quiz의 길이 ≤ 10</li>
<li>연산 기호와 숫자 사이는 항상 하나의 공백이 존재. 단 음수를 표시하는 &quot;마이너스 기호와 숫자 사이에는 공백이 존재하지 않는다.&quot;</li>
</ol>
<ul>
<li>🧐이거 <strong>&quot;마이너스 기호와 숫자 사이에는 공백이 존재하지 않는다.&quot;</strong> 에 착안해서 이전 문자열 계산하기의 알고리즘을 활용해서 풀 수도 있겠는데?<ul>
<li>👉안됨. 왜냐하면 제약조건에 <strong>&quot;각 숫자의 맨 앞에 마이너스 기호가 하나 있을 수 있고 이는 음수를 의미&quot;</strong> 가 있어서 예를 들면, 3 - (-5) = 8일 때, 좌값을 sum(int(i))로 파싱하면 int(&quot;-(-5)&quot;)가 되기 때문에 <span style="color: red;">런타임 에러가 발생</span></li>
</ul>
</li>
</ul>
<ol start="3">
<li>[연산자]는 + 와 - 중 하나</li>
<li>X, Y, Z는 각각 0부터 9까지 숫자로 이루어진 정수를 의미하며, 각 숫자의 맨 앞에 마이너스 기호가 하나 있을 수 있고 이는 음수를 의미</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>퀴즈를 풀기 위해 문자열의 퀴즈 원소들을 split()을 사용해서 공백을 기준으로 하나씩 나눔</li>
</ol>
<p>2.(우값에 해당하는)수식 판독기: '='를 기준으로 나눠야 하는데, 최종적으로 int값을 판단해야함</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(quiz):
    # 배열의 원소들을 split()을 사용해서 전부 나눔
    quiz_ls = [q.split() for q in quiz]
    op = &quot;&quot;
    l_val = []
    r_val = []
    res = []  # 수식이 옳다면 &quot;O&quot;를 틀리다면 &quot;X&quot;를 순서대로 담은 배열

    for r in quiz_ls:
        for s in r:
            # 등호를 만나면 우값을 int로 바꿈
            if s == &quot;=&quot;:
                r_val.append(int(r[r.index(s) + 1]))
                break
            elif s == &quot;+&quot; or s == &quot;-&quot;:
                op = s
            else:
                l_val.append(int(s))

            if len(l_val) == 2 and op == &quot;+&quot;:
                temp = sum(l_val)
                l_val = []
                l_val.append(temp)
            elif len(l_val) == 2 and op == &quot;-&quot;:
                temp = l_val[0] - l_val[1]
                l_val = []
                l_val.append(temp)
        # l_val과 r_val이 맞는지 검사
        if l_val[0] == r_val[0]:
            res.append(&quot;O&quot;)
        else:
            res.append(&quot;X&quot;)
        l_val = []
        r_val = []
    return res</code></pre>
<p>💡리스트를 초기화할 때는 <code>clear()</code>를 사용하는 대신, <code>ls = []</code>와 같이 빈 객체를 할당한 후 참조 객체를 옮겨주는 것이 더 빠르다고 함</p>
<ul>
<li>일반적으로, <code>ls = []</code> 는 새로운 빈 리스트 객체를 만들어 변수에 재할당하는 방식으로, O(1) 시간에 처리된다.</li>
<li>반면에 <code>ls.clear()</code>는 기존 리스트의 모든 요소를 제거하기 위해 &quot;내부적으로 각 요소의 참조를 해제하는 과정&quot;을 거치므로, 요소 수에 따라 O(n) 시간이 소요된다.</li>
</ul>
<h1 id="💻더-나은-코드-구현">💻더 나은 코드 구현</h1>
<pre><code class="language-python">def solution(quiz):
    ans = []
    # 배열의 원소들을 split(' = ')을 사용해서 좌값과 우값을 먼저 나눔
    for q in quiz:
        l_val, r_val = q.split(' = ')
        a, op, b = l_val.split()
        r_val = int(r_val)

        if op == '+':
            l_val = int(a) + int(b)
        else:
            l_val = int(a) - int(b)
        ans.append(&quot;O&quot; if l_val == r_val else &quot;X&quot;)
    return ans</code></pre>
<ol>
<li>`for r in quiz_ls:<pre><code> for s in r:` 와 같이 쓸데없는 이중 for문을 `for q in quiz:
 l_val, r_val = q.split(' = ')`로 quiz 일차원 배열 안에서 등호를 기준으로 좌값과 우값 리스트로 나누어서 모두 해결하게 수정하였다.</code></pre></li>
<li><code>a, op, b = l_val.split()</code> -&gt; 제약조건에 여전히 _&quot;연산 기호와 숫자 사이는 항상 하나의 공백이 존재&quot;_가 있어서 <strong>공백을 기준으로 operands와 operator로 언패킹</strong>하였다.</li>
<li><code>ans.append(&quot;O&quot; if l_val == r_val else &quot;X&quot;)</code> -&gt; <code>append([True] if (조건문) else [False])</code>로 코드를 간략화하였다.</li>
</ol>
<p>📝히든 테케가 안보여서 제약조건이 무엇을 의미하는지 잘 파악이 안 될 수도 있는데, 아무튼 이 문제의 시사점은 <code>str.split()</code>의 인자를 공백만 사용하는 것이 아니라, <strong>문제의 큰 그림을 봐서 인자에 어떤 것을 넣을 지 잘 생각하는 것이 포인트!</strong></p>