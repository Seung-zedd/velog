<h1 id="📜문제-해석">📜문제 해석</h1>
<p>문자열 배열 s1과 s2가 주어질 때 같은 원소의 개수를 return하라.</p>
<p>🚧제약조건</p>
<ol>
<li>1 ≤ s1, s2의 길이 ≤ 100</li>
<li>1 ≤ s1, s2의 원소의 길이 ≤ 10</li>
<li>s1과 s2의 원소는 알파벳 소문자로만 이루어져 있습니다</li>
<li>s1과 s2는 각각 중복된 원소를 갖지 않습니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>제약조건에 s1과 s2 중 어느 것의 배열의 길이가 더 큰지 나와있지 않으므로 len(s1) &gt;= len(s2)와 그 외의 경우 2가지로 나누고, 완전탐색 방식으로 코드를 구현하였다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(s1, s2):
    count = 0

    if len(s1) &gt;= len(s2):
        for s in s1:
            for t in s2:
                if s == t:
                    count += 1
    else:
        for t in s2:
            for s in s1:
                if t == s:
                    count += 1
    return count</code></pre>
<h1 id="💻기발한-풀이">💻기발한 풀이</h1>
<pre><code class="language-python">def solution(s1, s2):
    return len(set(s1)&amp;set(s2));</code></pre>
<p>다른 사람은 <strong>같은 원소의 개수를 리턴하라</strong>라는 문장을 보고 세트로 변환해서 <strong>교집합 연산자</strong>를 사용할 생각을 했다는 것이 놀라웠다.</p>
<p>📝따라서, 문제를 대충 읽고 바로 입출력 예시를 보는 것보다, <strong>제시된 문제 요구사항과 제약조건</strong>을 꼼꼼히 읽고 코드를 설계하는 것이 코드 품질 및 시간복잡도 면에서 높은 효율을 가져올 것이다.</p>
<hr />
<h1 id="📚참고자료">📚참고자료</h1>
<p>🔗집합 연산자: <a href="https://dojang.io/mod/page/view.php?id=2315">https://dojang.io/mod/page/view.php?id=2315</a></p>