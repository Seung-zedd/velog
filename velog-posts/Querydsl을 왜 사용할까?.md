<p>📢이 시리즈는 <a href="https://product.kyobobook.co.kr/detail/S000001624717">백견불여일타 스프링 부트 쇼핑몰 프로젝트 with JPA</a> 책을 보고 작성한 내용으로, 모든 저작권은 해당되는 책에 있습니다.</p>
<p>복잡한 쿼리는 보통 <code>@Query</code>안의 String 타입으로 JPQL을 작성해서 처리하는데, 
🚩이렇게 메서드를 작성하면 JPQL에 오타가 생겨도 <span style="color: blue;"><strong>컴파일 타임</strong></span>에서는 syntax 에러를 확인할 수 없고 <span style="color: red;"><strong>런타임 환경에서만 확인할 수 있기 때문에 에러를 조기에 발견할 수가 없다.</strong></span></p>
<p>🔨이를 해결해주는 것이 Querydsl이다.
🔖Querydsl: JPQL을 코드로 작성할 수 있도록 도와주는 빌더 API다. 즉, SQL문을 문자열이 아닌 <strong>코드로 작성</strong>하기 때문에 <strong>컴파일러의 도움</strong>을 받을 수 있다.</p>
<h1 id="👍querydsl의-장점">👍Querydsl의 장점</h1>
<ul>
<li>고정된 SQL문이 아닌 조건에 맞게 <strong>동적으로 쿼리</strong> 생성</li>
<li>비슷한 쿼리를 <strong>재사용</strong>할 수 있으며 <strong>제약 조건 조립 및 가독성 향상</strong></li>
<li>IDE의 도움을 받아서 <strong>자동 완성 기능</strong> 이용할 수 있기 때문에 생산성 향상</li>
</ul>
<h1 id="🏭querydsl-사용-방법">🏭Querydsl 사용 방법</h1>
<h2 id="1-의존성-설정">1. 의존성 설정</h2>
<p>build.gradle파일에 QueryDSL 관련 의존성을 추가한다.</p>
<pre><code class="language-java">dependencies {
    (...생략...)

    // QueryDSL 의존성 추가
    implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
    annotationProcessor &quot;com.querydsl:querydsl-apt:5.0.0:jakarta&quot;
    annotationProcessor &quot;jakarta.annotation:jakarta.annotation-api&quot;
    annotationProcessor &quot;jakarta.persistence:jakarta.persistence-api&quot;
}

// Q-Type 클래스 생성 위치 설정
def generatedDir = 'src/main/generated'

sourceSets {
    main.java.srcDirs += [ generatedDir ]
}

tasks.withType(JavaCompile) {
    options.getGeneratedSourceOutputDirectory().set(file(generatedDir))
}

clean.doLast {
    file(generatedDir).deleteDir()
}</code></pre>
<p>⚠️위의 <code>clean.doLast</code>부분을 작성하지 않으면 <strong>Gradle에서 clean 태스크는 기본적으로 build 디렉토리만 삭제</strong>하기 때문에 Q-Type 파일들은 그대로 남아있게 되어 <span style="color: blue;"><strong>컴파일 오류가 발생</strong></span>하거나 <span style="color: red;"><strong>예기치 못한 Exception이 발생</strong></span>할 수 있다.
👉build.gradle 설정에서 Q-Type 클래스가 생성될 위치를 <code>src/main/generated</code>로 지정했기 때문</p>
<h2 id="2-jpaqueryfactory-빈bean-등록">2. JPAQueryFactory 빈(Bean) 등록</h2>
<p>QueryDSL 쿼리를 생성하고 실행하는 핵심적인 역할을 하는 <strong>JPAQueryFactory를 스프링 빈으로 먼저 등록</strong>해야 한다. <code>src/main/java/configuration</code> 패키지에서 QuerydslConfig 클래스에<code>@Configuration</code> 어노테이션을 사용하여 설정 클래스를 만들고 EntityManager를 주입받아 JPAQueryFactory를 생성한다.</p>
<pre><code class="language-java">package com.example.demo.config;

import com.querydsl.jpa.impl.JPAQueryFactory;
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class QuerydslConfig {

    @PersistenceContext
    private EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }
}</code></pre>
<p>🔎참고: <code>@PersistenceContext</code>는 EntityManager를 주입받기 위한 표준 어노테이션이다. <code>@Autowired</code>를 사용해도 동일하게 동작하지만, <strong>영속성 컨텍스트를 주입받는다는 의미</strong>를 명확히 하기 위해 <code>@PersistenceContext</code> 사용을 권장한다.</p>
<p>빈 등록이 완료됬으면, 터미널에서 <code>./gradlew build</code>를 입력하면 <code>src/main/generated</code> 디렉토리에 Q로 시작되는 클래스가 생성된 것을 확인할 수 있다.</p>
<p>이제 빈으로 등록된 JpaQueryFactory를 이용해서 상품을 조회하는 테스트 코드를 만들어보자.</p>
<h3 id="💻itemrepositorytest">💻ItemRepositoryTest</h3>
<pre><code class="language-java">    @Test
    @DisplayName(&quot;Querydsl 조회 테스트1&quot;)
    void queryDslTest() {
        // given
        createItemList();
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        QItem qItem = QItem.item; // 플러그인을 통해 자동으로 생성된 QItem 객체를 생성


        // when
        JPAQuery&lt;Item&gt; query = queryFactory.selectFrom(qItem)
                .where(qItem.itemSellStatus.eq(ItemSellStatus.SELL))
                .where(qItem.itemDetail.like(&quot;%&quot; + &quot;테스트 상품 상세 설명&quot; + &quot;%&quot;))
                .orderBy(qItem.price.desc());
        List&lt;Item&gt; itemList = query.fetch();

        // then
        for (Item item : itemList) {
            log.info(item.toString());
        }

    }</code></pre>
<ul>
<li>영속성 컨텍스트를 사용하기 위해 EntityManger 빈을 주입한다.</li>
<li>JpaQueryFactory를 활용하여 쿼리를 동적으로 생성하는데, 생성자의 파라미터에 EntityManger 객체를 주입한다.</li>
<li>플러그인을 통해 자동으로 생성된 QItem 객체를 생성
💡자바 소스코드지만 SQL문과 비슷하게 소스코드를 작성할 수 있다.</li>
<li>fetch를 이용해서 쿼리 결과를 리스트로 반환한다.<ul>
<li><code>fetch()</code> 호출 시점에 쿼리문이 실행된다.</li>
</ul>
</li>
</ul>