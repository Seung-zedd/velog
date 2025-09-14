<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42889">https://school.programmers.co.kr/learn/courses/30/lessons/42889</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>노트 필기한 사고의 흐름을 따라가서 작성할 것</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>마찬가지</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class FailureSolution {
    public int[] solution(int N, int[] stages) {
        // 1. N을 for문을 돌리면서 실패율을 구한다.
        List&lt;FailureRate&gt; rateList = new ArrayList&lt;&gt;();

        // N을 점차 증가
        for (int i = 0; i &lt; N; i++) {
            int numerator = 0; // N에 해당하는 수 카운팅
            int denominator = 0;
            int stageNum = i + 1;
            for (int j = 0; j &lt; stages.length; j++) {
                // 분자
                if (stageNum == stages[j]) {
                    numerator++;
                }
                // 분모
                if (stageNum &lt;= stages[j]) {
                    denominator++;
                }
            }
            rateList.add(new FailureRate(stageNum, ((double) numerator / denominator)));
        }



        // 3. 스테이지의 번호가 담겨있는 배열을 return
        List&lt;Integer&gt; numList = new ArrayList&lt;&gt;();

        // 2. &quot;각 스테이지의 번호&quot;를 실패율(val)의 내림차순으로 정렬
        rateList.sort((r1, r2) -&gt; {
            // 만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다
            if (r1.rate == r2.rate) {
                return Integer.compare(r1.idx, r2.idx);
            } else {
                return Double.compare(r2.rate, r1.rate);
            }
        });

        for (FailureRate r : rateList) {
            numList.add(r.idx);
        }

        return numList.stream().mapToInt(Integer::intValue).toArray();
    }

    private static class FailureRate {
        int idx;
        double rate;

        public FailureRate(int idx, double rate) {
            this.idx = idx;
            this.rate = rate;
        }
    }
}</code></pre>
<ul>
<li>💡참고로, 직접 <code>sort()</code> 메서드를 사용하지 않고, 이전에 풀었던 <a href="https://velog.io/@csj0209/Programmerslv1%EB%AA%A8%EC%9D%98%EA%B3%A0%EC%82%AC">모의고사 문제</a>를 활용해서 <code>Math.max()</code>로 풀고 numList에 인덱스를 저장했으면 해당하는 failureRate 값을 -1로 초기화하는 방식으로 풀려고 했으나, 시간 초과가 나서 과감하게 포기했다. </li>
</ul>
<p>제출의 결과는 다음과 같다:
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/81d4b543-bbbd-4bee-97d4-0721dfa9bc67/image.png" /></p>
<h2 id="🚩pain-point">🚩Pain Point</h2>
<p>denominator가 0이라는 것은 “스테이지 N에 도달한 사람 자체가 한 명도 없다”는 뜻입니다.</p>
<p>예를 들어, N = 5이고, stages = <code>[3, 3, 3, 3, 3]</code>이라면, </p>
<p><code>실패율 = stages 배열에서 값이 정확히 N 인 원소의 개수 / stages 배열에서 값이 N 이상(≥ N)인 원소의 개수</code> 
인데, <code>N &gt;= 4</code>이면, 4이상인 원소가 없으므로, 분모가 0이 되서 <span style="color: red;"><strong>Divded by zero 런타임 에러</strong></span>가 발생한다. 바로 이 에러에 의해서 edge case가 통과하지 못했기 때문에 위와 같은 제출 결과가 발생한 것이다.</p>
<p>따라서, 위의 edge case를 고려해서 수정한 코드는 다음과 같다.</p>
<h1 id="🔧코드-구현">🔧코드 구현</h1>
<pre><code class="language-java">import java.util.*;

public class FailureSolution {
    public int[] solution(int N, int[] stages) {
        // 1. N을 for문을 돌리면서 실패율을 구한다.
        List&lt;FailureRate&gt; rateList = new ArrayList&lt;&gt;();

        // N을 점차 증가
        for (int i = 0; i &lt; N; i++) {
            int numerator = 0; // N에 해당하는 수 카운팅
            int denominator = 0;
            int stageNum = i + 1;

            for (int j = 0; j &lt; stages.length; j++) {
                // 분자
                if (stageNum == stages[j]) {
                    numerator++;
                }
                // 분모
                if (stageNum &lt;= stages[j]) {
                    denominator++;
                }
            }

            // 실패율 계산 (j 루프 바깥에서)
            double rate = 0.0;
            if (denominator != 0) {
                rate = (double) numerator / denominator;
            }
            rateList.add(new FailureRate(stageNum, rate));
        }

        // 2. &quot;각 스테이지의 번호&quot;를 실패율(val)의 내림차순으로 정렬
        rateList.sort((r1, r2) -&gt; {
            // 만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다
            if (r1.rate == r2.rate) {
                return Integer.compare(r1.idx, r2.idx);
            } else {
                return Double.compare(r2.rate, r1.rate);
            }
        });

        // 3. 스테이지의 번호가 담겨있는 배열을 return
        List&lt;Integer&gt; numList = new ArrayList&lt;&gt;();
        for (FailureRate r : rateList) {
            numList.add(r.idx);
        }

        return numList.stream().mapToInt(Integer::intValue).toArray();
    }

    private static class FailureRate {
        int idx;
        double rate;

        public FailureRate(int idx, double rate) {
            this.idx = idx;
            this.rate = rate;
        }
    }
}
</code></pre>
<ul>
<li>📢주의해야 할 점은, 실패율을 계산할 때 <strong>j 루프 바깥</strong>에서 해야 리스트에 중복해서 저장하는 실수를 방지할 수 있다.</li>
</ul>
<h1 id="✍️교훈">✍️교훈</h1>
<p>프로그래밍 언어에서 <code>0 / 0</code>는 NaN(Not a Number) 또는 Divided by Zero 런타임 에러를 내뱉는데, 이것을 edge case로 간주하고 어떻게 처리하느냐가 문제의 시사점이였다.</p>
<p><a href="https://velog.io/@csj0209/Programmerslv3%EB%B2%A0%EC%8A%A4%ED%8A%B8%EC%95%A8%EB%B2%94">베스트앨범</a> 문제에서도 특정 조건에 따른 정렬과 같이 비슷한 로직이 적용되기 때문에 복습할 때 같이 풀면 도움이 될 것 같다.</p>