<h1 id="큐queue">큐(Queue)</h1>
<p>간단하게 대기줄을 생각하면 되는데, 먼저 들어간 데이터가 먼저 나오는 <strong>FIFO(First-In First-Out)</strong> 자료구조다.</p>
<h2 id="큐의-연산">큐의 연산</h2>
<p><code>enqueue</code>: 큐의 뒤(rear)에 데이터를 삽입
<code>dequeue</code>: 큐의 앞(front)에서 데이터를 삭제</p>
<h2 id="💻큐의-구현">💻큐의 구현</h2>
<pre><code class="language-java">public class Main {
    public static void main(String[] args) {
        Deque&lt;Integer&gt; q = new LinkedList&lt;&gt;();
        // enqueue: rear에 데이터 삽입
        q.add(1); // [1]
        q.add(2); // [1, 2]
        q.add(3); // [1, 2, 3]
        q.addFirst(0); // [0, 1, 2, 3]

        // dequeue: front에서 데이터 삭제
        q.removeFirst(); // [1, 2, 3]
        q.removeFirst(); // [2, 3]
        q.removeLast(); // [2]
    }
}</code></pre>
<p>큐는 ArrayList와 Doubly LinkedList 2가지로 구현할 수 있다. 
그런데, 전자로 구현하면 <code>dequeue</code>를 하고나서 뒤에 있는 원소들을 left-shift해줘야 하기 때문에 O(N)이 걸린다. 따라서 대부분은 Doubly Linked List로 구현된 자료구조를 사용한다.</p>
<p>📓지난 시간에 ArrayList와 시간복잡도를 비교하면서 <strong>Doubly LinkedList는 head와 tail 포인터를 이용</strong>하므로 <code>insert_front(back)</code>와 <code>remove_front(back)</code>은 O(1)이 걸린다는 것을 학습하였다.
👉따라서, 큐의 enqueue와 dequeue의 연산과 맞아떨어지기 때문에 Doubly LinkedList가 효율적이라는 것을 알 수 있다.
💡deque는 doubly-ended-queue의 약어인데, 양쪽에 동일하게 데이터를 삽입/삭제가 가능한 자료구조다.</p>
<h1 id="스택stack">스택(Stack)</h1>
<p>얘는 쌓여있는 접시를 생각하면 쉽다. 즉, 맨 꼭대기로부터 접시를 쌓거나 제거할 수 있기 때문에 <strong>LIFO(Last-In, First-Out)</strong> 자료구조를 갖는다.</p>
<h2 id="스택의-연산">스택의 연산</h2>
<p><code>push</code>: stack의 top에 데이터를 추가
<code>pop</code>: stack의 top에 데이터를 삭제</p>
<h2 id="스택의-구현">스택의 구현</h2>
<p>LinkedList도 동일하게 동작시킬 수 있지만, 스택의 연산은 top에서만 데이터를 삽입/삭제하면 되기 때문에 굳이 포인터를 추가해서 메모리를 더 사용하기보다는, 기본적인 <strong>ArrayList</strong>만으로도 O(1)을 가지기 때문에 리스트를 사용한다.</p>