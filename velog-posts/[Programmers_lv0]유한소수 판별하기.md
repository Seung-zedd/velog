<h1 id="📜문제-해석">📜문제 해석</h1>
<p>유한소수: 소수점 아래 숫자가 계속되지 않고 유한개인 소수</p>
<p>유한소수가 되기 위한 분수의 조건:
1)&quot;기약분수&quot;로 나타내었을 때,
2) 분모의 소인수가 2 또는 5만 존재
∵ 분수 11/22는 기약분수로 나타내면 1/2 입니다. 분모 2는 소인수가 2 뿐이기 때문에 유한소수 입니다.</p>
<p>이때, a/b가 유한소수이면 1을, 무한소수라면 2를 return</p>
<p>[Hint]
분자와 분모의 &quot;최대공약수&quot;로 약분하면 기약분수
-&gt; gcd(a, b)
if a, b가 서로소이면, gcd(a, b) = 1</p>
<p>edge case: 정수도 유한소수로 분류</p>
<p>🚧제약 조건
a, b는 정수, 0 &lt; a ≤ 1,000, 0 &lt; b ≤ 1,000</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>먼저 gcd(a, b)를 이용해서 gcd_val을 추출한다.</li>
<li>케이스 분류를 하는데 gcd(a, b)를 하기 전 가장 큰 분류인 <code>정수는 유한소수로 판단 or a == b</code>를 if문 가장 위에 둔다.</li>
<li>필터링 걸리면 gcd_val을 사용해서 &quot;기약분수&quot;로 나타내었을 때, 분모의 소인수가 2 또는 5만 존재하면 유한소수로 판단하는데 여기서 또 케이스가 나뉜다.
3-1. 기약분수이고 (유한소수 or 무한소수)
3-2. 기약분수가 아니고(서로소 관계) (유한소수 or 무한소수)</li>
</ol>
<p>이 알고리즘을 코드로 구현하면 다음과 같다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(a, b):
    # gcd(a, b)를 사용하여 먼저 최대공약수를 구함
    def gcd(n, d):
        if d == 0:
            return n
        return gcd(d, n % d)

    gcd_val = gcd(a, b)
    # 1. 정수는 유한소수로 판단 or a == b
    if a == b or b == 1:
        return 1
    # 기약분수로 만듦 =&gt; gcd_val &gt; 1
    b //= gcd_val
    is_finite_frac = b % 2 == 0 or b % 5 == 0
    # 2. 처음에 기약분수 x(서로소 관계) and (유한소수 or 무한소수)
    if gcd_val == 1 and (a != b):
        return 1 if is_finite_frac else 2
    # 3. 기약분수 and (유한소수(분모의 소인수가 2 또는 5) or 무한소수)
    return 1 if gcd_val &gt; 1 and is_finite_frac else 2</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/da542f2e-21a7-414e-96c7-6857d55db220/image.png" /></p>
<p>😫도저히 또 다른 edge case가 무엇인지 생각이 안나서 AI의 도움을 받았다.</p>
<p>내가 틀린 부분은 <strong>기약분수로 나타냈을 때 분모의 소인수가 2 또는 5만 존재하는 것</strong>을 코드로 잘못 구현했다는 것이다. 
이 말은, 기약분수로 나타냈을 때, 예를 들어 b = 14이면 <code>is_finite_frac = b % 2 == 0 or b % 5 == 0</code>는 7을 소인수로 가지고 있음에도 True가 되서 잘못된 코드가 되어버린다.</p>
<h1 id="🤖모범-답안-코드">🤖모범 답안 코드</h1>
<pre><code class="language-python">def solution(a, b):
    # gcd(a, b)를 사용하여 먼저 최대공약수를 구함
    def gcd(n, d):
        if d == 0:
            return n
        return gcd(d, n % d)

    gcd_val = gcd(a, b)
    # 기약분수로 만듦
    b //= gcd_val

    # 분모가 1이면 항상 유한소수
    if b == 1:
        return 1

    # 분모의 소인수가 2와 5만 존재하는지 확인
    # 2로 나눌 수 있는 만큼 나눔
    while b % 2 == 0:
        b //= 2
    # 5로 나눌 수 있는 만큼 나눔
    while b % 5 == 0:
        b //= 5

    # 만약 b가 1이 되었다면, 소인수가 2와 5만 존재했다는 의미
    return 1 if b == 1 else 2</code></pre>
<p>📓소인수분해 문제를 다시 복습하고 나서, 이 문제를 풀었더니 위에서 왜 <code>while b % 2 == 0:</code>과 <code>while b % 5 == 0:</code>로 계속해서 반복문을 실행하는지 깨달았다. 일단 내가 스스로 다시 짠 코드는 다음과 같다.</p>
<h1 id="💻소인수분해를-복습하고-난-코드-구현">💻소인수분해를 복습하고 난 코드 구현</h1>
<pre><code class="language-python">def solution(a, b):
    def gcd(n, d):
        if d == 0:
            return n
        return gcd(d, n % d)

    gcd_val = gcd(a, b)
    # 분수를 기약분수로 만들기 위한 분모
    denom_factor = b // gcd_val

    # a == b =&gt; gcd_val == b
    # b == 1 =&gt; a/b는 항상 정수
    if b == gcd_val or b == 1:
        return 1
    # 분모의 소인수가 2와 5만 존재하는지가 관건
    while denom_factor % 2 == 0:
        denom_factor //= 2
    while denom_factor % 5 == 0:
        denom_factor //= 5

    # 소인수 2와 5만 존재했다면 denom_factor는 1이 됨
    # 다른 소인수가 있다면 denom_factor &gt; 1
    return 1 if denom_factor == 1 else 2</code></pre>
<p>이 문제의 요구사항은 &quot;분모의 소인수가 2와 5만 존재해야 합니다.&quot; 즉, 분모에 초점을 맞췄기 때문에 케이스 분류도 분모를 기준으로 하였다.</p>
<ol>
<li><p>a/b가 정수이면 유한소수로 분류</p>
<ul>
<li>a/b = 1이면 항상 정수가 되는데, 이것은 gcd_val == b라는 것과 동일하다.</li>
<li>분모가 1이면 항상 정수</li>
</ul>
</li>
<li><p>소인수분해 문제 코드를 다시 한번 살펴보자.</p>
<pre><code class="language-python">while d &lt;= n:
 if n % d == 0:
     n //= d
 else:
     d += 1
# ...생략</code></pre>
<p>n을 denom_factor라고 하고 <strong>d가 2 또는 5라고 고정된 소인수</strong>라고 보면, 코드의 로직이 정확히 일치한다.</p>
</li>
</ol>
<ul>
<li>단, 여기서는 d가 2와 5로 고정됬기 때문에, <code>d += 1</code>과 <code>while d &lt;= n</code> 부분은 필요가 없다.</li>
</ul>
<ol start="3">
<li>📓(소인수분해 핵심 부분 복습)
n(여기서는 denom_factor)을 나눌 수 있는 소수들로 계속 나누다 보면 더 이상 나눌 수 없는 상태가 되는데, 이 상태가 바로 <code>n == 1</code>일 때고 이것은 <strong>소인수로 다 쪼개져서 더 이상 나눌 수 없는 상태</strong>를 의미한다.</li>
</ol>
<p>따라서, <code>return 1 if denom_factor == 1 else 2</code>는 분모의 소인수가 정확히 2 또는 5만 존재해서 유한소수로 판별하기 때문에 1을 리턴하고, 그렇지 않다는 것은 denom_factor &gt; 1이라는 것이기 때문에 무한소수로 판별되서 2를 리턴한다는 의미다.</p>