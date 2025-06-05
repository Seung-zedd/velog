<p>📢two sum은 여러 번 풀어봤으므로, 문제 해석 부분은 생략하겠습니다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>백트래킹 조합 함수 템플릿을 사용해 후보군을 찾는다.</li>
<li>조합의 한 집합을 모두 더했을 때 target과 일치하면
일단 temp 리스트에 val들을 삽입한다.</li>
<li>nums의 val과 temp의 val이 일치하면: 인덱스를 추출한다.</li>
<li>추출한 인덱스들을 result에 삽입하고 result를 리턴</li>
</ol>
<p>⚠️아래의 코드 구현 부분은 테케를 통과하기 위한 코드만을 구현했기에 <strong>매우 지저분하므로 스킵</strong>하셔도 됩니다. </p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class TwoSumBacktrack {
    public int[] twoSum(int[] nums, int target) {
        List&lt;List&lt;Integer&gt;&gt; ans = new ArrayList&lt;&gt;();
        List&lt;Integer&gt; temp = new ArrayList&lt;&gt;(); // 조합에서 구한 val의 인덱스를 구하기 위함
        backtrack(nums, ans, new boolean[nums.length], new ArrayList&lt;&gt;(), 0, 2, target);

        int[][] resultVal = new int[nums.length][2]; // ans에서 result를 추출하고 플래그를 설정하기 위함
        int[] result = new int[2];

        for (List&lt;Integer&gt; integerList : ans) {
            if (integerList.get(0) + integerList.get(1) == target) {
                temp.add(integerList.get(0));
                temp.add(integerList.get(1));
                break;
            }
        }

        for (int i = 0; i &lt; nums.length; i++) {
            if (resultVal[i][1] == 0 &amp;&amp; (nums[i] == temp.get(0) || nums[i] == temp.get(1))) {
                resultVal[i][0] = i;
                resultVal[i][1] = 1;
            }

        }

        boolean[] flag = new boolean[2];
        for (int i = 0; i &lt; resultVal.length; i++) {
            if (!flag[0] &amp;&amp; resultVal[i][1] == 1) {
                result[0] = resultVal[i][0];
                flag[0] = true;
            }
            if (flag[0] &amp;&amp; resultVal[i + 1][1] == 1) {
                result[1] = resultVal[i + 1][0];
                flag[1] = true;
            }

            if (flag[0] &amp;&amp; flag[1]) {
                break;
            }
        }

        return result;

    }

    private static void backtrack(int[] nums, List&lt;List&lt;Integer&gt;&gt; ans, boolean[] visited, List&lt;Integer&gt; curr, int start, int r, int target) {
        // base condition
        if (curr.size() == r) {
            ans.add(new ArrayList&lt;&gt;(curr));
            return;

        }

        // iteration
        for (int i = start; i &lt; nums.length; i++) {
            if (!visited[i]) {
                visited[i] = true;
                curr.add(nums[i]);
                backtrack(nums, ans, visited, curr, i + 1, r, target);
                visited[i] = false;
                curr.removeLast();
            }
        }
    }
}</code></pre>
<ul>
<li>자바에서는 리스트의 한 행을 모두 더해주는 <code>sum()</code> 함수가 없어서 위와 같이 integerList를 직접 순회해서 temp에 추가시켰다.</li>
<li>또한, <code>[3, 3]</code>과 같은 테케를 통과시키기 위해 flag라는 장치를 추가하였다.</li>
</ul>
<p>⏰내가 짠 코드의 시간복잡도는 조합 후보에서 target을 찾기 위한 $$O(n)$$, 백트래킹을 활용한 $$O(n^2)$$, 그리고 nums 길이인 $$O(n)$$과 resultVal의 길이만큼 순회해서 $$O(n)$$이고, 다 더하면 $$O(n^2 + 3n)$$인데 점근법으로 계산하면 최종적으로 $$O(n^2)$$이 걸린다. 
그러나, 제약조건이 $$10^4$$인데다가, 조합 백트래킹을 사용해서 $$10^8$$과 더불어 재귀함수를 그만큼 호출했기 때문에 아래와 같이 시간초과가 났다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/24a1cb64-f9e7-4db7-8799-acf81c5b6dd1/image.png" /></p>
<hr />
<h1 id="🤖수정된-코드-구현">🤖수정된 코드 구현</h1>
<pre><code class="language-java">public int[] twoSum(int[] nums, int target) {
        boolean found = false;
        int[] result = new int[2];
        backtrack(nums, result, new ArrayList&lt;&gt;(), 0, target, found);
        return result;

    }

    private static void backtrack(int[] nums, int[] result, List&lt;Integer&gt; curr, int start, int target, boolean found) {
        // 조기 종료: 이미 답을 찾은 경우
        if (found) return;

        // base condition
        if (curr.size() == 2) {
            if (nums[curr.get(0)] + nums[curr.get(1)] == target) {
                result[0] = curr.get(0);
                result[1] = curr.get(1);
                found = true;
            }

            return;
        }

        // iteration
        for (int i = start; i &lt; nums.length; i++) {
            curr.add(i); // 인덱스를 저장
            backtrack(nums, result, curr, i + 1, target, found);
            curr.removeLast();
        }
    }</code></pre>
<ul>
<li>curr에 값 대신 인덱스를 저장하였다.</li>
<li>target을 만족시키는 원소들을 모두 찾으면 flag를 true로 설정해서 재귀 호출을 최소화시켰다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/d670b46b-f7dd-43f3-a9ee-b84cfcd3d75d/image.png" /></p>
<p>📝백트래킹 조합을 실전 문제에 이렇게 적용할 수도 있다는 것만 알아두고, 코테 문제를 풀 때는 항상 최소의 시간복잡도를 고려해서 알고리즘을 설계하자!</p>