<p>지금까지 &quot;Do it! 점프 투 스프링 부트 3&quot; 책을 보면서 여러 가지 기능들을 구현했는데, 스프링의 어떤 프레임워크를 사용해서 어떤 기능들을 구현했는지 나열하면서 살펴보자.</p>
<h1 id="☘️스프링-프레임워크기술">☘️스프링 프레임워크/기술</h1>
<ul>
<li><p>Spring Boot</p>
<ul>
<li>웹 애플리케이션의 전체 구조와 실행 환경 제공</li>
</ul>
</li>
<li><p>Spring MVC </p>
<ul>
<li>@Controller, @Service, @Repository 구조로 요청-응답 처리</li>
<li>URL과 메서드 매핑, 뷰 렌더링</li>
</ul>
</li>
<li><p>Spring Data JPA</p>
<ul>
<li>엔티티 기반 데이터베이스 연동</li>
<li>Repository를 통한 CRUD 구현</li>
</ul>
</li>
<li><p>Spring Security</p>
<ul>
<li>로그인, 로그아웃, 회원가입, 권한(인가) 처리</li>
<li>인증/인가 로직과 보안 설정</li>
</ul>
</li>
<li><p>Thymeleaf</p>
<ul>
<li>서버사이드 템플릿 엔진</li>
<li>HTML 템플릿에서 동적 데이터 바인딩</li>
</ul>
</li>
<li><p>Lombok</p>
<ul>
<li>Getter/Setter, 생성자 등 반복 코드 자동 생성</li>
</ul>
</li>
<li><p>H2</p>
<ul>
<li>개발/운영 환경 데이터베이스</li>
</ul>
</li>
</ul>
<h1 id="⚙️구현한-주요-기능">⚙️구현한 주요 기능</h1>
<ul>
<li><p>회원가입/로그인/로그아웃</p>
<ul>
<li>Spring Security 기반 인증/인가</li>
<li>사용자 정보 관리</li>
</ul>
</li>
<li><p>게시글 CRUD</p>
<ul>
<li>게시글 등록, 조회, 수정, 삭제</li>
<li>게시글 리스트/상세 페이지 구현</li>
</ul>
</li>
<li><p>댓글(답변) 기능</p>
<ul>
<li>게시글에 대한 답변(댓글) 작성, 삭제</li>
</ul>
</li>
<li><p>질문/답변 추천(Like) 기능</p>
<ul>
<li>게시글/답변 추천(좋아요) 기능<ul>
<li>다대다 관계를 <strong>브릿지 엔티티</strong>를 사용해서 일대다, 다대일 관계로 풀어냈고, 중복 추천을 방지하기 위해 <code>@Table</code>에 uniqueConstraints로 복합키를 사용하였다.</li>
<li>컬렉션은 읽기만 가능하기 때문에 다 관계에 연관관계 편의 메서드를 작성해서 양방향 엔티티에 값을 설정하였다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<ul>
<li><p>페이징 처리</p>
<ul>
<li>게시글 목록의 페이지네이션</li>
</ul>
</li>
<li><p>검색 기능</p>
<ul>
<li>게시글 제목/내용/작성자 기준 검색</li>
<li>네이티브 쿼리를 작성해서 동적으로 검색 가능하게 만들었다.</li>
<li>처음 루트 페이지로 접속할 때 QuestionService에서 검색어가 없으면 전체 조회하는 로직과 검색어가 있으면 검색하는 로직 2가지를 if-else로 분기해서 메서드로 만들었다.<ul>
<li><strong>빈 문자열 처리는 검색 기능의 필수사항</strong> &amp;&amp; 페이징을 통한 데이터 렌더링 문제 해결</li>
</ul>
</li>
</ul>
</li>
</ul>
<ul>
<li><p>템플릿 공통 레이아웃</p>
<ul>
<li><p>Thymeleaf 레이아웃 기능 활용</p>
</li>
<li><p>내비게이션 바, 공통 헤더/푸터</p>
</li>
</ul>
</li>
<li><p>에러/권한 예외 처리</p>
<ul>
<li>인증/인가 실패 시 예외 처리 및 안내 메시지</li>
</ul>
</li>
</ul>
<h1 id="다음-쇼핑몰-jpa-프로젝트에-적용할-kpt">다음 쇼핑몰 JPA 프로젝트에 적용할 KPT</h1>
<h2 id="keep">Keep</h2>
<p>스프링 시큐리티를 활용한 인증/인가, 게시글 CRUD 구현(좋아요 기능에 복합키로 중복 방지 포함) 및 페이징 처리</p>
<h2 id="problem">Problem</h2>
<p>신선하게 azure app service로 배포하려고 했으나, azure mysql for flexible server와 app service 연동 문제 및 기타 문제로 인해 1주일간 삽질을 했음에도 최종적으로 배포 실패함😭</p>
<h2 id="try">Try</h2>
<ul>
<li>프론트 영역의 타임리프 -&gt; <strong>Vue.js</strong>로 이쁘게 화면 렌더링(어짜피 나중에 마이크로소프트 LLM &amp; MCP 학습할 때 타입스크립트 지식 필요)</li>
<li><strong>linear</strong>로 워크플로우 관리하기<ul>
<li>나중에 대규모 프로젝트에는 Jira를 사용하는데 미리 체험하기 위해서</li>
</ul>
</li>
<li><strong>AWS S3</strong>로 이미지 업로드 및 버킷 관리하기<ul>
<li>dev용은 H2로 update 설정해서 테스트하고, prod용은 AWS RDS를 사용해서 mysql로 데이터를 관리하기</li>
</ul>
</li>
<li><strong>MapStruct</strong>를 활용해서 Entity를 Dto로 변환하기</li>
<li>** OAuth 2.0**을 사용해서 소셜 로그인 구현하기</li>
</ul>
<hr />
<p>📝게시판 프로젝트를 통해 배운 점은 MVC 패턴에 어느정도 익숙해졌고, 동적 검색 기능 구현 중에 빈 문자열 문제도 처리하였으며, 정적 팩토리 메서드 + 빌더 패턴을 사용해서 Entity를 Dto로 성공적으로 변환하였다는 것이다. </p>