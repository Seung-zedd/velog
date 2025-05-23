<h1 id="삭제를-위한-자바스크립트-작성하기">삭제를 위한 자바스크립트 작성하기</h1>
<h3 id="💻question_detail-템플릿-파일">💻question_detail 템플릿 파일</h3>
<pre><code class="language-javascript">const delete_elements = document.getElementsByClassName(&quot;delete&quot;);
    Array.from(delete_elements).forEach(function (element) {
        element.addEventListener('click', function () {
            if (confirm(&quot;정말로 삭제하시겠습니까?&quot;)) {
                location.href = this.dataset.uri;
            }
        });
    });</code></pre>
<ul>
<li>🚩function을 화살표 함수로 바꾸면 this를 바인딩 하지 않고, &quot;상위 스코프의 this를 사용&quot;하기 때문에 예상했던 결과와 다르게 동작
👉array function이 간편해보여도, 제대로 알고 사용하자!</li>
</ul>
<h1 id="답변-삭제-기능-추가하기">답변 삭제 기능 추가하기</h1>
<h2 id="🤔어떻게-answerservicedeleteanswer로-영속성-컨텍스트에서-answer를-삭제했는데-answer의-부모-엔티티인-question의-id를-조회할-수-있나">🤔어떻게 answerService.delete(answer)로 영속성 컨텍스트에서 answer를 삭제했는데, answer의 부모 엔티티인 question의 id를 조회할 수 있나?</h2>
<p>👉spring.jpa.open_in_view=true : 응답이 완료될 때 커넥션을 반납하기 때문에 <strong>컨트롤러의 HTTP 메서드가 종료되기 전(응답하기 전)까지는 영속성 컨텍스트에 엔티티가 여전히 존재</strong>하기 때문에(DB로 <code>flush()</code>하기 전) <code>answer.getQuestion.getId()</code>도 가능했던거임.</p>
<h2 id="자바스크립트에서-null과-undefined의-차이점">자바스크립트에서 null과 undefined의 차이점</h2>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/b7d72498-0789-4de5-a7c0-930e5b06d410/image.png" /></p>
<ol>
<li>null: 변수 메모리는 존재하지만 빈 객체를 가리키는 객체 포인터이기 때문에 형태가 존재하고, 의도적으로 <strong>값이 비어있음을 명시</strong>하기 위해 사용한다.</li>
<li>undefined: 변수가 초기화되지 않았거나, 객체의 속성이 존재하지 않았을 때 <strong>자동으로 할당되는 값</strong>이다.</li>
</ol>
<p>즉, undefined는 말그대로 아래의 짤과 일치하는 상태다.</p>
<p>개발자: 아 있었는데? 
자바스크립트: 아니 형태 자체도 없어요 그냥
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/307b512d-ed52-483d-b500-e65c55f1f15c/image.png" /></p>
<h1 id="311-앵커-기능-추가하기">3‑11 앵커 기능 추가하기</h1>
<p>🤔왜 답변 엔티티를 삭제했는데 Referential integrity constraint violation 에러가 발생했지?
👉AnswerVoter가 Answer를 참조하고 있는 상태에서 SQL 쓰기 지연 저장소로부터 <code>flush()</code>가 호출되어 Answer가 삭제되면, DB는 AnswerVoter의 외래키(ANSWER_ID)가 유효하지 않다고 판단하여 참조 무결성 제약 조건 위반 에러를 발생시킨다. 
이는 Answer가 단순히 null이 되는 것이 아니라, <strong>DB에서 해당 레코드가 완전히 제거</strong>되기 때문
=&gt; 즉, <code>flush()</code>이후에 Answer 엔티티가 null값이 되는게 아니라, <span style="color: red;"><strong>레코드 자체가 삭제되기 때문에 참조 무결성 전제조건 위반 에러가 발생</strong></span>했던 것!!</p>
<p>🔨따라서, <strong>부모-자식 객체는 보통 생명주기가 동일</strong>하기 때문에 Question과 Answer 클래스에서 <strong>자식 객체</strong>인 QuestionVoter와 AnswerVoter에 각각<strong><code>CascadeType.REMOVE</code>를 설정</strong>한다.</p>
<h1 id="313-검색-기능-추가하기">3‑13 검색 기능 추가하기</h1>
<p>🤔inner join과 left outer join의 차이점은?
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/c788270a-e6ee-4bab-bd3a-b28cf4c458b1/image.png" /></p>
<p>inner join: <strong>두 table에 공통된 데이터</strong>가 존재하는 행에 대해서만 데이터를 검색
left outer join: <strong>주 테이블</strong>을 기준으로 하는 모든 데이터를 포함한 데이터를 검색하기 때문에, 대상 테이블에 null값이 있을 경우, 그대로 join해서 출력</p>
<hr />
<p>🔗null과 undefined의 차이점: <a href="https://inpa.tistory.com/entry/%F0%9F%93%9A-null-undefined-NaN#null_%EC%9D%98_%EC%83%81%ED%83%9C">https://inpa.tistory.com/entry/%F0%9F%93%9A-null-undefined-NaN#null_%EC%9D%98_%EC%83%81%ED%83%9C</a>
🔗left outer join과 (inner) join의 차이점, 그리고 JOIN을 하는 이유: <a href="https://jay-din.tistory.com/128">https://jay-din.tistory.com/128</a></p>