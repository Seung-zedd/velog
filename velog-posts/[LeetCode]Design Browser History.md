<p><a href="https://leetcode.com/problems/design-browser-history/description/">https://leetcode.com/problems/design-browser-history/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>당신은 homepage로부터 시작하는 브라우저 탭을 가지고 있습니다. 그리고 당신은 다른 url 페이지를 방문할 수 있고, 기록에 있는 steps만큼 뒤로 이동하거나 앞으로 이동할 수 있습니다.</p>
<ul>
<li><code>BrowserHistory(string homepage)</code>: homepage 인자를 갖는 객체를 인스턴스화해라.</li>
<li><code>void visit(String url)</code>: 현재 페이지를 방문해라. <strong>단, 모든 앞의 기록은 날라간다.</strong></li>
<li><code>String back(int steps)</code>: 현재 페이지에서 뒤로 이동하는데, 기록에서 x steps 만큼 뒤로 갈 수 있고, <code>steps &gt; x</code>라면 x steps만큼 가서 현재 url 페이지를 리턴하라.</li>
<li><code>String forward(int steps)</code>: 위와 마찬가지</li>
</ul>
<p>🚧제약조건
홈페이지나 url의 길이, 그리고 steps는 사실상 시간복잡도와 관련이 없는데, 이유는 <strong>총 노드 개수에 따라 시간복잡도가 달라지기 때문</strong></p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>BrowserHistory를 생성할 때, homepage라는 인자를 갖고 인스턴스화한다. 그런데, <code>back(int steps)</code>의 steps가 현재 기록되어 있는 노드보다 크다면 결국에는 homepage를 리턴해야 되는데, <code>return cur.url(=homepage)</code>가 되려면 <strong>homepage도 Node의 value</strong>가 될 수 밖에 없다는 결론이 나온다.</p>
<h2 id="🤔array가-아니라-doubly-linkedlist로-구현할-수-밖에-없는-이유">🤔Array가 아니라 Doubly LinkedList로 구현할 수 밖에 없는 이유</h2>
<p>문제 요구사항 중 <code>visit()</code> 함수는 <em>단, 모든 앞의 기록은 날라간다.</em> 라는 조건이 핵심인데, 만약에 <strong>Array로 구현</strong>하고 <code>visit()</code>로 새 데이터를 만든 다음 <code>forward()</code>를 호출하면,
메모리에 기존 데이터가 남아있어서 <strong><span style="color: red;">현재 페이지를 리턴하지 않고  다음 원소에 있는 기존 url을 리턴하니까 조건에 위배</span></strong>되기 때문이다.
👉따라서, List의 자료구조 중 LinkedList를 선택한다.</p>
<p>어짜피 특정 원소에 접근에서 value만 읽으면 되는데, 왜 
💡더 쉽게 생각하면, 이전에 학습했던 링크드 리스트 CRUD 함수 구현과 마찬가지로 <strong>BrowserHistory 생성자 함수</strong>를 호출할 때 <strong>Node cur가 homepage 파라미터를 가진 노드 객체를 가리키게 하면 된다.</strong></p>
<pre><code class="language-java">class LinkedList {
    Node head;

    public LinkedList() {
        this.head = null;
    }</code></pre>
<ul>
<li>visit(): <code>append()</code>함수와 비슷하게 cur 포인터를 이동시켜서 newNode를 연결하면 된다.</li>
<li>back(): cur를 기점으로 이전 노드로 이동시키고, 만약 steps &gt; x 라면 <code>cur.prev != null</code>일 때까지 계속해서 뒤로 이동시키고 null이 나오면 cur.url을 리턴하면 된다.<ul>
<li>현재 노드 수만큼의 x step 뒤로 이동하라고 하면, 간단하게 step이 0이 아닐 때까지 step을 1씩 감소시키면서 뒤로 이동시키고 0이 되면 cur.url을 리턴하면 된다.</li>
<li>forward(): back과 마찬가지</li>
</ul>
</li>
</ul>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class BrowserHistory {
    private String homepage;
    private Node cur; // 포인터를 이동해서 url을 리턴하기 위함

    static class Node {
        String url;
        Node next;
        Node prev;

        public Node(String url) {
            this.url = url;
            this.next = null;
            this.prev = null;
        }
    }

    public BrowserHistory(String homepage) {
        cur = new Node(homepage);
    }

    public void visit(String url) {
        Node newNode = new Node(url);
        cur.next = newNode; // 생성된 노드와 연결
        newNode.prev = cur;
        cur = cur.next; // cur를 업데이트
    }

    public String back(int steps) {
        while (cur.prev != null &amp;&amp; steps != 0) {
            cur = cur.prev;
            steps -= 1;
        }
        return cur.url;
    }


    public String forward(int steps) {
        while (cur.next != null &amp;&amp; steps != 0) {
            cur = cur.next;
            steps -= 1;
        }
        return cur.url;
    }
}</code></pre>
<p>⏰시간복잡도는 생성된 노드 수 만큼 <code>back()</code>과 <code>forward()</code>를 하기 때문에 O(N)이 걸린다.</p>