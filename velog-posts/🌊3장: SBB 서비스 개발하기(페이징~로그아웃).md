<h1 id="🏁이-장의-목표">🏁이 장의 목표</h1>
<ul>
<li>SBB를 상용 게시판 수준으로 개발한다. </li>
<li>부트스트랩을 적용하여 게시판을 더 보기 좋게 만든다. </li>
<li>게시물 등록, 삭제, 수정부터 로그인, 로그아웃, 페이징 등의 기능을 구현한다.</li>
</ul>
<p>✂이 시리즈의 목적은 UI/템플릿이 아닌, 서비스의 동작 과정을 파악하기 위함이므로 부트스트랩 관련은 생략하였다.</p>
<h1 id="302-페이징-기능-추가하기">3‑02 페이징 기능 추가하기</h1>
<p>🔖페이징(paging): 입력된 정보나 데이터를 <strong>여러 페이지에 나눠 표시</strong>하고, <strong>사용자가 페이지를 이동</strong>할 수 있게 하는 기능</p>
<h3 id="💻questionrepostiory">💻QuestionRepostiory</h3>
<pre><code class="language-java">public interface QuestionRepository extends JpaRepository&lt;Question, Long&gt; {
    @Override
    Page&lt;Question&gt; findAll(Pageable pageable);
}</code></pre>
<p>JpaRepository가 기본적으로 제공하는 <code>findAll(Pageable pageable)</code> 메서드를 사용해서 Question 타입의 Page 객체를 리턴하게 하였다.</p>
<h3 id="💻questionservice">💻QuestionService</h3>
<pre><code class="language-java">public class QuestionService {
    (...생략)
    public Page&lt;Question&gt; getList(int page) {
        Pageable pageable = PageRequest.of(page, 10);
        return questionRepository.findAll(pageable);
    }
}</code></pre>
<ul>
<li>사용자가 요청한 page를 10개의 덩어리 단위로 보여주게 하는 pageable 객체를 findAll 인자에 전달하였다.</li>
</ul>
<h3 id="💻questioncontroller">💻QuestionController</h3>
<pre><code class="language-java">public class QuestionController {
    (...생략)
    @GetMapping(&quot;/list&quot;)
    public String list(Model model, @RequestParam(value = &quot;page&quot;, defaultValue = &quot;0&quot;) int page) {
        Page&lt;Question&gt; paging = questionService.getList(page);
        model.addAtrribute(&quot;paging&quot;, paging);
        return &quot;question_list&quot;;
    }
}</code></pre>
<ul>
<li><p>사용자가 GET 방식으로 요청한 URL에서 page 값을 가져오기 위해 <code>@RequestParam</code>을 파라미터로 전달하였다.</p>
<ul>
<li><a href="http://localhost:8080/question/list?page=0%EC%97%90%EC%84%9C">http://localhost:8080/question/list?page=0에서</a> ? 다음인 쿼리 파라미터를 가져오기 위함</li>
</ul>
</li>
<li><p>💡스프링부트에서 페이지의 시작 번호는 0이기 때문에 defaultValue를 0으로 설정해야 한다.</p>
</li>
<li><p>🔎Page 객체는 <strong>전체 데이터 수를 조회</strong>하기 위한 추가 쿼리(count 쿼리)를 발생시켜 <span style="color: red;"><strong>오버헤드가 발생</strong></span>한다. </p>
<ul>
<li>하지만 <code>question_list.html</code> 파일에서 페이지네이션의 번호 인덱스를 페이지 번호 계산 및 표시를 함으로써 동적으로 변경되게 하였고, 이전/다음 버튼의 활성화 여부도 페이지 전체 데이터 수에 의존하기 때문에 Page 객체를 사용하였다.</li>
</ul>
</li>
</ul>
<h2 id="최신순으로-데이터-조회하기">최신순으로 데이터 조회하기</h2>
<p>QuestionService의 PageRequest.of() 메서드에서 Sort 객체를 추가만 하면 된다. 
다만, Question의 createDate 필드를 기준으로 정렬할 것이기 때문에 약간의 로직이 추가로 필요하다.</p>
<h3 id="💻questionservice-1">💻QuestionService</h3>
<pre><code class="language-java">public class QuestionService {
    (...생략)
    public Page&lt;Question&gt; getList(int page) {
        List&lt;Sort.Order&gt; sorts = new ArrayList&lt;&gt;();
        sorts.add(Sort.Order.desc(&quot;createDate&quot;));
        // 한 페이지에 10개의 데이터를 최신순으로 보여줌
        Pageable pageable = PageRequest.of(page, 10, Sort.by(sorts));
        return questionRepository.findAll(pageable);
    }
}</code></pre>
<ul>
<li>Question 엔티티를 생성할 때마다 createDate도 매핑되기 때문에 이를 <code>&lt;Sort.Order&gt;</code> 타입의 리스트에 createDate를 내림차순으로 삽입한 다음, of 메서드에 sort 객체를 전달해서 sorts 리스트를 기준으로 정렬하게 하였다.<ul>
<li>createDate 이외에 다른 기준으로 정렬하고 싶으면 <code>sorts.add()</code>를 활용하면 된다.</li>
</ul>
</li>
</ul>
<h1 id="305-스프링-시큐리티의-정의-및-환경설정">3‑05 스프링 시큐리티의 정의 및 환경설정</h1>
<p>📢스프링 시큐리티를 사용하기 위해서는 build.gradle에서 <code>implementation 'org.springframework.boot:spring-boot-starter-security'</code> 디펜던시를 추가하면 된다.</p>
<p>🔖인증(Authenticate): 로그인과 같은 사용자의 신원을 확인
🔖인가(Authorize): 인증된 사용자가 어떤 일을 할 수 있는지 관리하는 것</p>
<p>⚠️스프링 시큐리티는 기본적으로 인증되지 않은 사용자가 웹 서비스를 사용할 수 없게 만들기 때문에 웹 서버를 실행하면 인증을 위한 로그인 화면이 나타난다.</p>
<p>따라서 다음과 같이 스프링 시큐리티의 설정을 담당할 SecurityConfig 클래스 파일을 만들어보자.</p>
<h3 id="💻securityconfig">💻SecurityConfig</h3>
<pre><code class="language-java">@Configuration 
@EnableWebSecurity 
public class SecurityConfig {
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests((authorizeHttpRequests) -&gt;
        authorizeHttpRequests
        .requestMatchers(new AntPathRequestMatcher(&quot;/**&quot;)).permitAll());
        return http.build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}</code></pre>
<p>🔖@Configuration: 스프링의 환경 설정 파일임을 의미
🔖@EnableWebSecurity: <strong>모든 요청 URL이 스프링 시큐리티의 제어</strong>를 받도록 만드는 어노테이션으로, 스프링 시큐리티를 활성화시킴</p>
<ul>
<li>🔎사용자가 입력한 비밀번호 평문을 스프링 시큐리티에서 제공하는 BCryptPasswordEncoder 클래스를 사용하여 <strong>암호화</strong>하기 위해 수동으로 빈을 등록하였다.<ul>
<li>PasswordEncoder는 껍데기만 있는 인터페이스라 구현 클래스인 BCryptPasswordEncoder 인스턴스를 주입해야 한다.</li>
</ul>
</li>
</ul>
<pre><code class="language-java">http
        .authorizeHttpRequests((authorizeHttpRequests) -&gt;
        authorizeHttpRequests
        .requestMatchers(new AntPathRequestMatcher(&quot;/**&quot;)).permitAll());</code></pre>
<p>위의 부분은 인증되지 않은 모든 페이지의 요청을 허락한다는 의미다.</p>
<h2 id="🤔h2를-tcp연결로-하면-오류가-발생하지-않은-이유">🤔h2를 tcp연결로 하면 오류가 발생하지 않은 이유</h2>
<p>TCP 연결 방식으로 H2에 접근하면, <strong>H2는 별도의 서버로 동작</strong>하게 됩니다. 이 경우 H2 콘솔은 H2 서버가 자체적으로 제공하는 웹 인터페이스를 사용하게 되므로, Spring Boot 애플리케이션 내부의 Spring Security 필터가 적용되지 않습니다. </p>
<blockquote>
<p>결정적으로 H2 콘솔은 스프링과 상관없는 앱이기 때문에 POST 요청을 하면 CSRF 토큰을 발행하지 않아서 403 Forbidden 오류가 발생한다.</p>
</blockquote>
<p>💡파일 대신 TCP 연결로 h2 DB에 접근하면 여러 클라이언트가 동시에 데이터베이스에 접근할 수 있기 때문에 유용하다.</p>
<h1 id="306-회원-가입-기능-구현하기">3‑06 회원 가입 기능 구현하기</h1>
<p>🏁사용자가 회원 가입할 수 있는 화면을 만들고 기능을 완성해보자.</p>
<p>화면을 만들기 전에, 회원 가입 기능을 사용하기 위한 회원 엔티티, 리포지토리, 회원의 데이터를 입력하기 위한 폼, 회원 가입 폼을 GET하고, 폼에 기입한 데이터를 서버로 POST 요청하기 위한 컨트롤러를 순서대로 만들어보자.</p>
<h3 id="💻회원-엔티티">💻회원 엔티티</h3>
<pre><code class="language-java">@Entity
@Getter
@Setter
public class SiteUser {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(unique = true)
    private String username;

    private String password;

    @Column(unique = true)
    private String email;
}</code></pre>
<ul>
<li>사용자의 ID인 username과 이메일은 값이 중복되지 않게 <code>@Column(unique = true)</code>로 설정하였다.</li>
<li>💡스프링 시큐리티에 User 클래스가 있기 때문에 오용 방지를 위해 SiteUser로 만들었다.</li>
</ul>
<h3 id="💻userrepository">💻UserRepository</h3>
<pre><code class="language-java">public interface UserRepository extends JpaRepository&lt;SiteUser, Long&gt; {  

}</code></pre>
<ul>
<li>사용자의 데이터를 CRUD하기 위해 JpaRepository를 상속하였다.</li>
</ul>
<h3 id="💻userservice">💻UserService</h3>
<pre><code class="language-java">@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;
    private final PasswordEncoder passwordEncoder;

    public void create(String username, String email, String password) {
        SiteUser user = new SiteUser();
        user.setUsername(username);
        user.setEmail(email);
        user.setPassword(passwordEncoder.encode(password));
        userRepository.save(user);
    }
}</code></pre>
<ul>
<li>SecurityConfig에서 빈으로 수동 등록한 PasswordEncoder를 UserService가 주입한 다음, <code>encode()</code> 메서드로 사용자의 비밀번호를 암호화하였다.
💡<code>@Bean</code>은 스프링 컨테이너에서 싱글톤이 보장되서 메모리 효율면에서 좋다.</li>
</ul>
<h3 id="💻회원-가입-폼">💻회원 가입 폼</h3>
<pre><code class="language-java">@Getter
@Setter
public class UserCreateForm {
    @Size(min = 3, max = 25, message = &quot;사용자 ID는 3자 이상 25자 이하로 입력해야 합니다.&quot;)
    @NotBlank(message = &quot;사용자ID는 필수항목입니다.&quot;)
    private String username;

    @NotBlank(message = &quot;비밀번호는 필수항목입니다.&quot;)
    private String password1;

    @NotBlank(message = &quot;비밀번호 확인은 필수항목입니다.&quot;)
    private String password2;

    @NotBlank(message = &quot;이메일은 필수항목입니다.&quot;)
    @Email
    private String email;
}</code></pre>
<ul>
<li>🔖@Email: 해당 속성의 값이 이메일 형식과 일치하는지를 검증</li>
</ul>
<p>이제 회원 컨트롤러를 만들어서 회원 가입 폼을 GET 요청하기 위한 메서드와 사용자가 입력한 데이터를 서버에서 처리하는 POST 요청 메서드를 만들어보자.</p>
<h3 id="💻usercontroller">💻UserController</h3>
<pre><code class="language-java">@Controller
@RequiredArgsConstructor
@RequestMapping(&quot;/user&quot;)
@Slf4j
public class UserController {
    private final UserService userService;

    @GetMapping(&quot;/signup&quot;)
    public String showSignup(UserCreateForm userCreateForm) {
        return &quot;signup_form&quot;;
    }

    @PostMapping(&quot;/signup&quot;)
    public String signup(@Valid UserCreateForm userCreateForm, BindingResult bindingResult) {
        // 사용자가 폼 내용을 다 입력하지 않았을 경우
        if (bindingResult.hasFieldErrors(&quot;username&quot;) &amp;&amp; bindingResult.hasFieldErrors(&quot;password1&quot;) &amp;&amp; bindingResult.hasFieldErrors(&quot;password2&quot;) &amp;&amp; bindingResult.hasFieldErrors(&quot;email&quot;)) {
            bindingResult.reject(&quot;AllFieldsEmpty&quot;, &quot;ID, 패스워드, 이메일을 모두 입력해주세요.&quot;);
            log.error(&quot;사용자가 회원 가입 폼을 모두 입력하지 않음: {}&quot;, bindingResult.getGlobalErrors());
            return &quot;signup_form&quot;;
        }

        if (bindingResult.hasErrors()) {
            log.error(&quot;폼 검증 오류: {}&quot;, bindingResult.getAllErrors());
            return &quot;signup_form&quot;;
        }

        // 비밀번호와 비밀번호 확인이 서로 일치하지 않은 경우
        if (!userCreateForm.getPassword1().equals(userCreateForm.getPassword2())) {
            bindingResult.rejectValue(&quot;password2&quot;, &quot;passwordIncorrect&quot;, &quot;2개의 패스워드가 일치하지 않습니다&quot;);
            return &quot;signup_form&quot;;
        }

        try {
            userService.create(userCreateForm.getUsername(), userCreateForm.getEmail(), userCreateForm.getPassword1());

        } catch (DataIntegrityViolationException e) {
            bindingResult.reject(&quot;signupFailed&quot;, &quot;이미 등록된 사용자입니다.&quot;);
            log.error(&quot;사용자가 중복 회원 가입을 시도: {}&quot;, bindingResult.getAllErrors());
            return &quot;signup_form&quot;;
        } catch (Exception e) {
            bindingResult.reject(&quot;signupFailed&quot;, e.getMessage());
            log.error(&quot;그 밖의 다른 예외: {}&quot;, bindingResult.getAllErrors());
            return &quot;signup_form&quot;;
        }
        return &quot;redirect:/&quot;;
    }

}</code></pre>
<ul>
<li>검증 로직에서 <code>if (!userCreateForm.getPassword1().equals(userCreateForm.getPassword2()))</code>로 <strong>비밀번호와 비밀번호 확인 데이터가 일치하지 않으면</strong> bindingResult로 에러 객체를 만든 다음, 템플릿 파일에서 에러 객체를 전달함으로써 화면에 에러를 표시하였고, 다시 회원 가입 폼 페이지를 리턴하였다.<ul>
<li>💡<code>bindingResult.rejectValue(필드명, 오류 코드, 오류 메시지)</code>에서 임의로 &quot;passwordIncorrect&quot;라는 오류 코드를 만들었는데, 대형 프로젝트에서는 번역과 관리를 위해 <strong>오류 코드를 잘 정의</strong>해서 사용해야 한다.</li>
</ul>
</li>
<li>try~catch문을 사용해서 사용자 ID 또는 이메일 주소가 이미 존재할 경우에는 <code>DataIntegrityViolationException</code>이라는 예외를 발생시켜서 ’이미 등록된 사용자입니다.’ 라는 오류 메시지를 화면에 표시하도록 했다.<ul>
<li>username과 email에 <code>@Column(unique = true)</code>로 매핑했기 때문에 중복된 ID로 회원가입을 시도할 경우 서버에서 <span style="color: red;"><code>Unique index or primary key violation</code> 에러가 발생</span>함<ul>
<li>그 밖에 다른 예외들은 Exception으로 해당 에러의 구체적인 메시지를 <code>e.getMessage()</code>로 화면에 표시하였다.</li>
</ul>
</li>
</ul>
</li>
<li>에러 발생없이 회원 가입 폼을 잘 입력하면, 루트 페이지로 이동하게 하였다.</li>
</ul>
<h1 id="307-로그인과-로그아웃-기능-구현하기">3‑07 로그인과 로그아웃 기능 구현하기</h1>
<p>SBB 게시판 서비스는 여러 사람들이 사용하기 때문에 <strong>질문한 사람, 답변한 사람을 구별하기 위한 목적으로 로그인</strong>은 필수 기능이다.</p>
<h2 id="로그인-url-등록하기">로그인 URL 등록하기</h2>
<h3 id="💻securityconfig-1">💻SecurityConfig</h3>
<pre><code class="language-java">@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    @Bean
    SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        // 인증되지 않은 모든 페이지의 요청을 허락
        http
                .authorizeHttpRequests(authorizeHttpRequests -&gt; authorizeHttpRequests.requestMatchers(new AntPathRequestMatcher(&quot;/**&quot;)).permitAll())
                .formLogin(formLogin -&gt; formLogin.loginPage(&quot;/user/login&quot;)
                .defaultSuccessUrl(&quot;/&quot;)) // 로그인에 성공하면 루트로 리다이렉트
        ;        
        return http.build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }    
}</code></pre>
<ul>
<li>🔖<code>.formLogin()</code>: 스프링 시큐리티의 로그인 설정을 담당하는 부분으로, 로그인 페이지의 URL은 /user/login이고 로그인 성공 시에는 루트 URL로 이동함을 의미한다.</li>
</ul>
<p>로그인 URL을 스프링 시큐리티 환경 설정에서 등록했으니, UserController로 해당 URL을 매핑해보자.</p>
<h3 id="💻usercontroller-1">💻UserController</h3>
<pre><code class="language-java">public class UserController {
    (...생략)
    @GetMapping(&quot;/login&quot;)
    public String login() {
        return &quot;login_form&quot;;
    }
}</code></pre>
<ul>
<li>💡실제 로그인을 진행하는 <code>@PostMapping</code> 방식의 메서드는 <strong>스프링 시큐리티 가 대신 처리</strong>하므로 우리가 직접 코드를 작성하여 구현할 필요가 없다.<ul>
<li>🔎그런데 템플릿 파일의 form 메서드 부분은 post로 되었는데, 위에 명시한대로 스프링 시큐리티가 대신 처리하기 때문에 우리는 사용자가 입력한 데이터만 <code>th: action</code>으로 url을 매핑하고 <code>method=&quot;post&quot;</code>로 설정만 하면 된다.</li>
</ul>
</li>
<li>💡스프링 시큐리티의 <strong>로그인이 실패</strong>할 경우에는 시큐리티의 기능으로 인해 <strong>로그인 페이지로 리다이렉트</strong>된다. <ul>
<li>페이지 매개변수로 error가 함께 전달되므로, 템플릿 파일에서 error를 전달해서 화면에 표시하게 하였다.</li>
</ul>
</li>
</ul>
<p>🚩그런데, <strong>스프링 시큐리티에 무엇을 기준으로 로그인</strong>해야 하는지 아직 설정하지 않았기 때문에 로그인을 수행할 수는 없다. 
🔨따라서 우리는 회원가입 기능을 통해 회원 정보를 DB에 저장했으므로 <strong>DB에서 회원 정보를 조회하여 로그인하는 방법</strong>을 사용할 것이다.</p>
<h2 id="user-리포지터리-수정하기">User 리포지터리 수정하기</h2>
<pre><code class="language-java">public interface UserRepository extends JpaRepository&lt;SiteUser, Long&gt; {
    Optional&lt;SiteUser&gt; findByUsername(String username);
}</code></pre>
<ul>
<li>🔖(뒤에서 만들)UserSecurityService: DB에서 사용자를 조회하는 서비스를 만들고, <strong>스프링 시큐리티가 로그인 시에 사용</strong>하기 위한 서비스 <ul>
<li>얘는 사용자 ID를 조회하는 기능이 필요하기 때문에 위와 같이 메서드를 추가하였다.</li>
</ul>
</li>
</ul>
<h2 id="userrole-파일-생성하기">UserRole 파일 생성하기</h2>
<p>스프링 시큐리티는 인증(Authentication)뿐만 아니라 권한(Authorization)도 관리한다. 
따라서 사용자가 로그인(인증)한 후에, <strong>사용자에게 ADMIN 또는 USER와 같은 권한을 부여</strong>함으로써 스프링 시큐리티에게 사용자에게 부여할 권한과 관련된 내용을 알려줘야 한다.</p>
<h3 id="💻userrole">💻UserRole</h3>
<pre><code class="language-java">@Getter
public enum UserRole {
    ADMIN(&quot;ROLE_ADMIN&quot;), USER(&quot;ROLE_USER&quot;);

    UserRole(String value) {
        this.value = value;
    }

    private final String value;
}</code></pre>
<ul>
<li><p>보통 <strong>권한명은 enum 열거형</strong>으로 작성하고 </p>
<ul>
<li>ADMIN 권한(관리자 권한)을 지닌 사용자가 다른 사람이 작성한 질문이나 답변을 <strong><code>@PreAuthorize()</code>가 매핑된 메서드를 사용</strong>함으로써 수정 가능하도록 만들 수도 있다.</li>
</ul>
</li>
<li><p>💡위와 같이 <strong>생성자에 별도로 String 타입의 value</strong>를 넣어줘야 하는 이유는 UserSecurityService에서 주입할 SimpleGrantedAuthority 인스턴스가 다음과 같기 때문이다:</p>
<ul>
<li>🤝Spring Security에서는 권한(authority)을 표현할 때 <code>ROLE_</code> 접두사를 사용하는 관례가 있으므로, 위와 같이 value를 작성한다.</li>
</ul>
</li>
</ul>
<pre><code class="language-java">public SimpleGrantedAuthority(String role) {
        Assert.hasText(role, &quot;A granted authority textual representation is required&quot;);
        this.role = role;
    }</code></pre>
<h3 id="💻usersecurityservice">💻UserSecurityService</h3>
<pre><code class="language-java">@Service
@RequiredArgsConstructor
public class UserSecurityService implements UserDetailsService {
    private final UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        SiteUser siteUser = userRepository.findByUsername(username).orElseThrow(() -&gt; new UsernameNotFoundException(&quot;사용자를 찾을 수 없습니다.&quot;));

        List&lt;GrantedAuthority&gt; authorities = new ArrayList&lt;&gt;();
        // 사용자 ID가 admin일 경우에는 ADMIN 권한 부여
        if (&quot;admin&quot;.equals(username)) {
            authorities.add(new SimpleGrantedAuthority(UserRole.ADMIN.getValue()));
        } else {
            // 그렇지 않으면 USER 권한 부여
            authorities.add(new SimpleGrantedAuthority(UserRole.USER.getValue()));
        }
        return new User(siteUser.getUsername(), siteUser.getPassword(), authorities);
    }
}</code></pre>
<ul>
<li>스프링 시큐리티가 제공하는 UserDetailService 인터페이스의 <em>loadUserByUsername</em> 메서드를 강제로 오버라이딩 해야 한다.<ul>
<li>🔖<code>loadUserByUsername(String username)</code>: 현재 로그인한 사용자명(ID)으로 스프링 시큐리티의 사용자(User) 객체를 조회하여 리턴한다.</li>
</ul>
</li>
<li>사용자명이 'admin'인 경우 =&gt; ADMIN 권한(ROLE_ADMIN)을 부여, 그 이외의 경우에는 USER 권한(ROLE_USER)를 부여했다.</li>
<li>마지막으로 스프링 시큐리티에서 제공하는 <code>USER(username, password, authorities)</code>객체를 리턴했다.<ul>
<li>스프링 시큐리티는 <strong>리턴된 User 객체의 비밀번호가 사용자로부터 입력받은 비밀번호와 일치하는지를 검사하는 기능</strong>을 내부에 가지고 있다.</li>
</ul>
</li>
</ul>
<p>이제 인증과 권한 부여 프로세스를 자동으로 처리하기 위해 SecurityConfig 코드를 수정해보자.</p>
<h3 id="💻securityconfig-2">💻SecurityConfig</h3>
<pre><code class="language-java">@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    (...생략)

    @Bean
    AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
        return authenticationConfiguration.getAuthenticationManager();
    }
}</code></pre>
<ul>
<li>🔖AuthenticationManager: 사용자 인증과 권한을 부여한 UserSecurityService와 내부적으로 PasswordEncoder를 사용하여 인증과 권한 부여 프로세스를 처리한다.</li>
</ul>
<h2 id="로그아웃-기능-구현하기">로그아웃 기능 구현하기</h2>
<p>🤩얘는 그냥 스프링 시큐리티를 활용한 SecurityConfig 클래스에서 코드를 약간만 수정해주면 쉽게 구현할 수 있다.</p>
<h3 id="💻securityconfig-3">💻SecurityConfig</h3>
<pre><code class="language-java">@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {
    @Bean
    SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        // 인증되지 않은 모든 페이지의 요청을 허락
        http
                .authorizeHttpRequests(authorizeHttpRequests -&gt; authorizeHttpRequests.requestMatchers(new AntPathRequestMatcher(&quot;/**&quot;)).permitAll())
                .formLogin(formLogin -&gt; formLogin.loginPage(&quot;/user/login&quot;)
                        .defaultSuccessUrl(&quot;/&quot;)) // 로그인에 성공하면 루트로 리다이렉트;
                .logout(logout -&gt; logout
                        .logoutRequestMatcher(new AntPathRequestMatcher(&quot;/user/logout&quot;))
                        .logoutSuccessUrl(&quot;/&quot;)
                        .invalidateHttpSession(true));
        return http.build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
        return authenticationConfiguration.getAuthenticationManager();
    }
}</code></pre>
<ul>
<li>로그아웃 URL을 /user/logout으로 설정하고 로그아웃이 성공하면 루트 페이지로 이동시키고 생성된 사용자 세션을 <code>.invalidateHttpSession(true)</code>를 통해 삭제하도록 처리했다.</li>
</ul>
<p>✨로그아웃 링크 클릭 시, alert창을 띄우게 기능을 추가했는데 navbar.html의 로그아웃 a 태그에서 <code>onclick=&quot;return confirm('정말 로그아웃하시겠습니까?');&quot;</code>만 추가하면 간단하게 구현할 수 있다.</p>