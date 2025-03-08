<h1 id="📜문제-해석">📜문제 해석</h1>
<p><strong>한 개 이상의 항의 합으로 이루어진 식을 다항식</strong>이라고 합니다. 다항식을 계산할 때는 <strong>동류항끼리 계산해 정리</strong>합니다. 덧셈으로 이루어진 다항식 polynomial이 매개변수로 주어질 때, 동류항끼리 더한 결괏값을 문자열로 return하는 solution 함수를 만들어라.</p>
<p>🚧제약 조건</p>
<ol>
<li>polynomial은 양의 정수, 공백, 'x', '+'로 이루어져 있습니다.</li>
<li>항과 연산기호 사이에는 <strong>항상 공백</strong>이 존재합니다.
👉split(' + ')하라는 의미</li>
<li>polynomial에는 일차 항과 상수항만 존재합니다.</li>
<li>결괏값에 상수항은 마지막에 둡니다.
(⭐⭐⭐⭐⭐)5. 계수 1은 생략합니다.</li>
</ol>
<h2 id="🔎문제에서-추출할-수-있는-단서">🔎문제에서 추출할 수 있는 단서</h2>
<ol>
<li>한 개 이상의 항의 합으로 이루어진 식을 다항식: 일차항 + 상수항, 일차항, 상수항으로 분류해서 리턴하라는 의미</li>
<li>동류항끼리 계산해 정리: 변수가 붙은 계수와 상수항으로 나누어서 계산을 하라는 의미</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>더하기를 기준으로 일차항, 상수항으로 분류</li>
<li>일차항 중에 앞에 생략된 계수 1을 살린다.</li>
<li>일차항에서 sum()을 사용할껀데, str타입 숫자만 가능하기 때문에 <code>s.append(s[0])</code>이런 식으로 숫자만 추출한다.</li>
</ol>
<h1 id="💻1차-코드-구현">💻1차 코드 구현</h1>
<pre><code class="language-python">def solution(polynomial):
    # 더하기를 기준으로 파싱
    ls = polynomial.split(&quot; + &quot;)

    # 일차항과 상수항으로 분류
    x_ls = [s for s in ls if &quot;x&quot; in s]
    num_ls = [s for s in ls if &quot;x&quot; not in s]
    x_omitted_ls = []

    # 먼저 앞에 생략된 1계수를 살림
    for s in x_ls:
        if s == &quot;x&quot;:
            s = &quot;1x&quot;
            x_omitted_ls.append(s[0])
        else:
            x_omitted_ls.append(s[0])
    # 분류한 일차항 계수와 상수항 계수를 합함
    coeff_x = sum([int(i) for i in x_omitted_ls])
    num = sum([int(i) for i in num_ls])
    term = str(coeff_x) + &quot;x&quot; 

    # 1차항 + 상수항, 1차항, 상수항 케이스 별로 리턴
    if coeff_x and num:
        return term + &quot; + &quot; + str(num)
    elif coeff_x and (num == 0):
        return term
    elif coeff_x == 0:
        return str(num)</code></pre>
<p>🚩문제점: <code>else: x_omitted_ls.append(s[0])</code>의 경우 &quot;12x&quot;일 때, 앞의 첫차리 수만 추출하기 때문에 12가 아닌 1이 리스트에 추가된다.</p>
<p>🔨해결 방법: 변수 x를 제거하고 싶으면 <code>x_omitted_ls.append.(s[:-1])</code>로 마지막 문자 'x' 이전까지만 추가를 한다.</p>
<ul>
<li>💡참고로, string안의 substring을 추출할 때는 슬라이싱 기법을 사용한다.</li>
</ul>
<p>🤖chatgpt의 힘을 빌려, 일차항과 상수항으로 분류를 할 때 리스트를 추가하는 것이 아니라, <strong>&quot;일차항의 계수와 상수항만 먼저 추출&quot;</strong>한 다음, 리턴할 때 변수 'x'를 붙이는 작업을 하도록 코드를 수정하였다.</p>
<h1 id="💻2차-코드-구현">💻2차 코드 구현</h1>
<pre><code class="language-python">def solution(polynomial):
    # 더하기를 기준으로 파싱
    ls = polynomial.split(&quot; + &quot;)

    # 일차항과 상수항으로 분류
    coeff = 0
    const = 0

    for s in ls:
        if 'x' not in s:
            const += int(s)
        elif s == 'x':
            coeff += 1
        else:
            coeff += int(s[:-1])
    # 일차항 + 상수항, 일차항, 상수항으로 분류해서 리턴
    if coeff and const:
        return str(coeff) + 'x' + ' + ' + str(const)
    elif const == 0:
        return str(coeff) + &quot;x&quot;
    elif coeff == 0:
        return str(const)</code></pre>
<p>🚩문제점: 'x + 10' 또는 'x'와 같이 x의 계수가 1일 때를 처리하지 못해서 '1x + 10' 또는 '1x'로 리턴이 되었다.
🔨해결 방법: 케이스 분류 return문에서 계수가 1인 경우를 <code>return 'x' + ' + ' + str(const) if coeff == 1 else str(coeff) + 'x' + ' + ' + str(const)</code>와 같이 삼항 연산자를 사용해서 처리한다.</p>
<h1 id="💻3차-코드-구현">💻3차 코드 구현</h1>
<pre><code class="language-python">def solution(polynomial):
    # 더하기를 기준으로 파싱
    ls = polynomial.split(&quot; + &quot;)

    # 일차항과 상수항으로 분류
    coeff = 0
    const = 0

    for s in ls:
        if 'x' not in s:
            const += int(s)
        elif s == 'x':
            coeff += 1
        else:
            coeff += int(s[:-1])
    # 일차항 + 상수항, 일차항, 상수항으로 분류해서 리턴
    if coeff and const:
        term = 'x' if coeff == 1 else str(coeff) + 'x'
        return term + ' + ' + str(const)
    elif const == 0:
        return &quot;x&quot; if coeff == 1 else str(coeff) + &quot;x&quot;
    elif coeff == 0:
        return str(const)</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/dd32e614-b858-442c-be48-f4e282d55b5d/image.png" />
💡위의 그림과 같이, <code>term = 'x' if coeff == 1 else str(coeff) + 'x'</code>는 coeff가 1이 아니면 else 부분이 term의 변수로 할당이 된다는 의미다.</p>
<hr />
<h1 id="📝정리">📝정리</h1>
<p>코드 문제점의 흐름을 살펴보면, </p>
<ol>
<li>💻잘못된 구현 방법: 슬라이싱 기법이 생각이 안나서 일차항의 계수 잘못 추출
🔨슬라이싱으로 해결 &amp;&amp; 🤖계수추출법으로 코드 수정</li>
<li>📜문제 제대로 안 읽음: 제약조건 5번의 &quot;계수 1은 생략합니다.&quot;
🔨리턴문에서 삼항 연산자를 사용해서 처리</li>
</ol>
<p>👨‍💻평가: 지금까지 푼 문제 중 가장 오래 걸렸기 때문에 적어도 5번은 시간차를 두고 풀 것!</p>