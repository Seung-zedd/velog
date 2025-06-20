<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42578">https://school.programmers.co.kr/learn/courses/30/lessons/42578</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>코니는 매일 다른 옷을 조합하여 입는것을 좋아합니다.
코니가 가진 옷이 아래와 같고
현재 상태: [동그란 안경, 긴 코트, 파란색 티셔츠]
다음날: 청바지를 추가로 입거나 동그란 안경 대신 검정 선글라스를 착용하거나 해야합니다.</p>
<p>의상 착용 요구조건</p>
<ol>
<li><p>코니는 <strong>각 종류별로 최대 1가지 의상만 착용</strong>할 수 있습니다.
=&gt; Key: 의상의 종류, Value: 의상의 이름으로 하라는 의미</p>
</li>
<li><p>착용한 의상의 일부가 겹치더라도, 다른 의상이 겹치지 않거나, 혹은 의상을 추가로 더 착용한 경우에는 서로 다른 방법으로 옷을 착용한 것으로 계산합니다.</p>
</li>
</ol>
<p>-&gt; 위의 예에서, [동그란 안경, 긴 코트, 파란색 티셔츠] =&gt; [동그란 안경, 파란색 티셔츠, 청바지, 긴 코트]는 다른 방법으로 계산
(⭐⭐⭐⭐⭐이게 핵심 조건이었음)3. 코니는 <strong>하루에 최소 한 개의 의상</strong>은 입습니다.</p>
<p>param: 코니가 가진 의상들이 담긴 2차원 배열 clothes</p>
<p>요구사항: 서로 다른 옷의 조합의 수를 return</p>
<p>🚧제약 조건</p>
<ol>
<li>코니가 가진 의상의 수는 1개 이상 30개 이하입니다.</li>
</ol>
<p>-&gt; <code>1 &lt;= 의상의 이름 &lt;= 30</code>
2. 같은 이름을 가진 의상은 존재하지 않습니다.
-&gt; Value는 unique하다.
3. clothes의 모든 원소는 문자열로 이루어져 있습니다.
=&gt; String 타입
4. 모든 문자열의 길이는 1 이상 20 이하인 자연수이고 알파벳 소문자 또는 '_' 로만 이루어져 있습니다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>HashMap을 {key: 의상의 이름, value: 의상의 종류}로 초기화를 하고 clothes 길이만큼 순회해서 map에 삽입한다.</li>
<li>서로 다른 옷의 조합을 계산:
1)1가지만 입는 경우: answer += map.size()
🚩2)2가지 이상을 입는 경우: 이것이 해결이 안됨!</li>
</ol>
<p>저 2가지를 입는 경우를 해결하기 위해서 map에서 꺼낸 Value를 스택에 넣고(다음 Value와 비교하기 위함)다음 원소를 순회할 때 <code>if(!stack.peek().equals(e.getValue()): answer += 1;</code>로 해결은 했는데, 문제는 3가지 이상일 때다.</p>
<p>결국 발상은 AI의 도움을 받아서 다음과 같이 설계를 새롭게 하였다.</p>
<h1 id="🤖수정된-코드-설계">🤖수정된 코드 설계</h1>
<p>위의 핵심 조건인 코니는 하루에 &quot;최소 한 개&quot;의 의상을 입습니다 == (전체 경우의 수 - 한 개도 입지 않는 알몸 상태)를 계산해주면 된다.</p>
<p>또한, 서로 다른 옷의 조합의 수를 구하기 위해 naive하게 <strong><span style="color: red;">조합 알고리즘</span></strong>을 사용하면 $$O(2^n)$$ = 2^30 = (2^10)<em>3 ≒ (10^3)</em>3 = $$10^9$$ &gt; $$10^8$$ 이기 때문에 <strong><span style="color: red;">시간 초과</strong></span>가 난다.
👉따라서, 해시맵을 사용해서 다음과 같이 수학적으로 접근해야 한다.</p>
<ol>
<li>의상 카테고리 분류: {key: 의상의 종류, value: 의상 종류의 개수}</li>
<li>안 입는 경우 포함하기: value인 각 의상 종류의 개수 + 1 (∵안 입는 경우도 경우의 수에 포함)</li>
<li>전체 경우의 수 계산: 모든 의상 종류의 개수를 곱한 후 - 1
ex) (의상1 종류 선택지) x (의상2 종류 선택지) x ... - 1
(∵1 == 한 개도 입지 않는 알몸 상태)
💡모든 경우의 수를 구하기 위한 각 카테고리 선택지를 곱하는 연산이 데이터베이스론의 <strong>카티션 프로덕트</strong>와 정확히 일치한다.</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">import java.util.*;

public class ClothSolution {
    public int solution(String[][] clothes) {
        // map: &lt;의상의 종류, 의상 종류의 개수&gt;
        Map&lt;String, Integer&gt; map = new HashMap&lt;&gt;();
        for (int i = 0; i &lt; clothes.length; i++) {
            map.put(clothes[i][1], map.getOrDefault(clothes[i][1], 0) + 1);
        }

        // 안 입는 경우도 포함
        int answer = 1;
        for (Map.Entry&lt;String, Integer&gt; e : map.entrySet()) {
            answer *= e.getValue() + 1;
        }
        // 알몸 상태 제외
        return --answer;

    }
}</code></pre>
<p>⏰clothes의 길이만큼 map에 원소를 삽입해서 $$O(n)$$, <code>map.entrySet()</code>으로 의상의 개수를 가져오기 위해 $$O(n)$$, 마지막으로 1을 빼준 상수 시간 $$O(1)$$을 모두 더하면 점근적으로 $$O(n)$$ 선형시간이 걸린다.</p>
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>💡주어진 param이 {의상의 이름, 의상의 종류}여서 단순하게 해시맵에 <code>{key: 의상의 이름, value: 의상의 종류}</code>를 삽입하였는데, 사실상 의상의 이름은 중요하지 않고, 문제 요구사항이 <strong>서로 다른 옷의 조합의 수</strong>를 return하는 것이기 때문에 의상의 종류 개수를 구하는 것이 이 문제의 핵심이였다.
즉, 애초에 내가 설계한 부분은 <strong>의상의 종류 개수를 value</strong>로 설정하지 않았기 때문에 제대로 풀지 못한 것이다.(+ 수학적 사고 부재)</p>
<p>한편, 중복되는 의상의 종류를 카운트하기 위해 <code>map.getOrDefault()</code>를 사용한 것을 보면 이전의 &quot;완주하지 못한 선수&quot;에서 <strong>동명이인을 처리한 로직과 비슷</strong>한 것 같은데, 이번 문제는 <strong>중복되는 key를 처리한 value를 어떻게 수학적으로 접근</strong>할 것인가가 가장 핵심적인 부분이라고 할 수 있다.</p>