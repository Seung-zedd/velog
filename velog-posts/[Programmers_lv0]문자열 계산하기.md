<h1 id="📜문제-해석">📜문제 해석</h1>
<p>my_string은 &quot;3 + 5&quot;처럼 문자열로 된 수식입니다. 문자열 my_string이 매개변수로 주어질 때, 수식을 계산한 값을 return하라.</p>
<p>🚧제약조건</p>
<ol>
<li>연산자는 +, -만 존재합니다.</li>
<li>문자열의 시작과 끝에는 공백이 없습니다.</li>
<li>0으로 시작하는 숫자는 주어지지 않습니다.</li>
<li>return type 은 <strong>정수형</strong>입니다.</li>
<li>my_string의 숫자와 연산자는 <strong>공백 하나로 구분</strong></li>
<li>계산에 사용하는 숫자는 1 이상 20,000 이하인 자연수 =&gt; 딕셔너리 사용 불가!</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>숫자와 연산자는 공백 하나로 구분되기 때문에, <code>split()</code>을 사용해서 공백을 기준으로 리스트에 str 원소 하나씩 삽입한다.</li>
<li><strong>연산자 상태를 기억하기 위한 장치</strong>가 필요한데, 나는 <code>op = &quot;&quot;</code>와 같이 문자열 변수로 상태를 저장하기로 하였다.</li>
<li><code>res = []</code>로 <strong>숫자들을 저장</strong>하고, 만약 res의 길이가 2개이고, 특정 연산자가 들어왔으면 더하거나 빼고 그 값을 res에 삽입한다. </li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(my_string):
    ls = my_string.split()
    op = &quot;&quot; # to store state
    res = [] # to store temporary result

    for s in ls:
        if s == '+' or s == '-':
            op = s
        else: res.append(int(s))
        if len(res) == 2 and op == '+':
            temp = sum(res)
            res = []
            res.append(temp)
        elif len(res) == 2 and op == '-':
            temp = res[0] - res[1]
            res = []
            res.append(temp)
    return res[0]</code></pre>
<p>🤔나는 마지막 라인 위 <code>if~elif</code> 코드에 중복이 보여서 저것을 어떻게 중복을 줄일 수 있을까 한참 고민을 했는데, 아래와 같이 <strong>operator 변환을 통해 모든 숫자들을 <code>sum()</code>으로 처리</strong>하면 된다는 것을 알게 되었다.</p>
<hr />
<p>여기서 자료구조 전공 때 배운 binary operator -&gt; unary operator로 전환해서 구현한 기발한 코드가 있다.</p>
<h1 id="💻기발한-코드">💻기발한 코드</h1>
<pre><code class="language-python">def solution(my_string):
    return sum(int(i) for i in my_string.replace(' - ', ' + -').split(' + '))</code></pre>
<ol>
<li><code>my_string.replace(&quot; - &quot;, &quot; + -&quot;).split(&quot; + &quot;)</code>: split()의 디폴트는 <em>공백을 기준으로 요소들을 분할해서 리스트에 담아두는데</em>, 제약조건에서 &quot;my_string의 숫자와 연산자는 <strong>공백 하나로 구분</strong>&quot; 하기 때문에 split의 인자에 ' + '와 같이 <strong>양쪽에 공백을 두었다.</strong></li>
</ol>
<ul>
<li>동작 과정은 <code>replace()</code>가 먼저 실행되어 양쪽에 공백을 둔 연산자(' - ')를 (' + -')로 바꾸는데, 이유는 <strong>음수 부호는 숫자에 붙임으로써 sum()만 실행하기 위해서다.</strong></li>
<li>만약 split('+')로 하면 매개변수 문자열에 공백이 있기 때문에 <code>['3 ', ' 4 ', ...]</code>와 같이 분할이 된다.<ul>
<li>📓iterable(또는 sequence)객체를 문자열로 변환해주는 함수는 <code>join()</code>!</li>
<li>심지어는 split(' + ')를 기준으로 하기 때문에 <strong>dest 인자를 &quot;+ -&quot;로 중간에 공백을 둔 미친 디테일</strong>까지 있다!</li>
</ul>
</li>
</ul>
<h2 id="unary-operator-vs-binary-operator">Unary operator VS Binary operator</h2>
<p>Unary operator: 하나의 피연산자에 대해서만 연산하는 연산자로, 주로 하나의 value에만 적용시킨다.</p>
<ul>
<li>Unary 마이너스 부호(-x), 증가(++), 감소(--), NOT 논리 연산자(!flag)</li>
</ul>
<p>Binary operator: 2개의 피연산자에 대해 작용하는 연산자로, 사칙연산할 때 주로 쓰임</p>
<ul>
<li>대입 연산자(=), Logical AND(&amp;&amp;), Logical OR(||)</li>
</ul>
<p>참고로, unary나 binary 둘 다 산술연산에 대해 최적화됬기 때문에 성능 이슈가 아니라 <strong>단순한 코드 중복 때문</strong>에 저렇게 구현을 했다고 한다.😅</p>
<hr />
<p>📚참고 자료
🔗Difference between Unary and Binary Operators: <a href="https://www.geeksforgeeks.org/difference-between-unary-and-binary-operators/">https://www.geeksforgeeks.org/difference-between-unary-and-binary-operators/</a></p>