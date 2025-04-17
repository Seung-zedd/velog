<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/e8cbcfb8-ae63-4075-a0a1-df26f43641c7/image.png" />
...Two sum 문제를 여러 번 풀었는데, 같은 문제도 여러 방식으로 풀어서 그냥 로고와 함께 또썸으로 이름 짓겠다.</p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>int형 nums 배열과 int형 target이 주어졌을 때, 더해서 target을 만족하는 2개의 숫자 인덱스를 리턴하라.
(단, 같은 원소는 다시 사용할 수 없고, 각각의 input에는 정확히 하나의 솔루션만 있다고 가정한다. 즉, 답은 반드시 존재한다)</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>for문으로 순회하는데:
다음값을 알 수 없으니, 현재값을 기준으로 해서 보수를 구한다.
👉<a href="https://velog.io/@csj0209/LeetCodeTwo-Sum#%EB%B3%B4%EC%88%98complement%EB%9E%80">보수</a>는 이 하이퍼링크를 참조하자. 별로 어렵지 않다.</p>
<ol>
<li><code>map.put(nums[i], target - nums[i])</code>로 다음에 매칭될 value를 저장한다.</li>
<li>만약에 다음 회차에서 value가 key에 존재하면:
  return <code>[nums[i], target - nums[i]]</code></li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class TwoSumSolution {
    public int[] twoSum(int[] nums, int target) {
        HashMap&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();
        int[] answer = new int[2];

        for (int i : nums) {
            if (map.containsValue(i)) {
                answer[0] = indexOf(nums, target - i); // 현재 값의 인덱스
                answer[1] = indexOf(nums, i); // 보수의 인덱스
            }
            map.put(i, target - i); // &lt;현재 값, 보수&gt;
        }
        return answer;

    public static int indexOf(int[] arr, int target) {
        for (int i = 0; i &lt; arr.length; i++) {
            if (arr[i] == target) {
                return i;
            }
        }
        return -1;
    }
}</code></pre>
<p>🚩이렇게 하면 nums = [3, 3], target = 6 테케에 정확히 걸린다. 왜냐하면 <span style="color: red;"><strong><code>indexOf()</code> 함수는 nums의 인덱스에 의존</strong></span>하기 때문에, answer[0] = nums에서 key의 인덱스인 0을 저장하고, answer[1] = nums에서 value 인덱스를 저장하는데 3의 인덱스는 nums에서 처음으로 3에 매칭되기 때문에 마찬가지로 0이 된다.
👉테스트 케이스는 입출력의 예시일 뿐이지, <span style="color: red;"><strong>절대로 코드 구현을 테케에 의존해서는 안 된다!</strong></span>(왜냐하면 데이터 크기는 N으로 추상화되서 어떤 인덱스에 어떤 원소가 있는지 모르기 때문)
👨‍💻<strong>입출력 예시</strong>는 코드 구현했을 때 제대로 돌아가는지 <strong>디버깅</strong>하기 위한 도구에 불과하다는 것을 느끼자!
🚩뿐만 아니라, <code>map.containsValue()</code>를 조건으로 사용하면 value가 존재하는지 확인하기 위해 모든 값을 탐색하기 때문에 O(N)가 걸린다!</p>
<h2 id="🤔문제-발생-원인">🤔문제 발생 원인</h2>
<ol>
<li>보수가 for문을 순회하면서 현재값에 따라 결정(가변)된다는 것을 모름</li>
<li>map에 key와 value를 무엇으로 저장해야 하는지 모름
📜&quot;You may assume that each input would have exactly one solution.&quot; 즉, <strong>각각의 input은 unique한 key를 보장</strong>해주기 때문에 nums[i]를 key로 쓰라는 암묵적인 힌트였음!</li>
<li>따라서 answer[0]에 보수의 index를, answer[1]에 왜 현재의 index를 저장해야 하는지 모름
🔨보수가 key로서 존재한다는 것은 <strong>이전의 turn에서 현재값 + 보수 = target을 만족하는 현재값이 key로 존재</strong>한다는 거니까 answer[0]에 보수의 index를 그대로 저장하면 된다.</li>
</ol>
<p>따라서 수정된 코드 설계는 다음과 같다.</p>
<h1 id="⚙️수정된-코드-설계">⚙️수정된 코드 설계</h1>
<p>for문을 순회하면서:
보수 = target - 현재값</p>
<ol>
<li>if map에 보수가 key로 존재하면
answer[0] = map.get(보수) // map의 value
answer[1] = i // 현재 인덱스</li>
<li>else: <code>map.put(현재값, 현재 인덱스)</code></li>
</ol>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public int[] twoSum(int[] nums, int target) {
        HashMap&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();
        int[] answer = new int[2];

        for (int i = 0; i &lt; nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                answer[0] = map.get(complement);
                answer[1] = i;
            }
            map.put(nums[i], i);
        }
        return answer;
    }</code></pre>
<p>💡항상 <code>containsKey(Object key)</code>는 key를 기반으로 탐색하기 때문에 O(1)만 걸린다는 것을 숙지하자!</p>