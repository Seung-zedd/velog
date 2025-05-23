<h1 id="308-글쓴이-항목-추가하기">3‑08 글쓴이 항목 추가하기</h1>
<h2 id="📜문제-요구사항">📜문제 요구사항</h2>
<ol>
<li><strong>질문 또는 답변을 작성한 사용자는 반드시 로그인</strong>되어 있어야 한다.</li>
<li>게시판의 질문 목록과 답변 상세 페이지에는 누가 글을 작성했는지 알려 주는 '글쓴이' 항목을 추가해보자.</li>
</ol>
<h2 id="🧐요구사항-해석">🧐요구사항 해석</h2>
<ol>
<li>질문 또는 답변을 생성하려면 반드시 <strong>로그인된 사용자의 객체</strong>를 얻어오고 로그인된 사용자로부터 <strong>API를 호출</strong>할 수 있어야 한다.</li>
<li><strong>질문, 답변 엔티티에는 글쓴이(author) 필드를 추가</strong>해서 질문 목록과 답변 상세 페이지에 글쓴이 항목을 화면에 표시할 수 있다.</li>
</ol>
<hr />
<p>위의 내용을 바탕으로 서버 코드를 수정해보자.</p>
<h2 id="엔티티에-속성-추가하기">엔티티에 속성 추가하기</h2>
<h3 id="💻question-엔티티">💻Question 엔티티</h3>
<pre><code class="language-java">public class Question {
    (...생략)
    @ManyToOne(fetch = FetchType.LAZY)
    private SiteUser author; // 사용자 1명이 질문 여러 개 작성
}</code></pre>
<h3 id="💻answer-엔티티">💻Answer 엔티티</h3>
<pre><code class="language-java">public class Answer {
    (...생략)
    @ManyToOne(fetch = FetchType.LAZY)
    private SiteUser author; // 사용자 1명이 질문 여러 개 작성
}</code></pre>
<ul>
<li>반대로 질문, 답변 1개에 여러 명의 사용자가 작성할 수는 없기 때문에 <code>@ManyToOne</code>만 작성자 필드에 매핑하였다.</li>
</ul>
<h2 id="답변-컨트롤러와-서비스-업데이트하기">답변 컨트롤러와 서비스 업데이트하기</h2>
<p>엔티티의 속성이 추가되었기 때문에 <strong>새로운 데이터를 저장하려면 컨트롤러와 서비스에도 관련 내용을 업데이트</strong>해야 한다.(단, 우리는 스프링 데이터 JPA를 리포지토리로 사용했기 때문에 추가로 리포지토리를 수정할 필요는 없다.)</p>
<h3 id="💻answercontroller">💻AnswerController</h3>
<pre><code class="language-java">public class AnswerController {

    @PostMapping(&quot;/create/{id}&quot;)
    public String createAnswer(Model model, @PathVariable(&quot;id&quot;) Long id, @Valid AnswerForm answerForm, BindingResult bindingResult, Principal principal) {
    (...생략)
    }
}   </code></pre>
<ul>
<li>🔖Principal: 스프링 시큐리티가 제공하며, 이 객체를 통해 <strong>로그인한 사용자의 정보</strong>를 알 수 있다.<ul>
<li><code>principal.getName()</code>을 호출하면 현재 로그인한 사용자의 사용자 ID를 알 수 있다.</li>
</ul>
</li>
</ul>
<h3 id="💻userservice">💻UserService</h3>
<pre><code class="language-java">public class UserService {
    (생략...)
    public SiteUser getUser(String username) {
        return userRepository.findByUsername(username).orElseThrow(() -&gt; new DataNotFoundException(&quot;siteUser not found&quot;));
    }
}</code></pre>
<ul>
<li>리포지토리의 findByUsername 메서드를 통해 쉽게 작성할 수 있다.<ul>
<li>사용자 ID에 해당하는 데이터가 없을 때는 예외가 발생한다.</li>
</ul>
</li>
</ul>
<h3 id="💻answerservice">💻AnswerService</h3>
<pre><code class="language-java">public Answer create(Question question, String content, SiteUser author) {
        Answer answer = new Answer();
        answer.setQuestion(question);
        answer.setCreateDate(LocalDateTime.now());
        answer.setContent(content);
        answer.setAuthor(author);
        answerRepository.save(answer);

        return answer;
    }</code></pre>
<ul>
<li>userService로부터 현재 로그인한 사용자를 siteUser 객체로 반환하고, 그것을 파라미터로 전달해 답변 엔티티에 설정하였다.</li>
</ul>
<p>답변을 생성할 때 관련된 작성자 로직을 만들었으므로, 다시 AnswerController로 돌아가서 메서드의 바디 부분을 완성해보자.</p>
<h3 id="💻answercontroller-1">💻AnswerController</h3>
<pre><code class="language-java">public class AnswerController {
    private final QuestionService questionService;
    private final AnswerService answerService;
    private final UserService userService;

    @PostMapping(&quot;/create/{id}&quot;)
    public String createAnswer(Model model, @PathVariable(&quot;id&quot;) Long id, @Valid AnswerForm answerForm, BindingResult bindingResult, Principal principal) {
            Question question = questionService.getQuestion(id);
            SiteUser siteUser = userService.getUser(principal.getName());
        // 사용자가 내용을 입력하지 않으면 질문 detail로 리다이렉트시킴
        if (bindingResult.hasErrors()) {
            model.addAttribute(&quot;question&quot;, question);
            return &quot;question_detail&quot;;
        }

        Answer answer = answerService.create(question, answerForm.getContent(), siteUser);
        log.info(&quot;created answer with siteUser: {}, content: {}&quot;, siteUser.getUsername(), answerForm.getContent());
        return String.format(&quot;redirect:/question/detail/%s#answer_%s&quot;, id, answer.getId());

    }
}   </code></pre>
<ul>
<li><code>principal.getName()</code>를 통해 현재 로그인한 작성자 siteUser 객체를 얻어 답변을 등록할 때 사용했다.</li>
</ul>
<p>📢질문 컨트롤러와 서비스 업데이트 코드는 위의 답변 도메인과 비슷하기 때문에 생략하였다.</p>
<h4 id="🤔왜-postmapping에서만-valid와-bindingresult를-사용해야-하는가">🤔왜 PostMapping에서만 @Valid와 BindingResult를 사용해야 하는가?</h4>
<p>보통 _Validation_은 요구사항에 걸맞게 엔티티의 필드값에 매핑되는데, <strong>POST</strong> 방식은 클라이언트가 서버한테 데이터 생성/수정 같은 요청을 할 때, <strong>서버의 규칙에 맞는 리소스를 생성</strong>해야 하기 때문이다. 즉, <strong>서버로 리소스가 들어올 때</strong>는 입력 데이터의 신뢰성을 위해 <strong>유효성 검증이 필수적</strong>이다.</p>
<ul>
<li>Validation 종류를 알고 싶으면 <a href="https://developerjisu.tistory.com/100#%EC%9C%A0%ED%9A%A8%EC%84%B1%20%EA%B2%80%EC%82%AC%20%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%20%EC%A2%85%EB%A5%98-1">유효성 검사 어노테이션 종류</a>를 참고할 것</li>
</ul>
<h2 id="로그인-페이지로-이동시키기">로그인 페이지로 이동시키기</h2>
<p>🚩로그아웃 상태에서 질문 또는 답변을 등록하면 화면에 500 Internal Server Error가 보일텐데, 로그인을 해야만 생성되는 <strong>principal 객체가 현재는 로그아웃 상태라 null</strong>이라서 발생했기 때문이다.</p>
<p>🔨principal 객체를 사용하는 메서드에 <code>@PreAuthorized(&quot;isAuthenticated()&quot;)</code>를 매핑해야 된다.</p>
<ul>
<li>🔖<code>@PreAuthorized(&quot;isAuthenticated()&quot;)</code>: 로그인한 사용자만 호출할 수 있는 메서드</li>
</ul>
<p>만약 로그아웃 상태에서 위의 메서드가 호출되면 <strong>로그인 페이지로 강제 이동</strong>된다.</p>
<p>👨‍💻QuestionController에서 <code>@GetMapping(&quot;/create&quot;)</code>과 <code>@PostMapping(&quot;/create&quot;)</code>가 매핑된 메서드에 <code>@PreAuthorized(&quot;isAuthenticated()&quot;)</code>를 추가해보자. 마찬가지로 AnswerController에서 <code>@PostMapping(&quot;/create/{id}&quot;)</code>가 매핑된 메서드에도 추가하면 된다.</p>
<p>마지막으로 스프링 시큐리티의 설정도 수정해야 하는데, 이를 위해 SecurityConfig 코드를 수정해보자.</p>
<h3 id="💻securityconfig">💻SecurityConfig</h3>
<pre><code class="language-java">@Configuration 
@EnableWebSecurity 
@EnableMethodSecurity 
public class SecurityConfig {
(... 생 략 ...)
}</code></pre>
<ul>
<li>🔖<code>@EnableMethodSecurity</code>: @PreAuthorize 애너테이션을 사용하기 위해 반드시 필요한 설정으로, prePostEnabled의 default는 true로 설정되어 있다.<ul>
<li>로그아웃 상태에서 [질문 등록] 버튼을 누르면 로그인 페이지로 이동하는데, 이는 <strong>로그인 후에 원래 가려고 했던 페이지로 리다이렉트 시키는 스프링 시큐리티의 기능</strong> 덕분에 가능한 것이다. </li>
</ul>
</li>
</ul>
<h1 id="309-수정과-삭제-기능-추가하기">3‑09 수정과 삭제 기능 추가하기</h1>
<p>우리가 위와 같은 기능을 추가하기 위해서는 다음과 같은 절차가 필요하다.
📢참고로 화면에 종속되는 템플릿 엔진은 언제든지 바꿀 수 있기 때문에 템플릿 파일 코드는 생략했다.</p>
<h2 id="질문-수정-기능-생성하기">질문 수정 기능 생성하기</h2>
<ul>
<li>🗹질문, 답변 엔티티에 수정 날짜 필드 추가하기</li>
<li>🗹질문 컨트롤러 수정: GET 방식으로 사용자가 작성한 질문 내용 조회</li>
<li>🗹질문 서비스 수정</li>
<li>🗹질문 컨트롤러 수정: POST 방식으로 사용자가 수정한 질문 내용 서버에 저장</li>
</ul>
<p>먼저 Question, Answer 엔티티에 LocalDateTime modifyDate 필드를 각각 추가하자.</p>
<p>그리고 나서 사용자가 작성한 질문 데이터를 조회하기 위해 아래와 같이 @GetMapping으로 매핑된 메서드를 추가해보자.</p>
<h3 id="💻questioncontroller">💻QuestionController</h3>
<pre><code class="language-java">public class QuestionController {
    (...생략)

    @PreAuthorize(&quot;isAuthenticated()&quot;)
    @GetMapping(&quot;/modify/{id}&quot;)
    public String modifyQuestion(QuestionForm questionForm, @PathVariable(&quot;id&quot;) Long id, Principal principal) {
        Question question = questionService.getQuestion(id);
        validateAuthor(principal, question);
        questionForm.setSubject(question.getSubject());
        questionForm.setContent(question.getContent());
        return &quot;question_form&quot;;
    }

    private void validateAuthor(Principal principal, Question question) {
        if (!question.getAuthor().getUsername().equals(principal.getName())) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, &quot;수정 권한이 없습니다.&quot;);
        }
    }
}</code></pre>
<ul>
<li>questionService로부터 현재 질문 id의 값으로 조회를 한 다음에, <code>validateAuthor</code> 메서드로 질문 작성자가 현재 로그인한 사용자명과 같은지 검증을 한다. <ul>
<li>만약 같으면 DB로부터 질문 데이터를 질문 폼으로 가져와서 사용자한테 질문 폼을 보여주고, 그렇지 않으면 &quot;수정권한이 없습니다.&quot;라는 404 클라이언트 에러 코드를 화면에 보여준다.</li>
</ul>
</li>
</ul>
<h4 id="🤔question_detailhtml-템플릿-파일에서는-아래와-같이-로그인한-사용자의-id와-작성자가-같을-때만-수정-버튼을-ui-레벨에서-보여주는데-굳이-위와-같이-서버단에서-validate-검증-로직이-추가로-필요할까">🤔question_detail.html 템플릿 파일에서는 아래와 같이 로그인한 사용자의 ID와 작성자가 같을 때만 수정 버튼을 UI 레벨에서 보여주는데 굳이 위와 같이 서버단에서 validate 검증 로직이 추가로 필요할까?</h4>
<pre><code class="language-html">&lt;a th:href=&quot;@{|/question/modify/${question.id}|}&quot; class=&quot;btn btn-sm btn-outline-secondary&quot;
   sec:authorize=&quot;isAuthenticated()&quot;
   th:if=&quot;${question.author != null and #authentication.getPrincipal().getUsername() == question.author.username}&quot;
   th:text=&quot;수정&quot;&gt;&lt;/a&gt;</code></pre>
<p>💡UI 버튼을 숨겼더라도, 공격자가 <code>/question/modify/1234</code>와 같이 직접적으로 URL에 API를 호출하는 공격을 할 수도 있기 때문에 추가로 <strong>서버단에서 검증 로직을 구현하는 것은 매우 중요</strong>하다.</p>
<p>🔎CSRF 공격: _사용자 모르게 실행_되는 악의적인 요청으로, CSRF 토큰은 &quot;이 요청이 진짜 우리 웹사이트에서 발생한 것인가?&quot;를 확인하기 위한 장치라고 보면 된다.</p>
<ul>
<li>사용자 모르게 실행되기 때문에 얼마든지** XSS 공격과 결합**될 수 있으며, 공격자는 XSS를 통해 삽입된 악성 스크립트를 이용해 사용자가 CSRF 공격을 통해 의도하지 않은 행위를 웹 서버에 요청하게 한다.<ul>
<li>📝정리하자면, XSS는 웹 사이트 취약점을 발견하여 악성 스크립트를 삽입하는 공격을 의미하고, CSRF는 공격자를 사용자로 착각하게 만들어서 사용자가 의도하지 않은 요청을 웹 서버에 보내는 공격을 말한다.</li>
</ul>
</li>
</ul>
<p>이제 수정된 질문을 서비스에서 처리하고 리포지토리를 통해 저장할 수 있도록 코드를 수정해보자. </p>
<h3 id="💻questionservice">💻QuestionService</h3>
<pre><code class="language-java">public void modify(Question question, String subject, String content) {
        question.setSubject(subject);
        question.setContent(content);
        question.setModifyDate(LocalDateTime.now());
        questionRepository.save(question);
    }</code></pre>
<p>서비스에 수정 작업 메서드를 만들었으므로, 이제 @PostMapping으로 매핑된 메서드를 추가해서 클라이언트한테 수정된 질문을 보여주자.</p>
<h3 id="💻questioncontroller-1">💻QuestionController</h3>
<pre><code class="language-java">@PreAuthorize(&quot;isAuthenticated()&quot;)
    @PostMapping(&quot;/modify/{id}&quot;)
    public String modifyQuestion(@Valid QuestionForm questionForm, BindingResult bindingResult, Principal principal, @PathVariable(&quot;id&quot;) Long id) {
        // 폼 검증
        String resultPage = fieldErrorHandler.handleError(bindingResult, &quot;question_form&quot;);
        if (resultPage != null) {
            return resultPage;
        }
        // 서비스 로직 실행
        Question question = questionService.getQuestion(id);
        validateAuthor(principal, question);
        questionService.modify(question, questionForm.getSubject(), questionForm.getContent());
        log.info(&quot;modified question with ID: {}, subject: {}, content: {}, modifyDate: {}&quot;, question.getId(), question.getSubject(), question.getContent(), question.getModifyDate());
        return String.format(&quot;redirect:/question/detail/%s&quot;, id);
    }</code></pre>
<ul>
<li>POST 요청에서 폼(Form)은 사용자의 입력을 검증하기 때문에 유효성 검사를 위해 폼 객체에 <code>@Valid</code>를 달고, 뒤에 에러 검출을 위한 BindingResult를 파라미터로 전달하였다.<ul>
<li>🔎DTO(Data Transfer Object)도 마찬가지로 <strong>순수한 입력 데이터를 전달하기 때문에 유효성 검사가 필요</strong>하다. <ul>
<li>다만, 폼 객체는 웹 폼에서 입력된 데이터를 담기 위한 DTO의 한 종류라는 것에 유의하자.(폼 객체 ⊂ DTO)</li>
</ul>
</li>
</ul>
</li>
<li>검증이 성공적으로 수행되면, 사용자한테 현재 질문 페이지를 보여주게 하였다.</li>
</ul>
<h2 id="질문-삭제-기능-생성하기">질문 삭제 기능 생성하기</h2>
<ul>
<li>🖼️<code>javascript:void(0)</code>: 템플릿 파일에서 삭제 버튼은 href 속성값을 이렇게 설정했는데, <strong>클릭 시 페이지가 다른 페이지로 이동하거나 현재 페이지를 다시 로딩하지 않게 하기</strong> 위해서다.<ul>
<li>대신에, 인라인 자바스크립트로 <code>confirm(&quot;정말로 삭제하시겠습니까?&quot;)</code> dialect 메시지 창을 띄우기 위해, data-uri에 현재 URL값을 넣어주었다.</li>
<li>🔎자바스크립트 코드는 화면 출력이 완료된 후에 자바스크립트가 실행되는 것이 좋기 때문에 <strong><code>&lt;/body&gt;</code> 태그 바로 위에 삽입하는 것을 추천</strong>한다.👍</li>
</ul>
</li>
</ul>
<p>다시 서버단으로 돌아와서 우리가 질문 삭제 기능을 생성하기 위해서는 다음과 같은 절차가 필요하다.</p>
<ul>
<li>🗹질문 서비스 삭제 기능 추가</li>
<li>🗹질문 컨트롤러 삭제 메서드 추가(@GetMapping에 주의)</li>
</ul>
<h3 id="💻questionservice-1">💻QuestionService</h3>
<pre><code class="language-java">public void delete(Question question) {
        questionRepository.delete(question);
    }</code></pre>
<ul>
<li>우리는 스프링 데이터 JPA를 사용하기 때문에 delete 메서드를 리포지토리한테 위임하면 된다.</li>
</ul>
<h3 id="💻questioncontroller-2">💻QuestionController</h3>
<pre><code class="language-java">@PreAuthorize(&quot;isAuthenticated()&quot;)
    @GetMapping(&quot;/delete/{id}&quot;)
    public String deleteQuestion(Principal principal, @PathVariable(&quot;id&quot;) Long id) {
        Question question = questionService.getQuestion(id);
        validateAuthor(principal, question);
        questionService.delete(question);
        log.info(&quot;after deleting question with ID: {}, subject: {}&quot;, question.getId(), question.getSubject());
        return &quot;redirect:/&quot;;
    }</code></pre>
<ul>
<li>질문을 삭제하고 나면 질문 데이터가 사라지기 때문에 질문 목록으로 리다이렉트 시켰다.</li>
</ul>
<h4 id="🤔왜-삭제-요청을-하는데-deletemapping을-하지-않고-대신에-getmapping을-메서드에-매핑했나요">🤔왜 삭제 요청을 하는데 @DeleteMapping을 하지 않고 대신에 @GetMapping을 메서드에 매핑했나요?</h4>
<p>먼저 아래의 HTML 코드와 자바스크립트 코드를 살펴보자.</p>
<pre><code class="language-javascript">&lt;a href=&quot;javascript:void(0);&quot; th:data-uri=&quot;@{|/question/delete/${question.id}|}&quot;
   class=&quot;delete btn btn-sm btn-outline-secondary&quot; sec:authorize=&quot;isAuthenticated()&quot;
   th:if=&quot;${question.author != null and #authentication.getPrincipal().getUsername() == question.author.username}&quot;
   th:text=&quot;삭제&quot;&gt;&lt;/a&gt;</code></pre>
<ul>
<li>사용자가 삭제 버튼을 누르면 자바스크립트 코드가 실행되고, data-uri 속성값에 URL을 별도로 설정하였다.<ul>
<li>🔎참고로, <code>javascript:void(0)</code>은 아무값도 리턴하지 않는다는 의미인데, dialog 메시지 박스처럼 <strong>별도의 확인 절차</strong>를 중간에 끼워 넣기 위해서 사용한다.</li>
</ul>
</li>
</ul>
<hr />
<pre><code class="language-javascript">const delete_elements = document.getElementsByClassName(&quot;delete&quot;);
Array.from(delete_elements).forEach(function (element) {
    element.addEventListener('click', function () {
        if (confirm(&quot;정말로 삭제하시겠습니까?&quot;)) {
            location.href = this.dataset.uri;
        }
    });
});</code></pre>
<p>다음으로 자바스크립트 코드는 사용자가 &quot;정말로 삭제하시겠습니까?&quot;라는 dialog 메시지 박스에서 확인 버튼을 누르면 위에서 설정한 dataset-uri를 <strong>브라우저의 location.href로 설정해서 GET 요청을 발생</strong>시킨다. 그리고 이것을 QuestionController에서 <code>@GetMapping</code> 메서드가 전달받아서 처리한다.</p>
<p>🔎서버를 RESTful API로 설계한다면, <code>@DeleteMapping</code> 메서드를 작성하는게 맞다.</p>
<h2 id="답변-수정-기능-추가하기">답변 수정 기능 추가하기</h2>
<ul>
<li>🗹답변 서비스에 답변 조회하기  </li>
<li>🗹답변 서비스에 답변 수정하기 </li>
<li>🗹답변 컨트롤러에 GET 방식으로 답변 입력 폼 보여주기</li>
<li>🗹답변 컨트롤러에 POST 방식으로 사용자가 입력한 답변 폼 서버에서 처리하기</li>
</ul>
<p>위의 요구사항을 순서대로 구현해보자. 이미 질문에서 수정 기능을 구현했으므로 어렵지 않게 구현할 수 있다.</p>
<h3 id="💻answerservice-1">💻AnswerService</h3>
<pre><code class="language-java">public class AnswerService {
    (...생략)

    public Answer getAnswer(Long id) {
        return answerRepository.findById(id).orElseThrow(() -&gt; new DataNotFoundException(&quot;answer not found&quot;));
    }

    public void modify(Answer answer, String content) {
        answer.setContent(content);
        answer.setModifyDate(LocalDateTime.now());
        answerRepository.save(answer);
    }
}</code></pre>
<h3 id="💻answercontroller-2">💻AnswerController</h3>
<pre><code class="language-java">public class AnswerController {
    (...생략)
    private final AuthorValidator authorValidator;

    @PreAuthorize(&quot;isAuthenticated()&quot;)
    @GetMapping(&quot;/modify/{id}&quot;)
    public String modifyAnswer(AnswerForm answerForm, @PathVariable(&quot;id&quot;) Long id, Principal principal) {
        Answer answer = answerService.getAnswer(id);
        authorValidator.validateAuthor(principal, answer, Answer::getAuthor);
        answerForm.setContent(answer.getContent());
        return &quot;answer_form&quot;;
    }

    @PreAuthorize(&quot;isAuthenticated()&quot;)
    @PostMapping(&quot;/modify/{id}&quot;)
    public String modifyAnswer(@Valid AnswerForm answerForm, BindingResult bindingResult, @PathVariable(&quot;id&quot;) Long id, Principal principal) {
        if (bindingResult.hasErrors()) {
            log.error(&quot;폼 검증 오류: {}&quot;, bindingResult.getAllErrors());
            return &quot;answer_form&quot;;
        }
        Answer answer = answerService.getAnswer(id);
        authorValidator.validateAuthor(principal, answer, Answer::getAuthor);
        answerService.modify(answer, answerForm.getContent());
        log.info(&quot;modified answer with ID: {}, content: {}, modifyDate: {}&quot;, answer.getId(), answer.getContent(), answer.getModifyDate());
        return String.format(&quot;redirect:/question/detail/%s#answer_%s&quot;, answer.getQuestion().getId(), answer.getId());
    }

}</code></pre>
<ul>
<li>질문과 마찬가지로 작성자와 현재 사용자명(사용자 ID)가 일치한지 확인한 뒤, 수정을 위한 답변 폼을 사용자에게 response로 보여주게 하였다.</li>
<li><del>QuestionController에서 사용한 validateAuthor 메서드명도 동일하지만, 파라미터가 달라서 답변 컨트롤러에도 validateAuthor 메서드를 새로 만들어서 캡슐화 하였다.</del></li>
</ul>
<p>♻️validateAuthor 로직이 중복되기 때문에 아예 클래스로 승격시켜서 검증 로직의 관심사를 분리시켰다.</p>
<h3 id="💻authorvalidator-클래스">💻AuthorValidator 클래스</h3>
<pre><code class="language-java">package com.mysite.sbb.common;

import com.mysite.sbb.user.SiteUser;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ResponseStatusException;

import java.security.Principal;
import java.util.function.Function;

@Component
public class AuthorValidator {
    // T 타입 파라미터는 getAuthor() 메서드가 있어야 함
    public &lt;T&gt; void validateAuthor(Principal principal, T entity, Function&lt;T, SiteUser&gt; authorExtractor) {
        SiteUser author = authorExtractor.apply(entity);
        if (!author.getUsername().equals(principal.getName())) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, &quot;수정 권한이 없습니다.&quot;);
        }
    }

}</code></pre>
<ul>
<li>원래는 Object를 파라미터로 전달해서 Question, Answer 엔티티로 다운캐스팅하려 했는데, 이렇게 하면 새로운 엔티티가 추가되면 코드를 수정해야 하므로 <strong>Function 함수형 인터페이스</strong>를 사용해서 검증이라는 <strong>관심사를 별도 클래스로 분리해 단일 책임 원칙(SRP)을 준수</strong>시켰다.</li>
</ul>
<p>🔎참고로 Function 함수형 인터페이스 함수 바디는 이렇게 생겼다.</p>
<pre><code class="language-java">@FunctionalInterface
public interface Function&lt;T, R&gt; {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);
}</code></pre>
<p>📓자바 제네릭 개념이 잘 생각이 안나면 <a href="https://velog.io/@csj0209/%EC%A0%9C%EB%84%A4%EB%A6%AD-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%97%B0%EC%9E%A5-%EC%A0%84%EB%B0%98%EC%A0%84-%EC%97%B0%EC%9E%A5-%ED%9B%84%EB%B0%98%EC%A0%84">제네릭 클래스(연장 전반전, 연장 후반전)</a>을 참고할 것</p>
<h2 id="답변-삭제-기능-추가하기">답변 삭제 기능 추가하기</h2>
<p>🤩질문 삭제 기능과 동일하므로 빠르게 알아보자.</p>
<ul>
<li>🗹답변 서비스에 삭제 메서드 추가</li>
<li>🗹답변 컨트롤러에 <code>@GetMapping</code> 메서드 추가<ul>
<li>질문 삭제 기능과 마찬가지로, 삭제 버튼을 누르면 <code>location.href = this.dataset.uri</code>로 설정되기 때문에 GET 방식으로 요청한다.<ul>
<li>삭제 버튼에 delete 클래스를 적용했기 때문에 별도의 스크립트를 작성하지 않고 기존의 delete_elements 변수가 있는 스크립트를 그대로 사용하였다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3 id="💻answerservice-2">💻AnswerService</h3>
<pre><code class="language-java">public void delete(Answer answer) {
        answerRepository.delete(answer);
    }</code></pre>
<p>😎어때요, 참 쉽죠? 이전에 언급했다시피 스프링 데이터 JPA의 인터페이스를 그대로 사용하면 되기 때문에 별 코드를 추가하지 않았다.</p>
<h3 id="💻answercontroller-3">💻AnswerController</h3>
<pre><code class="language-java">public class AnswerController {
    (...생략)

    @PreAuthorize(&quot;isAuthenticated()&quot;)
    @GetMapping(&quot;/delete/{id}&quot;)
    public String deleteAnswer(Principal principal, @PathVariable(&quot;id&quot;) Long id) {
        Answer answer = answerService.getAnswer(id);
        authorValidator.validateAuthor(principal, answer, Answer::getAuthor);

        log.info(&quot;deleting answer with ID: {}, content: {}&quot;, answer.getId(), answer.getContent());
        answerService.delete(answer);
        log.info(&quot;after deleting answer with ID: {}, content: {}, question ID: {}&quot;, answer.getId(), answer.getContent(), answer.getQuestion().getId());
        return String.format(&quot;redirect:/question/detail/%s&quot;, answer.getQuestion().getId());
    }
}</code></pre>
<ul>
<li>QuestionController의 삭제 기능 메서드와 매우 유사하다.</li>
</ul>
<h2 id="수정-일시-표시하기">수정 일시 표시하기</h2>
<p>📖책에는 질문 내용과 답변 내용에 수정 일시가 나타나도록 기능을 추가하였는데, 나는 이것을 응용해서 수정 날짜가 없으면 생성된 날짜를 표시하게 하고, 그렇지 않으면 수정 날짜를 갱신해서 표시하게 하였다.</p>
<h3 id="💻question_detailhtml">💻question_detail.html</h3>
<pre><code class="language-html">&lt;div class=&quot;d-flex justify-content-end&quot;&gt;
                &lt;div class=&quot;badge bg-light text-dark p-2 text-start&quot;&gt;
                    &lt;div class=&quot;mb-2&quot;&gt;
                        &lt;span th:if=&quot;${question.author != null}&quot; th:text=&quot;${question.author.username}&quot;&gt;&lt;/span&gt;
                        &lt;!-- 수정 시간이 있을 경우 --&gt;
                        &lt;span th:if=&quot;${question.modifyDate != null}&quot;
                              th:text=&quot;${#temporals.format(question.modifyDate, 'yyyy-MM-dd HH:mm')}&quot;&gt;&lt;/span&gt;
                        &lt;!-- 수정 시간이 없을 경우(작성 시간만 있을 경우) --&gt;
                        &lt;span th:if=&quot;${question.modifyDate == null}&quot;
                              th:text=&quot;${#temporals.format(question.createDate, 'yyyy-MM-dd HH:mm')}&quot;&gt;&lt;/span&gt;
                    &lt;/div&gt;
                &lt;/div&gt;
            &lt;/div&gt;


&lt;!-- 답 변 반 복 시 작 --&gt;
&lt;div class=&quot;d-flex justify-content-end&quot;&gt;
                &lt;div class=&quot;badge bg-light text-dark p-2 text-start&quot;&gt;
                    &lt;div class=&quot;mb-2&quot;&gt;
                        &lt;span th:if=&quot;${answer.author != null}&quot; th:text=&quot;${answer.author.username}&quot;&gt;&lt;/span&gt;
                        &lt;!-- 수정 시간이 있을 경우 --&gt;
                        &lt;span th:if=&quot;${answer.modifyDate != null}&quot;
                              th:text=&quot;${#temporals.format(answer.modifyDate, 'yyyy-MM-dd HH:mm')}&quot;&gt;&lt;/span&gt;
                        &lt;!-- 수정 시간이 없을 경우(작성 시간만 있을 경우) --&gt;
                        &lt;span th:if=&quot;${answer.modifyDate == null}&quot;
                              th:text=&quot;${#temporals.format(answer.createDate, 'yyyy-MM-dd HH:mm')}&quot;&gt;&lt;/span&gt;
                    &lt;/div&gt;
                &lt;/div&gt;
            &lt;/div&gt;</code></pre>
<ul>
<li><p>🤔이것도 나중에 스프링의 Auditing 기능을 배우면 엔티티 생성 및 수정할 때 타임스탬프를 알아서 추적해주기 때문에 서비스 계층의 <code>create()</code>, <code>modify()</code>를 호출할 때 <code>answer.setCreateDate(LocalDateTime.now())</code>와 같이 setter로 관리할 필요가 없다. 아래와 같이 말이다:</p>
<pre><code class="language-java">@Entity
@EntityListeners(AuditingEntityListener.class)
public class Answer {
  @CreatedDate
  private LocalDateTime createDate;

  @LastModifiedDate
  private LocalDateTime modifyDate;
  // ...
}</code></pre>
</li>
</ul>
<h1 id="310-추천-기능-추가하기">3‑10 추천 기능 추가하기</h1>
<p>질문이나 답변을 본 다른 사용자들이 질문 상세 페이지에 있는 [추천] 버튼을 통해 반응을 남길 수 있도록 ‘추천’ 기능을 구현해 보자. 
그런데 답변에는 질문 관련 도메인도 생각해야 하므로 우선 질문 도메인에 추천 기능을 먼저 생성해보자.</p>
<h2 id="질문-추천-기능-생성하기">질문 추천 기능 생성하기</h2>
<ul>
<li>🗹 질문, 답변 엔티티에 voter 속성 추가</li>
<li>🗹 브릿지 엔티티 생성</li>
<li>🗹 브릿지 엔티티를 저장하기 위한 리포지토리 생성</li>
<li>🗹 템플릿 파일에 추천 버튼 생성 및 confirm() 메시지로 팝업 창 생성</li>
<li>🗹 질문 서비스에 추천 메서드 추가</li>
<li>🗹 질문 컨트롤러에 @GetMapping 추천 메서드 추가</li>
</ul>
<p>먼저 질문, 답변 엔티티에 SiteUser 객체 타입의 voter 속성을 추가해보자.
📖이때, question과 voter의 관계는 M:N이므로 책에서는 <code>@ManyToMany</code> 어노테이션을 사용했다. 
🚩그런데, 실무에서는 <code>@ManyToMany</code>를 사용하면 중간 엔티티(Bridge entity라고 함)에 필요한 속성을 추가할 수 없기 때문에 가급적 사용을 지양하라고 하였다. 
🔨따라서 우리는 M:N 관계를 1:M과 N:1로 풀어서 추천 기능을 만들어볼 것이다.</p>
<p>코드를 작성하기 전에, 두 엔티티의 관계를 모델링해보면 다음과 같다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/215ccc98-7759-426a-a021-25efac40420c/image.png" /></p>
<ul>
<li>위의 모델링을 바탕으로 해서 엔티티의 연관관계를 매핑해보자.</li>
</ul>
<h3 id="💻question">💻Question</h3>
<pre><code class="language-java">public class Question {
    (...생략)

    @OneToMany(mappedBy = &quot;question&quot;, cascade = CascadeType.REMOVE)
    private Set&lt;QuestionVoter&gt; questionVoters;
}</code></pre>
<ul>
<li>질문에 voter가 포함되기 때문에 질문 엔티티가 삭제되면 CASCADE 기능을 활용해서 questionVoter 엔티티도 삭제되도록 하였다.</li>
</ul>
<h3 id="💻questionvoter">💻QuestionVoter</h3>
<pre><code class="language-java">@Entity
@Table(
        // 묶어서 유니크 제약조건 설정(중복 추천 방지)
        uniqueConstraints = @UniqueConstraint(columnNames = {&quot;QUESTION_ID&quot;, &quot;SITE_USER_ID&quot;})
)
public class QuestionVoter {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @Setter(AccessLevel.NONE)
    @JoinColumn(name = &quot;QUESTION_ID&quot;)
    private Question question;

    @ManyToOne(fetch = FetchType.LAZY)
    @Setter(AccessLevel.NONE)
    @JoinColumn(name = &quot;SITE_USER_ID&quot;)
    private SiteUser siteUser;

    //* 컬렉션 쪽은 읽기만 하기 때문에 양방향일 때 연관관계 편의 메서드 사용
    public void takeQuestion(Question question) {
        this.question = question;
        question.getQuestionVoters().add(this);
    }

    public void takeSiteUser(SiteUser siteUser) {
        this.siteUser = siteUser;
        siteUser.getQuestionVoters().add(this);
    }

}</code></pre>
<ul>
<li>중복 추천을 방지하기 위해 추가로 <strong>클래스 레벨에서 테이블로 묶어서 유니크 제약조건을 설정</strong>하였다.</li>
<li><strong>컬렉션 타입(Question -&gt; QuestionVoter)은 읽기 전용</strong>이기 때문에, 엔티티 값이 설정되지 않아 null값이 들어가는 것을 방지하기 위해 양방향 연관관계 편의 메서드를 작성하였다.<ul>
<li>이미 <strong>연관관계 편의 메서드</strong>로 값을 양쪽에 세팅했기 때문에 <code>@Setter(AccessLevel.NONE)</code>를 Question과 SiteUser 객체에 매핑을 해서 실수로 값을 다시 설정하는 것을 방지하였다.</li>
</ul>
</li>
</ul>
<h4 id="🤔객체가-중복으로-저장되는-것을-방지하기-위해-set-타입으로-questionvoter를-저장함에도-클래스-레벨에서-추가로-유니크-제약조건을-설정한-이유가-무엇인가요">🤔객체가 중복으로 저장되는 것을 방지하기 위해 Set 타입으로 QuestionVoter를 저장함에도 클래스 레벨에서 추가로 유니크 제약조건을 설정한 이유가 무엇인가요?</h4>
<p>🤖<span style="color: red;"><strong>Java의 Set 컬렉션</strong></span>은** 한 객체 인스턴스가 동일하다고 판단될 때<strong>만 중복을 허용하지 않습니다. 
그러나 JPA에서 엔티티 컬렉션(<code>Set&lt;QuestionVoter&gt;</code>)을 사용할 때, <span style="color: red;"></strong>DB에 중복된 값이 들어오는 것을 근본적으로 막지 못합니다**</span>.</p>
<ul>
<li>예를 들어, 서로 다른 트랜잭션이나 동시성 문제 등으로 Set엔 중복이 없더라도 DB에는 중복이 저장될 수 있습니다.</li>
</ul>
<p>즉, 사용자가 GET 방식으로 추천을 서버에 요청하면 서버에서는 question 엔티티와 questionVoter 엔티티를 id값으로 식별해서 저장하는데, 순수한 자바 객체에서는 인스턴스의 해시코드값 외에는 id값을 안전하게 식별할 수 있는 방법이 한계가 있기 때문에, 데이터베이스에 UniqueConstraint 제약조건을 두어 INSERT 단계에서 중복 추천을 완전히 막아야 합니다.</p>
<h3 id="💻questionvotersrepository">💻QuestionVotersRepository</h3>
<pre><code class="language-java">public interface QuestionVotersRepository extends JpaRepository&lt;QuestionVoter, Long&gt; {
    boolean existsByQuestionAndSiteUser(Question question, SiteUser siteUser);

}</code></pre>
<ul>
<li>중복 추천을 방지하기 위해 질문에 대해 추천한 사용자가 이미 존재하는지 검증하기 위한 로직을 추가하였다.</li>
</ul>
<hr />
<p>다음으로 템플릿 파일을 통해 추천 버튼을 생성 및 <code>confirm()</code>으로 팝업 메시지를 생성해보자.</p>
<h3 id="💻question_detailhtml-1">💻question_detail.html</h3>
<pre><code class="language-html">&lt;a href=&quot;javascript:void(0);&quot; class=&quot;recommend btn btn-sm btn-outline-secondary&quot;
                   th:data-uri=&quot;@{|/question/vote/${question.id}|}&quot;&gt;
                    추천
                    &lt;span class=&quot;badge rounded-pill bg-success&quot;
                          th:text=&quot;${#lists.size(question.questionVoters)}&quot;&gt;&lt;/span&gt;
                &lt;/a&gt;

&lt;!-- 추천 관련 인라인 스크립트 --&gt;
&lt;script layout:fragment=&quot;script&quot; type='text/javascript' th:inline=&quot;javascript&quot;&gt;
    /* 서버에서 전달된 voteError 값을 JavaScript 변수로 가져옴 */
    var voteError = [[${voteError != null ? voteError : &quot;null&quot;
    }]]
    ;

const recommend_elements = document.getElementsByClassName(&quot;recommend&quot;);
    Array.from(recommend_elements).forEach(function (element) {
        element.addEventListener('click', function () {
            if (confirm(&quot; 정말로 추천하시겠습니까?&quot;)) {
                location.href = this.dataset.uri;
            }
        });
    });

&lt;/script&gt;</code></pre>
<ul>
<li>📌위의 voteError 변수 부분은 서버에서 전달된 voteError 값을 템플릿 파일로 못가져와서 작성한 부분인데, 일단 서버 전체적인 부분을 보고 다시 살펴보자.</li>
<li>타임리프에서는 변수에 값이 없으면 null로 렌더링하기 때문에 null로 에러 메시지 여부를 판단하였다.</li>
</ul>
<h3 id="💻questionservice-2">💻QuestionService</h3>
<pre><code class="language-java">public class QuestionService {
    (...생략)

    public void vote(Question question, SiteUser siteUser) {
        if (questionVotersRepository.existsByQuestionAndSiteUser(question, siteUser)) {
            throw new IllegalStateException(&quot;이미 추천한 사용자입니다.&quot;);
        }
        QuestionVoter questionVoter = new QuestionVoter();
        questionVoter.takeQuestion(question);
        questionVoter.takeSiteUser(siteUser);
        // questionRepository.save(question);
        questionVotersRepository.save(questionVoter);
    }
}</code></pre>
<ul>
<li>중복 검증 로직을 통과하면 QuestionVoter 인스턴스를 새로 생성해서 vote를 요청한 질문 및 사용자를 연관관계 편의 메서드를 통해 값을 설정하였다.</li>
<li>💡<code>questionRepository.save(question)</code>을 제거한 이유는 이미 questionVoter의 연관관계 편의 메서드로 양쪽에 id값을 세팅했고 따라서<code>questionVotersRepository.save(questionVoter)</code>만 해도 뷰 계층에서 <code>question.questionVoter</code>로 접근이 가능해서 추천 수가 정확하게 표시되었다.<ul>
<li>questionVoter와 question은 <strong>대칭적 관계</strong>!</li>
</ul>
</li>
</ul>
<h3 id="💻questioncontroller-3">💻QuestionController</h3>
<pre><code class="language-java">@PreAuthorize(&quot;isAuthenticated()&quot;)
    @GetMapping(&quot;/vote/{id}&quot;)
    public String voteQuestion(RedirectAttributes redirectAttributes, Principal principal, @PathVariable(&quot;id&quot;) Long id) {
        Question question = questionService.getQuestion(id);
        SiteUser siteUser = userService.getUser(principal.getName());

        try {
            questionService.vote(question, siteUser);
            log.info(&quot;after voting question with ID: {}, siteUser: {}&quot;, question.getId(), siteUser.getId());
        } catch (IllegalStateException e) {
            redirectAttributes.addFlashAttribute(&quot;voteError&quot;, e.getMessage());
            log.error(&quot;duplicate vote error for question ID: {}, siteUser: {}&quot;, question.getId(), siteUser.getId());
        }

        return String.format(&quot;redirect:/question/detail/%s&quot;, id);
    }</code></pre>
<ul>
<li>💡만약에 중복 추천이 발생했을 경우, voteError 메시지를 Model에 담아서 프론트로 전달해야 하는데, <strong>리다이렉트 이후에는 Model에 있는 임시 데이터가 사라져서</strong> alert에 담길 에러 메시지가 사라진다. 
따라서 RedirectAttributes를 메서드 파라미터로 전달하면 해결할 수 있다.<ul>
<li>🔎<code>addFlashAttribute</code>로 전달한 값은 <strong>세션에 임시 저장</strong>되었다가, 리다이렉트 된 후** 일회성으로 URL에 데이터를 노출시키지 않고** 뷰에서 사용할 수 있다. </li>
<li>🔎<code>Model.addAttribute(&quot;key&quot;, value)</code>는 뷰 계층으로 바로 렌더링할 때 사용한다.</li>
</ul>
</li>
<li>위의 question_detail.html 파일에서 redirectAttributes에 담긴 voteError를 JavaScript 변수로 가져온 다음, alert로 전달시켜서 중복 여부를 일회성 팝업 메시지로 표시하게 하였다.</li>
</ul>
<h2 id="답변-추천-기능-생성하기">답변 추천 기능 생성하기</h2>
<p>🤩질문 추천 기능과 유사하기 때문에 생략하였다.</p>
<h1 id="311-앵커-기능-추가하기">3‑11 앵커 기능 추가하기</h1>
<p>🔖앵커: HTML에서 URL 호출시 원하는 위치로 이동시켜줌, <code>&lt;a&gt;</code>태그가 바로 앵커 태그다.
🔎SSR이 아닌 CSR 환경(React.js, Vue.js)에서는 보통 Ajax 비동기 통신 기술로 이 문제를 해결한다고 한다.</p>
<p>🏁우리의 목표는 답변 등록, 답변 수정, 답변 추천 시 앵커 태그를 이용하여 원하는 위치로 이동시키는 것이다.</p>
<p>💡앵커 태그의 id 속성이 유일하지 않고 <span style="color: red;"><strong>중복된 값이 존재</strong></span>한다면 맨 처음 한 개를 제외한 <span style="color: red;"><strong>나머지 앵커는 제대로 동작하지 않는다.</strong></span></p>
<p>🤩이미 기능들을 완성시켰기 때문에 redirect를 리턴하는 메서드에 다음과 같이 수정만 해주면 된다.</p>
<pre><code class="language-java">return String.format(&quot;redirect:/question/detail/%s#answer_%s&quot;,
answer.getQuestion().getId(), answer.getId());</code></pre>
<ul>
<li>몰론 이렇게 수정하려면 앵커 포맷팅 부분에 answer의 id를 가져와야 하는데, 우리는 AnswerService에서 create 리턴을 void로 설정하였으므로, 이것을 Answer 객체로 리턴시키게 수정한 다음, 
AnswerController에서 <code>answerService.create()</code>를 호출해서 Answer 객체를 가져오고 
<code>answer.getId()</code>로 포맷팅 부분을 매칭시켜주면 된다.</li>
</ul>
<h1 id="312-마크다운-적용하기">3‑12 마크다운 적용하기</h1>
<p>🎉축하한다! 당신은 이 글을 보고 나면 나만의 게시판에 텍스트를 마크다운 문법으로 적용시킬 수가 있게 된다.</p>
<blockquote>
<p>📢마크다운 문법은 인터넷에 이미 널리 알려져 있으므로, 문법 소개는 하지 않겠습니다.</p>
</blockquote>
<ul>
<li>🗹 build.gradle에 마크다운 라이브러리 설치하기</li>
<li>🗹 마크다운 컴포넌트 클래스 만들기</li>
<li>🗹 템플릿에 마크다운 적용하기(&lt;- 타임리프 문법에 종속되므로 생략)</li>
</ul>
<p>gradle 파일에 <code>implementation 'org.commonmark:commonmark:0.21.0'</code>를 적용하면 된다.
🔎스프링부트가 내부적으로 관리하는 라이브러리라면 버전 정보를 명시하지 않을 경우 가장 궁합이 잘 맞는 버전을 자동으로 선택해준다.</p>
<h2 id="마크다운-컴포넌트-작성하기">마크다운 컴포넌트 작성하기</h2>
<p>우리는 질문, 답변 도메인에만 종속되는 것이 아니라 CommonUtil이라는 이름으로 좀 더 범용적으로 사용할 수 있는 마크다운 컴포넌트를 만들 것이다.</p>
<pre><code class="language-java">package com.mysite.sbb.common.util;

import org.commonmark.ext.autolink.AutolinkExtension;
import org.commonmark.ext.gfm.tables.TablesExtension;
import org.commonmark.node.Node;
import org.commonmark.parser.Parser;
import org.commonmark.renderer.html.HtmlRenderer;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class CommonUtil {
    // 마크다운 텍스트를 HTML 문서로 변환하여 리턴
    public String markdown(String markdown) {
        Parser parser = Parser.builder()
                .extensions(Arrays.asList(
                        TablesExtension.create(), // 표 지원
                        AutolinkExtension.create() // 자동 링크 변환 지원
                ))
                .build();
        Node document = parser.parse(markdown);
        HtmlRenderer renderer = HtmlRenderer.builder().build();
        return renderer.render(document);
    }
}</code></pre>
<ul>
<li>markdown 메서드는 마크다운 문법이 적용된 일반 텍스트를 변환된 HTML로 리턴한다.</li>
<li>🔖<code>@Component</code>: 클래스 레벨에서 선언하면 스프링부트가 관리하는 빈으로 등록된다.<ul>
<li>🔎<code>@Controller</code>, <code>@Service</code>, <code>@Repository</code>와 같이 많이 쓰이는 Sterotype 어노테이션도 마찬가지로 컴포넌트 빈으로 등록되는데, 단지 내부 코드를 보면 <code>@AliasFor</code>로 인터페이스 타입의 빈이 등록된 것을 알 수 있다.</li>
</ul>
</li>
</ul>
<h1 id="313-검색-기능-추가하기">3‑13 검색 기능 추가하기</h1>
<p>SBB 게시판에는 질문, 답변 데이터가 계속 쌓이기 때문에 검색 기능은 필수다. 
따라서 검색 기능을 구현하기 위해서는:</p>
<ol>
<li>검색어를 입력할 수 있는 텍스트 창 생성 </li>
<li>검색어를 입력하여 조회하면 검색어에 해당되는 질문들 표시
와 같은 흐름이 필요하다.</li>
</ol>
<ul>
<li>🗹QuestionRepository 네이티브 쿼리 매핑한 메서드 추가</li>
<li>🗹QuestionService getList 메서드 수정</li>
<li>🗹QuestionController 질문 목록 조회 메서드 수정</li>
</ul>
<p>먼저, 질문 리포지토리의 쿼리를 매핑하기 전에, 다음과 같은 SQL 쿼리를 살펴보자.</p>
<pre><code class="language-mySQL">select
    distinct q.id, 
    q.author_id,
    q.content, 
    q.create_date, 
    q.modify_date, 
    q.subject
from question q 
left outer join site_user u1 on q.author_id=u1.id 
left outer join answer a on q.id=a.question_id 
left outer join site_user u2 on a.author_id=u2.id where
    q.subject like '%스프링%' 
    or q.content like '%스프링%' 
    or u1.username like '%스프링%' 
    or a.content like '%스프링%' 
    or u2.username like '%스프링%'</code></pre>
<ul>
<li>question, answer, site_user 테이블을 대상으로 ‘스프링’ 이라는 문자열이 포함된 데이터를 검색한다.</li>
<li>question 테이블을 기준으로 answer, site_user 테이블을 outer join하여 문자열 '스프링'을 검색한다.<ul>
<li>outer join 대신 (inner) join을 사용할 경우, 교집합으로 검색되어 데이터 검색 결과가 누락될 수 있음</li>
</ul>
</li>
<li>총 3개의 테이블을 대상으로 outer join(검색 결과가 합집합)하여 검색하면 <strong>중복된 결과</strong>가 나올 수 있어서 select문에 <code>distinct</code>를 함께 적어 중복을 제거<ul>
<li><strong>한 질문에 여러 답변</strong>이 달려 있을 때 질문 정보(<code>q.id</code>)가 중복되어 여러 행으로 나타날 수 있기 때문에 <code>distinct</code>를 별도로 추가하였다.</li>
</ul>
</li>
</ul>
<h4 id="🤔left-outer-join을-사용한-이유">🤔LEFT OUTER JOIN을 사용한 이유?</h4>
<ul>
<li>질문(question)에 답변(answer)이 없는 경우도 결과에 포함시키기 위함</li>
<li>질문 작성자(site_user), 답변 작성자(site_user) 정보도 모두 포함</li>
</ul>
<p>즉, <strong>질문(기준 테이블)을 기준</strong>으로 해서, 관련된 모든 데이터를 합집합(outer join)으로 가져오려고 사용했다.</p>
<h3 id="💻questionrepository">💻QuestionRepository</h3>
<pre><code class="language-java">public interface QuestionRepository extends JpaRepository&lt;Question, Long&gt; {
    (...생략)

    @Query(&quot;select &quot;
            + &quot;distinct q &quot;
            + &quot;from Question q &quot;
            + &quot;left outer join SiteUser u1 on q.author = u1 &quot;
            + &quot;left outer join Answer a on a.question = q &quot;
            + &quot;left outer join SiteUser u2 on a.author = u2 &quot;
            + &quot;where &quot;
            + &quot;q.subject like %:kw% &quot;
            + &quot;or q.content like %:kw% &quot;
            + &quot;or u1.username like %:kw% &quot;
            + &quot;or a.content like %:kw% &quot;
            + &quot;or u2.username like %:kw% &quot;)
    Page&lt;Question&gt; findAllByKeyword(@Param(&quot;kw&quot;) String kw, Pageable pageable);
}</code></pre>
<ul>
<li>📢<code>@Query</code>는 JPQL을 기반으로 동작하기 때문에 반드시 테이블 기준이 아닌 <strong>엔티티 기준</strong>으로 작성해야 한다.<ul>
<li><strong>조인문</strong>에서는 <strong>엔티티의 속성명</strong>을 사용해야 한다.</li>
</ul>
</li>
<li>매개변수로 전달할 kw 문자열은 <code>@Param(&quot;kw&quot;) String kw</code>처럼 어노테이션으로 전달해야 한다.<ul>
<li>그러면 <code>@Query</code>안에서 :kw로 참조된다.</li>
</ul>
</li>
<li>📢네이티브 쿼리이기 때문에 문자열을 + 연산자로 합칠 때 반드시 <strong>끝에 띄어쓰기</strong>를 해줘야 한다.  </li>
<li><strong>OR 연산자를 사용</strong>해서 제목, 내용, 질문 작성자, 답변 내용, 답변 작성자에 검색할 키워드가 <strong>하나라도 만족</strong>하는 경우 해당 행을 반환한다.<ul>
<li>이때, 반환된 행을 <strong>페이징</strong>해서 보여주기 위해 Page 객체를 반환하였다.</li>
</ul>
</li>
</ul>
<h3 id="💻questionservice-3">💻QuestionService</h3>
<pre><code class="language-java">public class QuesitonService {
    (...생략)

    public Page&lt;Question&gt; getList(int page, String kw) {
        List&lt;Sort.Order&gt; sorts = new ArrayList&lt;&gt;();
        sorts.add(Sort.Order.desc(&quot;createDate&quot;));
        // 한 페이지에 10개의 데이터를 최신순으로 보여줌
        Pageable pageable = PageRequest.of(page, 10, Sort.by(sorts));
        return questionRepository.findAllByKeyword(kw, pageable);
    }</code></pre>
<ul>
<li>검색어를 의미하는 kw를 getList 파라미터에 추가하고 <strong>현재 페이지를 기준으로 10개의 페이지</strong>를 보여준다.<ul>
<li>이때, 10개의 페이지를 <strong>최신순</strong>으로 보여준다.</li>
</ul>
</li>
</ul>
<h3 id="💻questioncontroller-4">💻QuestionController</h3>
<pre><code class="language-java">public class QuestionController {
    (...생략)

    @GetMapping(&quot;/list&quot;)
    public String list(Model model, @RequestParam(value = &quot;page&quot;, defaultValue = &quot;0&quot;) int page, @RequestParam(value = &quot;kw&quot;, defaultValue = &quot;&quot;) String kw) {
        Page&lt;Question&gt; paging = questionService.getList(page, kw);
        model.addAttribute(&quot;paging&quot;, paging);
        model.addAttribute(&quot;kw&quot;, kw);
        return &quot;question_list&quot;;
    }</code></pre>
<ul>
<li>📢검색어가 입력되지 않을 경우 kw값이 null이 되는 것을 방지하기 위해 <strong>빈 문자열을 기본값으로 설정</strong>한다.</li>
<li>화면에서 입력한 검색어를 화면에 그대로 표시하기 위해 <code>model.addAttribute(&quot;kw&quot;, kw)</code>로 kw값을 저장해서 템플릿 파일로 전달해줬다.</li>
</ul>
<h2 id="🤔템플릿-파일에서-form-태그에서-post-방식이-아닌-get-방식을-사용한-이유">🤔템플릿 파일에서 form 태그에서 POST 방식이 아닌 GET 방식을 사용한 이유</h2>
<p>2 페이지에서 3 페이지로 이동한 후, '뒤로가기' 를 통해 2 페이지로 이동할 경우, 브라우저는 <strong><span style="color: red;">동일한 POST 요청을 방지하기 위해 '만료된 페이지입니다.'라는 오류를 발생</span></strong>시킨다.</p>
<p>🔨위의 키워드를 기준으로 검색하는 것과 같이 여러 매개변수를 조합하여 게시물 목록을 조회할 때는 GET 방식을 사용하는 것을 강력히 권장
👉GET 방식은 <strong>멱등성이 보장</strong>되기 때문에 동일한 요청을 여러 번 수행해도 서버의 상태를 변경하지 않는다. 따라서 <strong>검색과 페이징은 GET 방식</strong>을 사용하자!</p>
<p>🔎GET:** URL에 검색 조건이 포함<strong>되어 특정 검색 결과나 페이지를 북마크/공유할 수 있음
POST 요청: HTTP Body에 데이터를 담아서 서버한테 전달하기 때문에 **URL을 노출시키지 않아</strong> 주로 사용자의 개인정보를 전달할 때 사용한다.</p>
<p>📖책에서는 인라인 스크립트로 사용자가 검색창을 클릭할 때만 스크립트를 동작시키게 하였는데, 나는 이것을 응용해서 검색창을 클릭 또는 키보드 엔터키를 누를 때 동작시키게 만들었다.</p>
<h3 id="💻question_listhtml">💻question_list.html</h3>
<pre><code class="language-html">&lt;script layout:fragment=&quot;script&quot; type='text/javascript'&gt;
    const page_elements = document.getElementsByClassName(&quot;page-link&quot;);
    Array.from(page_elements).forEach(function (element) {
        element.addEventListener('click', function () {
            document.getElementById('page').value = this.dataset.page;
            document.getElementById('searchForm').submit();
        });
    });

    function doSearch() {
        document.getElementById('kw').value = document.getElementById('search_kw').value;
        document.getElementById('page').value = 0; // 검색버튼을 클릭할 경우 0페이지부터 조회
        document.getElementById('searchForm').submit();
    }

    // 버튼은 클릭을 해야하니까 버튼의 id값을 이벤트 리스너 객체로 설정
    const btn_search = document.getElementById('btn_search');
    btn_search.addEventListener('click', doSearch);

    // 키보드 입력은 input에 대응하므로 input의 id값을 이벤트 리스너 객체로 설정
    const searchKw = document.getElementById('search_kw');
    searchKw.addEventListener('keyup', function(event) {
        if (event.key === 'Enter') {
            doSearch();
        }
    });
&lt;/script&gt;</code></pre>
<ul>
<li><code>doSearch()</code> 부분이 클릭 부분과 키보드 엔터 부분에 중복되서 적용하기 때문에 함수로 만들어서 캡슐화하였다.<ul>
<li>검색 버튼을 클릭(또는 엔터키)하는 경우는 <strong>새로운 검색</strong>에 해당되므로 <strong>page에 항상 0을 설정</strong>하여 <code>submit()</code>을 호출해서 form 태그 부분을 첫 페이지로 요청하도록 했다.</li>
</ul>
</li>
</ul>