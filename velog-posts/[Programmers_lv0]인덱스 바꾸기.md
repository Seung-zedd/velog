<h1 id="📜문제-해석">📜문제 해석</h1>
<p>문자열 my_string과 정수 num1, num2가 매개변수로 주어질 때, my_string에서 인덱스 num1과 인덱스 num2에 해당하는 문자를 바꾼 문자열을 return하라.</p>
<p>🚧제약조건</p>
<ol>
<li>my_string은 소문자로 이루어져 있습니다.</li>
<li>num1 ≠ num2</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(my_string, num1, num2):
    temp = &quot;&quot;

    for idx, s in enumerate(my_string):
        if idx == num1:
            temp += my_string[num2]
        elif idx == num2:
            temp += my_string[num1]
        else:
            temp += s
    return temp</code></pre>
<h2 id="🤔왜-c언어의-swap처럼-단순하게-문자열을-변경하면-안되는걸까">🤔왜 C언어의 swap처럼 단순하게 문자열을 변경하면 안되는걸까?</h2>
<p><code>my_string[num1], my_string[num2] = my_string[num2], my_string[num1]</code> 로 단순하게 변경하려고 했는데, 이렇게 하면 다음과 같은 에러가 발생한다:
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/d3351711-8f90-4433-9e98-058abcd090af/image.png" /></p>
<p>이유는 파이썬에서 <strong>문자열은 immutable(불변) 객체</strong>라서, 인덱싱해서 문자열을 직접 변경할 수 없는 읽기 전용 객체이기 때문이다.(여기서는 assign operator로 내용을 변경하려고 하였음.)
👉따라서 문자열의 내용을 변경하고 싶으면 위와 같이 직접 문자열을 생성해야 한다.</p>