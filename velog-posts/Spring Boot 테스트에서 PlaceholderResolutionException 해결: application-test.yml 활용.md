<p>안녕하세요! Spring Boot 프로젝트를 개발하다가 테스트 시 <span style="color: red;"><strong>PlaceholderResolutionException</strong></span> 에러를 만난 적 있나요? 저는 최근 해커톤에서 이 문제를 겪었는데, 환경 변수 기반 플레이스홀더(<code>${...}</code>)가 테스트 컨텍스트에서 해석되지 않아 ApplicationContext 로드가 실패하는 거였어요. 이 글에서 에러 원인과 해결 과정을 공유할게요. (Spring Boot 3.x, Gradle 환경 기준입니다.)</p>
<h1 id="🚩문제-상황">🚩문제 상황</h1>
<p>테스트 클래스(예: BubApplicationTests)에서 @SpringBootTest를 사용해 ApplicationContext를 로드하려 할 때 아래 에러가 발생했습니다:</p>
<pre><code class="language-txt">Caused by: org.springframework.util.PlaceholderResolutionException: Could not resolve placeholder 'jwt.secret' in value &quot;${jwt.secret}&quot;</code></pre>
<ul>
<li><p>🤔증상: 빈 생성 과정에서 <code>@Value</code>나 yml 플레이스홀더가 해석되지 않아 <span style="color: red;"><strong>UnsatisfiedDependencyException</strong></span>으로 이어짐.</p>
</li>
<li><p>발생 지점: affirmationService나 JwtTokenProvider 같은 빈에서 jwt.secret, clova.api-key 등의 값이 필요했지만, 테스트 환경에서 환경 변수(예: <code>${DEV_JWT_SECRET}</code>)가 설정되지 않았어요.</p>
</li>
</ul>
<p>application-dev.yml은 dev 프로파일에서 환경 변수를 참조하도록 되어 있었는데, 테스트 시 이 값들이 주입되지 않아 실패한 거예요.</p>
<h1 id="🔍원인-분석">🔍원인 분석</h1>
<p>Spring Boot의 <code>@SpringBootTest</code>는 전체 컨텍스트를 로드하지만, 테스트 환경에서 dev-yml의 플레이스홀더가 제대로 해석되지 않음.</p>
<p>주요 원인:</p>
<ul>
<li><p>환경 변수(예: DEV_JWT_SECRET)가 IDE나 CI에서 정의되지 않음.</p>
</li>
<li><p>테스트 프로파일(test)이 별도로 설정되지 않아 dev yml의 의존성 에러가 발생.</p>
</li>
<li><p>결과: JwtTokenProvider나 AuthService 같은 빈 생성 실패 → 전체 컨텍스트 로드 실패.</p>
</li>
</ul>
<p>이 에러는 런타임이 아닌 빈 초기화 단계에서 발생하니, <code>contextLoads()</code> 같은 간단 테스트에서도 바로 드러납니다.</p>
<h1 id="🛠️해결-방법">🛠️해결 방법</h1>
<p>테스트 전용 yml 파일(application-test.yml)을 만들어 <strong>더미 값으로 플레이스홀더를 오버라이드</strong>했습니다. 단계별로 따라 해보세요.</p>
<h2 id="단계-1-application-testyml-생성">단계 1: application-test.yml 생성</h2>
<p><code>src/test/resources</code>에 파일을 만들고, application-dev.yml 구조를 따라 더미 값 설정. (H2 in-memory DB로 변경해 실 DB 의존성 제거.)</p>
<pre><code class="language-yml">spring:
  config:
    activate:
      on-profile: test  # 테스트 프로파일

  # 데이터베이스 설정 (테스트용 in-memory H2 DB)
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
    username: test-username
    password: test-password

  # JPA 설정
  jpa:
    hibernate:
      ddl-auto: create
    database-platform: org.hibernate.dialect.H2Dialect

  # 카카오 로그인 설정 (더미 값)
  security:
    oauth2:
      client:
        registration:
          kakao:
            client-id: test-kakao-client-id
            client-secret: test-kakao-client-secret
            redirect-uri: http://localhost:8080/auth/kakao/callback
            authorization-grant-type: authorization_code
            client-authentication-method: client_secret_post
            scope:
              - profile_nickname
              - profile_image
              - account_email
        provider:
          kakao:
            authorization-uri: https://kauth.kakao.com/oauth/authorize
            token-uri: https://kauth.kakao.com/oauth/token
            user-info-uri: https://kapi.kakao.com/v2/user/me
            user-name-attribute: id

# JWT 설정 (더미 secret)
jwt:
  secret: test-secret-key-for-unit-tests-12345678901234567890123456789012
  access-token-expiration-in-seconds: 3600
  refresh-token-expiration-in-seconds: 1209600

# Clova 설정 (더미 키)
clova:
  api-key: test-clova-api-key</code></pre>
<h2 id="단계-2-테스트-클래스에-activeprofiles-추가">단계 2: 테스트 클래스에 @ActiveProfiles 추가</h2>
<p>테스트 클래스에서 test 프로파일을 활성화해 application-test.yml을 로드.</p>
<pre><code class="language-java">import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

@SpringBootTest
@ActiveProfiles(&quot;test&quot;)  // test 프로파일 활성화
class BubApplicationTests {

    @Test
    void contextLoads() {
        // 컨텍스트 로드 테스트
    }
}</code></pre>
<h2 id="단계-3-테스트-실행-및-확인이거는-위의-단계에서-빌드-해결이-되면-안해도-됩니다">단계 3: 테스트 실행 및 확인(이거는 위의 단계에서 빌드 해결이 되면 안해도 됩니다.)</h2>
<ul>
<li><p>gradlew test 또는 IDE에서 Run Tests 실행.</p>
</li>
<li><p>성공 시: 플레이스홀더가 더미 값으로 주입되어 에러 사라짐.</p>
</li>
<li><p>(옵션) 테스트에 assertion 추가로 값 확인:</p>
</li>
</ul>
<pre><code class="language-java">@Test
void contextLoads(@Value(&quot;${jwt.secret}&quot;) String jwtSecret) {
    assertNotNull(jwtSecret);  // 주입 확인
}</code></pre>
<h1 id="✍️배운-점">✍️배운 점</h1>
<ol>
<li><p>Spring Boot 테스트에서 환경 변수 의존성을 피하려면 application-test.yml을 별도로 만들어야 함. 이걸 통해 실제 환경과 테스트를 분리할 수 있어요.</p>
</li>
<li><p>해커톤처럼 급한 상황에서 이런 트러블슈팅은 시간 절약의 핵심! 나중에 CI/CD에서 더 유용할 거예요.</p>
</li>
</ol>
<p>이 에러로 고생하신 분들, 위 방법 시도해보세요. 댓글로 피드백 주시면 좋겠어요! 😊</p>