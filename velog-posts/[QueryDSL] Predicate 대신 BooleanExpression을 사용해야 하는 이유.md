<p>🤔QueryDSL로 동적 쿼리를 작성하다 보면 <code>where</code> 절에 들어갈 조건을 <strong>메서드로 분리</strong>하는 경우가 많습니다. 
이때 메서드의 반환 타입으로 <code>Predicate</code>와 <code>BooleanExpression</code> 중 무엇을 선택해야 할까요?</p>
<p>👉결론부터 말하면, <strong><code>BooleanExpression</code>을 사용하는 것이 훨씬 더 안전하고 유연</strong>합니다. 이번 포스팅에서는 그 이유를 명확하게 알아보겠습니다.</p>
<hr />
<h2 id="predicate-vs-booleanexpression">Predicate vs BooleanExpression</h2>
<p>두 타입의 가장 큰 차이점은 <strong>메서드 체이닝(<code>and</code>, <code>or</code>) 지원 여부</strong>입니다. <code>BooleanExpression</code>은 <code>Predicate</code>를 상속받아 유용한 기능들을 추가한 클래스입니다.</p>
<table>
<thead>
<tr>
<th align="left">구분</th>
<th align="left">Predicate</th>
<th align="left">BooleanExpression</th>
</tr>
</thead>
<tbody><tr>
<td align="left"><strong>타입</strong></td>
<td align="left">인터페이스 (상위 타입)</td>
<td align="left">클래스 (하위 타입)</td>
</tr>
<tr>
<td align="left"><strong>주요 특징</strong></td>
<td align="left">모든 조건절을 대표하는 범용 타입</td>
<td align="left"><code>.and()</code>, <code>.or()</code> 같은 조합 메서드 지원</td>
</tr>
<tr>
<td align="left"><strong>장점</strong></td>
<td align="left">범용성</td>
<td align="left"><strong>조합과 재사용성</strong>이 뛰어남, Null-safe한 코드</td>
</tr>
<tr>
<td align="left"><strong>단점</strong></td>
<td align="left">조합(Composition) 기능 부재</td>
<td align="left"><code>Predicate</code>에 비해 특정 기능에 초점</td>
</tr>
</tbody></table>
<hr />
<h2 id="predicate의-한계">Predicate의 한계</h2>
<p>만약 동적 쿼리 메서드의 반환 타입을 <code>Predicate</code>로 선언하면, <span style="color: red;"><strong>다른 조건과 조합(Composition)하는 것이 불가능합니다.</strong></span></p>
<p><code>Predicate</code>는 인터페이스이므로 <code>.and()</code>와 같은 메서드를 가지고 있지 않기 때문입니다.</p>
<pre><code class="language-java">// ❌ 반환 타입이 Predicate인 경우
private Predicate usernameEq(String usernameCond) {
    // ... 로직 생략
}

// 다른 곳에서 조합하려고 하면 .and() 메서드가 없어 컴파일 에러 발생
Predicate condition = usernameEq(&quot;test&quot;).and(ageEq(10)); 
// -&gt; ERROR: Cannot resolve method 'and' in 'Predicate'</code></pre>
<h2 id="✨booleanexpression의-강력함">✨BooleanExpression의 강력함</h2>
<p>반면, 반환 타입을 BooleanExpression으로 지정하면 메서드 체이닝을 통해 조건을 자유롭게 조합할 수 있습니다. 이는 <strong>코드의 재사용성과 가독성을 극적으로 향상</strong>시킵니다.</p>
<pre><code class="language-java">// ✅ 반환 타입이 BooleanExpression인 경우
private BooleanExpression usernameEq(String usernameCond) {
    return usernameCond != null ? member.username.eq(usernameCond) : null;
}

private BooleanExpression ageEq(Integer ageCond) {
    return ageCond != null ? member.age.eq(ageCond) : null;
}

// .and()를 사용해 자유롭게 조합 가능
List&lt;Member&gt; result = queryFactory
        .selectFrom(member)
        .where(usernameEq(&quot;member1&quot;).and(ageEq(10))) // 안전하고 깔끔한 조합
        .fetch();</code></pre>
<p>👍where 절의 또 다른 장점: <code>where()</code> 메서드는 <strong>null인 파라미터를 무시</strong>하므로, <code>where(usernameEq(&quot;member1&quot;), ageEq(null))</code>과 같이 사용하면 ageEq 조건은 자동으로 제외됩니다.</p>
<p>🤓참고로 where 절에 <code>.and()</code>로 연결하는 것 뿐만 아니라 아래와 같이 <strong>콤마를 사용해서 여러 개의 메서드를 조합</strong>하는 것도 가능합니다.</p>
<pre><code class="language-java">//* 장점: 메서드를 다른 쿼리에서도 재활용할 수 있고 캡슐화를 할 수 있어서 코드 가독성 향상
    @Test
    @DisplayName(&quot;where 다중 파라미터 테스트&quot;)
    void dynamicQuery_WhereParam() {
        // given
        String usernameParam = &quot;member1&quot;;
        Integer ageParam = 10;

        // when
        List&lt;Member&gt; result = searchMember2(usernameParam, ageParam);

        // then
        assertThat(result.size()).isEqualTo(1);
    }

    //* where 절은 아래와 같이 콤마로 여러 개의 메서드를 파라미터로 넘겨주는 것이 Best Practice
    private List&lt;Member&gt; searchMember2(String usernameCond, Integer ageCond) {
        return queryFactory
                .selectFrom(member)
                .where(usernameEq(usernameCond), ageEq(ageCond))
                .fetch();
    }

    private BooleanExpression usernameEq(String usernameCond) {
        return usernameCond != null ? member.username.eq(usernameCond) : null;
    }

    private BooleanExpression ageEq(Integer ageCond) {
        return ageCond != null ? member.age.eq(ageCond) : null;
    }</code></pre>
<h1 id="✍️결론">✍️결론</h1>
<p>동적 쿼리의 핵심은 <strong>조건들을 안전하게 조합하고 재사용</strong>하는 것입니다.</p>
<p>BooleanExpression은 <code>.and()</code>, <code>.or()</code>와 같은 조합 기능을 제공하여 이를 가능하게 해줍니다. 
따라서 QueryDSL로 동적 쿼리 조건을 메서드로 분리할 때는, 반환 타입을 <strong>항상 BooleanExpression으로 지정하는 것을 권장</strong>합니다.</p>