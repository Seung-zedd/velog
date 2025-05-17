<h1 id="jpa-cascade-type">JPA Cascade Type</h1>
<p>JPA의 여러가지 Cascade 타입을 알아보기 전에, Cascading이 무엇인지 먼저 살펴보자.</p>
<blockquote>
<p>Cascading은 우리가 타겟 엔티티에 대해 어떤 액션을 취할 때, 같은 액션이 <strong>연관된 엔티티에도 적용</strong>되는 것을 말한다.</p>
</blockquote>
<p>JPA 스펙에 명시된 cascade type은 ALL, PERSIST, MERGE, REMOVE, REFRESH, 그리고 DETACH 열거형을 지원한다.
💡참고로 하이버네이트 구현체는 추가적으로 REPLICATE, SAVE_UPDATE, LOCK 열거형을 추가로 지원한다.</p>
<h2 id="cascadetypeall">CascadeType.ALL</h2>
<p>부모 엔티티(타겟)부터 자식 엔티티(연관된 엔티티)까지의 <strong>모든 연산들을 전파</strong>시킨다.</p>
<ul>
<li>모든 연산에는 하이버네이트 구현체의 열거형도 포함한다.<pre><code class="language-java">@Entity
public class Person {
  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private int id;
  private String name;
  @OneToMany(mappedBy = &quot;person&quot;, cascade = CascadeType.ALL)
  private List&lt;Address&gt; addresses;
}</code></pre>
</li>
<li>자식 엔티티는 보통 컬렉션 타입으로 구성되기 때문에 <code>@OneToMany</code>를 붙이는데, 이때 우리가 지금부터 알아볼 cascading들은 <strong>컬렉션에 있는 모든 자식 객체에 전파</strong>가 된다는 것에 유의하자.</li>
</ul>
<h2 id="cascadetypepersist">CascadeType.PERSIST</h2>
<p>transient 인스턴스를 영속화시키는 연산을 의미한다. 
당연히 <strong>부모 엔티티로부터 자식 엔티티까지 영속성이 전파</strong>된다. </p>
<ul>
<li>리포지토리에서 부모 엔티티를 save하면 자식 엔티티도 자동으로 save가 된다는 말이다.</li>
</ul>
<h2 id="cascadetypemerge">CascadeType.MERGE</h2>
<p>병합 연산은 <strong>주어진 객체의 업데이트 상태를 동일한 id를 가진 영속성 객체에 복사</strong>하는 것을 의미한다.</p>
<pre><code class="language-java">@Test
public void whenParentSavedThenMerged() {
    int addressId;
    Person person = buildPerson(&quot;devender&quot;);
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    addressId = address.getId();
    session.clear();

    Address savedAddressEntity = session.find(Address.class, addressId);
    Person savedPersonEntity = savedAddressEntity.getPerson();
    savedPersonEntity.setName(&quot;devender kumar&quot;);
    savedAddressEntity.setHouseNumber(24);
    session.merge(savedPersonEntity);
    session.flush();
}</code></pre>
<p>엔티티의 상태를 업데이트하기 위해 <code>flush()</code>로 영속성 컨텍스트를 DB에 먼저 반영시켰고, 동일한 id를 가진 엔티티를 불러와서 부모 엔티티와 자식 엔티티의 상태를 모두 변경시켰다.
이때, <code>session.merge(savedPersonEntity)</code>를 해서 부모 엔티티만 merge시켰지만, 자식 엔티티도 merge가 똑같이 적용된다.</p>
<p>다음과 같은 하이버네이트의 SQL 결과 쿼리를 살펴보자.</p>
<pre><code class="language-sql">Hibernate: select address0_.id as id1_0_0_, address0_.city as city2_0_0_, address0_.houseNumber as houseNum3_0_0_, address0_.person_id as person_i6_0_0_, address0_.street as street4_0_0_, address0_.zipCode as zipCode5_0_0_ from Address address0_ where address0_.id=?
Hibernate: select person0_.id as id1_1_0_, person0_.name as name2_1_0_ from Person person0_ where person0_.id=?
Hibernate: update Address set city=?, houseNumber=?, person_id=?, street=?, zipCode=? where id=?
Hibernate: update Person set name=? where id=?</code></pre>
<p>부모 엔티티와 더불어 자식 엔티티에도 update 쿼리가 나갔음을 확인할 수 있다.</p>
<h2 id="cascadetyperemove">CascadeType.REMOVE</h2>
<p>엔티티에 대응되는 행을 영속성 컨텍스트와 DB로부터 제거한다.
💡DB의 실제 레코드가 제거되는 시점은 트랜잭션이 커밋되기 전에 <code>flush()</code>가 호출될 때다.
🔎하이버네이트의 CascadeType.DELETE도 위와 동일한 연산을 실행한다.</p>
<h2 id="cascadetypedetach">CascadeType.DETACH</h2>
<p>영속성 컨텍스트에서 엔티티를 제거해서 <strong>JPA가 엔티티의 상태를 관리하지 않는다.</strong> 이때, 자식 엔티티 또한 마찬가지로 영속성 컨텍스트에서 제거된다.</p>
<h2 id="cascadetypelock">CascadeType.LOCK</h2>
<p>영속성 컨텍스트에 엔티티를 다시 붙여서 관리하는 연산을 말한다.</p>
<h2 id="cascadetyperefresh">CascadeType.REFRESH</h2>
<p><strong>DB로부터의 주어진 인스턴스의 값</strong>을 다시 불러오는 연산을 말한다. 우리는 <code>persist()</code> 호출로 영속성 컨텍스트 저장 이후에 인스턴스의 상태를 변경할 수도 있는데, 이때 REFRESH 연산을 하면 <strong>초기에 DB에 저장되었던 인스턴스의 상태</strong>를 불러올 수 있다.</p>
<pre><code class="language-java">@Test
public void whenParentRefreshedThenChildRefreshed() {
    Person person = buildPerson(&quot;devender&quot;);
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    person.setName(&quot;Devender Kumar&quot;);
    address.setHouseNumber(24);
    session.refresh(person);

    assertThat(person.getName()).isEqualTo(&quot;devender&quot;);
    assertThat(address.getHouseNumber()).isEqualTo(23);
}</code></pre>
<ul>
<li>즉, <code>flush()</code> 이후의 set 상태로 변경되는 것이 아니라, 초기의 상태로 불러올 수 있다.</li>
</ul>
<h2 id="cascadetypereplicate">CascadeType.REPLICATE</h2>
<p>우리가 <strong>하나 이상의 data source를 가지고 있고 data를 동기화하고 싶을 때</strong> 보통 사용한다. sync 연산 또한 마찬가지로 연관된 자식 엔티티에도 적용이 된다.</p>
<h2 id="cascadetypesave_update">CascadeType.SAVE_UPDATE</h2>
<p>연산 방법이 PERSIST와 MERGE를 합친 것과 비슷한데, 이것은 특별히 하이버네이트 구현체일 때만 사용할 수 있다. 따라서 일반 JPA 스펙에서는 <code>cascade = {CascadeType.PERSIST, CascadeType.MERGE}</code>로 동일하게 연산을 적용할 수가 있다.</p>
<hr />
<h1 id="208-루트-url-사용하기">2‑08 루트 URL 사용하기</h1>
<p>🔖루트 URL: 도메인명 뒤에 아무런 주소도 덧붙이지 않는 URL</p>
<ul>
<li><a href="http://localhost:8080">http://localhost:8080</a><ul>
<li>컨트롤러에서는 <code>@GetMapping(&quot;/&quot;)</code>로 하면 메인 페이지를 매핑할 수 있다
🔖리다이렉트: 클라이언트가 요청하면 새로운 URL로 전송하는 것</li>
</ul>
</li>
</ul>
<h1 id="209-서비스-활용하기">2‑09 서비스 활용하기</h1>
<h2 id="🤔서비스가-필요한-이유">🤔서비스가 필요한 이유</h2>
<p>🔖서비스(service): 스프링에서 <strong>데이터 처리</strong>를 위해 작성하는 클래스</p>
<ol>
<li>복잡한 코드 <strong>모듈화</strong></li>
<li>엔티티 객체를 <strong>DTO 객체로 변환</strong>할 수 있다.</li>
</ol>
<ul>
<li>엔티티 클래스는 DB와 밀접한 위치에 있는 클래스이고 민감한 데이터가 포함될 수 있기 때문에, <span style="color: red;"><strong>컨트롤러나 템플릿같은 뷰 계층에 엔티티 객체를 노출시키면 안된다!</strong></span></li>
</ul>
<h1 id="212-답변-기능-만들기">2‑12 답변 기능 만들기</h1>
<p>🔖@RequestParam: 사용자의 요청으로부터의 쿼리 파라미터, 폼 파라미터, 심지어는 파일들을 추출할 때 사용한다.</p>
<ul>
<li><strong>value와 변수 이름이 동일</strong>해야 하지만, 종종 name 속성을 사용해서 변수 이름과 다르게 사용할 수도 있다.</li>
</ul>
<h1 id="302-페이징-기능-추가하기">3‑02 페이징 기능 추가하기</h1>
<p>💡참고로, 아래의 3가지 용어들은 JPA 관련 라이브러리에 이미 포함된 페이징 패키지들이다.
🔖Page: 페이징을 위한 클래스
🔖PageRequest: <strong>현재 페이지와 한 페이지에 보여줄 게시물 개수</strong> 등을 설정하여 페이징 요청을 하는 클래스</p>
<p>🔖Pageable: 페이징을 처리하는 인터페이스</p>
<ul>
<li>인터페이스 이름은 끝이 -able로 끝남</li>
</ul>
<p>🔖Pagination: 서버가 아주 큰 데이터셋이 있고 우리가 그 <strong>데이터셋을 덩어리(chunk) 단위로 사용자한테 보여주고 싶을 때 사용</strong>한다.
🔖정렬: 페이징하는 동안 특정 기준을 가지고 데이터를 정렬하고 싶을 때 사용
👉우리는 위의 2가지 기능을** 스프링 데이터 JPA**를 통해 쉽게 사용할 수 있다.</p>
<p>Product 엔티티를 만들어서 간단하게 Pagination과 Sort를 구현해보자.</p>
<h3 id="💻product-엔티티">💻Product 엔티티</h3>
<pre><code class="language-java">@Entity 
public class Product {

    @Id
    private long id;
    private String name;
    private double price; 

    // constructors, getters and setters 

}</code></pre>
<h3 id="📜productrepository-인터페이스">📜ProductRepository 인터페이스</h3>
<pre><code class="language-java">public interface ProductRepository extends PagingAndSortingRepository&lt;Product, Integer&gt; {

    List&lt;Product&gt; findAllByPrice(double price, Pageable pageable);
}</code></pre>
<ul>
<li>페이징과 정렬을 위한 <code>findAll(Pageable pageable)</code>과 <code>findAll(Sort sort)</code> 메서드를 사용할 수 있다.<ul>
<li>또한, 위와 마찬가지로 Pageable, Sort 객체를 파라미터로 전달해서 <strong>사용자 정의 메서드</strong>로 만들 수도 있다.</li>
<li>💡PagingAndSortingRepository 대신 JpaRepository를 상속해도 동일하게 Pageable, Sort 객체를 사용할 수 있다.</li>
</ul>
</li>
</ul>
<p>서비스 계층에서 리포지토리의 메서드를 사용하기 위해서는:</p>
<ol>
<li>Pageable 인터페이스의 구현체가 되는 PageRequest 객체를 생성해야 한다.</li>
</ol>
<pre><code class="language-java">Pageable firstPageWithTwoElements = PageRequest.of(0, 2);

Pageable secondPageWithFiveElements = PageRequest.of(1, 5);</code></pre>
<ul>
<li>정적 팩토리 메서드인 <code>of()</code>는 Pageable의 인터페이스를 오버라이딩하기 때문에 Pageable 인터페이스 타입을 리턴한다.
⚠️스프링부트에서 <strong>페이지는 0부터 시작</strong>하므로, 사용자에게 첫 페이지를 보여주고 싶을 때는 0을 1번째 인자에 넣으면 된다. </li>
</ul>
<ol start="2">
<li><strong>서비스 계층</strong>에서 우리가 사용하려고 하는 리포지토리 메서드의 인자로 PageRequest 객체를 전달해야 한다.</li>
</ol>
<pre><code class="language-java">Page&lt;Product&gt; allProducts = productRepository.findAll(firstPageWithTwoElements);

List&lt;Product&gt; allTenDollarProducts = 
  productRepository.findAllByPrice(10, secondPageWithFiveElements);</code></pre>
<p>💡<code>findAll(Pageable pageable)</code> 메서드는 <strong>기본적으로 <code>Page&lt;T&gt;</code> 객체를 리턴</strong>한다.
그러나, 우리는 사용자 정의 메서드를 통해 <code>Page&lt;T&gt;</code>, <code>Slice&lt;T&gt;</code> 또는 <code>List&lt;T&gt;</code>의 객체를 리턴할 수도 있다.
🔎<code>Page&lt;T&gt;</code> 인스턴스는 엔티티의 리스트를 가지고 있는 것 뿐만 아니라, 사용 가능한 페이지의 전체 수도 알기 때문에 그것을 알기 위한 추가 쿼리가 나가서 <span style="color: red;"><strong>오버헤드 비용이 발생</strong></span>한다. 
따라서 오버헤드 비용을 줄이고 싶으면 <code>Slice&lt;T&gt;</code> 또는 <code>List&lt;T&gt;</code> 객체를 리턴하자.
🔎<code>Slice&lt;T&gt;</code>는 다음 슬라이스가 사용 가능한지 아닌지에 대해서만 안다.</p>
<p>보통 정렬 기능을 사용할 때는 페이지 기능도 함꼐 사용하기 때문에 다음과 같이 PageRequest 인자에 Sort 객체를 전달해주기만 하면 된다.</p>
<pre><code class="language-java">Pageable sortedByName = 
  PageRequest.of(0, 3, Sort.by(&quot;name&quot;));

Pageable sortedByPriceDesc = 
  PageRequest.of(0, 3, Sort.by(&quot;price&quot;).descending());

Pageable sortedByPriceDescNameAsc = 
  PageRequest.of(0, 5, Sort.by(&quot;price&quot;).descending().and(Sort.by(&quot;name&quot;)));</code></pre>
<p>또한, 우리는 위에서 JPA가 제공해주는 메서드를 통해 정렬만 구현할 수도 있다.</p>
<pre><code class="language-java">Page&lt;Product&gt; allProductsSortedByName = productRepository.findAll(Sort.by(&quot;name&quot;));</code></pre>
<hr />
<p>📚참고자료
🔗JPA Cascade Type: <a href="https://www.baeldung.com/jpa-cascade-types">https://www.baeldung.com/jpa-cascade-types</a>
🔗Pagination and Sort using Spring Data JPA: <a href="https://www.baeldung.com/spring-data-jpa-pagination-sorting">https://www.baeldung.com/spring-data-jpa-pagination-sorting</a></p>