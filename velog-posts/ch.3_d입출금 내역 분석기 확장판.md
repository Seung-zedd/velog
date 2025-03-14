<h1 id="📢긴급-공지사항">📢긴급 공지사항</h1>
<p>이 책을 읽어보면서 SOLID 원칙은 적어도 앱 서비스를 만들어본 사람한테 해당되기 때문에 제 수준에는 이 책이 안 맞는 것 같아 잠정적으로 중단하게 되었습니다.🙇</p>
<h1 id="🚩도전-과제">🚩도전 과제</h1>
<p>🤓마크 저커버그: 오우, 훌륭합니다! 당신의 실력을 보아하니 CSV파일 말고도 다른 파일 포맷 형식도 지원하는 프로그램도 만들 수 있겠죠? 그리고 저는 콘솔로 결과를 출력하는 것이 아니라 HTML 형식으로 리포트를 출력하길 원합니다!</p>
<p>👨‍💻(일단 Accepted하자.)네! 가능합니다!
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/24cb88b7-12d3-4629-87fd-9246d36c77e0/image.png" /></p>
<h1 id="🏁목표">🏁목표</h1>
<ul>
<li><strong>개방 폐쇄 원칙(Open Closed Principle)</strong>을 배움으로써 코드베이스에 유연성을 추가하고 유지보수성을 개선하는데 도움을 준다.</li>
<li><strong>인터페이스의 적절한 사용성</strong>에 대한 가이드라인과 <strong>높은 커플링을 피하는 기법</strong>을 배운다.</li>
<li>언제 API에 예외를 포함하거나 포함하지 않을지를 결정하는 <strong>자바의 예외 처리 방법</strong>을 배운다.</li>
</ul>
<h1 id="📜확장된-입출금-내역-분석기-요구-사항">📜확장된 입출금 내역 분석기 요구 사항</h1>
<ol>
<li>특정 입출금 내역을 검색할 수 있는 기능, 예를 들어 주어진 날짜 범위 또는 특정 범주의 입출금 내역 얻기</li>
<li>검색 결과의 요약 통계를 텍스트, HTML 등 다양한 형식으로 만들기</li>
</ol>
<p>🔨1번의 요구사항은 <strong>함수형 인터페이스</strong>를 통한 <strong>OCP 원칙</strong>을 활용하여 반복 로직과 비즈니스 로직의 결합을 제거하고, 내역의 요구사항이 변경되더라도 얼마든지 대응할 수 있다. </p>
<ul>
<li>왜냐하면 구상체를 새로 만들고 함수 호출 부분에서 마치 정수기 필터만 갈아끼우듯이 인자를 갈아끼우기만 하면 되기 때문<ul>
<li>OCP의 본질: 확장에는 열려 있고(구상체를 만들기만 하면 됨), 변경에는 닫혀 있다(코드 변경없이 인자만 교체)</li>
</ul>
</li>
</ul>
<h1 id="😄ocp-원칙의-장점">😄OCP 원칙의 장점</h1>
<ol>
<li>기존 코드를 바꾸지 않으므로 기존 코드가 잘못될 가능성이 줄어듦</li>
<li>코드가 중복되지 않으므로 <strong>기존 코드의 재사용성</strong>이 높아진다.
👉모듈화 가능성 UP</li>
<li>결합도가 낮아지므로 <strong>코드 유지보수성</strong>이 좋아진다.
👉커플링이 줄어들기 때문에 디버깅하기 유리!</li>
</ol>
<h2 id="😈갓-인터페이스god-interface">😈갓 인터페이스(God Interface)</h2>
<p>지난 챕터에서 배운 갓 클래스와 비슷하게, 갓 인터페이스란 <strong>구현 클래스의 모든 메서드를 정의한 인터페이스</strong>를 말한다.</p>
<pre><code class="language-java">//! should be banned since it's a God interface
public interface BankTransactionProcessor {
    double calculateTotalAmount();
    double calculateTotalInMonth(Month month);
    double calculateTotalInJanuary(); // 헬퍼 연산
    double calculateAverageAmount();
    double calculateAverageAmountForCategory(Category category); // 헬퍼 연산
    List&lt;BankTransaction&gt; findTransactions(BankTransactionFilter bankTransactionFilter);

}</code></pre>
<h3 id="🚩갓-인터페이스의-문제점">🚩갓 인터페이스의 문제점</h3>
<ol>
<li><p>모든 _헬퍼 연산_이 명시적인 API 정의에 포함되면서 <strong>인터페이스가 복잡</strong>해진다.</p>
</li>
<li><p>인터페이스를 바꾸면 구현 클래스의 구현한 모든 코드들도 바뀐 내용을 지원하도록 갱신해야 한다.</p>
</li>
<li><p>인터페이스가 도메인 객체의 특정 접근자에 종속되면(위 인터페이스의 Month, Category 부분) 만약 도메인 객체의 세부 내용이 바뀔 경우, 인터페이스도 바꾸고 구현 메서드도 전부 바꿔야 한다.
😫배보다 배꼽이 더 커짐!</p>
</li>
</ol>
<p>🔖헬퍼 연산: 주로 <strong>특정 작업</strong>을 수행하는 데 도움을 주는 <strong>작은 유틸리티 메서드</strong>를 의미하는데, 인터페이스를 복잡하게 만드는 원인이 된다.</p>
<ul>
<li>심지어 유틸리티 메서드는 응집도 순서도 중 가장 낮은 순위에 있음</li>
</ul>
<p>그렇다고 다음과 같이 각 동작을 별도의 인터페이스로 정의하면 필요한 기능을 찾기가 어렵다.</p>
<pre><code class="language-java">interface CalculateTotalAmount {
    double calculateTotalAmount();
}

interface CalculateAverage {
    double calculateAverage();
}

interface CalculateTotalInMonth {
    double calculateTotalInMonth(Month month);
}</code></pre>
<p>🤣누가 인터페이스하고 추상 메서드 네이밍을 같게 짓겠는가? 이러한 인터페이스 설계는 안티 응집도(anti-cohesion)문제가 발생해서 찾기도 힘들고 나중에 새로운 인터페이스만 추가될 뿐이다.</p>
<h2 id="명시적-api-vs-암묵적-api">명시적 API vs 암묵적 API</h2>
<p>BankTransactionProcessor는 단순히 입출금 내역에서 <strong>통계적 연산을 수행하는 클래스</strong>일 뿐이기 때문에 인터페이스로 구현할 필요가 없다.</p>
<p>그렇다면, 핵심 메서드를 간단하게 <code>findTransactions()</code>라고 해야할까, 아니면 구체적으로 <code>findTransactionsGreaterThanEqual()</code>이라고 정의 해야할까?</p>
<p>후자
👍장점: 메서드명이 기능을 잘 설명하고 있고 사용하기가 쉬움 
👎단점: 특정 상황에 국한되어 각 상황에 맞는 새로운 메서드를 많이 만들어야 함</p>