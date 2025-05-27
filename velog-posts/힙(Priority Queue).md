<p>📢이번 글에서는 자바 언어로 힙 자료구조를 구현하는데 초점을 맞출 것이니, 힙 자료구조에 대한 자세한 내용은 <a href="https://velog.io/@csj0209/%ED%9E%99priority-queue">힙(파이썬 버전)</a>을 참고하세요.</p>
<h1 id="우선순위-큐-클래스-사용법">우선순위 큐 클래스 사용법</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/6e9ef0cb-04ae-4fab-ab7c-66aa66e96e57/image.png" />
우선순위 큐 마지막 공간에 7을 넣으면
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/32d98c54-f41e-49e7-a7c1-2d6def4e66ec/image.png" />
7이 포함된 subtree에서 부모 노드와 값을 비교하고, 자식 노드가 부모 노드보다 값이 작으므로(우선순위가 큼) 서로 스왑을 한다.</p>
<p>자바의 유틸 라이브러리를 사용하면 아래와 같이 간단하게 구현할 수 있다.</p>
<h3 id="💻우선순위-큐">💻우선순위 큐</h3>
<pre><code class="language-java">package src;

import java.util.*;

public class Main {
    public static void main(String[] args) {
        PriorityQueue&lt;Integer&gt; pq = new PriorityQueue&lt;&gt;();
        pq.offer(1);
        pq.offer(10);
        pq.offer(8);
        pq.offer(17);
        pq.offer(25);
        pq.offer(16);
        pq.offer(7);

        for (Integer i : pq) {
            System.out.println(i); // 1 10 7 17 25 16 8
        }
    }

}</code></pre>
<ul>
<li>기본은 우선순위가 낮은 숫자가 부여되고,
만약 <strong>높은 숫자가 우선순위(max heap)</strong>가 되게 하고 싶다면 선언 시 <code>Collections.reverseOrder()</code> 메서드를 <strong>new 인자</strong>에 넣으면 된다.</li>
<li>컬렉션 타입의 <code>add()</code> 메서드도 있지만, <strong>Queue 인터페이스</strong>를 그대로 구현한 클래스가 PriorityQueue이므로 <strong>메서드의 일관적 사용</strong>을 위해 <code>offer()</code>, <code>poll()</code>, <code>peek()</code>를 그대로 사용할 것을 권장한다.<ul>
<li><code>offer()</code>는 큐가 가득 찼을 때 예외가 아니라 false를 반환하므로(<code>poll()</code>도 마찬가지), 예외 처리 없이도 안전하게 사용할 수 있다.<ul>
<li>offer() 내부에는 <code>siftUp()</code>이 구현되어 있고, poll()도 마찬가지로 내부적으로 <code>siftDown()</code>이 구현되어 있다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<hr />
<p>📚참고자료
🔗[Java] PriorityQueue(우선순위 큐) 클래스 사용법 &amp; 예제 총정리: <a href="https://coding-factory.tistory.com/603">https://coding-factory.tistory.com/603</a></p>