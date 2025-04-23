<p><a href="https://leetcode.com/problems/longest-consecutive-sequence/description/">https://leetcode.com/problems/longest-consecutive-sequence/description/</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>int형 nums 배열이 정렬되지 않은 채로 주어졌을 때, 연속적인 원소 시퀀스의 길이를 리턴하라.(단, O(N) 내에 런타임을 구성해야 한다. -&gt; sort 쓰지 말라는 뜻!)</p>
<p>🚧제약 조건
<code>0 &lt;= nums.length &lt;= 10^5</code> =&gt; longest_length = 0으로 초기화하라는 뜻
<code>-10^9 &lt;= nums[i] &lt;= 10^9</code> =&gt; 최솟값을 찾으시오</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>먼저 map에 &lt;Integer, Boolean&gt; 형태로 nums에 있는 모든 원소를 저장한다.
👉그래야 <code>containsKey()</code>로 검사해서 시퀀스를 뽑아낼 수 있음</li>
<li><ol>
<li>제약 조건에 의해 최솟값을 세팅해야 되는데, 이를 위해 min_val을 Integer.MAX_VALUE로 설정하고 <code>Math.min()</code> 메서드를 사용해서 최솟값을 업데이트한다.</li>
</ol>
</li>
<li><code>map.containsKey(min_val)</code>이 참이면 map에 있는 기존의 min_val을 제거하고 min_val을 1씩 증가시켜서 key chaining을 사용해서 sequence를 뽑아낸다.</li>
<li>최종적으로 longest_length를 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class LcsSolution {
    public int longestConsecutive(int[] nums) {
        HashMap&lt;Integer, Boolean&gt; map = new HashMap&lt;&gt;();
        List&lt;Integer&gt; keyStack = new ArrayList&lt;&gt;();
        int longest_length = 0; // due to zero of nums.length
        int min_val = Integer.MAX_VALUE;

        // nums의 길이가 0이면 그대로 리턴
        if (nums.length == 0) {
            return longest_length;
        }
        // 배열 -&gt; Set(중복되는 요소 제거하기 위함)
        Set&lt;Integer&gt; numSet = Arrays.stream(nums)
                .boxed() // int -&gt; Integer
                .collect(Collectors.toSet());

        // 먼저 nums[i]를 모두 저장 &amp;&amp; 최솟값 설정
        for (int i : numSet) {
            map.put(i, true);
            min_val = Math.min(i, min_val);
        }

        for (int i = 0; i &lt; map.size(); i++) {
            if (map.isEmpty()) {
                break;
            }
            while (map.containsKey(min_val)) {
                map.remove(min_val); // for key chaining
                min_val += 1;
                longest_length += 1; // for return consecutive length
            }
            keyStack.add(longest_length);
            longest_length = 0; // 다시 0으로 초기화

            //! 똑같이 외부 루프 내에서 반복하기 때문에 최종적으로 O(N^2)이 걸림
            min_val = Integer.MAX_VALUE;
            for (Integer key : map.keySet()) {
                if (key &lt; min_val) {
                    min_val = key;
                }
            }
        }

        // longest_length 최댓값을 리턴
        int max_val = Integer.MIN_VALUE;
        for (int num : keyStack) {
            max_val = Math.max(num, max_val);
        }

        return max_val;
    }
}</code></pre>
<p>🚩이 코드의 문제점은 외부 루프 내에서 최소값을 찾기 위해 <code>map.keySet()</code>을 순회하는 부분인데, 이 작업이 외부 루프가 반복될 때마다 수행되어 O(n²) 시간 복잡도가 발생한다는 것이다.
💡<code>while (map.containsKey(min_val))</code>은 key를 탐색하는데 O(1)만 걸리기 때문에 문제가 되지 않는다.</p>
<hr />
<p>😵2시간 이상 고민해도 답을 못찾아서 AI의 도움을 받았다.
👉nums에서 시퀀스를 찾기 위한 다른 방법이 필요</p>
<p>🤖코드 설계는 다음과 같습니다:</p>
<ol>
<li>시퀀스의 <strong>시작점</strong>만 처리합니다(<code>num-1</code>이 없는 숫자).</li>
<li>시작점을 찾으면, <strong>해당 지점부터 연속된 숫자</strong>를 찾아 시퀀스 길이를 계산합니다.</li>
</ol>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public class LcsSolution {
    public int longestConsecutive(int[] nums) {
        HashSet&lt;Integer&gt; set = new HashSet&lt;&gt;();
        int streak = 1; // longest_streak을 업데이트하기 위해 전역변수 설정

        if (nums.length == 0) {
            return 0;
        }

        for (int i : nums) {
            set.add(i);
        }

        for (Integer num : set) {
            if (!set.contains(num - 1)) {
                int curNum = num; // 시퀀스 시작점으로 설정
                int curStreak = 1; // 다른 시퀀스 검사 위해 다시 1로 설정

                while (set.contains(curNum + 1)) {
                    curStreak += 1;
                    curNum += 1;
                }
                streak = Math.max(curStreak, streak); // 시퀀스 업데이트
            }
        }
        return streak;
    }
}</code></pre>
<ol>
<li>시퀀스의 시작점은 <code>if (!set.contains(num - 1)) int curNum = num</code>로 처리하였다.</li>
<li><ol>
<li>다른 시퀀스도 있을 수 있기 때문에 아래의 while문을 빠져나가면 다시 curStreak를 1로 설정하였다.</li>
</ol>
</li>
<li>시작점을 찾으면, 해당 지점부터 연속된 숫자를 찾기 위해 if문 내에서 <code>while (set.contains(curNum + 1)) curNum += 1</code>로 처음에 코드 구현했던 key chaining과 비슷하게 구현했고, 연속된 숫자를 찾을 때마다 <code>curStreak += 1</code>을 하였다.</li>
<li>마지막으로 가장 최댓값인 streak를 리턴한다.</li>
</ol>
<p>💡자바에서는 for문, if문 내의 <strong>블록으로 변수 스코프를 정의</strong>하기 때문에 streak를 업데이트 하기 위해 메소드 레벨에서 streak를 1로 초기화하였다.</p>
<p>🤔for문 내부의 while문도 N번 반복하니까 최종적으로 O(N^2)이 걸리지 않나요?
👉연속된 숫자를 따라가며 <strong>각 숫자를 딱 한 번씩만 방문</strong>하고, 만약 방문을 했다면 if문에서 걸러지기 때문에 최종적으로 <strong>for문의 원소만큼만 순회해서 O(N)</strong>만 걸린다.</p>
<ul>
<li>추가로, <code>set.contains()</code>함수는 O(1)만 걸림</li>
</ul>