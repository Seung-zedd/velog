<h1 id="3-mysql로-데이터베이스-구축">3. mySQL로 데이터베이스 구축</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/0b4b0a38-5742-40a1-b62c-bff20c050689/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/abdac4d9-6e36-442c-81aa-7a0813d7edf3/image.png" /></p>
<h2 id="create문으로-먼저-테이블-만들기">CREATE문으로 먼저 테이블 만들기</h2>
<p>📢이전 예제와는 다르게 employee 테이블에서 department의 외래키를 참조해야 하기 때문에 오류가 발생한다. 따라서 add FOREIGN KEY들은 모두 alter table에서 진행하도록 한다.</p>
<pre><code class="language-mysql">

drop table if exists employee cascade;
drop table if exists department cascade;
drop table if exists dept_locations cascade;
drop table if exists works_on cascade;
drop table if exists project cascade;
drop table if exists dependent cascade;

CREATE TABLE employee (

    first_name VARCHAR(50) not null, 
    middle_initial char(1) not null,
    last_name VARCHAR(50) not null,
    ssn char(10) primary key not null,
    birth_date Date,
    address varchar(50) not null,
    sex char(1) not null, # due to check condition
    salary int,
    super_ssn char(10) not null,
    dno int
);

create table department (
    dname varchar(50) not null,
    dno int primary key not null,
    mgr_ssn char(10) not null,
    mgr_start_date date
);

create table dept_locations (
    dno int not null,
    dlocation varchar(50) not null,
    primary key (dno, dlocation)
);

create table works_on (
    essn char(10) not null,
    pno int not null,
    hours float,
    primary key (essn, pno)
);

create table project (
    pname varchar(50) not null,
    pno int primary key not null,
    plocation varchar(50) not null,
    dno int not null
);

create table dependent (
    essn char(10) not null,
    dependent_name varchar(50) not null,
    sex char(1) not null,
    birth_date date,
    relationship varchar(50) not null,
    primary key (essn, dependent_name)
);
</code></pre>
<p>📢GUI로 테이블을 만드는 것과 쿼리로 직접 테이블을 만드는 것은 서로 charset이 다르기 때문에 incompatible 에러가 발생하므로 주의할 것!</p>
<h2 id="insert문으로-인스턴스-값-추가하기">INSERT문으로 인스턴스 값 추가하기</h2>
<p>📢ALTER문으로 외래키 제약조건을 먼저 추가하면 Cannot add or update a child row: a foreign key constraint fails라는 에러가 발생하는데, 이유는 외래키를 가진 employee 테이블에서 기본키를 가진 department 테이블에 값이 존재하지 않기 때문이다.
👉따라서, <strong>제약조건을 추가하기 전에 모든 테이블에 인스턴스 값을 먼저 추가</strong>하도록 하자.</p>
<p>📢워크벤치 그리드 gui에서 값을 직접 넣을 때 값을 넣지 않더라도, &quot;&quot;로 인식을 하기 때문에 NULL이 포함된 레코드는 쿼리를 통해서 넣을 것!</p>
<h2 id="alter문으로-제약조건-추가하기">ALTER문으로 제약조건 추가하기</h2>
<pre><code class="language-mysql">alter table employee
add FOREIGN KEY(dno) REFERENCES department(dno),
add FOREIGN KEY(super_ssn) REFERENCES employee(ssn),
add CONSTRAINT chk_sex CHECK (sex = 'F' or sex = 'M');

alter table department
add foreign key(mgr_ssn) references employee(ssn);

alter table dept_locations
add FOREIGN KEY(dno) REFERENCES department(dno);

alter table works_on
add FOREIGN KEY(essn) REFERENCES employee(ssn),
add FOREIGN KEY(pno) REFERENCES project(pno);

alter table project
add FOREIGN KEY(dno) REFERENCES department(dno);

alter table dependent
add FOREIGN KEY(essn) REFERENCES employee(ssn),
add CONSTRAINT chk_dependent_sex CHECK (sex = 'F' or sex = 'M');</code></pre>
<h1 id="4-질의에-따른-sql-쿼리-작성하기">4. 질의에 따른 sql 쿼리 작성하기</h1>
<p>[질의1] Research 부서에서 일하는 모든 사원의 이름과 주소를 검색하라.</p>
<pre><code class="language-mysql">select e.first_name, e.middle_initial, e.last_name, e.address
from employee e
inner join department d
on e.dno = d.dno
where d.dname = 'Research';</code></pre>
<p>💡참고로 교수님은 암시적 조인을 사용했는데, 결과값은 같으므로 나의 명시적 조인을 작성하였다.</p>
<p>[질의2] Stafford에 위치한 모든 프로젝트에 대하여 프로젝트 번호와 관리부서 번호, 부서 관리자의 성, 주소, 생년월일을 나열하라.</p>
<p>자, 질의문이 상당히 복잡한데, 우선 하나의 쿼리로 먼저 작성해보자.</p>
<ol>
<li><em>Stafford에 위치한 모든 프로젝트에 대하여 프로젝트 번호와 관리부서 번호</em>: <code>select p.pno, p,dno from project p where p.plocation = 'Stafford';</code></li>
<li><em>부서 관리자의 성, 주소, 생년월일</em>: 부서 관리자의 성, 주소, 생년월일은 employee 테이블에 있으니까 project 테이블과 조인을 해야겠지? 그런데, 부서 관리자는 department의 테이블에 있네?<ul>
<li>p.dno 외래키를 가지고 있는 project 테이블에서 department 테이블의 d.dno를 참조한다. =&gt; <code>inner join department d on p.dno = d.dno</code></li>
<li>d.mgr_ssn 외래키를 갖고 있는 department 테이블에서 employee 테이블의 e.super_ssn을 참조한다 =&gt; <code>inner join employee e on d.mgr_ssn = e.super_ssn</code></li>
</ul>
</li>
</ol>
<p>위의 과정들을 합치면 아래와 같다👇</p>
<pre><code class="language-mysql">select p.pno, p.dno, e.last_name, e.address, e.birth_date from project p
inner join department d on p.dno = d.dno # controls
inner join employee e on d.mgr_ssn = e.super_ssn # manages
where p.plocation = 'Stafford';</code></pre>
<p>💡참고로, 여러 개의 테이블을 조인하고 싶으면 그냥 간단하게 <code>inner join..on</code>을 추가하면 된다.</p>
<p>[질의3] 이름이 'John B.Smith'인 사원의 생년월일과 주소를 검색하라.</p>
<pre><code class="language-mysql">select birth_date, address 
from employee
where first_name = 'John' and middle_initial = 'B' and last_name = 'Smith';</code></pre>
<p>[질의4] 주소가 'Houston, Texas'인 모든 사원의 이름을 검색하라.</p>
<pre><code class="language-mysql">select first_name, middle_initial, last_name
from employee
where address like '%Houston, TX%';</code></pre>
<ul>
<li>특정 문자열을 포함할 때는 <code>like '%str%'</code>을 사용한다.</li>
</ul>
<p>[질의5] 1950년대에 태어난 모든 사원의 이름을 검색하라.</p>
<pre><code class="language-mysql">select first_name, middle_initial, last_name
from employee
where birth_date between '1950-01-01' and '1959-12-31';</code></pre>
<ul>
<li>특정 범위 내에서 검색하고 싶으면 <code>between..and</code> 문법을 사용하면 된다.<ul>
<li>참고로 value는 number, text 그리고 date 타입이 된다.</li>
</ul>
</li>
</ul>
<p>👩‍🏫다른 풀이</p>
<pre><code class="language-mysql">select first_name, middle_initial, last_name
from employee
where birth_date like '195%';</code></pre>
<ul>
<li><code>like '김%'</code>와 같이 김으로 시작되는 문자열을 검색할 때 쓰는데, 195로 시작되는 텍스트만 검색하면 된다.</li>
</ul>
<p>[질의6] 'ProductX' 프로젝트에 참여하는 모든 사원의 이름과 10%씩 올린 급여를 구하여라.</p>
<pre><code class="language-mysql">select e.first_name, e.middle_initial, e.last_name, salary * 1.1 as increased_salary
from employee e
inner join project p on e.dno = p.dno
where p.pname = 'ProductX';</code></pre>
<p>😓앗! project 테이블을 다시 살펴보니, dno가 5인 다른 프로젝트 이름들도 존재하기 때문에 이 쿼리는 잘못되었다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/f0480985-5ac8-49ca-8d7a-9516164119b1/image.png" /></p>
<p>👩‍🏫 올바른 풀이</p>
<pre><code class="language-mysql">select e.first_name, e.middle_initial, e.last_name, salary * 1.1 as increased_salary
from employee e
inner join works_on w on e.ssn = w.essn
inner join project p on w.pno = p.pno
where p.pname = 'ProductX';</code></pre>
<p>💡works_on은 연관된 개체가 다대다 관계를 가지기 때문에 따로 테이블을 생성한 것인데, E-R 관계가 잘 안보이면 ERD를 다시 볼 것!</p>
<p>[질의7] 급여가 30,000달러에서 40,000달러 사이에 있는 5번 부서의 모든 사원의 이름을 검색하라.</p>
<pre><code class="language-mysql">select e.first_name, e.middle_initial, e.last_name
from employee e
where e.dno = 5 and (salary &gt;= 30000 and salary &lt;= 40000);</code></pre>
<hr />
<p>⭐⭐⭐⭐⭐[질의8] 부서이름과 사원 이름 및 각 사원이 근무하는 프로젝트 이름들의 리스트를 검색하는데, 부서이름 순서대로, 그리고 각 부서 내에서는 사원의 성과 이름의 알파벳 순서대로 구하라.</p>
<p>🤔SQL 강의록에서는 '각<del>', '별</del>'은 group by로 처리하라고 했는데, 교수님은 다음과 같은 쿼리를 작성하였다.</p>
<pre><code class="language-mysql">select d.dname, e.first_name, e.last_name, p.pname
from department d
inner join employee e on d.dno = e.dno
inner join works_on w on e.ssn = w.essn
inner join project p on w.pno = p.pno
order by d.dname, e.last_name, e.first_name;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/7f04812e-8132-4d8d-a18d-17528bd59010/image.png" /></p>
<p>😓이 문제를 어렵게 생각한 이유는, <strong>먼저 테이블(개체 및 관계)를 생각</strong>하지 않고 '각~'이라는 키워드에 꽂혀서 그룹핑을 먼저 생각했기 때문이다.</p>
<h2 id="⚙️-해결-과정">⚙️ 해결 과정</h2>
<ol>
<li><p><strong>해당 속성이 있는 테이블</strong>들을 모두 찾기: 
부서 이름이 있는 부서 테이블, 사원 이름이 있는 사원 테이블, 사원이 근무하는 근무 테이블, 그리고 프로젝트 이름 이 있는 프로젝트 테이블을 먼저 찾아내야 한다.
<code>from department d, employee e, works_on w, project p</code></p>
</li>
<li><p>테이블이 여러 개가 있기 때문에** ERD<strong>를 다시 보고 관계가 있는 **외래키</strong>를 찾아서 <strong>테이블끼리 조인</strong>을 한다.</p>
</li>
</ol>
<ul>
<li><code>inner join employee e on d.dno = e.dno</code>  <ul>
<li>Department와 Employee 간의 works_for 관계(외래키는 dno)</li>
</ul>
</li>
<li><code>inner join works_on w on e.ssn = w.essn</code> <ul>
<li>Employee와 Works_on 간의 관계(외래키는 ssn이고, Employee와 Project는 다대다 관계라 별도의 릴레이션인 Works_on을 통해서 접근해야함)</li>
</ul>
</li>
<li><code>inner join project p on w.pno = p.pno</code> <ul>
<li>Works_on과 Project 간의 관계(외래키는 pno)</li>
</ul>
</li>
</ul>
<p>💡테이블이 N개라면, 당연히 조인 연산자는 (N-1)개가 필요</p>
<p>📝또한, group by는 <strong>같은 값을 가진 row끼리 하나의 그룹으로 뭉쳐주는 키워드</strong>이기 때문에 이 데이터들을 묶어서 어디에 활용할 것인가를 생각해야 한다.(e.g. 집계함수: COUNT(), AVG(), SUM(), MIN(), MAX())</p>
<hr />
<p>[질의9] 상사가 없는 모든 사원의 이름을 검색하라.</p>
<pre><code class="language-mysql">select first_name, middle_initial, last_name
from employee
where super_ssn is null;</code></pre>
<hr />
<p>⭐⭐⭐⭐⭐[질의10] 평균봉급이 가장 높은 부서 번호를 검색하라.</p>
<ol>
<li><p>부서별 평균봉급을 먼저 검색하기 위해서 먼저 dno를 그룹핑한다.</p>
<pre><code class="language-mysql">select avg(salary), dno
from employee
group by dno;</code></pre>
<p>💡select절에 <code>avg()</code>와 같은 집계함수를 사용했기 때문에 salary 속성은 group by에 추가하지 않아도 된다.</p>
</li>
<li><p>1에서 가장 높은 부서 번호를 검색하기 위해 max()를 사용해야 하는데 max(avg())와 같이 사용할 수 없어서 하위 쿼리를 작성한다.</p>
</li>
</ol>
<pre><code class="language-mysql">select avg(salary), dno
from employee
where salary = (select max(salary) from employee)
group by dno;</code></pre>
<p>😣다시 생각해보니, salary 속성에서 가장 높은 값이 55000이기 때문에 where절은 잘못되었다. 따라서 모범 답안에 맞춰서 교정하도록 한다.</p>
<ul>
<li>심지어, 질의문에서는 &quot;평균봉급이 가장 높은 <strong>부서 번호를 검색</strong>하라.&quot;라고 되어있는데 쓸데없이 salary 속성도 검색하였다.</li>
</ul>
<p>👩‍🏫 올바른 풀이</p>
<pre><code class="language-mysql">select dno
from employee
group by dno having avg(salary) &gt;= all (
    select avg(salary) 
    from employee
    group by dno
);</code></pre>
<h2 id="🤔group-by를-통해서-최댓값을-리턴하려면-어떻게-해야할까">🤔group by를 통해서 최댓값을 리턴하려면 어떻게 해야할까?</h2>
<p>💡ALL: <strong>부속 질의문의 결과 값 <span style="color: blue;">모두</span>와 비교한 결과가 참</strong>이면 검색 조건을 만족(비교 연산자와 함께 사용)
이 키워드를 이용해서 위의 복잡한 쿼리를 풀어보면 다음과 같다:</p>
<ol>
<li><p>외부쿼리:</p>
<pre><code class="language-mysql">select dno, avg(salary)
from employee
group by dno;</code></pre>
<p>결과값: {1: 55000, 4: 31000, 5: 33250}
👉편의를 위해 딕셔너리 형태로 작성하였음.</p>
</li>
<li><p>서브쿼리:</p>
<pre><code class="language-mysql">select avg(salary)
from employee
group by dno;</code></pre>
<p>결과값: <code>[55000, 31000, 33250]</code> 마찬가지로 동일하다.</p>
</li>
</ol>
<p>그런데, ALL 키워드를 다시 한번 보면 서브쿼리의 결과 값 모두와 비교한 결과가 참일 때만 검색 조건을 만족한다고 하였다.</p>
<p>외부 쿼리값이 55000인 경우:
55000 ≥ 55000 → 참
55000 ≥ 31000 → 참
55000 ≥ 33250 → 참
→ 조건을 모두 만족한다.</p>
<p>반면 외부 쿼리값이 31000이나 33250인 경우는,
31000나 33250은 내부 쿼리값인 55000과 비교했을 때 거짓이 되어 검색 조건을 만족하지 않는다.
📝따라서, 최종적으로 avg(salary)의 최댓값인 55000을 가진 dno를 검색한다.</p>
<hr />
<p>[질의11] 'Research' 부서에 근무하는 모든 사원의 급여의 합, 최고 급여, 최소 급여, 평균 급여를 구하라.</p>
<pre><code class="language-mysql">SELECT sum(salary) AS total_salary, max(salary) AS max_salary, min(salary) AS min_salary, AVG(salary) AS avg_salary
FROM employee e
INNER JOIN department d ON e.dno = d.dno
WHERE d.dname = 'Research';</code></pre>
<hr />
<p>[질의12] 'Research' 부서에서 근무하는 총 사원수를 검색하라.</p>
<pre><code class="language-mysql">SELECT count(*) AS num_of_employee FROM employee e
INNER JOIN department d ON e.dno = d.dno
WHERE d.dname = 'Research';</code></pre>
<p>💡테이블의 &quot;모든 행&quot;을 카운트하기 때문에 중복 여부 상관 x</p>
<hr />
<p>[질의13] 각 부서에 대해서 부서번호, 부서에 속한 사원들의 수, 각 부서에 속한 사원들의 평균 급여를 구하라.</p>
<pre><code class="language-mysql">select dno, count(*) as num_of_employee, avg(salary) as avg_salary  
from employee
group by dno;</code></pre>
<hr />
<p>[질의14] 두 명 이상의 사원이 근무하는 각 프로젝트에 대해서 프로젝트 번호, 프로젝트 이름, 프로젝트에서 근무하는 사원의 수를 검색하라.</p>
<h2 id="⚙️-해결-과정-1">⚙️ 해결 과정</h2>
<ol>
<li><p>먼저, 두 명 이상의 사원이 근무하는 프로젝트를 알아내기 위해 group by를 사용하여 pno를 먼저 검색한다.</p>
<pre><code class="language-mysql">select pno, count(*) as num_of_employee
from works_on
group by pno having num_of_employee &gt;= 2;</code></pre>
<p>=&gt; {1: 2, 2: 3, 3: 2, 10: 3, 20: 3, 30: 3} # {pno: num_of_employee}</p>
</li>
<li><p>프로젝트 번호, 프로젝트 이름, (프로젝트에서 근무하는 사원의 수)를 검색: <code>select p.pno, p.pname from project p</code></p>
</li>
<li><p>위의 works_on 테이블과 project 테이블을 조인한다.</p>
<pre><code class="language-mysql">select w.pno, p.pname, count(*) as num_of_employee
from works_on w
inner join project p on w.pno = p.pno
group by pno having num_of_employee &gt;= 2;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/fa5fc058-ee84-434e-8cff-a16189d787ea/image.png" /></p>
</li>
</ol>
<p>💡주 테이블을 works_on으로 했기 때문에 count(*)는 works_on의 행을 대상으로 집계한다.</p>
<p>👩‍🏫 교수님이 작성한 쿼리</p>
<pre><code class="language-mysql">select p.pno, p.pname, count(*) as num_emp
from employee e, works_on w, project p
where w.essn=e.ssn and p.pno = w.pno
group by p.pno, p.pname
having count(*) &gt;= 2</code></pre>
<p>🤖employee 테이블과의 조인이 필요한 이유: 
<strong>향후 확장성</strong>을 위해 나중에 쿼리에 employee의 추가 정보를 포함하거나, 특정 조건(예: 부서, 근속기간 등)을 필터링할 필요가 생길 때, 이미 employee 테이블과 조인되어 있으면 쿼리 수정이 용이합니다.</p>
<hr />
<p>⭐⭐⭐⭐⭐⭐⭐[질의15] 2명 이상의 사원이 근무하는 각 부서에 대해서 부서 번호, 부서이름과 40,000달러가 넘는 급여를 받는 사원의 수를 검색하라.</p>
<h2 id="⚙️-해결-과정-2">⚙️ 해결 과정</h2>
<ol>
<li><p>근무하는 두명 이상의 사원을 먼저 검색한다.</p>
<pre><code class="language-mysql">select w.essn, count(*) as num_of_employee
from works_on w
group by w.essn having num_of_employee &gt;= 2;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/baa8f9f9-0603-4d0e-af09-03628b4f5e6f/image.png" /></p>
</li>
<li><p>부서 번호, 부서 이름, 40,000달러가 넘는 급여를 받는 사원을 검색</p>
<pre><code class="language-mysql">select e.dno, d.dname, ssn
from employee e
inner join department d on e.dno = d.dno
where salary &gt; 40000;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/9da7bb9f-feb6-4b8a-b88c-3f05898decd6/image.png" />
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/48e2bceb-438a-4948-88dc-934ffa4925c1/image.png" />
급여가 40000 달러 초과인 사람만 조회했으므로 검색 결과가 참이다.</p>
</li>
<li><p>위의 2가지를 짬뽕시킨다.</p>
<pre><code class="language-mysql">select e.dno, d.dname, e.salary, count(*) as num_of_employee
from employee e
inner join department d on e.dno = d.dno
inner join works_on w on e.ssn = w.essn
group by w.essn having num_of_employee &gt;= 2 and e.salary &gt; 40000;</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/5de8b127-add8-489a-bf81-c4c264206454/image.png" />
😫앗! 위의 employee 테이블에 따르면 2명 이상 근무하는 부서 중 40000달러를 초과한 사람은 43000달러를 받는 1명만 나와야되는데 결과값이 다르다!!</p>
</li>
</ol>
<h2 id="🤔왜-이런-결과가-발생한걸까">🤔왜 이런 결과가 발생한걸까?</h2>
<p>이유는 간단하다. &quot;근무하는&quot;이라는 문장에 꽂혀서 관계를 생각하지 않고 <strong><span style="color: red;">질의문과 ERD를 꼼꼼하게 보지 않았기 때문</span></strong>이다. 질의문을 다시 한번 살펴보자.🔎</p>
<blockquote>
<p>2명 이상의 사원이 근무하는 각 부서에 대해서 <strong>부서 번호, 부서이름과 40,000달러가 넘는 급여를 받는 사원의 수</strong>를 검색하라.</p>
</blockquote>
<p>employee 테이블: 부서 번호(department의 외래키), 급여 
department 테이블: 부서이름
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/25b085ef-7f10-4fc8-8fe1-46fa5be950e3/image.png" /></p>
<p>📢 자! 어디에 works_on 테이블을 가져오라는 단서가 있었지? 질의문을 꼼꼼하게 볼 것!</p>
<p>👩‍🏫 올바른 풀이</p>
<pre><code class="language-mysql">select e.dno, d.dname, count(*) as num_emp_over_40000
from employee e, department d
where e.salary &gt; 40000 and e.dno = d.dno and e.dno in (    
select e.dno
from employee e
group by e.dno having count(*) &gt;= 2
)
group by e.dno, d.dname;</code></pre>
<p>위의 모범 답안 쿼리 도출 과정도 상당히 복잡하기 때문에, 역시 나눠서 생각해보자.</p>
<ol>
<li><p>2명 이상의 사원이 근무하는 각 부서에 대해 검색</p>
<pre><code class="language-mysql">select e.dno, count(*) as num_emp
from employee e
group by e.dno having num_emp &gt;= 2;</code></pre>
</li>
<li><p>부서이름과 40000달러가 넘는 급여를 받는 부서를 검색</p>
<pre><code class="language-mysql">select d.dname, e.dno
from department d
inner join employee e on d.dno = e.dno
where e.salary &gt; 40000;</code></pre>
</li>
</ol>
<p>위의 2가지 과정을 종합하면, 
👩외부 쿼리: 부서별로 부서 번호와 부서 이름, 그리고 40,000달러가 넘는 급여를 받는 사원의 수를 구하되,
👶서브 쿼리: 부서 전체의 사원 수가 2명 이상인 부서만 선택</p>
<pre><code class="language-mysql">select e.dno, d.dname, count(*) as num_emp_over_40000
from employee e
inner join department d on e.dno = d.dno
where e.salary &gt; 40000 and e.dno in (
      select e.dno
      from employee e
      group by e.dno
      having count(*) &gt;= 2
)
group by e.dno, d.dname;</code></pre>
<p>🔖IN 연산자(다중 행 부속 질의문에 사용): 부속 질의문의 결과 값 중 일치하는 것이 있으면 검색 조건이 참</p>
<p>💡참고로, ONLY_FULL_GROUP_BY 모드가 활성화되어 있으면, <strong>SELECT 문에 있는 모든 열은 집계 함수가 되거나 GROUP BY 절에 나타나야 한다.</strong></p>
<ul>
<li>위의 쿼리는 department 테이블의 dno가 기본키 역할을 해서d.dname을 유일하게 결정하기 때문에 사실상 GROUP BY절에 d.dname을 작성하지 않아도 되지만 안전장치를 걸어두자.</li>
</ul>
<hr />
<h1 id="📚참고-자료">📚참고 자료</h1>
<p>🔗between 문법: <a href="https://www.w3schools.com/mysql/mysql_between.asp">https://www.w3schools.com/mysql/mysql_between.asp</a>
🔗group by 사용 이유 및 오류 해결법: <a href="https://kimsyoung.tistory.com/entry/GROUP-BY%E4%B8%8B-%EC%98%A4%EB%A5%98%EB%AC%B8-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0?category=822739">https://kimsyoung.tistory.com/entry/GROUP-BY%E4%B8%8B-%EC%98%A4%EB%A5%98%EB%AC%B8-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0?category=822739</a></p>