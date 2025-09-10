<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42840">https://school.programmers.co.kr/learn/courses/30/lessons/42840</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<pre><code class="language-txt">1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...</code></pre>
<p>요구사항: 1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 <code>answers</code>가 주어졌을 때, <strong>가장 많은 문제를 맞힌 사람이 누구</strong>인지 배열에 담아 return하라.</p>
<h2 id="🚧제약조건">🚧제약조건</h2>
<ol>
<li>시험은 최대 10,000 문제($$10^4$$)로 구성되어있습니다.</li>
<li>문제의 정답은 1, 2, 3, 4, 5중 하나입니다.</li>
<li>가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.</li>
</ol>
<p>📢이 문제는 정답의 길이가 수포자의 패턴보다 길 경우, <strong>나머지 연산자를 사용해서 반복을 이용</strong>하는 것이 핵심인데 이것을 파악 못했기 때문에 책에 있는 코드 설계 과정을 그대로 옮겼습니다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>러프하게 보면, 코드 설계 과정은 다음과 같다.</p>
<p>수포자의 찍기 패턴 분석 -&gt; 파라미터로 주어진 정답과 수포자의 정답들을 비교하면서 맞은 개수 카운팅 -&gt; 가장 많은 문제를 맞힌 사람을 리턴</p>
<hr />
<p>위의 과정을 구체화시키면 다음과 같다.</p>
<ol>
<li>수포자들의 문제를 찍는 패턴을 분석(2차원 배열)</li>
</ol>
<ul>
<li>🤔왜 2차원 배열을 사용하지?<ul>
<li>row는 1번 수포자, 2번 수포자, 3번 수포자의 찍기 패턴을, col은 수포자의 해당하는 답에 접근하기 위해서</li>
</ul>
</li>
</ul>
<ol start="2">
<li><p>수포자들의 점수를 저장할 배열 초기화</p>
</li>
<li><p>각 수포자의 패턴과 정답이 얼마나 일치하는지 확인</p>
</li>
</ol>
<ul>
<li>만약 답이 <code>[1, 3, 5, 7, 9, 2, 4, 5]</code>면 답의 길이가 8이고, 수포자 1의 패턴 길이는 5이므로, 일대일 매핑을 하면, <code>[2, 4, 5]</code>가 남는데, 이것은 8 % 5 = 3이므로, 이 나머지 값을 다시 수포자 1의 col로 접근하면 된다.</li>
</ul>
<ol start="4">
<li>가장 높은 점수를 저장</li>
<li>가장 높은 점수를 가진 <strong>수포자들의 번호</strong>를 찾아서 리스트에 담음</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class MockTestSolution {
    public int[] solution(int[] answers) {
        // 1. 수포자들의 문제를 찍는 패턴을 분석(2차원 배열)
        int[][] patterns = {
                {1, 2, 3, 4, 5},
                {2, 1, 2, 3, 2, 4, 2, 5},
                {3, 3, 1, 1, 2, 2, 4, 4, 5, 5}
        };

        // 2. 수포자들의 점수를 저장할 배열 초기화
        // index == 수포자의 번호, value = 수포자의 맞은 점수
        int[] scores = new int[3];

        // 3. 각 수포자의 패턴과 정답이 얼마나 일치하는지 확인
        // 만약에 len(answers) &gt; len(patterns)면? -&gt; patterns를 % 연산자를 이용해서 반복
        for (int i = 0; i &lt; answers.length; i++) {
            for (int j = 0; j &lt; patterns.length; j++) {
                // Pain Point: 나머지 연산자를 사용해서 남은 문제들을 patterns의 col에 매칭시킴
                if (answers[i] == patterns[j][i % patterns[j].length]) {
                    scores[j]++;
                }
            }
        }

        // 4. 가장 높은 점수를 저장(최고 점수가 0일 수도 있으므로, 정수형 최솟값으로 초기화)
        int maxScore = Integer.MIN_VALUE;

        // scores의 value를 업데이트
        for (int i = 0; i &lt; scores.length; i++) {
            maxScore = Math.max(maxScore, scores[i]);
        }

        // 5. 가장 높은 점수를 가진 수포자들의 번호를 찾아서 리스트에 담음
        List&lt;Integer&gt; numList = new ArrayList&lt;&gt;();

        for (int i = 0; i &lt; scores.length; i++) {
            // scores의 인덱스를 리스트에 삽입
            if (maxScore == scores[i]) {
                numList.add(i + 1);
            }
        }

        return numList.stream().mapToInt(Integer::intValue).toArray();
    }
}</code></pre>
<h2 id="🚩pain-point">🚩Pain Point</h2>
<ol>
<li>나머지 연산자가 왜 반복을 의미하는지 이해를 못했음.
👉숫자와 배열의 예시를 생각하고, 직접 연산을 함으로써 이해를 하였다.(row는 N번 수포자에 해당, col은 N번 수포자의 정답에 해당)</li>
<li>scores를 다시 for문으로 돌리면서 <code>if(최고 점수 == scores[i]): numList에 수포자의 번호에 해당하는 인덱스를 삽입</code>이라는 로직을 생각하지 못함.
👉scores 배열의 인덱스는 수포자의 번호를, 값은 수포자의 맞은 점수를 의미한다.</li>
</ol>