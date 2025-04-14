<p><a href="https://leetcode.com/problems/valid-parentheses/description/">https://leetcode.com/problems/valid-parentheses/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p><code>'(', ')', '{', '}', '[' ,']'</code>의 문자가 포함된 문자열 s가 주어졌을 때, 입력 문자열이 유효한지 검사하라. </p>
<p>문자열이 유효하기 위해서는:</p>
<ol>
<li>열린 괄호들은 같은 타입의 괄호들로 닫혀야 한다.</li>
<li>열린 괄호들은 올바른 순서로 닫혀야 한다.</li>
<li>모든 닫힌 괄호는 일치하는 열린 괄호 타입이 있다.</li>
</ol>
<p>🚧제약 조건
1 &lt;= s.length &lt;= 10^4 
=&gt; 시간복잡도 O(N^2)을 넘지 말라는 의미
2. 문자열 s는 오직 '()[]{}'로 구성되어 있다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>문자열 s를 for문으로 순회하는데:
if 열린 괄호: stack.push()
else 닫힌 괄호: stack.peek()로 top 원소를 확인한 후, 매칭되는 열린 괄호가 있으면 stack.pop()</p>
<p>우선 내가 기존에 생각한 알고리즘은 다음과 같다:</p>
<ol>
<li>괄호에 상관없이 무조건 스택에 추가</li>
<li>(스택의 쌍을 맞추기 위해)스택의 크기가 2 이상이면:
현재의 문자가 닫힌 괄호고 스택의 top - 1 원소가 매칭되는 열린 괄호면</li>
<li>스택을 2번 삭제</li>
<li>스택이 비어있으면 true를, 그렇지 않으면 false를 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class ValidSolution {
    public boolean isValid(String s) {
        List&lt;Character&gt; stack = new ArrayList&lt;&gt;();
        for (int i = 0; i &lt; s.length(); i++) {
            char c = s.charAt(i);
            stack.add(c); // 닫힌 괄호만 주어진 경우도 있기 때문에 무조건 넣어줘야됨

            // 빈 stack을 pop()하지 않기 위해 if 조건 걸어둠
            // stack.get(stack.lastIndexOf(c) - 1): stack(top - 1)의 원소를 가져옴
            if (stack.size() &gt; 1) {
                if (c == ')' &amp;&amp; stack.get(stack.lastIndexOf(c) - 1) == '(') {
                    stack.removeLast();
                    stack.removeLast();
                } else if (c == '}' &amp;&amp; stack.get(stack.lastIndexOf(c) - 1) == '{') {
                    stack.removeLast();
                    stack.removeLast();
                } else if (c == ']' &amp;&amp; stack.get(stack.lastIndexOf(c) - 1) == '[') {
                    stack.removeLast();
                    stack.removeLast();
                }
            }

        }
        return stack.isEmpty() ? true : false;
    }
}</code></pre>
<p>🚩괄호를 검사하는 부분이 하드코딩되어 있어서 만약에 괄호 종류가 더 많으면 적합한 풀이는 아님!
🤦‍♂️submit에는 성공했는데, 리스트를 stack처럼 사용하고 있어서 직관적인 풀이는 아니다. 왜 이런 문제가 발생했을까?
👉파이썬에서 <code>stack = []</code>와 같이 리스트로 사용하라고 해서, 자바도 똑같이 ArrayList로 사용했기 때문. </p>
<p>그러나, 자바에는 Stack이라는 컬렉션 프레임워크가 존재한다. 
👇다시 한번 아래의 구조도를 음미해보자.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/77e77b95-9404-4ed4-9e28-255645df6d95/image.png" /></p>
<hr />
<p>따라서, 자바의 Stack 자료구조의 메서드가 무엇이 있는지 파악한 후, 수정된 코드 설계를 따라서 코드 구현을 다시 해보자.</p>
<h1 id="자바의-stack-선언-및-메서드">자바의 stack 선언 및 메서드</h1>
<p>선언은 <code>Stack&lt;T&gt; stackName = new Stack&lt;&gt;();</code>형태로 선언할 수 있으며, 데이터 타입은 클래스 또는 래퍼 클래스로 들어가야 한다.</p>
<ul>
<li><code>push(T t)</code>: 데이터를 스택에 추가하고, <strong>해당 인자를 반환</strong>한다.</li>
<li><code>add()</code>: 마찬가지로 데이터를 스택에 추가하지만, 리스트 인터페이스에 있는 메서드를 오버라이딩 해서 <strong>boolean 값을 리턴</strong>한다.</li>
<li><code>peek()</code>: 스택의 마지막 요소, 즉 top의 원소를 반환하며, <strong>스택에는 변화를 주지 않는다.</strong><ul>
<li>만약, 스택이 비었을 경우, peek() 호출 시 <span style="color: red;"><strong>NoSuchElementException 예외가 발생한다.</strong></span></li>
</ul>
</li>
<li><code>pop()</code>: 스택의 마지막 요소를 제거함과 동시에 해당 값을 반환</li>
<li><code>empty()</code>: 스택이 비어있는지의 여부를 boolean으로 반환</li>
<li><code>search()</code>: 메서드의 <strong>인자</strong>를 스택에서 검색하여 <strong>해당 위치를 반환</strong><ul>
<li>이때, 스택에 가장 top에 있는 item(or element)는 1을 리턴한다. 즉, top에서 멀어질수록 1씩 증가</li>
</ul>
</li>
</ul>
<p>다시 문제로 돌아와서, 우리가 사용해야할 메서드는 괄호를 넣기 위한 <code>push()</code>, top의 원소를 확인하기 위한 <code>peek()</code>, 괄호가 매칭되면 꺼내야하는 <code>pop()</code>, 그리고 <strong>닫힌 괄호는 반드시 같은 타입의 열린 괄호</strong>가 있기 때문에 스택이 비었는지 확인하기 위한 <code>empty()</code>로 총 4가지만 사용하면 된다.</p>
<h1 id="⚙️수정된-코드-설계">⚙️수정된 코드 설계</h1>
<ol>
<li>for문 안에서 문자열 s를 <code>str.charAt(idx)</code>를 사용해서 문자 c로 변환한다.</li>
<li>if 열린 괄호: <code>stack.push()</code>
👉만약에 닫힌 괄호만 주어지면, 위의 if문을 거치지 않기 때문에 바로 false를 리턴</li>
<li>else 닫힌 괄호: <code>stack.peek()</code>로 top 원소를 확인한 후, 매칭되는 열린 괄호가 있으면 <code>stack.pop()</code>
👉매칭되는 괄호가 없으면 가차없이 false를 리턴</li>
<li>for문이 끝나면 <code>return stack.isEmpty()</code>로 boolean값 리턴</li>
</ol>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public class ValidSolution {
    public boolean isValid(String s) {
        Stack&lt;Character&gt; stack = new Stack&lt;&gt;();

        for (int i = 0; i &lt; s.length(); i++) {
            char c = s.charAt(i);

            if (c == '(' || c == '{' || c == '[') {
                stack.push(c);
            } else if (stack.isEmpty()) {
                return false; // 열린 괄호가 스택에 없으면 false
            } else if ((c == ')' &amp;&amp; stack.peek() == '(') || (c == '}' &amp;&amp; stack.peek() == '{') || (c == ']' &amp;&amp; stack.peek() == '[')) {
                stack.pop();
            } else {
                return false;
            }
        }
        return stack.isEmpty();

    }

}</code></pre>
<hr />
<p>📚참고자료
🔗자바 stack 메서드 종류: <a href="https://ittrue.tistory.com/200">https://ittrue.tistory.com/200</a></p>