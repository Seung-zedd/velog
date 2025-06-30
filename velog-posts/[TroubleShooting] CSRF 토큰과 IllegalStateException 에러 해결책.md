<h1 id="🚩문제-상황-the-problem">🚩문제 상황 (The Problem)</h1>
<p>Spring Boot 3.x 환경에서 Spring Security와 Thymeleaf를 사용하여 회원가입 폼을 구현하던 중, 다음과 같은 에러 메시지와 함께 <span style="color: red;"><strong>페이지 렌더링에 실패</strong></span>했습니다.</p>
<h2 id="👀가장-먼저-눈에-띄는-에러-thymeleaf단">👀가장 먼저 눈에 띄는 에러 (Thymeleaf단)</h2>
<pre><code class="language-txt">org.thymeleaf.exceptions.TemplateProcessingException: Exception evaluating SpringEL expression: &quot;_csrf.parameterName&quot; (template: &quot;/member/memberForm&quot; - line 55, col 30)</code></pre>
<p>에러 로그를 더 깊이 파고들었을 때 발견한 진짜 원인 (Root Cause)</p>
<pre><code class="language-txt">java.lang.IllegalStateException: Cannot create a session after the response has been committed</code></pre>
<p>이 문제는 특히 <code>&lt;form&gt;</code> 태그에 <strong>th:object를 사용하여 모델 객체와 폼을 바인딩했을 때 발생</strong>했으며, 단순히 CSRF 보호 기능을 비활성화(<code>csrf.disable()</code>)하면 해결되었기 때문에 문제의 원인이 Spring Security의 CSRF 처리 방식에 있음을 확신할 수 있었습니다.</p>
<h1 id="🤔원인-분석">🤔원인 분석</h1>
<p>문제의 핵심은 <strong>&quot;타이밍(Timing) 충돌&quot;</strong>이었습니다.</p>
<p>Spring Security와 Thymeleaf가 <strong>서로 다른 속도</strong>로 일하려고 하면서 문제가 발생한 것입니다.</p>
<h2 id="spring-security의-게으른-준비-lazy-initialization">Spring Security의 '게으른 준비' (Lazy Initialization)</h2>
<p>최신 Spring Security는 효율성을 위해, 요청이 들어왔을 때 <strong>CSRF 토큰</strong>이나 그것을 저장할 <strong>세션(Session)</strong> 같은 보안 관련 요소들을 &quot;실제로 필요해지기 전까지는&quot; 만들지 않고 기다립니다.</p>
<h2 id="thymeleaf의-성실한-렌더링-eager-rendering">Thymeleaf의 '성실한 렌더링' (Eager Rendering)</h2>
<p>반면, Thymeleaf는 컨트롤러로부터 렌더링 명령을 받으면 HTML 페이지를 위에서부터 부지런히 그리기 시작합니다.</p>
<p>특히 <code>&lt;form th:action=&quot;@{...}&quot; method=&quot;post&quot;&gt;</code> 태그를 만나면, CSRF 공격을 막기 위해 필요한 숨겨진 <strong>_csrf 토큰을 &quot;지금 당장&quot; 달라고 Spring Security에 요청</strong>합니다.</p>
<h2 id="엇갈린-타이밍과-충돌">엇갈린 타이밍과 충돌</h2>
<p>Thymeleaf가 &quot;토큰 주세요!&quot;라고 <strong>요청하는 시점</strong>은, 이미 페이지의 다른 부분들이 그려지면서 <strong>브라우저로 응답(HTTP Response)이 전송되기 시작한 후</strong>일 가능성이 높습니다.</p>
<p>뒤늦게 토큰 요청을 받은 Spring Security는 &quot;어? 토큰을 저장할 세션이 없네. 지금부터 만들어야겠다!&quot; 라고 시도하지만, 
이미 브라우저로 응답이 출발(Committed)했기 때문에 서버는 &quot;이미 보내기 시작한 응답에 이제 와서 새로운 세션을 만들 수는 없어!&quot;라며 <span style="color: red;"><strong>IllegalStateException 에러를 발생</strong></span>시키는 것입니다.</p>
<p>결론적으로, 이 에러는 <strong>Thymeleaf의 즉시적인 토큰 요청</strong>과 <strong>Spring Security의 지연된 세션 생성</strong>이라는 두 가지 동작 방식이 <strong>충돌</strong>하여 발생한 문제였습니다.</p>
<h1 id="🔨해결-방안">🔨해결 방안</h1>
<p>이 타이밍 충돌을 해결하는 방법은 크게 두 가지 접근법이 있으며, 두 방법 모두 훌륭한 해결책입니다.</p>
<h2 id="해결-방안-1-spring-security에게-미리-준비시키기-securityconfig-수정">해결 방안 1: Spring Security에게 미리 준비시키기 (SecurityConfig 수정)</h2>
<p>&quot;게으르게 기다리지 말고, 미리미리 준비해!&quot;라고 Spring Security에 명시적으로 알려주는 방법입니다. 보안 관련 설정은 SecurityConfig 파일 안에서 해결하므로, 역할과 책임이 명확해지는 장점이 있습니다.</p>
<h3 id="💻securityconfigjava">💻SecurityConfig.java</h3>
<pre><code class="language-java">import static org.springframework.security.config.Customizer.withDefaults;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // ... (다른 설정들)

            // 💡 CSRF 보호 기능을 '기본 설정으로 사용하겠다'고 명시적으로 선언
            .csrf(withDefaults());

        return http.build();
    }
}</code></pre>
<ul>
<li>이 코드는 Spring에게 요청 처리 초기에 CSRF 관련 기능들을 미리 준비시키도록 하여, Thymeleaf가 토큰을 필요로 할 때 타이밍 문제 없이 제공할 수 있게 해줍니다.</li>
</ul>
<h2 id="해결-방안-2-thymeleaf를-기다리게-하기-applicationyml-수정">해결 방안 2: Thymeleaf를 기다리게 하기 (application.yml 수정)</h2>
<p>&quot;성급하게 응답부터 보내지 말고, 모든 준비가 끝날 때까지 기다려!&quot;라고 Thymeleaf의 동작 방식을 변경하는 방법입니다.</p>
<h3 id="💻application-commonyml-또는-applicationyml">💻application-common.yml (또는 application.yml)</h3>
<pre><code class="language-yml">spring:
  thymeleaf:
    servlet:
      produce-partial-output-while-processing: false</code></pre>
<ul>
<li>이 설정은 Thymeleaf가 템플릿 페이지 전체를 100% 완성하고 내부적으로 모든 처리가 끝날 때까지 <strong>브라우저로 응답을 보내는 것을 보류</strong>시킵니다. 모든 준비가 끝난 후에 완성된 페이지를 한 번에 보내므로, <strong>타이밍 충돌의 원인을 원천적으로 제거</strong>할 수 있습니다.</li>
</ul>
<h1 id="📝결론">📝결론</h1>
<p>Spring Boot 3 환경에서 <code>th:object</code>를 사용하는 폼과 Spring Security를 함께 사용할 때 발생하는 <code>_csrf</code> 관련 에러는, 두 프레임워크의 내부 동작 타이밍이 어긋나면서 발생하는 문제입니다.</p>
<p>이 문제를 저는 아래의 참고자료 중 해결 방안 2와 해결 방안 3를 동시에 사용해서 해결할 수 있었습니다. 명시적으로는 스프링 시큐리티한테 <code>.csrf(withDefaults());</code>로 알려줬어도 실패했기 때문에 해결 방안 3을 사용함으로써 근본적인 csrf 토큰 문제를 해결하였습니다.</p>
<hr />
<p>📚참고자료
🔗Spring Security 및 CSRF 토큰: <a href="https://velog.io/@gprhf651/Spring-Security-%EB%B0%8F-CSRF-%ED%86%A0%ED%81%B0">https://velog.io/@gprhf651/Spring-Security-%EB%B0%8F-CSRF-%ED%86%A0%ED%81%B0</a></p>