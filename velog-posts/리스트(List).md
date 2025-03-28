<p>⚠️이 시리즈의 저작권은 <a href="https://www.inflearn.com/course/%EC%BD%94%EB%94%A9%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%85%EB%AC%B8-%ED%8C%8C%EC%9D%B4%EC%8D%AC/dashboard">인프런 코딩테스트 [ ALL IN ONE ] 강의</a>에 있습니다.
📢이미 이 강의를 파이썬으로 완강했기 때문에, 지금부터 작성하는 포스트는 <strong>모두 자바 코드</strong>로 작성할 예정입니다.
참고로 중복되는 개념은 이 시리즈에서는 작성을 안 할 것이기 때문에 파이썬 시리즈를 참고하시면 됩니다.</p>
<p>우선 다양한 자료구조들을 보기 전에 자바의 컬렉션 프레임워크 계층을 살펴보자.
<img alt="" src="https://i.imgur.com/fwYCJhe.png" /></p>
<h1 id="리스트">리스트</h1>
<p>순서를 가지고 원소들을 저장하는 자료구조로, sequence라고도 불린다.</p>
<ul>
<li>static array로도 구현할 수 있고, dynamic array로도 구현할 수 있다.</li>
</ul>
<h2 id="static-array">static array</h2>
<p><strong>고정된 저장 공간</strong>과 <strong>순차적으로 데이터 저장</strong>을 할 수 있다는 특징이 있다.</p>
<ul>
<li>배열은 연속적으로 저장되어 있기 때문에 <code>O(1)</code>로 원하는 데이터로 인덱싱해서 접근할 수 있다.<ul>
<li><code>int[] array = new int[5];</code>로 배열을 초기화한다.</li>
</ul>
</li>
</ul>
<h2 id="dynamic-arraystatic-array의-fixed-size를-보완">dynamic array(static array의 fixed-size를 보완)</h2>
<p>🔖resize: 데이터를 계속 추가하다가 기존에 할당된 size를 초과하게 되면, size를 늘린 배열을 새로 선언하고 그곳으로 모든 데이터를 옮기고 기존의 배열은 메모리에서 삭제(free)하는 과정을 말함<br />🔖Doubling: 일반적으로 2배 큰 크기로 resize 한다.
💡자바에서는 컬렉션 프레임워크의 ArrayList를 주로 사용한다.</p>
<pre><code class="language-java">import java.util.*;

public class App {
    public static void main(String[] args) throws Exception {
        ArrayList&lt;Integer&gt; arr = new ArrayList&lt;&gt;();

        arr.add(3);
        arr.add(7);
        arr.add(9);

        for (Integer i : arr) {
            System.out.println(i);
        }
        System.out.println();

    }
}</code></pre>
<p>🚩참고로, <code>addFirst()</code>는 리스트 제일 앞에 원소를 추가하는 메서드인데, LinkedList나 Deque에 있는 메서드로 일반적인 ArrayList에서는 사용할 수 없다.</p>
<h2 id="⏰static-array와-dynamic-array의-시간복잡도">⏰static array와 dynamic array의 시간복잡도</h2>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/787ad197-7d0e-478e-8aa8-acd07b562890/image.png" /></p>