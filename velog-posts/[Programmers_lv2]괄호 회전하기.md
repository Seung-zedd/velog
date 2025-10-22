<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/76502">https://school.programmers.co.kr/learn/courses/30/lessons/76502</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>Leetcode에서 징하게 풀었던 ValidParenthesis 문제 + 문자열 길이만큼 왼쪽으로 회전하는 문제다. 
이때, s가 올바른 괄호 문자열이 되게 하는 x의 개수를 리턴하라.</p>
<h2 id="🚧제약-조건">🚧제약 조건</h2>
<p>s의 길이는 1 이상 1,000 이하입니다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>문자열 길이만큼 for문으로 순회한다.</li>
<li>이때 <code>rotateWithStreams()</code>로 문자열 s를 회전시킨다.</li>
<li><code>rotateWithStreams()</code> 메서드 내부에서 회전시킨 결과 문자열을 <code>isValidParenthesis()</code>로 올바른 괄호 문자열인지 체크한다.</li>
<li>누적시킨 결과값을 리턴한다.</li>
</ol>
<p>😅참고로, 배열 회전하는 로직을 몰라서 <code>rotateWithStreams()</code>를 찾아봤는데, 다시 보니까 파라미터가 배열이 아니라 문자열이라서 이름 수정을 해야됬었는데 귀찮아서 그냥 사용했다.
<br /></p>
<p><code>rotateWithStreams()</code> 메서드 로직은 아래 이미지를 그대로 코드로 구현하면 된다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/72e821be-2434-4d50-aea8-996c4c949c40/image.jpg" /></p>
<br />

<p>위의 알고리즘 순서를 그대로 코드로 구현하면 아래와 같다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">
import java.util.*;

public class ValidParenthesisLeftRotateSolution {
    public int solution(String s) {
        // isEmpty()의 true 값을 카운팅하기 위한 장치 필요
        int count = 0;

        // 이 s를 왼쪽으로 x (0 ≤ x &lt; (s의 길이)) 칸만큼 회전시켰을 때
        for (int i = 0; i &lt; s.length(); i++) {
            // 회전을 순차적으로 하고
            count += rotateWithStreams(s, i);
        }

        return count;
    }

    /**
     *
     * @param s: 회전할 배열
     * @param k: 회전할 위치 수
     * @return
     */
    private static int rotateWithStreams(String s, int k) {
        // 왼쪽으로 1번만 회전
        //! 길지 않은 문자열 끼리는 + 연산자로 더할 수 있음
        String result = s.substring(k) + s.substring(0, k);
        // 왼쪽 회전 결과를 파라미터로 넘겨서 괄호 유효성 검사 함수 호출
        return isValidParenthesis(result) ? 1 : 0;

    }


    private static boolean isValidParenthesis(String s) {
        Stack&lt;Character&gt; stack = new Stack&lt;&gt;();

        for (int i = 0; i &lt; s.length(); i++) {
            // 열린 괄호는 무조건 푸시
            if (s.charAt(i) == '(' || s.charAt(i) == '{' || s.charAt(i) == '[') {
                stack.push(s.charAt(i));
            } else if (!stack.isEmpty()) {
                if (s.charAt(i) == ')' &amp;&amp; stack.peek() == '(') {
                    stack.pop();
                } else if (s.charAt(i) == '}' &amp;&amp; stack.peek() == '{') {
                    stack.pop();
                } else if (s.charAt(i) == ']' &amp;&amp; stack.peek() == '[') {
                    stack.pop();
                }
            } else {
                // 닫힌 괄호는 매칭이 안되므로 false를 리턴
                return false;
            }
        }

        return stack.isEmpty();
    }
}</code></pre>
<ul>
<li>⚠️<code>substring()</code>의 결과도 String이기 때문에 <strong>+ 연산자로 문자열끼리 조합이 가능</strong>하다. <a href="https://velog.io/@csj0209/Programmerslv2-%EB%B0%A9%EB%AC%B8-%EA%B8%B8%EC%9D%B4">(방문 길이 문제 참조할 것)</a><ul>
<li><code>String.substring(beginIdx, endIdx)</code>에서 begin은 inclusive고 end는 exclusive기 때문에 <em><code>if (k == 0)</code>부분을 제거했다.</em><ul>
<li>⚠️0 이상 0 미만인 인덱스가 없으므로 항상 빈 문자열 &quot;&quot;을 리턴한다. 
또한, <strong><code>k &lt;= x &lt; k</code>의 결과값은 항상 0</strong>이기 때문에 beginIndex와 endIndex가 같으면 항상 빈 문자열이 반환되는 원리다.</li>
</ul>
</li>
</ul>
</li>
<li>어짜피 제약조건에서 문자열 s의 최대 길이가 $$10^3$$이기 때문에 + 연산자를 사용해도 무방하다.</li>
</ul>
<h2 id="🤔언제-stringjoin을-사용하는가">🤔언제 String.join()을 사용하는가?</h2>
<p>join()은 <strong>여러 문자열 요소들을 구분자로 연결</strong>하는 메서드다.</p>
<p>함수 시그니처:</p>
<pre><code class="language-java">String.join(CharSequence delimiter, CharSequence... elements)</code></pre>
<ul>
<li>💡참고로 <code>...</code>는 <u>개수가 정해지지 않거나 개수가 무수히 많을 때</u> 사용되는 가변 인자(variable arguments)다.</li>
</ul>
<p>예시:</p>
<pre><code class="language-java">String.join(&quot;-&quot;, &quot;2025&quot;, &quot;10&quot;, &quot;21&quot;)  // → &quot;2025-10-21&quot;
String.join(&quot;, &quot;, &quot;사과&quot;, &quot;바나나&quot;, &quot;오렌지&quot;)  // → &quot;사과, 바나나, 오렌지&quot;</code></pre>
<p>그렇기 때문에, <code>String.join()</code> 대신 문자열을 회전하는 경우에는 <code>String.substring()</code>이 더 적합하다!</p>