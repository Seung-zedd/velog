<h1 id="code-convention">Code convention</h1>
<ol>
<li><p>브랜치 기능 구현이 완료되면 codeRabbit한테 코드 리뷰를 받기 위해 base branch는 dev으로 해서 PR을 생성한다.</p>
</li>
<li><p>피드백을 바탕으로 코드 리팩토링이 완료되면 다시 한번 PR을 생성해서 최종적으로 리뷰를 받는다.</p>
</li>
<li><p>모든 리뷰가 통과가 되면 최종적으로 dev에 merge PR request를 한다.</p>
</li>
</ol>
<h2 id="🔧프로젝트-기술-스택">🔧프로젝트 기술 스택</h2>
<p>Spring Boot 3.x, Java 21, JPA, PostgreSQL</p>
<h2 id="📚api-문서화-규칙">📚API 문서화 규칙</h2>
<p><a href="http://localhost:8080/v3/api-docs">http://localhost:8080/v3/api-docs</a> 에서 json 파일을 직접 복사해서 txt파일로 저장하고, Apidog로 직접 업로드합니다.(github-action으로 해결하려고 했으나 계속 실패해서 그냥 지웠음.)</p>
<h2 id="🚀-팀의-git-워크플로우-my-team-git-workflow">🚀 팀의 Git 워크플로우 (My Team Git Workflow)</h2>
<p>이 프로젝트는 main 브랜치와 dev 브랜치를 중심으로, '기능 기반 브랜치 전략(Feature Branch Strategy)'을 사용합니다.</p>
<ul>
<li><p>🚢main: 배포 가능한 안정적인 버전의 코드만 존재하는 브랜치.</p>
</li>
<li><p>🔧dev: 개발의 중심이 되는 브랜치. 모든 기능은 이곳으로 합쳐져 통합 테스트를 거칩니다.</p>
</li>
<li><p>✨feature/*: 하나의 기능을 개발하기 위한 브랜치. dev에서 시작하여 dev으로 합쳐집니다.</p>
</li>
</ul>
<ol>
<li>기능 개발 시작 (Starting a New Feature): 새로운 기능 개발은 항상 최신 상태의 dev 브랜치에서 시작합니다.<pre><code class="language-txt"># 1. 메인 개발 브랜치(dev)로 이동합니다.
git checkout dev
</code></pre>
</li>
</ol>
<h1 id="2-원격-저장소의-최신-내용을-가져와-로컬-dev-브랜치를-업데이트합니다">2. 원격 저장소의 최신 내용을 가져와 로컬 dev 브랜치를 업데이트합니다.</h1>
<p>git pull origin dev</p>
<h1 id="3-새로운-기능-브랜치를-생성하고-그-브랜치로-이동합니다">3. 새로운 기능 브랜치를 생성하고 그 브랜치로 이동합니다.</h1>
<h1 id="브랜치-이름-규칙-feature기능-이름-예-featurelogin">브랜치 이름 규칙: feature/기능-이름 (예: feature/login)</h1>
<p>git checkout -b feature/새로운-기능</p>
<pre><code>
📢원격에 푸시하기 전에 로컬에서 구현한 기능들을 모두 테스트하는 것이 권장됩니다.(단위테스트 및 포스트맨을 통한 API 테스트 포함)

2. 기능 개발 및 원격에 올리기 (Develop &amp; Push): 현재 브랜치가 feature/새로운-기능 인 것을 항상 확인하며 코딩을 진행합니다. 작업은 작은 단위로 나누어 의미 있는 메시지와 함께 자주 커밋하는 것이 좋습니다.

```txt
# 1. 수정한 파일들을 스테이징합니다.
git add .

# 2. 커밋 메시지를 작성합니다.
git commit -m &quot;feat: 로그인 기능 기본 골격 추가&quot;

# 3. 처음 원격에 올릴 때, -u 옵션으로 로컬 브랜치와 원격 브랜치를 연결합니다.
git push -u origin feature/새로운-기능</code></pre><ol start="3">
<li>Pull Request (PR) 생성 및 코드 리뷰: 기능 개발이 완료되면, GitHub에서 <strong>Pull Request(PR)</strong>를 생성합니다.</li>
</ol>
<p>base branch: dev</p>
<p>compare branch: feature/새로운-기능</p>
<p>PR을 생성하면 CodeRabbit 같은 자동 코드 리뷰 도구가 피드백을 주거나, 동료 개발자가 코드 리뷰를 진행합니다. 피드백을 받으면, 로컬 feature/새로운-기능 브랜치에서 코드를 수정하고 다시 push하여 PR에 반영합니다.</p>
<p>🔃이 과정은 최종 승인(Approve)을 받을 때까지 반복됩니다.</p>
<p>병합 및 뒷정리 (Merge &amp; Clean up): 코드 리뷰가 끝나고 PR이 최종 승인되면, GitHub에서 Merge 버튼을 눌러 feature/새로운-기능 브랜치를 dev 브랜치에 합칩니다.
병합이 완료되면, 제 역할을 다한 기능 브랜치는 깔끔하게 삭제합니다.</p>
<pre><code class="language-txt"># 1. 다시 메인 개발 브랜치(dev)로 돌아옵니다.
git checkout dev

# 2. 방금 병합된 최신 내용을 원격 저장소에서 다시 가져옵니다.
git pull origin dev

# 3. 이제 필요 없어진 로컬 기능 브랜치를 삭제합니다.
git branch -d feature/새로운-기능

# 4. (선택) GitHub에 있는 원격 기능 브랜치도 삭제합니다.
git push origin --delete feature/새로운-기능
(보통 GitHub에서 Merge 후 바로 브랜치를 삭제하는 버튼이 나타납니다.)</code></pre>
<ol start="5">
<li>릴리즈 및 배포 (Release &amp; Deploy) dev 브랜치에 이번 버전에 포함될 모든 기능들이 통합되고, 충분한 테스트를 통해 안정성이 확보되었다고 판단되면 정식 버전을 배포합니다.</li>
</ol>
<pre><code class="language-txt"># 1. 배포의 기준이 될 main 브랜치로 이동합니다.
git checkout main

# 2. main 브랜치를 최신 상태로 업데이트합니다.
git pull origin main

# 3. dev 브랜치의 모든 내용을 main 브랜치로 병합합니다.
git merge dev

# 4. main 브랜치에 병합된 내용을 원격 저장소에 푸시합니다.
git push origin main

# 5. 역사적인 기록을 위해 버전을 명시하는 태그(Tag)를 생성하고, 태그도 푸시합니다.
git tag -a v1.0.0 -m &quot;Release version 1.0.0&quot;
git push origin v1.0.0</code></pre>
<h1 id="💬-커밋-메시지-컨벤션-commit-message-convention">💬 커밋 메시지 컨벤션 (Commit Message Convention)</h1>
<p>우리 프로젝트는 가독성 높은 커밋 히스토리를 위해 Gitmoji와 Conventional Commits를 조합한 컨벤션을 따릅니다.</p>
<h2 id="기본-형식">기본 형식</h2>
<pre><code class="language-txt">[Gitmoji] [타입]([스코프]): [제목]</code></pre>
<ul>
<li><p>Gitmoji: 커밋의 의도를 나타내는 대표 이모지</p>
</li>
<li><p>타입: Conventional Commits 타입 (feat, fix 등)</p>
</li>
<li><p>스코프 (선택 사항): 변경된 부분의 범위 (Jira 이슈 키 등)</p>
</li>
<li><p>제목: 커밋 내용에 대한 간결한 설명</p>
</li>
</ul>
<h2 id="주요-gitmoji-및-타입">주요 Gitmoji 및 타입</h2>
<table>
<thead>
<tr>
<th>Gitmoji</th>
<th>타입</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>✨ <code>:sparkles:</code></td>
<td>feat</td>
<td>새로운 기능을 추가할 때</td>
</tr>
<tr>
<td>🐛 <code>:bug:</code></td>
<td>fix</td>
<td>버그를 수정할 때</td>
</tr>
<tr>
<td>📚 <code>:books:</code></td>
<td>docs</td>
<td>README.md 등 문서를 수정할 때</td>
</tr>
<tr>
<td>🎨 <code>:art:</code></td>
<td>style</td>
<td>코드 포맷팅, 세미콜론 등 스타일 수정 (로직 변경 없음)</td>
</tr>
<tr>
<td>♻️ <code>:recycle:</code></td>
<td>refactor</td>
<td>코드 리팩토링 (기능 변경 없이 코드 구조 개선)</td>
</tr>
<tr>
<td>🚀 <code>:rocket:</code></td>
<td>deploy</td>
<td>빌드, 배포 관련 작업을 할 때</td>
</tr>
<tr>
<td>⚙️ <code>:gear:</code></td>
<td>chore</td>
<td>빌드 파일, 설정 파일 등 기타 자잘한 수정</td>
</tr>
<tr>
<td>✅ <code>:white_check_mark:</code></td>
<td>test</td>
<td>테스트 코드를 추가하거나 수정할 때</td>
</tr>
<tr>
<td>🚑 <code>:ambulance:</code></td>
<td>hotfix</td>
<td>운영 환경의 긴급한 버그를 수정할 때</td>
</tr>
</tbody></table>
<p>※books 부분은 깃허브에서 바로 수정 가능하니 무시하셔도 됩니다.</p>
<h2 id="커밋-메시지-예시">커밋 메시지 예시</h2>
<h3 id="새로운-기능-추가-jira-이슈-chec-58">새로운 기능 추가 (Jira 이슈 CHEC-58)</h3>
<p>git commit -m &quot;✨ feat(CHEC-58): 카카오 소G셜 로그인 기능 추가&quot;</p>
<h3 id="버그-수정-jira-이슈-chec-61">버그 수정 (Jira 이슈 CHEC-61)</h3>
<p>git commit -m &quot;🐛 fix(CHEC-61): 비회원 확언 조회 시 NPE 버그 수정&quot;</p>
<h3 id="문서-수정">문서 수정</h3>
<p>git commit -m &quot;📚 docs: README.md에 ERD 이미지 및 커밋 컨벤션 추가&quot;</p>
<h3 id="코드-리팩토링">코드 리팩토링</h3>
<p>git commit -m &quot;♻️ refactor: AuthService의 중복 코드 메소드로 분리&quot;</p>
<p>이 과정을 통해 main 브랜치는 항상 안정적이고 배포 가능한 상태를 유지하게 됩니다.</p>