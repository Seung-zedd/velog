<h1 id="📌keep">📌Keep</h1>
<h2 id="1-개발-도구-및-문서화-효율성-향상">1. 개발 도구 및 문서화 효율성 향상</h2>
<ul>
<li><p>** Mermaid**를 이용해서 앱의 플로우차트를 쉽게 구현할 수 있었음</p>
</li>
<li><p>Swagger-ui 대신** Apidog<strong>를 이용해서 쉽게 로컬에서 OAS(Open API Specification) 방식을 활용해서 **온라인에서 누구나 RESTful API 명세 확인</strong>이 가능하였음</p>
</li>
<li><p>main 브랜치에서 <code>README.md</code>로 서비스 소개 작성 + eraser.io를 활용한 시스템 아키텍처 다이어그램 구현으로 프로젝트 파악 용이성 증대</p>
</li>
</ul>
<h2 id="2-외부-api-연동-및-ai-도구-활용-성공">2. 외부 API 연동 및 AI 도구 활용 성공</h2>
<ul>
<li><p>네이버 클로바 API처럼 오픈소스 API와 서버를 연동하는게 별로 어렵지 않았음 (자체 서버가 클라이언트가 되어 <strong>HTTP 헤더 포맷</strong>만 맞추면 됨)</p>
</li>
<li><p><strong>Claude Code Pro 구독</strong>으로 JSON 파싱 등 복잡한 로직 구현 시 생산성 300% 향상</p>
<ul>
<li>⚠️단, 무지성 적용보다는 아키텍처 구조와 데이터 흐름 파악 후 코드 리딩이 필요함</li>
</ul>
</li>
</ul>
<h2 id="3-프로젝트-관리-및-기술-스택-확장">3. 프로젝트 관리 및 기술 스택 확장</h2>
<ul>
<li><p>Jira 첫 사용으로 기획자의 GitHub 티켓 연동 덕분에 티켓ID 기반 브랜치 생성으로 작업 트래킹 가능 (gitflow 전략)</p>
</li>
<li><p>MySQL에서 PostgreSQL로 확장했지만 JPA Hibernate의 Dialect 자동 변환으로 별다른 문제 없음</p>
</li>
</ul>
<h1 id="🚩problem">🚩Problem</h1>
<h2 id="1-oauth-20-소셜-로그인-구현의-높은-러닝커브">1. OAuth 2.0 소셜 로그인 구현의 높은 러닝커브</h2>
<ul>
<li>카카오 소셜 로그인 첫 구현으로 <code>4-5일</code> 소요<ul>
<li>But, JWT 기반 자체 토큰 시스템 구현 경험이 있어 다음번엔 <strong>커밋 내역 참고</strong>로 구현 시간 대폭 단축 가능</li>
</ul>
</li>
</ul>
<h2 id="2-비개발자-직군과의-커뮤니케이션-부족">2. 비개발자 직군과의 커뮤니케이션 부족</h2>
<ul>
<li>각자 이해하는 바가 달라** 정기 미팅 필요성**을 간과</li>
</ul>
<ul>
<li><p><strong>기술적 용어</strong>를 비개발자가 이해하기 쉽게 설명하는 스킬 부족</p>
</li>
<li><p>요구사항 정의서 기반 로직 구현에만 몰두해 의사소통 소홀</p>
</li>
</ul>
<h2 id="3-배포-우선순위-설정-실패-☠️가장-치명적">3. 배포 우선순위 설정 실패 (☠️가장 치명적)</h2>
<ul>
<li>서비스 기능 구현에만 급급해 <strong>배포 난이도</strong>를 사전 파악하지 못함<ul>
<li>결국 로컬에서만 앱 구동 가능한 상태로 해커톤 종료</li>
</ul>
</li>
</ul>
<h2 id="4-개발-일정-산정-능력-부족">4. 개발 일정 산정 능력 부족</h2>
<ul>
<li>개발 경험 부족으로 <strong>WBS(Work Breakdown Structure)</strong> 작성 불가<ul>
<li>특정 서비스 기능 구현 소요 시간에 대한 추정 역량 부족</li>
</ul>
</li>
</ul>
<h1 id="👊try">👊Try</h1>
<h2 id="1-프로젝트-시작-전-배포-환경-우선-구축">1. 프로젝트 시작 전 배포 환경 우선 구축</h2>
<ul>
<li><p>첫날에 간단한 &quot;Hello World&quot; API를 실제 배포 환경에 올려보고 CI/CD 파이프라인 구축
👉이건 서버 레포를 만들고 init commit을 한 다음에, 바로 실행해보면 좋을 것 같다.</p>
</li>
<li><p>Railway, Vercel, NCP 등 배포 옵션들을 미리 테스트해보고 프로젝트에 적합한 플랫폼 선정
👉프로젝트의 볼륨을 생각해서 배포 플랫폼을 선정하라는 의미</p>
</li>
</ul>
<h2 id="2-비개발자와의-효과적인-소통-방법-개발">2. 비개발자와의 효과적인 소통 방법 개발</h2>
<ul>
<li><p>🔖기술 용어 사전 미리 작성해서 공유 (예: API → &quot;앱이 서버와 데이터를 주고받는 통로&quot;)
👉<strong>노션</strong>에 간단하게 사전 형태로 작성하면 비개발자들이 구글링하는데에 대한 시간을 단축시킬 수 있음</p>
</li>
<li><p>🖥️매일 15분 스탠드업 미팅으로 진행상황 공유 및 이슈 조기 발견
👉특히나 이번 해커톤을 진행하면서 서버 API만 구현하면 다 될 줄 알았는데, <strong><span style="color: red;">브라우저에서의 API 통신은 예상치 못한 버그 및 에러가 발생</strong></span>할 수 있으니, 
🏭백엔드에서의 서비스 기능 구현 성공 -&gt; 프론트엔드와의 미팅 -&gt; API 통합 테스트의 파이프라인을 구축할 것</p>
</li>
<li><p>💻화면 공유를 통한 실시간 기능 시연으로 구현 결과물 명확히 전달</p>
</li>
</ul>
<h2 id="3-개발-일정-산정-스킬-향상">3. 개발 일정 산정 스킬 향상</h2>
<ul>
<li><p>비슷한 기능들의 <strong>과거 커밋 이력을 참고</strong>해서 <strong>구현 시간 추정</strong> (OAuth 구현 4-5일 → 다음엔 2-3일 목표)</p>
</li>
<li><p>각 기능을 더 작은 단위로 쪼개서 일별 태스크로 관리 (Jira 티켓 세분화)
👉하나의 서비스 티켓이라도, <strong>더 작은 단위(모듈)</strong>로 쪼개면 버그가 발생해도 롤백 비용을 줄일 수 있으므로 세분화를 습관으로 만들 것!</p>
</li>
<li><p>예상 시간의 1.5배를 버퍼로 잡아서 리스크 관리</p>
</li>
</ul>
<h2 id="4-claude-code-pro-활용법-고도화">4. Claude Code Pro 활용법 고도화</h2>
<ul>
<li><p>단순 코드 생성이 아닌 아키텍처 설계 리뷰 요청으로 활용 범위 확대
👉이건 coderabbitAI로 대체 가능하니 지금 그대로 유지하자. (단, 데드라인에 쫓겨 <strong>PR 리뷰</strong>를 제대로 하지 못하면 코드 품질이 떨어지므로 주의할 것!)</p>
</li>
<li><p>생성된 코드의 보안성, 성능, 유지보수성 검토 프로세스 수립
👉이것도 마찬가지로 coderabbitAI가 대신 해줌</p>
</li>
</ul>
<h2 id="5-다음-해커톤-준비를-위한-템플릿-구축">5. 다음 해커톤 준비를 위한 템플릿 구축</h2>
<ul>
<li>소셜 로그인, JWT, 기본 CRUD API가 포함된 Spring Boot 보일러플레이트 미리 준비
⭐깃허브에 <code>spring-boot-starter-auth</code>라는 레포를 만든 후 기존에 성공적으로 구현했던 Auth 로직을 여기에 때려박으면 되는데, 그 과정은 다음과 같다:</li>
</ul>
<ol>
<li><p>레포 구조 설정(패키지를 의미)</p>
</li>
<li><p>핵심 설정 파일 작성(build.gradle)</p>
</li>
<li><p>사용법: 내 레포를 JAR파일로 만들어서 다음과 같이 디펜던시에 추가하면 된다.</p>
<pre><code class="language-java"># 1. jar 파일 생성
./gradlew build
</code></pre>
</li>
</ol>
<h1 id="2-생성된-jar을-새-프로젝트의-libs-폴더에-복사">2. 생성된 jar을 새 프로젝트의 libs/ 폴더에 복사</h1>
<p>cp build/libs/spring-boot-starter-auth-1.0.0.jar ../new-project/libs/</p>
<h1 id="3-새-프로젝트에서-의존성-추가">3. 새 프로젝트에서 의존성 추가</h1>
<p>dependencies {
    implementation files('libs/spring-boot-starter-auth-1.0.0.jar')
}</p>
<pre><code>
4. 새 프로젝트에서 사용
```java
@SpringBootApplication
@EnableAuth  // 한 줄 추가로 인증 시스템 활성화
public class HackathonApplication {
    public static void main(String[] args) {
        SpringApplication.run(HackathonApplication.class, args);
    }
}</code></pre><ul>
<li>Mermaid 다이어그램과 Apidog 설정이 포함된 프로젝트 템플릿 제작
👉이건 플랫폼에서 알아서 자동화가 되기 때문에 패스</li>
</ul>