<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/12909">https://school.programmers.co.kr/learn/courses/30/lessons/12909</a></p>
<p>📢리트코드 문제에서 지<del>~</del>겹게 풀었으므로 과정은 생략하고 코드 구현만 올리겠습니다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class ValidParenthesisSolution {
    public boolean solution(String s) {
        Stack&lt;Character&gt; stack = new Stack&lt;&gt;();
        for (int i = 0; i &lt; s.length(); i++) {
            // String -&gt; char로 바꿔서 하나씩 순회
            char c = s.charAt(i);

            if ((!stack.empty() &amp;&amp; stack.peek() == '(') &amp;&amp; c == ')') {
                stack.pop();
            } else {
                stack.push(c);
            }
        }
        return stack.empty();
    }

}</code></pre>
<ul>
<li><del>String은 배열이 아니라 하나의 객체이기 때문에 s[i]와 같이 인덱스로 접근할 수 없다. 따라서, String을 char로 바꾸고 싶으면 <code>s.charAt(i)</code>를 사용하자.</del></li>
<li>🤖<code>for (char c : s.toCharArray())</code>를 사용하면 for-each문으로 더 간결하게 구현할 수 있습니다.</li>
</ul>