<h1 id="📜문제-해석">📜문제 해석</h1>
<p>알파벳이 담긴 배열 spell과 외계어 사전 dic이 매개변수
spell에 담긴 알파벳을 한번씩만 &quot;모두 사용한 단어&quot;가 dic에 존재한다면 1, 존재하지 않는다면 2를 return하는 함수를 만들어라.</p>
<p>🚧제약 조건</p>
<ol>
<li>spell과 dic의 원소는 알파벳 소문자로만 이루어져있습니다.</li>
<li><strong>spell의 원소를 모두 사용</strong>해 단어를 만들어야 합니다.</li>
<li>spell의 원소를 모두 사용해 만들 수 있는 단어는 dic에 두 개 이상 존재하지 않습니다.</li>
<li>dic과 spell 모두 중복된 원소를 갖지 않습니다.</li>
<li>2 ≤ spell의 크기 ≤ 10</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>입출력 예시를 보아하니, spell을 순열로 만들어서 dic와 직접 비교해야겠다는 생각이 들었다.
🚩문제는 이렇게 하면 2중 for문이 되어서 시간복잡도가 상당히 높다는 것이다.</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">from itertools import permutations
def solution(spell, dic):
    spell_permutation = permutations(spell)
    temp = &quot;&quot;

    for s in spell_permutation:
        for u in s:
            temp += u
        for d in dic:
            # 일단 길이가 먼저 맞아야됨
            if len(spell) == len(d) and d == temp:
                return 1
        temp = &quot;&quot;
    return 2</code></pre>
<ul>
<li>제약조건에 <code>2 ≤ spell의 크기 ≤ 10</code>가 있어서 <code>permutaitons(spell)</code>에 length 인자를 넣지 않았고 언패킹을 하지 않았다.</li>
<li>permutations()의 결과는 읽기 전용 자료형인 튜플을 반환하기 때문에 튜플의 원소를 <code>for u in s:</code>와 같이 직접 순회하였음.</li>
<li>dic의 모든 단어와 일치하지 않으면, temp를 초기화시키고, 다시 다른 순열 조합을 찾아서 매칭시킴</li>
</ul>
<p>⏰순열이라, 못해도 시간복잡도는 O(N!)이 걸리기 때문에 매우 안 좋은 코드다.</p>
<h1 id="💻더-나은-코드-구현">💻더 나은 코드 구현</h1>
<pre><code class="language-python">def solution(spell, dic):
    sorted_spell = &quot;&quot;.join(sorted(spell))

    for word in dic:
        if sorted_spell == &quot;&quot;.join(sorted(word)):
            return 1
    return 2</code></pre>
<ul>
<li>순열로 접근하는 것이 아니라, 정렬 알고리즘을 사용해서 dic에 있는 단어들을 일일이 비교하는 것이다.<ul>
<li>이렇게 하면, spell을 알파벳 순으로 정렬하고, dic에 있는 원소들도 알파벳 순으로 정렬하면서 비교하기 때문에 이전에 구현한 것보다 O(NlogN)으로 훨씬 빠르다.</li>
</ul>
</li>
<li>💡<code>join()</code>을 사용하지 않고, _<strong>spell에 담긴 알파벳을 한번씩만 &quot;모두 사용한 단어&quot;가 dic에 존재</strong>_하기만 하면 되기 때문에 <code>join()</code>대신 리스트 타입의 <code>sorted(spell)</code>과 <code>sorted(word)</code>를 비교해도 submit된다.</li>
</ul>