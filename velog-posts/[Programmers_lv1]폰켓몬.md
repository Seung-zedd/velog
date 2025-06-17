<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/1845">https://school.programmers.co.kr/learn/courses/30/lessons/1845</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>홍 박사님은 자신의 연구실에 있는 <strong>총 N마리의 폰켓몬 중에서 N/2마리를 가져가도 좋다</strong>고 했습니다.
홍 박사님 연구실의 폰켓몬은 종류에 따라 번호를 붙여 구분합니다. 따라서 <strong>같은 종류의 폰켓몬은 같은 번호</strong>를 가지고 있습니다.</p>
<p>요구사항: 당신은 최대한 다양한 종류의 폰켓몬을 가지길 원하기 때문에, <strong>최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택</strong>하려 합니다.
param: N마리 폰켓몬의 종류 번호가 담긴 배열 nums
return: N/2마리의 폰켓몬을 선택하는 방법 중, &quot;가장 많은 종류의 폰켓몬을 선택하는 방법&quot;의 개수를 리턴</p>
<p>🚧제약 조건
nums: 1차원 배열
nums의 길이(N)는 1 이상 10,000 이하의 자연수이며, 항상 짝수로 주어집니다.
=&gt; 1 &lt;= <code>nums.length</code> &lt;= $$10^4$$, 즉 $$O(nlogn)$$까지 가능</p>
<p>폰켓몬의 종류 번호는 1 이상 200,000 이하의 자연수로 나타냅니다.
=&gt; 1 &lt;= <code>nums[i]</code> &lt;= 200,200</p>
<p>가장 많은 종류의 폰켓몬을 선택하는 방법이 여러 가지인 경우에도, 선택할 수 있는 폰켓몬 종류 개수의 최댓값 하나만 return하면 된다.</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/70eee453-9d06-4f30-95b5-aaf15c6e6e5a/image.jpg" />
먼저 <code>&lt;nums[i], count&gt;</code>형식으로 해시맵에 넣는다. 그런데 문제의 조건에서는:</p>
<ol>
<li>&quot;최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택하려 합니다.&quot;</li>
<li>&quot;같은 종류의 폰켓몬은 같은 번호를 가지고 있습니다.&quot;
이라서, 첫번째를 종료조건으로 하고, 두번째를 hash collision을 활용해서 해시맵에 삽입하려고 한다.</li>
</ol>
<p>또한, 나는 &quot;최대한&quot;이라는 문장에 꽂혀서, 처음에는 <code>Math.max()</code>로 <code>map.get(nums[i])</code>로 value를 가져와서 업데이트하면 되는거 아닌가? 라고 생각을 하였다.
그런데, 애초에 키가 같으면 해시값은 업데이트되는 hash collision을 활용할 것이기 때문에 굳이 max 알고리즘을 사용하지 않고, <code>if (!map.containsKey(nums[i]))</code>로 hash collision을 구현하면 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/458f840f-1833-47a0-adb1-e73acc59d14d/image.jpg" />
즉, 위의 그림과 같이 <code>nums[i]</code>가 중복이 되면 스킵하도록 해서 값을 중복되게 저장하지 않게 하면 된다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class PocketMonSolution {
    public int solution(int[] nums) {
        Map&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();
        for (int i = 0; i &lt; nums.length; i++) {
            if (!map.containsKey(nums[i])) {
                map.put(nums[i], 1);
            }
            // 키가 있으면 스킵
        }

        // 종류 최대 탐색
        int sort = 0;
        for (int i = 0; i &lt; map.size(); i++) {
            if (sort == nums.length / 2) {
                break;
            }
            sort += map.get(nums[i]);

        }
        return sort;
    }
}</code></pre>
<ul>
<li>코드 설계의 첫번째 조건을 <code>if (sort == nums.length / 2)</code>로 하여서 종료 조건으로 구현하였다.</li>
</ul>
<p>⏰해시맵을 사용하기 때문에 최대 nums 길이만큼 순회해서 $$O(n)$$의 시간복잡도가 걸린다.</p>
<p>🤔그런데, 위의 2번째 그림을 살펴보자. 애초에 중복되는 원소를 HashSet을 사용하면 집합은 순서 x, 중복 x인 자료구조이기 때문에 더욱 더 효율적으로 알고리즘을 짤 수가 있다.</p>
<h1 id="💻코드-구현해시셋">💻코드 구현(해시셋)</h1>
<pre><code class="language-java">public class PocketMonHashSetSolution {
    public int solution(int[] nums) {
        HashSet&lt;Integer&gt; set = new HashSet&lt;&gt;();

        for (int i = 0; i &lt; nums.length; i++) {
            set.add(nums[i]); // 중복이 되지 않기 때문에 그대로 저장
        }

        // 종료 조건
        if (set.size() &lt; nums.length / 2) {
            return set.size();
        }
        return nums.length / 2;
    }
}</code></pre>
<ul>
<li>해시맵에서는 <code>nums[i]</code>마다 카운팅을 해야 하는 반면, <strong>해시셋은 삽입할 때 중복을 제거</strong>해주기 때문에 <code>nums[i]</code>를 그대로 삽입해도 된다.</li>
<li>최대한 많은 종류의 폰켓몬을 포함해서 N/2마리를 선택하는 것이 최종 목적이기 때문에, 
중복을 제거한 해시셋의 크기가 N/2보다 작으면 해시셋의 크기를 리턴하고, 그렇지 않으면 다 종류가 다른 것이기 때문에 N/2를 그대로 리턴하면 된다.</li>
</ul>
<p>⏰역시 nums의 길이만큼 순회해서 해시셋으로 원소를 삽입하기 때문에 $$O(n)$$이 걸린다.</p>
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>처음에는 러프하게 해시맵으로 원소를 카운팅했는데, hash collision을 활용해서 <code>if (!map.containsKey(nums[i]))</code>로 중복체크한 것을 해시셋 자료구조로 최적화할 수 있다는 것을 알았다.</p>
<p>🤖&quot;최대한 다양한 종류&quot;라는 조건을 보면, <strong>중복 제거</strong>가 핵심임을 파악해야 한다.</p>