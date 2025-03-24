<h1 id="📜문제-해석">📜문제 해석</h1>
<p>이진수를 의미하는 두 개의 문자열 bin1과 bin2가 매개변수로 주어질 때, 두 이진수의 합을 return하라.</p>
<p>🚧제약 조건</p>
<ol>
<li>return 값은 이진수를 의미하는 문자열</li>
<li>bin1과 bin2는 &quot;0&quot;을 제외하고 0으로 시작하지 않습니다.</li>
<li>1 ≤ bin1, bin2의 길이 ≤ 10</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>각각의 이진수 문자열을 for문으로 돌리면서 2를 곱해서 십진수로 변환(int)</li>
<li>십진수끼리 더함</li>
<li>십진수 -&gt; 이진수</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(bin1, bin2):
    num1 = 0
    num2 = 0
    # 각각의 이진수 문자열을 for문으로 돌리면서 2를 곱해서 십진수로 변환
    for idx, s in enumerate(bin1):
        num1 += pow(2, len(bin1) - idx - 1) * int(s)
    for idx, s in enumerate(bin2):
        num2 += pow(2, len(bin2) - idx - 1) * int(s)

    # 십진수끼리 더함
    temp = num1 + num2
    mod = &quot;&quot;
    while 2 &lt;= temp:
        mod += str(temp % 2)
        temp //= 2
    # 십진수 -&gt; 이진수
    result = mod + str(temp)
    return result[::-1]</code></pre>
<ul>
<li>마지막에 슬라이싱 인덱스를 활용해서 깔끔하게 거꾸로 출력하게 하였다.</li>
</ul>
<h2 id="🔧-개선-포인트">🔧 개선 포인트</h2>
<ul>
<li><code>pow(2, n)</code> 대신 <strong>비트 시프트 연산자(&lt;&lt;)</strong>을 사용하면 훨씬 효율적임<ul>
<li><code>num1 += (1 &lt;&lt; (len(bin1) - idx - 1)) * int(s)</code></li>
</ul>
</li>
<li>mod에 문자열 누적하는 대신, 리스트에 <code>append()</code>로 넣고 마지막에 <code>join()</code>으로 합치면 성능과 가독성 모두 좋아진다.<pre><code class="language-python">mod = []
while temp:
  mod.append(str(temp % 2))
  temp //= 2
return ''.join(mod[::-1])</code></pre>
</li>
<li>어짜피 1 % 2 = 1이고, 마지막에 temp는 0이 되기 때문에 while문을 탈출하기 때문</li>
</ul>
<h1 id="💻다른-사람의-코드">💻다른 사람의 코드</h1>
<pre><code class="language-python">def solution(bin1, bin2):
    answer = bin(int(bin1, 2) + int(bin2, 2))[2:]
    return answer</code></pre>
<ul>
<li><code>int(x, base=10)</code>: x는 변환할 값(문자열, 숫자, 객체 등)을 의미하고, base는 진법을 의미한다.<ul>
<li><code>int('1011', 2)</code>: &quot;1011&quot;을 2진수로 인식하고, 10진수로 변환한다.</li>
</ul>
</li>
<li>bin(): int형 정수를 &quot;0b&quot;가 앞에 붙은 이진 문자열로 변환해주는 내장 함수<ul>
<li>bin(23) &gt;&gt;&gt; '0b10111'</li>
<li>마지막에 <code>[2:]</code>를 붙인 이유는 앞에 0b가 붙었기 때문</li>
</ul>
</li>
</ul>
<p>📝직접 자릿수를 진수로 곱해서 변환하는 것도 좋지만, 파이썬 내장함수를 적극적으로 활용하자!</p>