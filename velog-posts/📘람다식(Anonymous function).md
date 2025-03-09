<p>접근 제어자, 반환형, 전달값 타입(또는 파라미터), 심지어는 <strong>함수이름까지 생략해서 작성할 수 있는 함수</strong>를 말한다. </p>
<ul>
<li><code>→</code>로 사용 가능</li>
</ul>
<h2 id="💻전달값이-없는-람다식">💻전달값이 없는 람다식</h2>
<pre><code class="language-java">        public void print() {
        String s = &quot;test&quot;;
        System.out.println(s);
    }


    () -&gt; {
        String s = &quot;test&quot;;
        System.out.println(s);
    }</code></pre>
<h2 id="💻전달값을-받는-람다식">💻전달값을 받는 람다식</h2>
<pre><code class="language-java">public void print(String s) {
        System.out.println(s);
    }

    s -&gt; System.out.println(s) // 파라미터가 1개면 소괄호도 생략 가능</code></pre>
<ul>
<li>메소드의 바디 부분이 <strong>1줄</strong>일 때는 <strong>중괄호와 세미콜론을 생략</strong>할 수 있다.</li>
<li>심지어 컴파일러가 데이터 타입을 추론할 수 있기 때문에 <strong>데이터 타입도 생략</strong>할 수 있다.</li>
</ul>
<h2 id="💻전달값이-2개이고-반환값이-1개인-람다식">💻전달값이 2개이고, 반환값이 1개인 람다식</h2>
<pre><code class="language-java">public int add(int x, int y) {
        return x + y;
    }
    // 아래와 같이 람다식으로 표현 가능
    (x, y) -&gt;  x + y</code></pre>
<p>⚠️return이 있으면 중괄호 생략 불가!</p>
<hr />
<p>그런데, 람다식을 생으로 사용할 수 있는건 아니고, 뒤에 배울 함수형 인터페이스에서 그 효력을 발휘할 수 있다.</p>