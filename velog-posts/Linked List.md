<p>🤔Linked List란?
: _Node_라는 구조체가 연결되는 형식으로 데이터를 저장하는 자료구조를 말한다.</p>
<p>🔖Node: 데이터 값(value)과 다음 주소값(next)로 구성되어 있는 구조체
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/f59e9e9e-8b3f-4f78-aeed-64ab38ecf678/image.png" /></p>
<p>💻Node는 간단하게 다음과 같이 클래스로 구현할 수 있다</p>
<pre><code class="language-python">class Node:
    def __init__(self, value = 0, next = None):
        self.value = value
        self.next = next</code></pre>
<p>💡지난 시간에 살펴본 Array는 메모리의 Random Access를 이용해서 순차적으로 데이터를 저장하는 방식을 사용하였지만, Linked List는 <strong>next 포인터로 다음 노드의 주소를 저장하면 논리적으로 연속성</strong>을 갖기 때문에 메모리 사용이 좀 더 자유롭다.</p>
<h2 id="⚙️링크드-리스트의-동작-과정">⚙️링크드 리스트의 동작 과정</h2>
<p>💻먼저 기본적인 링크드 리스트의 클래스는 다음과 같다.</p>
<pre><code class="language-python">class LinkedList:
    def __init__(self):
        self.head = None</code></pre>
<ul>
<li><code>self.head</code>: <strong>Linked List의 첫 번째 원소(노드)를 가리킴</strong><ul>
<li>그런데, 생성 초기에는 노드가 비어있으므로 <code>None</code>으로 설정해준다.</li>
</ul>
</li>
</ul>
<p>우리는 노드를 추가하고, 노드들을 &quot;논리적으로&quot; 연속성을 갖게 하는 <code>append</code> 함수를 살펴볼 것이다. 구체적으로 살펴보기 전에, 다음 2가지를 반드시 기억하자.</p>
<ol>
<li><strong>첫번째 노드</strong>는 무조건 <strong>head</strong>로 고정시켜준다.</li>
<li>노드를 추가할 때는 <strong>마지막 노드 다음에 추가</strong>해야 한다.
👉마지막 노드, 즉 <code>Node.next</code>값이 있는지 &quot;계속&quot; 확인하는 작업이 필요함</li>
</ol>
<p>🖼️위의 2가지를 숙지한 채로, 구체적으로 링크드 리스트의 append 함수의 동작 과정을 살펴보자.</p>
<img height="2000" src="https://velog.velcdn.com/images/csj0209/post/79d01a3a-2dea-4749-b99a-d07e898eefe3/image.png" width="2000" />

<ul>
<li>head는 첫번째 노드로 고정시켜줘야 하기 때문에, ptr 변수를 따로 불러와서 움직이게 하였다.</li>
</ul>
<p>🤔ptr는 변수에 불과한데 어떻게 <code>ptr.next</code>를 사용할 수 있나요?
❗<code>ptr = self.head = new_node</code>, 그러니까 <strong>ptr이 노드의 주소값</strong>을 가지고 있으면 가리키고 있는 노드의 next 포인터를 사용할 수가 있다.</p>
<p>👇지금부터 링크드 리스트의 CRUD를 구현하기 전에, <strong>head는 항상 1번째 노드(원소)를 가리키는 포인터</strong>고, ptr는 자유자재로 움직일 수 있는 포인터라는 것을 기억해두자.</p>
<p>🖼️아래의 insert 동작 과정을 살펴보자.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/4f58afd1-9eac-4c11-b9ae-470f772666a5/image.jpg" /></p>
<ul>
<li>idx가 0인 경우에도 idx &gt; 0일 때와 아이디어가 동일하다.</li>
</ul>
<h3 id="💻append-get-insert-함수in-java">💻append, get, insert 함수(in Java)</h3>
<pre><code class="language-java">class Node {
    int value;
    Node next;

    public Node(int value) {
        this.value = value;
        this.next = null;
    }
}

public class MyLinkedList {
    Node head;

    public MyLinkedList() {
        this.head = null;
    }

    public void append(int value) {
        Node newNode = new Node(value);
        if (head == null) {
            head = newNode;
        } else {
            Node ptr = head;
            while (ptr.next != null)
                ptr = ptr.next;
            ptr.next = newNode;
        }
    }

    public int get(int idx) {
        Node ptr = head;
        for (int i = 0; i &lt; idx; i++) {
            ptr = ptr.next;
        }
        return ptr.value;
    }

    public void insert(int idx, int value) {
        Node newNode = new Node(value);
        Node ptr = head;

        if (idx == 0) {
            newNode.next = head;
            head = newNode;
        } else {
            for (int i = 0; i &lt; idx - 1; i++) {
                ptr = ptr.next;
            }
            newNode.next = ptr.next;
            ptr.next = newNode;
        }
    }

}</code></pre>
<p>💡next 포인터를 Node 타입으로 선언한 이유는 그래야 <strong>다른 Node 객체를 참조</strong>할 수 있기 때문이다.
💡append 함수는 스택 영역에서 생성되어 지역 변수들이 사라지지만, 힙 영역에 있는 Node 객체는 여전히 남아있기 때문에 <strong>포인터가 가리키고 있는 주소값도 마찬가지로 유지</strong>된다.</p>
<p>🖼️remove 동작 과정도 insert와 유사하다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/4923ee36-268e-4f19-9cc1-acd778fac8d2/image.jpg" /></p>
<h3 id="💻removeidx-함수">💻remove(idx) 함수</h3>
<pre><code class="language-java">public void remove(int idx) {
        Node ptr = head;

        if (idx == 0) {
            head = head.next; // head를 이동
        } else {
            for (int i = 0; i &lt; idx - 1; i++) {
                ptr = ptr.next; // 바로 직전 노드까지 이동
            }
            ptr.next = ptr.next.next; // 삭제하려고 하는 노드의 next값을 바로 직전 노드가 가리키게 함
        }
    }</code></pre>
<h2 id="⚙️tail을-추가한-append-함수">⚙️tail을 추가한 append 함수</h2>
<p>이전에 구현한 append 함수는 ptr 노드가 newNode를 가리키게 하기 위해 while문을 타고 이동시켰지만, <strong>tail이 항상 newNode를 가리키게 만들면</strong> append 함수는 <code>O(1)</code>만 걸리게 구현할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/2039fe41-ffaa-4f16-88b8-bf82783197f8/image.jpg" /></p>
<h3 id="💻appendvalue함수-구현">💻append(value)함수 구현</h3>
<pre><code class="language-java">
public class LinkedList {
    Node head;
    Node tail;

    public LinkedList() {
        this.head = null;
        this.tail = null;
    }

    public void append(int value) {
        Node newNode = new Node(value);
        if (head == null) {
            head = newNode;
            tail = newNode;
        } else {
            tail.next = newNode;
            tail = tail.next; // tail은 항상 마지막 노드를 가리킴
        }
    }
} </code></pre>
<ul>
<li>결국, head는 항상 1번째 노드(원소)를 가리키게 하고, tail은 항상 마지막 노드(원소)를 가리키게만 수정하면 된다.</li>
</ul>
<h2 id="⚙️doubly-linkedlist의-append-동작-과정">⚙️Doubly LinkedList의 append 동작 과정</h2>
<p>별거없다. 그냥 Node 클래스에서 <strong>이전의 노드를 가리키기 위한 prev 포인터를 추가</strong>하고, 위의 구현한 tail 포인터를 활용한 append 함수에서 약간의 코드만 수정해주면 된다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/e6c06598-c585-4b63-bb3b-957a26c4fcb1/image.jpg" /></p>
<h3 id="💻더블-링크드-리스트-append-함수">💻더블 링크드 리스트 append 함수</h3>
<pre><code class="language-java">class DoublyNode {
    int value;
    DoublyNode next;
    DoublyNode prev;

    public DoublyNode(int value) {
        this.value = value;
        this.next = null;
        this.prev = null;
    }
}

public class MyDoublyLinkedList {
    DoublyNode head = null;
    DoublyNode tail = null;

    public void append(int value) {
        DoublyNode newNode = new DoublyNode(value);
        if (head == null) {
            head = newNode;
            tail = newNode;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = tail.next; // tail 업데이트
        }
    }
}</code></pre>
<h2 id="⏰linkedlist와-array의-시간복잡도-비교">⏰LinkedList와 Array의 시간복잡도 비교</h2>
<p>앞에는 링크드 리스트가 무엇이고 CRUD 동작 과정이 어떻게 되는지 사설이 좀 길었지만, 물리적인 메모리를 기반으로 구현한 Array와 시간복잡도를 비교해보면서 <strong>왜 링크드 리스트를 사용하는지</strong>를 알아보자.</p>
<p>📢Array는 메모리의 Random Access를 기반으로 하기 때문에 연속성을 갖는 반면, Linked List는 물리적으로 연속성을 갖지는 않지만, next 포인터로 다음 노드의 참조값을 갖기 때문에 _&quot;논리적으로 연속성을 갖는다&quot;_는 점에 유의하자. </p>
<table>
<thead>
<tr>
<th></th>
<th>Linked List</th>
<th>Array</th>
</tr>
</thead>
<tbody><tr>
<td>access/update</td>
<td>O(N)</td>
<td>O(1)</td>
</tr>
<tr>
<td>insert_front</td>
<td>O(1)</td>
<td>O(N)</td>
</tr>
<tr>
<td>insert_at</td>
<td>O(N)</td>
<td>O(N)</td>
</tr>
<tr>
<td>insert_back</td>
<td>O(N) 또는 O(1)</td>
<td>O(1)</td>
</tr>
<tr>
<td>remove_front</td>
<td>O(1)</td>
<td>O(N)</td>
</tr>
<tr>
<td>remove_at</td>
<td>O(N)</td>
<td>O(N)</td>
</tr>
<tr>
<td>remove_back</td>
<td>O(N) 또는 O(1)</td>
<td>O(1)</td>
</tr>
<tr>
<td>- access/update: 링크드 리스트의 경우 next 포인터를 타야하는 것과 달리, Array는 메모리의 Random Access를 이용해서 O(1)만 걸린다.</td>
<td></td>
<td></td>
</tr>
<tr>
<td>- insert_front/remove_front: 링크드 리스트는 head의 포인터를 이용만 하면 되기 때문에 O(1)이 걸리는 반면, Array는 원소들을 모두 right-shift 해줘야 하기 때문에 O(N)이 걸린다.</td>
<td></td>
<td></td>
</tr>
<tr>
<td>- insert_back/remove_back: 위와 다르게 링크드 리스트는 next 포인터를 타야되서 O(N)이 걸리는 것과 달리, Array는 그냥 맨 뒤에 삽입하거나 삭제하면 되서 O(1)이 걸린다.</td>
<td></td>
<td></td>
</tr>
</tbody></table>
<p>💡insert_back과 remove_back의 경우 tail 포인터가 추가된 Doubly Linked List로 구현되어있으면 O(1)만 걸린다.</p>
<p>📝결국, (더블)링크드 리스트를 사용하는 이유는 head 포인터(필요하다면 tail 포인터)를 사용함으로써 <strong>삽입/삭제가 O(1)</strong>만 걸리기 때문에 사용한다고 정리할 수 있다.</p>