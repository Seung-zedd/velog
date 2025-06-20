<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42577">https://school.programmers.co.kr/learn/courses/30/lessons/42577</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 한다.</p>
<p>구조대 : 119
박준영 : 97 674 223
지영석 : 11 9552 4421
-&gt; 구조대는 영석이의 접두사다.</p>
<p>param: 전화번호부에 적힌 전화번호를 담은 배열
요구사항: &quot;어떤 번호가 다른 번호의 접두어&quot;인 경우가 하나라도 있으면 false를 그렇지 않으면 true를 리턴하라.</p>
<p>🚧제약 조건</p>
<ol>
<li>1 &lt;= phone_book.length &lt;= $$10^6$$
=&gt; 정렬 $$O(nlogn)$$ 사용 불가하므로, $$O(n)$$까지만 가능(e.g. 해시)</li>
<li>1 &lt;= phone_book[i] &lt;= 20</li>
<li>phone_book[i]는 unique하다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>$$O(n)$$까지 가능하고 제약 조건 3에 의해 해시셋을 사용하자.</p>
<p>그런데, 다음과 같은 문제 요구사항을 어떻게 해석할지가 문제다.</p>
<ol>
<li>무엇을 접두사로 설정?</li>
<li>접두사와 다른 번호와 어떻게 비교?</li>
</ol>
<p>그래서 나는 다음과 같이 가설을 설정하였다.</p>
<ol>
<li><code>phone_book[i]</code>의 <strong>길이가 가장 짧은 것</strong>을 prefix로 설정</li>
<li>phone_book의 모든 원소를 저장한 해시셋을 순회하는데, <strong>해시셋의 원소가 prefix로 시작</strong>되면 false를 리턴한다.</li>
</ol>
<h2 id="💡반드시-짚고-넘어가자">💡반드시 짚고 넘어가자</h2>
<p>🔖<code>str.contains(String s)</code>: str이 s를 &quot;어디든&quot; 포함하면 true를 리턴한다.
🔖<code>str.startsWith(String s)</code>: str이 s로 시작되면 true를 리턴한다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class DirectorySolution {
    public boolean solution(String[] phone_book) {
        // set 삽입
        Set&lt;String&gt; set = new HashSet&lt;&gt;(Arrays.asList(phone_book));
        String prefix = phone_book[0];

        for (String s : phone_book) {
            if (prefix.length() &gt; s.length() &amp;&amp; prefix.contains(s)) {
                prefix = s;
            }
        }

        // prefix 매칭
        for (String s : set) {
            if (!s.equals(prefix) &amp;&amp; s.startsWith(prefix)) {
                return false;
            }
        }

        return true;

    }
}</code></pre>
<p>🤖위 코드의 근본적 문제점:</p>
<ol>
<li>잘못된 접두사 선택 로직</li>
</ol>
<ul>
<li>contains() 사용 오류: <code>prefix.contains(s)</code>는 문자열 중간에 포함되어도 true를 반환합니다.<ul>
<li>실제 접두사(startsWith) 여부를 확인하지 못합니다.</li>
</ul>
</li>
</ul>
<p>예시: phone_book = [&quot;1234&quot;, &quot;23&quot;, &quot;123&quot;]
prefix가 &quot;1234&quot; → &quot;23&quot;이 포함되어 있다고 판단해 prefix = &quot;23&quot;으로 변경
실제로 &quot;123&quot;이 &quot;1234&quot;의 접두사이지만, &quot;23&quot;을 기준으로 검사하므로 문제를 놓칩니다.</p>
<ol start="2">
<li>단일 접두사만 검사</li>
</ol>
<ul>
<li>모든 번호를 한 개의 접두사와만 비교합니다.<ul>
<li>다른 조합의 접두사 관계를 완전히 검사하지 못합니다.
예: [&quot;abc&quot;, &quot;ab&quot;, &quot;xy&quot;]에서 &quot;ab&quot;와 &quot;abc&quot;의 관계를 놓칩니다.</li>
</ul>
</li>
</ul>
<p>따라서 문제의 입출력 예시만 보고 prefix가 단순히 가장 짧은 길이라고 생각해서 설정하면 다른 조합의 접두사 관계를 놓칠 수가 있다.</p>
<p>🔨따라서, prefix를 <strong>substring을 이용</strong>해서 만들면 다른 조합의 접두사 관계도 검사할 수가 있다.</p>
<h1 id="🤖수정된-코드-구현">🤖수정된 코드 구현</h1>
<pre><code class="language-java">public class DirectorySolution {
    public boolean solution(String[] phone_book) {
        // set 삽입
        Set&lt;String&gt; set = new HashSet&lt;&gt;(Arrays.asList(phone_book));

        for (String s : phone_book) {
            for (int i = 1; i &lt; s.length(); i++) {
                String prefix = s.substring(0, i);
                if (set.contains(prefix)) {
                    return false;
                }
            }
        }

        return true;

    }
}</code></pre>
<ul>
<li>초기화할 때 생성자 인자에 <code>Arrays.asList()</code>를 넣어서 빠르게 해시셋으로 만들 수도 있다.</li>
<li>해시셋을 사용한 이유는 substring으로 만든 prefix가 해시셋에 포함하는지를 검사하기 위해서다.<ul>
<li>📢<code>str.contains()</code> != <code>set.contains()</code>, 즉 전자는 s가 &quot;어디든&quot; 포함하면 true를 리턴하는 반면에, 후자는 <strong>해시맵과 비슷하게 s 자체가 포함하면 true를 리턴</strong>한다.</li>
</ul>
</li>
<li><code>substring(0, i)</code>는 i미만까지 만들기 때문에 set 원소의 자기자신을 포함하지 않는다.</li>
</ul>
<p>⏰위 코드의 시간복잡도는 phone_book 배열에서 하나의 원소를 순회해서 substring을 만들기 때문에 $$O(N*M)$$이 걸리고, 최악의 경우에는 $$10^6 * 20 = 2 * 10^7 &lt; 10^8$$를 만족하기 때문에 통과한다.</p>
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>문제 제약 조건에서 <code>phone_book[i]</code>가 unique하다는 것을 캐치해서 해시셋을 사용해야 된다는 것은 알았는데,
첫째로 무엇을 prefix로 설정하고 둘째로 prefix와 다른 번호로 어떻게 비교하는지에 대해 잘못 코드 설계하였다.</p>
<p>사실 설계하면서 다른 번호와 비교하기 위한 장치로 substring을 생각했지만, 이것을 해시셋과 어떻게 연계해야할지를 몰랐다. 
따라서 우리는 코드 설계 부분에서 난관에 봉착한 문제 요구사항을 다음과 같이 해석할 수 있다.</p>
<ol>
<li>무엇을 접두사로 설정? -&gt; <strong>substring</strong>을 사용해서 각 전화번호에서 가능한 모든 접두사를 생성한다.</li>
<li>접두사와 다른 번호와 어떻게 비교? -&gt; 생성된 접두사(prefix)가 <strong>해시셋에 존재하는지 <code>contains()</code>로 확인</strong>한다.</li>
</ol>