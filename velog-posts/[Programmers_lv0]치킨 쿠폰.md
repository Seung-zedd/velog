<h1 id="📜문제-해석">📜문제 해석</h1>
<ol>
<li>치킨을 시켜먹으면 한 마리당 쿠폰을 한 장 발급</li>
<li>쿠폰을 열 장 모으면 치킨을 한 마리 서비스
2-1. 서비스 치킨에도 쿠폰이 발급</li>
</ol>
<p>&quot;시켜먹은 치킨의 수&quot; chicken이 매개변수로 주어질 때, 받을 수 있는 &quot;최대 서비스 치킨의 수&quot;를 return하라.</p>
<p>🚧제약 조건
0 ≤ chicken ≤ 1,000,000(치킨은 정수)
if 0 &lt;= chicken &lt; 10: return 0(서비스 치킨은 0개)</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>입출력 예시를 보니 얼핏 소인수분해 문제와 비슷한데, 10으로 나눈 몫을 서비스 치킨, 나머지를 쿠폰으로 두고 누적해서 계속 더하는 것으로 하였다. 그리고 while문을 빠져나간 다음에 쿠폰이 10개가 넘으면 10장당 1개로 서비스 치킨을 교환하였고, 마지막으로 서비스 치킨을 리턴하였다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(chicken):
    coupon = 0
    service_chicken = 0

    if 0 &lt;= chicken &lt; 10:
        return 0
    while 10 &lt;= chicken:
        service_chicken += chicken // 10
        coupon += chicken % 10 # 쿠폰 누적
        chicken //= 10
    coupon += chicken
    if coupon &gt;= 10:
        service_chicken += coupon // 10
    return service_chicken</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/188e1e00-ecfa-4a86-ae02-3e242c2383bc/image.png" />
테스트 케이스 1개만 딱 통과를 하지 못했는데 무엇을 빼먹었는지 몰라서 AI한테 물어보았다.</p>
<p>🤖<strong>&quot;서비스 치킨에도 쿠폰이 발급&quot;</strong>된다는 부분이 핵심이었습니다. 이 말은 서비스로 받은 치킨 1마리당 새로운 쿠폰 1장이 발급되며, 이 쿠폰들이 다시 10장이 모이면 추가 서비스 치킨을 받을 수 있다는 의미입니다.
따라서 코드는 다음과 같이 작성하면 됩니다:</p>
<h1 id="🤖코드-구현">🤖코드 구현</h1>
<pre><code class="language-python">def solution(chicken):
    answer = 0
    coupon = chicken

    while coupon &gt;= 10:
        # 쿠폰으로 받는 서비스 치킨 수
        service = coupon // 10
        answer += service
        # 남은 쿠폰 + 서비스 치킨으로 받은 새 쿠폰
        coupon = coupon % 10 + service
    return answer</code></pre>
<ul>
<li>&quot;서비스 치킨에도 쿠폰이 발급&quot;되기 때문에 이것을 while문에서 coupon으로 계속해서 연산하게 하였고, 별도의 answer를 변수로 둬서 분리를 하였다.</li>
</ul>
<p>📜다시 문제 요구사항을 코드로 옮기는 과정에서:</p>
<ol>
<li>치킨을 시켜먹으면 한 마리당 쿠폰을 한 장 발급</li>
</ol>
<ul>
<li>&quot;시켜먹은 치킨의 수&quot; chicken이 매개변수로 주어질 때, <code>coupon = chicken</code>으로 변환</li>
</ul>
<ol start="2">
<li>쿠폰을 열 장 모으면 치킨을 한 마리 서비스</li>
</ol>
<ul>
<li>입출력 예시에서 10으로 계속 나누는 것으로 보아 <code>while d &lt;= n:</code>이 자동으로 떠올라서 <code>while 10 &lt;= coupon</code>와 <code>service = coupon // 10</code>으로 구현<ul>
<li>최대 서비스 치킨의 수를 리턴해야 하기 때문에 <code>answer += service</code>로 서비스 치킨을 누적시킴</li>
</ul>
</li>
</ul>
<p>2-1. <strong>서비스 치킨에도 쿠폰이 발급</strong></p>
<ul>
<li>입출력 예시에는 나머지가 쿠폰으로 나와서 <code>coupon = coupon % 10</code>하고 서비스 치킨에도 쿠폰이 발급되기 때문에 <code>coupon = coupon % 10 + service</code>로 구현</li>
</ul>
<ol start="3">
<li>while문 빠져나가면 answer를 리턴</li>
</ol>
<p>📝아직도 문제 요구사항을 &quot;이해한 척&quot;하고 그냥 코드 설계 -&gt; 코드 구현으로 넘어가기 때문에 틀렸던 것이다. 문제 요구사항을 그냥 문제 지문이라고 생각하지 말고, &quot;고객의 중요한 요구사항&quot;이라고 간주하면 그래도 요구사항을 덜 빼먹지 않을까...?🤔</p>