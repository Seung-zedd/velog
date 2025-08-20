<p>안녕하세요! 이번 포텐데이 프로젝트를 진행하면서 Spring의 <code>@Transactional</code> 어노테이션에 대해 깊이 고민해볼 기회가 있었습니다. 
&quot;어? 이 메소드에는 왜 붙이고, 저 메소드에는 왜 안 붙이지?&quot;, &quot;단순 <code>save()</code>만 하는데도 꼭 필요한가?&quot; 와 같은 궁금증들을 정리해보고자 합니다.</p>
<h1 id="🤔-transactional의-핵심-왜-쓸까">🤔 @Transactional의 핵심: 왜 쓸까?</h1>
<p>가장 핵심적인 이유는 <strong>원자성(Atomicity)</strong>을 보장하기 위함입니다. 즉, <strong>하나의 작업 단위(주로 Service단의 비즈니스 로직)로 묶인 여러 DB 작업들이 모두 성공하거나, 하나라도 실패하면 모두 실패(Rollback)</strong>하도록 만드는 것이죠. 마치 은행 송금처럼, 'A 계좌에서 돈 빼기'와 'B 계좌에 돈 넣기'는 절대 분리될 수 없는 한 묶음이어야 합니다.</p>
<h2 id="✅-transactional이-꼭-필요한-경우">✅ @Transactional이 꼭 필요한 경우</h2>
<ol>
<li><strong>여러 DB 변경 작업</strong>이 하나의 로직에 묶여있을 때</li>
</ol>
<p>가장 대표적인 경우입니다. 하나의 비즈니스 로직을 위해 여러 번의 <code>save()</code>, <code>update()</code>, <code>delete()</code>가 필요할 때, 이들을 하나의 트랜잭션으로 묶어 <strong>데이터 정합성</strong>을 보장해야 합니다.</p>
<pre><code class="language-java">@Transactional
public CategoryResponseDto create(CategoryRequestDto requestDto) {
    // 비즈니스 로직 1: 중복 검사
    if (categoryRepository.existsByTypeAndName(type, name)) {
        throw new IllegalStateException(&quot;이미 존재하는 카테고리입니다.&quot;); // ← 여기서 실패하면 아래 로직은 실행되지 않음
    }

    // DB 작업 1: 카테고리 저장
    Category category = categoryMapper.to(requestDto);
    Category savedCategory = categoryRepository.save(category);

    // DB 작업 2: 관련 로그 저장
    // 만약 이 작업이 실패하면, 위에서 저장한 카테고리도 함께 롤백됩니다.
    someOtherRepository.save(relatedLogEntity);

    return categoryMapper.from(savedCategory);
}</code></pre>
<ol start="2">
<li><strong>비즈니스 로직(검증 등)과 DB 변경 작업</strong>이 함께 있을 때</li>
</ol>
<p>위 예시처럼, DB에 저장하기 전에 무언가 검증하는 로직이 있다면 <code>@Transactional</code>이 필수입니다. 만약 <span style="color: red;"><strong>검증에 실패해서 예외가 발생</span></strong> 했을 때, 이전에 수행된 DB 작업이 있다면 함께 <strong>롤백</strong>시켜야 하기 때문입니다.</p>
<h2 id="❓언제transactional을-안-써도-될까">❓언제@Transactional을 안 써도 될까?</h2>
<ol>
<li>단 하나의 <code>repository.save()</code>만 호출하는 경우
결론부터 말하면, 기술적으로는 필요 없습니다. 
Spring Data JPA는 <code>repository.save()</code>와 같은 각각의 리포지토리 메소드를 이미 <strong>내부적으로 트랜잭션으로 감싸서 처리</strong>하기 때문입니다.</li>
</ol>
<pre><code class="language-java">// 이 private 메소드는 @Transactional이 없어도 동작합니다.
private void saveAffirmationLog(String sentence) {
    AffirmationLog log = AffirmationLog.builder().sentence(sentence).build();
    affirmationLogRepository.save(log); // 이 메소드 자체가 이미 트랜잭션
}</code></pre>
<h3 id="💡-하지만-그럼에도-불구하고">💡 하지만 그럼에도 불구하고...</h3>
<p>서비스 계층의 <strong>public 메소드</strong>에는 <strong>일관성</strong>을 위해 <code>@Transactional</code>을 붙여주는 것이 좋습니다. 
지금은 단일 <code>save()</code>만 있지만, 나중에 이 메소드에 <strong>다른 비즈니스 로직이나 DB 작업이 추가</strong>될 수 있기 때문입니다. 
미리 붙여두는 것이 미래의 실수를 방지하는 좋은 방법입니다.</p>
<hr />
<h3 id="🔖private-메소드">🔖private 메소드</h3>
<p><code>@Transactional</code>은 Spring의 AOP(Proxy) 기반으로 동작합니다. 
따라서 외부에서 호출될 수 없는 <span style="color: red;"><strong>private 메소드에는 어노테이션을 붙여도 트랜잭션이 적용되지 않습니다.</strong></span> private 메소드 내의 로직은 이 메소드를 호출한 public 메소드의 트랜잭션에 포함되어 실행됩니다.</p>