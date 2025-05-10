<p>📢기능을 구현한 소스코드는 <a href="https://github.com/Seung-zedd/sbb_board">저의 리포지토리</a> 또는 <a href="https://github.com/pahkey/sbb3">책의 소스코드</a>
를 참고하시면 됩니다.</p>
<p>🔖컨트롤러: <strong>클라이언트의 URL 요청</strong>을 처리하는 계층</p>
<ul>
<li>GET, POST, PUT, PATCH, DELETE 등 여러가지 HTTP Method들이 있다.</li>
</ul>
<p>🔖폼(Form): <strong>사용자의 입력</strong>을 검증</p>
<ul>
<li>뒤에서 나올껀데, 사용자의 데이터를 처리하는 @RequestParam을 하나의 Form 객체로 묶어서 검증 처리를 할 수 있다.</li>
</ul>
<p>🔖DTO, 엔티티, 서비스: <strong>데이터베이스를 처리</strong>하기 위해 필요한 파일</p>
<p>🔖<code>src/main/resources</code>: <strong>자바 파일을 제외</strong>한 HTML, CSS, JS, 환경 파일(application.yml 등) 등을 저장하는 공간</p>
<ul>
<li>templates directory: 자바 코드를 삽입할 수 있는 HTML 형식의 파일을 저장하는 공간</li>
<li>static directory: css, js, 그리고 이미지 파일(jpg, png) 등을 저장할 수 있는 공간 </li>
<li>application.yml(또는 application.properties): sbb 폴젝트의 환경 변수, 데이터베이스 등의 설정을 저장</li>
</ul>
<p>💡이것이 가능한 이유는, Controller가 뷰 이름을 리턴하면 스프링의 ViewResolver는 </p>
<ol>
<li>templates의 동적 뷰 파일들을 탐색(스프링의 기본 템플릿 엔진은 타임리프)</li>
<li>static의 정적 리소스를 탐색
순서로 탐색하기 때문이다.</li>
</ol>
<h2 id="🔖controller와-restcontroller의-차이점">🔖<code>@Controller</code>와 <code>@RestController</code>의 차이점</h2>
<p><code>@Controller</code>: 문자열을 리턴하면 <strong>문자열에 해당하는 뷰 템플릿 파일을 탐색</strong>
<code>@RestController(@ResponseBody + @Controller)</code>: URL 요청에 대한 응답으로 <strong>자바 객체를 HTTP Response Body에 payload를 실어 보내는 것</strong>을 말한다.</p>
<ul>
<li>참고로, 스프링은 Jackson 라이브러리가 내장되어 있어서 Serialize(자바 객체 -&gt; JSON 변환)해서 클라이언트한테 보내줄 수 있다.</li>
</ul>
<h1 id="203-jpa로-데이터베이스-사용하기">2‑03 JPA로 데이터베이스 사용하기</h1>
<p>🔖엔티티(Entity): RM(Object-Relational-Mapping)에서는 자바 클래스를 말한다.</p>
<ul>
<li>데이터베이스의 테이블과 매핑되는데, 클래스 레벨에서 <code>@Entity</code>를 선언하면 알아서 매핑해준다.</li>
</ul>
<p>👍MySQL,오라클 DB, MS SQL과 같은 DBMS의 종류에 관계 없이 <strong>일관된 자바 코드</strong>를 사용할 수 있어서 프로그램을 유지·보수하기가 편리
🔖DB: 데이터를 담은 통
🔖DBMS: DB를 관리하는 소프트웨어</p>
<p>🔖JPA(Java Persistence API): 인터페이스 모음
🔖Hibernate: JPA를 구현한 <strong>실제 클래스</strong>이자 자바의 ORM 프레임워크
🔖JDBC(Java Database Connectivity): 자바 프로그램과 DB를 연결하여 데이터베이스 작업 수행을 가능하게 도와주는 자바 API 기술 </p>
<p>👨‍💻개발 환경에서는 H2로 가볍게 DB를 테스트하고, 실제 운영 시스템에서는 좀 더 규모 있는 DBMS(MySQL, 오라클 등)를 사용하는 것이 일반적</p>
<h2 id="springjpahibernateddl-auto의-규칙">spring.jpa.hibernate.ddl-auto의 규칙</h2>
<ul>
<li>none: 엔티티가 변경되더라도 DB 변경 x</li>
<li>update: <strong>엔티티의 변경된 부분</strong>만 DB에 적용</li>
<li>validate: 엔티티와 테이블 간에 차이점이 있는지만 <strong>검사</strong></li>
<li>create: 스프링부트 서버를 시작할 때 테이블을 모두 삭제한 후 다시 생성<ul>
<li>create-drop: 위와 동일하지만, <strong>스프링부트 서버를 종료</strong>할 때에도 테이블을 모두 삭제</li>
</ul>
</li>
</ul>
<p>💡개발 환경에서는 보통 update모드를 사용하고(테스트를 위함), 운영 환경에서는 none 또는 validate를 주로 사용</p>
<table>
<thead>
<tr>
<th>환경</th>
<th>ddl-auto 설정</th>
<th>이유</th>
</tr>
</thead>
<tbody><tr>
<td>개발 환경</td>
<td><code>update</code></td>
<td>빠른 기능 개발 및 테스트용</td>
</tr>
<tr>
<td>테스트/스테이징</td>
<td><code>validate</code></td>
<td>운영 환경과 동일하게 검증 목적</td>
</tr>
<tr>
<td>운영 환경</td>
<td><code>none</code></td>
<td>데이터 보호 + 마이그레이션 도구로 관리</td>
</tr>
<tr>
<td>🚨validate는 차이점이 있는지만 검사를 하지만, <span style="color: red;"><strong>불일치가 있는 상태에선 애플리케이션이 실행되지 않는다!</strong></span></td>
<td></td>
<td></td>
</tr>
</tbody></table>
<h1 id="204-엔티티로-테이블-매핑하기">2‑04 엔티티로 테이블 매핑하기</h1>
<p>먼저, 질문(Question) 엔티티와 답변(Answer) 엔티티에는 각각 어떤 속성들이 필요한지 아래의 다이어그램을 봐보자.</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/49295385-d4e9-499e-a9b1-05917ed5fd94/image.png" />
Question과 Answer는 1:N 관계로 설정하였다. 간단하게 보면, 부모와 자식 엔티티라고 생각해도 무방한데, 왜냐하면 실제로 어노테이션의 cascade타입을 <code>REMOVE</code>로 설정하였기 때문이다.</p>
<p>🤔왜 부모 엔티티 타입은 List로 데이터 타입을 지정해줘야 할까?
JPA는 <strong>컬렉션 타입을 프록시 객체로 감싸서 지연 로딩을 지원</strong>하기 때문이다.</p>
<ul>
<li>실제로 <code>@OneToMany</code>의 코드를 까보면 Fetch type이 default로 LAZY가 되어있다.</li>
</ul>
<h2 id="generatedvalue의-strategy-종류">@GeneratedValue의 strategy 종류</h2>
<p>🔖<code>@Id</code>: hibernate한테 이 어노테이션이 붙은 필드는 unique하고 not null &amp;&amp; unmodifiable하다고 알려줌</p>
<ul>
<li>데이터 타입은 주로 primitive type인 long 또는 Wrapper class인 Long을 사용하는데, 
전자는 값을 할당하지 않으면 인스턴스에 상관없이 default value로 0을 설정하기 때문에 DB에서 <span style="color: red;">duplicate entry 오류가 발생</span>하지만, 후자는 값을 할당하지 않으면 Null이 설정되기 때문에 수동으로 값을 설정해줘야 한다는 것을 알려줘서 보통 Long 타입을 사용한다.</li>
</ul>
<p>🔖<code>@GeneratedValue</code>: 위와 같이 수동으로 id값 설정 대신에 자동으로 생성하도록 해줌</p>
<h3 id="1-auto">1. AUTO</h3>
<p><strong>primary key 타입에 기반</strong>하여 JPA provider인 hibernate가 value를 결정한다.</p>
<ul>
<li>key 타입은 numerical 또는 _UUID_가 될 수 있는데, 전자는 sequence 또는 table generator에 기반하고, 후자는 UUID generator에 기반한다.</li>
</ul>
<p>🔖UUID(Universally Unique IDentifer): UUID generator에 의해 생성되는데 128비트 길이의 value를 가졌고, <strong>컴퓨터 시스템의 모든 시간을 통틀어서 고유값을 설정</strong>할 때 쓰인다.</p>
<p>👇아래와 같이 사용할 수 있다</p>
<pre><code class="language-java">@Entity
public class Course {

    @Id
    @GeneratedValue
    private UUID courseId;

    // ...
}</code></pre>
<h3 id="2-identity">2. IDENTITY</h3>
<p>IdentityGenerator에 의존하는 타입으로, 데이터베이스의 identity 컬럼에 의해 value가 생성된다.(주로 <strong>auto-incremented</strong> 타입)</p>
<ul>
<li>사용법은 id필드 위에 <code>@GeneratedValue (strategy = GenerationType.IDENTITY)</code>만 선언해주면 된다.</li>
</ul>
<p>⚠️batch update를 사용하지 못한다는 단점이 있다.</p>
<h3 id="3-sequence">3. SEQUENCE</h3>
<p>유일한 값을 순서대로 생성하는 특별한 DB 오브젝트로
<code>@SequenceGenerator</code>의 속성들을 의미한다.</p>
<p>엔티티 클래스에서는 다음과 같이 사용할 수 있다.</p>
<pre><code class="language-java">@Entity
@SequenceGenerator(
    name = &quot;user_seq&quot;,
    sequenceName = &quot;user_sequence&quot;,
    initialValue = 1,
    allocationSize = 10 // 시퀀스마다 DB로부터 메모리로 10개를 미리 할당해놓겠다는 의미
)
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = &quot;user_seq&quot;)
    private Long userId;
}</code></pre>
<ul>
<li>allocationSize는 쓰레드를 기준으로 한다.</li>
</ul>
<p>🤔IDENTITY와 SEQUENCE 타입의 차이점</p>
<ol>
<li>IDENTITY: 매번 트랜잭션에서 INSERT할 때마다 JDBC에서 새로운 ID를 요청하기 때문에 <span style="color: red;"><strong>성능이 저하</strong></span>될 수 있음</li>
<li>SEQUENCE: 미리 여러 개의 ID를 메모리로 확보 후 사용하기 때문에 <strong>성능 최적화</strong> 가능</li>
</ol>
<ul>
<li>여러 대의 서버에서 트랜잭션을 동시 호출하더라도, DB에서 메모리로 시퀀스 값을 확보하였기 때문에 <strong>동시성 문제가 해결가능</strong>하다.
💡순차적으로 ID값을 증가시키고 싶으면 allocationSize를 반드시 1로 설정할 것(중간에 서버 재시작시, 메모리에 캐싱되어있는 ID 값들이 사라져서 다음 블록을 가져오기 때문)</li>
</ul>
<h3 id="4-table권장-x">4. TABLE(권장 x)</h3>
<p>키 생성 전용 테이블을 하나 만들어서, <strong>DB 시퀀스를 흉내내는 전략</strong></p>
<ul>
<li>사실 ID 생성시에는 DB를 사용하기 때문에 이 전략을 자주 사용하지는 않음</li>
</ul>
<hr />
<p><code>@Column(columnDefinition = &quot;TEXT&quot;)</code>: columnDefinition은 열 데이터의 유형이나 성격을 정의할 때 사용하며, 우값은 '텍스트'를 컬럼 데이터로 넣을 수 있고, <strong>글자 수를 제한할 수 없는 경우에 사용</strong>한다.</p>
<hr />
<p>📚참고자료
🔗Generated Identifiers: <a href="https://www.baeldung.com/hibernate-identifiers">https://www.baeldung.com/hibernate-identifiers</a></p>