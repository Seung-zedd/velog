<h1 id="📜문제-해석">📜문제 해석</h1>
<p>문자열 before와 after가 매개변수로 주어질 때, before의 순서를 바꾸어 after를 만들 수 있으면 1을, 만들 수 없으면 0을 return 하라.</p>
<p>🚧제약 조건</p>
<ol>
<li>0 &lt; before의 길이 == after의 길이 &lt; 1,000</li>
<li>before와 after는 모두 소문자로 이루어져 있습니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li><p>순서를 바꾸어 -&gt; 역 인덱싱하는 건 줄 알았는데 65.0의 통과율을 보임
=&gt; 그럼 2중 for문으로 일일이 매치하는것밖에 안될듯...?
 예) &quot;acca&quot;, &quot;ccaa&quot;</p>
</li>
<li><p>그럼 알파벳의 개수만 다르면 순서를 바꾸어도 after를 만들 수 없나?
=&gt; 91.3의 통과율을 보임</p>
</li>
<li><p>정렬을 사용해서 문자열 구성이 완전히 같은지 비교하는 방법으로 수정</p>
</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(before, after): 
    before_dict = {} #{before: letter counting}
    after_dict = {}
    for s in before:
            before_dict[s] = before_dict.get(s, 0) + 1
    for s in after:
        after_dict[s] = after_dict.get(s, 0) + 1
    # 각 dict끼리 개수 비교
    for k, v in before_dict.items():
        for m, n in after_dict.items():
            if k == m and v != n:
                return 0
    return 1</code></pre>
<p>🚩_각 dict끼리 개수 비교_하는 곳에서 위와 같이 구현하면 key로 저장한 문자열들이 어느 한 dict에는 존재하지만 다른 dict에는 존재할 수 없다고 한다.
🔨따라서 두 딕셔너리가 완전히 동일한지 비교하려면 <code>return 1 if before_dict == after_dict else 0</code>로 수정하면 된다.</p>
<h1 id="🤖코드-구현">🤖코드 구현</h1>
<pre><code class="language-python">def solution(before, after):
    return 1 if sorted(before) == sorted(after) else 0</code></pre>
<p>🤔가만 있어보자... 이 비슷한 문제를 프로그래머스 어디에서 본 것 같은데?? 바로 <strong>외계어 사전</strong> 문제였다!
<a href="https://velog.io/@csj0209/Programmerslv0%EC%99%B8%EA%B3%84%EC%96%B4-%EC%82%AC%EC%A0%84#%EB%AC%B8%EC%A0%9C-%ED%95%B4%EC%84%9D">https://velog.io/@csj0209/Programmerslv0%EC%99%B8%EA%B3%84%EC%96%B4-%EC%82%AC%EC%A0%84#%EB%AC%B8%EC%A0%9C-%ED%95%B4%EC%84%9D</a></p>
<p>📝결국, A로 B 만들기 같은 <strong>문자열 매칭 문제</strong>는 <code>sorted()</code>로 <strong>정렬</strong>하면 각 문자열의 인덱스마다 일치하는지 쉽게 확인 가능하기 때문에 이 방법을 사용하자🔑 </p>