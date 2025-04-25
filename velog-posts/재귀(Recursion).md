<p>🔖재귀: 자신을 정의할 때, 자기 자신을 재호출하는 것을 말한다.</p>
<p>대표적인 재귀함수는 팩토리얼과 피보나치가 있는데, 지겹게 봤으니 자세한 함수 정의와 동작 과정은 생략한다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/8ace23ba-4e4d-431a-ad01-e9230db4b8c9/image.png" /></p>
<h1 id="재귀의-수학적-접근">재귀의 수학적 접근</h1>
<p>대부분의 재귀함수는 점화식(recurrence relation)으로 표현된다. 즉, 팩토리얼은 $$f(n) = n * f(n-1)$$로 표현되고, 피보나치 수열은 
$$f(n) = f(n-1) + f(n-2)$$ (단, n은 2 이상의 자연수)로 표현된다.(👉추후에 dp에서 많이 활용되니 참고해두자.)</p>
<p>💡재귀함수는 자기 자신을 계속해서 호출하는데, 이때 <strong>base condition</strong>을 걸어두지 않으면 stackoverflow가 발생하기 때문에 반드시 if문으로 걸어두자.</p>
<h3 id="💻대표적인-피보나치-함수">💻대표적인 피보나치 함수</h3>
<pre><code class="language-java">private static int fib(int n) {
        // base condition(f(0) = 1, f(1) = 1)
        if (n == 0 || n == 1)
            return 1;
        return fib(n - 1) + fib(n - 2);
    }</code></pre>
<p>💡참고로 메서드에 static을 선언한 이유는, 클래스 따로 만들기 귀찮아서 메인 함수에서 바로 메서드를 호출하기 위해서다.
(자바는 클래스 로더 기반으로 JVM에서 동작하기 때문에 static을 선언하지 않으면 non-static method는 인스턴스 없이는 동작하지 않는다는 컴파일 에러가 뜬다)</p>
<h1 id="⏰재귀의-시간복잡도">⏰재귀의 시간복잡도</h1>
<p>시간복잡도는 알고리즘을 코드로 구현하기 전에 항상 먼저 이해해야 <strong>런타임 초과 에러</strong>에 빠지지 않으므로 매우 중요하다!</p>
<blockquote>
<p>재귀의 시간복잡도 = 재귀 함수 호출 수 x (재귀 함수 하나당) 시간복잡도</p>
</blockquote>
<p>앞서 살펴본 팩토리얼은 총 n번 호출하는데 base condition을 만나면 return문을 실행하므로 상수 시간이 걸린다. 따라서 O(N)의 시간복잡도를 가진다. 반면에, 피보나치 함수는 f(n - 1)과 f(n - 2)를 계속해서 2배씩 호출하기 때문에 2^n이 걸리고 마찬가지로 return문만 실행하기 때문에 
$$O(2^N * 1) = O(2^N)$$이 걸린다.</p>