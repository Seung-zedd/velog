<h1 id="📜문제-해석">📜문제 해석</h1>
<p>spell에 담긴 알파벳을 한번씩만 모두 사용한 단어가 dic에 존재한다면 1, 존재하지 않는다면 2를 return하라.</p>
<h1 id="💻코드-구현정렬">💻코드 구현(정렬)</h1>
<pre><code class="language-java">public class TestSolution {
    public int solution(String[] spell, String[] dic) {
        Arrays.sort(spell);
        String temp = String.join(&quot;&quot;, spell);

        for (int i = 0; i &lt; dic.length; i++) {
            // dic 안에서의 문자도 정렬하기 위해 char형 배열로 리턴
            char[] chars = dic[i].toCharArray(); // String -&gt; char[]
            Arrays.sort(chars);
            dic[i] = new String(chars); // char[] -&gt; String
        }

        // temp와 dic를 비교
        for (int i = 0; i &lt; dic.length; i++) {
            if (temp.equals(dic[i])) {
                return 1;
            }
        }

        return 2;

    }
}</code></pre>
<ol>
<li>String 라이브러리의 join 메서드를 사용해서 하나로 합쳤다.</li>
</ol>
<ul>
<li>참고로, <code>String.join(&quot;&quot;, spell)</code> 대신 <code>new String(spell)</code>을 사용하도 되지 않을까 생각해봤는데, 매개변수 타입이 <code>char[]</code>이 아닌<code>String[]</code>이라 불가능하다.</li>
</ul>
<ol start="2">
<li>for문으로 dic[i]의 문자열 → String 타입 객체의 <code>toCharArray()</code>로 문자 배열 변환 → 정렬(∵배열 객체만 가능) → 다시 문자열로 변환</li>
<li>조합한 문자와 dic이 하나라도 같으면 1을 리턴하고, 그렇지 않으면 2를 리턴한다.</li>
</ol>
<h2 id="🔃문법-정리">🔃문법 정리</h2>
<ol>
<li><code>String.toCharArray()</code>: String -&gt; char[]</li>
<li><code>new String(char[])</code>: char[] -&gt; String</li>
</ol>
<p>💡위의 2가지는 자바에서 문자열과 문자 배열을 자유롭게 변환할 때 자주 사용하는 방법이므로 숙지해두자.</p>