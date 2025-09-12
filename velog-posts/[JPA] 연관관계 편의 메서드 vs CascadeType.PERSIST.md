<h1 id="연관관계-편의-메서드-vs-cascadetypepersist-왜-둘-다-필요할까">연관관계 편의 메서드 vs CascadeType.PERSIST, 왜 둘 다 필요할까?</h1>
<p>JPA에서 양방향 연관관계를 설정할 때, 많은 개발자들이 CascadeType.PERSIST 옵션만 있으면 모든 것이 자동으로 처리될 것이라고 오해합니다. 하지만 연관관계 편의 메서드와 <strong>영속성 전이(Cascade)</strong>는 완전히 다른 책임과 목적을 가진, 반드시 함께 사용해야 하는 환상의 파트너입니다.</p>
<p>이 둘의 차이를 &quot;출생 신고&quot;에 비유하면 아주 명확하게 이해할 수 있습니다.</p>
<p>연관관계 편의 메서드: 📝 서류 작성 단계</p>
<p>CascadeType.PERSIST: 🗄️ 서류 일괄 제출 단계</p>
<h2 id="연관관계-편의-메서드-객체-세상의-일-메모리">연관관계 편의 메서드: 객체 세상의 일 (메모리)</h2>
<p>이 메서드의 유일한 목적은 자바 메모리 위에서 객체들의 관계를 100% 완벽하게 만들어주는 것입니다. 즉, 객체 그래프의 일관성을 유지하는 역할을 합니다.</p>
<pre><code class="language-java">// Order.java
public void addOrderItem(OrderItem orderItem) {
    // 1. 내(Order) 리스트에 orderItem을 추가한다.
    this.orderItems.add(orderItem); 

    // 2. orderItem에게도 내가 너의 주인(Order)이라고 알려준다.
    orderItem.setOrder(this); 
}</code></pre>
<p>만약 이 메서드 없이 order.getOrderItems().add(orderItem)만 호출한다면, Order는 OrderItem을 알지만, OrderItem은 자신의 주인(order 필드)이 누구인지 모르는 '반쪽짜리' 관계가 됩니다. 이 상태에서 orderItem.getOrder()를 호출하면 NullPointerException이 발생하겠죠.</p>
<p>핵심: 데이터베이스에 저장하기 이전에, 순수한 자바 객체들끼리 서로를 완벽하게 참조하도록 관계를 맺어주는 코드입니다.</p>
<h2 id="cascadetypepersist-db-세상의-일-영속성">CascadeType.PERSIST: DB 세상의 일 (영속성)</h2>
<p>이 옵션은 JPA(Hibernate)에게 &quot;내가 데이터베이스에 저장될 때, 나에게 속한 자식들도 알아서 같이 저장해줘!&quot; 라고 지시하는 '편의 기능'입니다.</p>
<p>이 옵션이 없다면, 우리는 서비스 계층에서 부모와 자식을 각각 저장해야 합니다.</p>
<p>❌ Cascade가 없을 때 (불편한 코드)</p>
<pre><code class="language-java">orderRepository.save(order);
orderItemRepository.save(item1); // 자식들을 일일이 저장
orderItemRepository.save(item2);</code></pre>
<p>✅ CascadeType.PERSIST가 있을 때 (편리한 코드)</p>
<pre><code class="language-java">// 부모인 order만 저장하면, JPA가 알아서 자식들도 함께 저장해준다!
orderRepository.save(order); </code></pre>
<p>PERSIST는 <code>save()</code>가 호출되었을 때, INSERT 쿼리를 어떻게 전파할지에 대한 '명령'일 뿐, 객체들의 참조 관계를 설정해주지는 않습니다.
👉핵심: 하나의 <code>save()</code> 호출로 <strong>부모와 자식 객체를 한 번에 영속화</strong>시켜주는 편리한 위임 옵션입니다.</p>
<h2 id="📅한눈에-보는-최종-비교">📅한눈에 보는 최종 비교</h2>
<table>
<thead>
<tr>
<th>구분</th>
<th>연관관계 편의 메서드</th>
<th><code>CascadeType.PERSIST</code></th>
</tr>
</thead>
<tbody><tr>
<td><strong>역할</strong></td>
<td><strong>객체 관계 설정</strong></td>
<td><strong>영속성 전파</strong></td>
</tr>
<tr>
<td><strong>처리 시점</strong></td>
<td>객체 생성 및 관계 설정 시 (메모리)</td>
<td><code>save()</code> 호출 후 <code>flush()</code> 시점 (DB)</td>
</tr>
<tr>
<td><strong>목적</strong></td>
<td>객체 그래프의 일관성 유지</td>
<td><code>save()</code> 코드의 간결화</td>
</tr>
<tr>
<td><strong>비유</strong></td>
<td>📝 서류 양식에 부모, 자식 이름 정확히 기입하기</td>
<td>🗄️ 부모 서류 제출 시, 자식 서류도 함께 제출하기</td>
</tr>
</tbody></table>
<p>이처럼 두 가지는 서로 다른 계층에서 각자의 책임을 다하기 때문에, 안전하고 견고한 코드를 위해서는 반드시 둘 다 올바르게 구현해주어야 합니다.</p>