<p>📢본 예제의 저작권은 <a href="http://www.kocw.net/home/cview.do?cid=9c591659f017851e">http://www.kocw.net/home/cview.do?cid=9c591659f017851e</a> 에 있으므로, 무단 복제 및 배포를 금합니다.</p>
<h1 id="요구사항">요구사항</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/30f73724-ab3c-46e6-b4b1-d72d7d6cac4c/image.png" />
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/bbec87f1-a1df-4521-b18f-f447394eb52b/image.png" /></p>
<p>먼저 요구사항을 분석해서 아래와 같은 절차를 따라서 데이터베이스 설계를 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/b1b64058-c483-447d-9323-8146b53c0983/image.png" /></p>
<p>💡간단하게 개체 및 속성을 파악하기 위해, 간략화된 릴레이션을 먼저 작성한다.</p>
<ol>
<li>Department (name(unique), number(PK), a set of locations, manager, a set of employees, a set of projects)</li>
<li>Project (name(unique), number(PK), a single location, a set of employees, a single department)</li>
<li>Employee (name, SSN(PK), address, salary, sex, birthdate, a direct supervisor, a set of dependents(뜻: 부양가족), a single department, a set of projects)</li>
<li>Dependent (fist name(PK), sex, birthdate) </li>
</ol>
<h1 id="1-erd-그리기">1. ERD 그리기</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/8c7dfc66-e16f-4eb2-9604-2522d7089104/image.jpg" /></p>
<h2 id="🤦♂️놓친-부분">🤦‍♂️놓친 부분</h2>
<ol>
<li>💡<strong>관계(Relation)은 개체(Entity)간의 매핑 카디널리티</strong>를 가진다.</li>
</ol>
<ul>
<li>매핑 카디널리티는 대칭성을 가지기 때문에 한 쪽이 <code>a single of</code>이면 반대쪽은 반드시 <code>a set of</code>의 관계를 가진다.(정 못믿겠으면 요구사항을 보고 직접 ERD를 그려볼 것)</li>
</ul>
<ol start="2">
<li><p><code>a set of locations</code>를 놓쳐서 <strong>다중값</strong> 설정을 안했음.
예) 마켓팅팀이 1층 또는 2층 건물에 있을 수 있음</p>
</li>
<li><p>manager를 Employee의 속성으로 두었는데, 이것을 Employee(1) - manage (manager) - Department(1)의 관계를 가지는 것으로 변경해줘야 함 </p>
</li>
<li><p>Employee의<code>a direct supervisor</code>는 Employee(N) - supervise - Employee(1)의 관계를 가짐</p>
</li>
<li><p>Employee(M) - assign (hours) - Project(N)의 관계를 갖는 이유는 </p>
<ol>
<li><code>has a set of employees who work on it</code></li>
<li><code>works some number of hours per week **on each of a set of projects**</code>
요구사항 때문
💡결국, <strong>a single이냐 a set of이냐에 따라 매핑 카디널리티가 달라진다</strong>는 것을 알 수 있다.</li>
</ol>
</li>
<li><p>Employee(1) - depend - Dependent(N)의 관계와 <code>is related to a particular employee in a particular way (e.g., child, spouse, pet)</code>에 의해 Dependent 속성에 relationship을 추가해줘야함.</p>
</li>
<li><p><code>and who assumed that position on some date</code>는 Manages 관계의 Start_date 속성으로 들어감</p>
</li>
</ol>
<hr />
<p>👩‍🏫실제 답은 아래와 같습니다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/6326bcc4-3931-4ee3-9c16-be23930e76d8/image.png" /></p>