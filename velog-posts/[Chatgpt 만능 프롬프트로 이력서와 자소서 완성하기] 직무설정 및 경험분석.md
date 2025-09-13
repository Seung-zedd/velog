<blockquote>
<p>📢이 시리즈는 <a href="https://www.inflearn.com/course/chat-gpt-%EC%9D%B4%EB%A0%A5%EC%84%9C%EC%99%80-%EC%9E%90%EA%B8%B0%EC%86%8C%EA%B0%9C%EC%84%9C-%EC%99%84%EC%84%B1%ED%95%98%EA%B8%B0/dashboard">Chatgpt 만능 프롬프트로 이력서와 자소서 완성하기</a>를 듣고 필요한 부분을 정리한 것으로, 모든 글의 저작권은 해당 강의에 있습니다.</p>
</blockquote>
<h1 id="💡-신입-포지션에-어울리는-강점-어필-포인트">💡 신입 포지션에 어울리는 강점 어필 포인트</h1>
<ol>
<li>Spring Boot + DB 기반 <strong>CRUD</strong> 구현 경험</li>
</ol>
<ul>
<li>신입 공고 대부분에서 요구하는 “회원가입/로그인, 게시판, CRUD” 경험 이미 확보.</li>
<li>MySQL/PostgreSQL 모두 활용 가능성을 강조 → 공고 맞춤형 대응 가능.</li>
</ul>
<ol start="2">
<li><strong>외부 API</strong> 연동 경험 (Clova AI)</li>
</ol>
<ul>
<li>“외부 서비스 연동 경험”은 신입 지원자 중 드물게 차별화되는 포인트.</li>
<li>특히 핀테크/커머스/B2B SaaS 기업에서 자주 나오는 요건.</li>
</ul>
<ol start="3">
<li>팀 <strong>협업</strong> 경험 (GDSC, 해커톤)</li>
</ol>
<ul>
<li>실무와 유사한 팀 개발 환경 경험 있음 → 협업, Git, 코드 리뷰 참여 등 강조 가능.</li>
<li>결과보다는 “문제 해결 과정과 기여”를 구체적으로 풀어내면 효과적.</li>
</ul>
<ol start="4">
<li><strong>자기주도 학습</strong> 능력</li>
</ol>
<ul>
<li>부트캠프 이수 X → 독학과 개인 프로젝트로 기술 학습 및 구현 성공.</li>
<li>“새로운 기술을 빠르게 습득하고 적용할 수 있음” 강조 포인트.</li>
</ul>
<h2 id="✍️-추천-직무--채용-공고-매칭">✍️ 추천 직무 &amp; 채용 공고 매칭</h2>
<ul>
<li>적합한 직무<ul>
<li>웹 서비스 백엔드 (Spring Boot 기반 CRUD, 회원·인증 기능 경험 어필)</li>
<li>B2B SaaS 백엔드 (외부 API, 클라우드 연계 경험 강조)</li>
<li>핀테크/커머스 백엔드 (DB 모델링, 트랜잭션 처리 기본기 강조)</li>
</ul>
</li>
</ul>
<h1 id="📝-프로젝트별-정리">📝 프로젝트별 정리</h1>
<h2 id="1-sbb-게시판-프로젝트-a-hrefhttpsvelogiocsj0209sbbboard-ed9484eba19ceca09ded8ab8-ec9984ec84b1-ed9b84-ed9a8ceab3a0회고-글-링크a">1. sbb 게시판 프로젝트 (<a href="https://velog.io/@csj0209/sbbboard-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%99%84%EC%84%B1-%ED%9B%84-%ED%9A%8C%EA%B3%A0">회고 글 링크</a>)</h2>
<h3 id="🚩해결한-문제">🚩해결한 문제</h3>
<ul>
<li><p>단순 CRUD 기능을 넘어서, 회원 가입/로그인, 질문·답변 관계 처리, 게시판 구조 설계 같은 실제 서비스에 가까운 요구사항을 어떻게 구현할지 고민.</p>
</li>
<li><p>JPA를 사용하면서 발생하는 연관관계 매핑 문제와 N+1 쿼리 문제를 경험.</p>
</li>
<li><p>예외 처리, 입력값 검증, API 응답 구조 등 서비스 품질 문제도 마주함.</p>
</li>
</ul>
<h3 id="🤔해결-방법">🤔해결 방법</h3>
<ul>
<li><p>Spring Boot + Spring Data JPA로 계층형 아키텍처(Controller–Service–Repository) 적용.</p>
</li>
<li><p>정적 팩토리 메서드 + 빌더 패턴을 활용하여 DTO/엔티티 분리를 통해 불필요한 데이터 노출 방지 및 API 스펙 일관성 유지.</p>
</li>
<li><p>@ManyToOne, @OneToMany 매핑 구조 학습 후 적용 → N+1 문제는 fetch join, @EntityGraph 활용으로 개선.</p>
</li>
<li><p>Validation 애너테이션(@Valid, @NotNull 등)을 활용해 입력값 검증 강화.</p>
</li>
</ul>
<h3 id="💡결과">💡결과</h3>
<ul>
<li><p>로그인/회원가입부터 질문·답변 CRUD까지 정상 동작하는 게시판 서비스 완성.</p>
</li>
<li><p>단순 기능 구현을 넘어 DB 모델링·객체지향적 설계·ORM 최적화 경험을 쌓음.</p>
</li>
<li><p>“문제 상황 → 원인 파악 → JPA 최적화 적용” 과정을 직접 경험하면서 실무에서 자주 겪는 문제를 미리 체험.</p>
</li>
</ul>
<h2 id="2-포텐데이-x-네이버-클라우드-해커톤-고도화-트랙-a-hrefhttpsvelogiocsj0209ed95b4ecbba4ed86a4ed8faced8590eb8db0ec9db4-kpt-ed9a8ceab3a0회고-글-링크a">2. 포텐데이 x 네이버 클라우드 해커톤 (고도화 트랙) (<a href="https://velog.io/@csj0209/%ED%95%B4%EC%BB%A4%ED%86%A4%ED%8F%AC%ED%85%90%EB%8D%B0%EC%9D%B4-KPT-%ED%9A%8C%EA%B3%A0">회고 글 링크</a>)</h2>
<h3 id="🚩해결한-문제-1">🚩해결한 문제</h3>
<ul>
<li><p>“청년층이 쉽게 번아웃되는 문제”를 주제로, 사용자 맞춤형 긍정 확언(Affirmation) 서비스 설계 필요.</p>
</li>
<li><p>사용자의 성향에 따라 다른 문구를 추천해야 했고, 단순 랜덤 출력이 아닌 개인화된 추천이 과제였음.</p>
</li>
<li><p>짧은 해커톤 기간 내에 AI API 연동 + 서비스 아키텍처 구축을 해야 했음.</p>
</li>
</ul>
<h3 id="🤔해결-방법-1">🤔해결 방법</h3>
<ul>
<li><p>네이버 Clova Studio API와 프롬프트 엔지니어링을 활용하여 자연어 생성 모델 기반 긍정 문구를 사용자 맞춤형으로 출력.</p>
</li>
<li><p>사용자 입력을 분석하고, API 응답을 가공하여 UI에 전달하는 구조 설계.</p>
</li>
<li><p>협업 도구(GitHub, Notion, Figma, Jira)로 프론트/백엔드/기획 파트를 분업.</p>
</li>
<li><p>짧은 개발 시간 제약 → 최소 기능(MVP)부터 구현 후 점진적으로 고도화.</p>
</li>
</ul>
<h3 id="💡결과-1">💡결과</h3>
<ul>
<li><p>고도화 트랙까지 진출 → 완성도 높은 MVP를 끝까지 제출.</p>
</li>
<li><p>비록 수상은 못 했지만, 실제 AI API를 서비스에 연동하고, 사용자 맞춤형 기능을 구현한 경험 확보.</p>
</li>
<li><p>클라우드 API 사용법, 짧은 일정 내 우선순위 관리, 팀 단위 협업의 중요성을 체득.</p>
</li>
</ul>