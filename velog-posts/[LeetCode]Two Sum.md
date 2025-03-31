<h1 id="📜문제-해석">📜문제 해석</h1>
<p>int형 배열 nums와 int형 target이 주어졌을 때, 더해서 target이 되는 nums의 index 2개를 리턴하라.</p>
<p>각각의 input에는 정확히 하나의 솔루션만 존재하고, <strong>같은
원소를 2번 사용할 수 없다.</strong></p>
<p>🚧제약 조건</p>
<ol>
<li><code>2 &lt;= nums.length &lt;= 10^4</code></li>
<li>하나의 유효한 답변만 존재합니다.</li>
</ol>
<p>코드 설계 부분은 단순한 2중 for문 구현이므로 생략하였다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] arr = new int[2];

        for (int i = 0; i &lt; nums.length; i++) {
            for (int j = i + 1; j &lt; nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    arr[0] = i;
                    arr[1] = j;
                    break;
                }
            }
        }
        return arr;
    }
}</code></pre>
<p>💡if문에서 <code>return new int[]{i, j};</code>하면 static array를 선언하지 않고도 바로 배열의 인덱스들을 리턴할 수 있다.</p>
<p>📓자바에서 배열을 선언하는 방법을 다시 살펴보자.</p>
<ol>
<li><p>배열 선언 후 값 할당: 우값에 크기 지정은 필수다.</p>
<pre><code class="language-java">int[] arr = new int[2]; // 크기 2인 배열 선언
arr[0] = 1;
arr[1] = 3;</code></pre>
</li>
<li><p>배열을 선언과 동시에 초기화(new 필요)</p>
<pre><code class="language-java">int[] arr = new int[]{1, 3}; // 배열을 생성하면서 값을 즉시 초기화</code></pre>
</li>
<li><p>new없이 배열을 선언과 동시에 초기화(C 스타일)</p>
<pre><code class="language-java">int[] arr = {1, 3}; // 배열 선언과 동시에 초기화 (new 생략 가능)</code></pre>
</li>
</ol>
<p>💡return 문에서는 <code>return new int[]{i, j};</code>와 같이 new가 필요한데, 크기를 지정하는 것이 아니라 i와 j 값을 넣어 반환하기 때문</p>
<p>그러면 위의 코드를 다음과 같이 수정할 수 있다</p>
<pre><code class="language-java">class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i &lt; nums.length; i++) {
            for (int j = i + 1; j &lt; nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[] {i, j};
                }
            }
        }
        return new int[] {-1, -1}; // 리턴 타입이 int[]이라 넣어줘야함
    }
}</code></pre>
<hr />
<p>위에서는 2중 for문으로 완전 탐색으로 문제를 풀었지만, 이번에는 <strong>정렬 &amp; 투 포인터</strong>를 활용해서 시간복잡도를 더 낮춘 방법으로 문제를 풀어볼 것이다.</p>
<h1 id="⚙️정렬과-투-포인터를-활용한-코드-설계">⚙️정렬과 투 포인터를 활용한 코드 설계</h1>
<p>⚠️투 포인터는 <strong>항상 정렬된 상태에서 사용</strong>해야 한다.(binary search와 비슷한듯...?)</p>
<pre><code class="language-python"># 의사코드

# 1. 오름차순 정렬을 먼저 한다.
nums.sort() 

# 2. nums의 리스트에서 l = 0, r = len(nums) - 1로 설정한다.

# 3. 문제 조건에서 &quot;같은 원소를 2번 사용할 수 없다.&quot;라고 하였으므로 
# l이 r보다 작을 때까지 loop를 계속 반복시킨다.
while l &lt; r:
        if nums[l] + nums[r] == target:
            return [l, r]
        elif nums[l] + nums[r] &gt; target:
            r -= 1 # target보다 크기 때문에 오른쪽의 포인터를 왼쪽으로 이동 
        else:
            l += 1 # target보다 작으면 왼쪽의 포인터를 오른쪽으로 이동</code></pre>
<h1 id="💻코드-구현-1">💻코드 구현</h1>
<pre><code class="language-java">class TwoPointerSolution {
    public int[] twoSum(int[] nums, int target) {
        Arrays.sort(nums); // 오름차순 정렬

        int l = 0;
        int r = nums.length - 1;

        while (l &lt; r) {
            if (nums[l] + nums[r] == target) {
                return new int[] { l, r };
            } else if (nums[l] + nums[r] &gt; target) {
                r -= 1;
            } else {
                l += 1;
            }
        }
        return new int[] { -1, -1 };
    }
}</code></pre>
<p>🚩그런데 투 포인터는 정렬된 배열을 전제로 사용하는데, 문제는 배열을 오름차순으로 정렬해버리면 기존의 value가 저장된 인덱스도 수정된다는 것이다.
예) nums = [3,2,4], target = 6이면 nums.sort() -&gt; [2, 3, 4]가 되고 위의 코드를 실행하면 기댓값인 [1, 2]가 리턴되는 것이 아니라 [0, 2]가 리턴이 되서 submit되지 못한다.</p>
<p>🔨따라서 {num: index}를 저장하기 위한 <code>HashMap&lt;Integer, Integer&gt;</code>를 추가로 써서 기존의 num에 있는 index를 저장하도록 한다.</p>
<h1 id="💻hashmap을-사용한-코드-구현">💻HashMap을 사용한 코드 구현</h1>
<pre><code class="language-java">
    public int[] twoSum(int[] nums, int target) {
        // # {num: index}
        HashMap&lt;Integer, Integer&gt; map = new HashMap&lt;&gt;();
        for (int i = 0; i &lt; nums.length; i++) {
            map.put(nums[i], i);
        }

        Arrays.sort(nums); // 오름차순 정렬

        int l = 0;
        int r = nums.length - 1;

        while (l &lt; r) {
            if (nums[l] + nums[r] == target) {
                return new int[] {map.get(nums[l]), map.get(nums[r])};
            } else if (nums[l] + nums[r] &gt; target) {
                r -= 1;
            } else {
                l += 1;
            }
        }
        return new int[] { -1, -1 };
    }</code></pre>
<p>🚩여기서 또 문제가 되는게, HashTable(또는 HashMap)은 같은 키 값은 저장하지 못하기 때문에 만약 중복되는 키가 존재하면 value를 업데이트 시켜버린다.</p>
<p>🔨따라서, 2차원 배열을 HashMap처럼 구현하는 수 밖에 없다.</p>
<p>🤔프로그래머스 레벨0의 등수 매기기는 딕셔너리로 풀이가 가능했는데, two sum은 불가능한 이유가 뭘까?
👉&quot;등수 매기기&quot; 문제는 공동 등수를 duplicate index로 처리가 가능한 반면, <strong>two sum 문제는 각 value마다 index가 다르기 때문</strong>에 HashMap으로는 duplicate element를 처리할 수 없다.</p>
<h1 id="💻2차원-배열을-활용한-코드-구현">💻2차원 배열을 활용한 코드 구현</h1>
<pre><code class="language-java">
    public int[] twoSum(int[] nums, int target) {
        // 2D를 HashMap처럼 구현(Key: arr[0] = num, Value: arr[1] = index)
        int[][] numIndex = new int[nums.length][2]; // {num: index}가 nums의 길이만큼 있음
        for (int i = 0; i &lt; nums.length; i++) {
            numIndex[i][0] = nums[i];
            numIndex[i][1] = i;
        }
        // 오름차순 정렬(based on num)
        Arrays.sort(numIndex, (o1, o2) -&gt; {
            return o1[0] - o2[0];
        }); 

        int l = 0;
        int r = nums.length - 1;

        while (l &lt; r) {
            if (numIndex[l][0] + numIndex[r][0] == target) {
                return new int[] {numIndex[l][1], numIndex[r][1]};
            } else if (numIndex[l][0] + numIndex[r][0] &gt; target) {
                r -= 1;
            } else {
                l += 1;
            }
        }
        return new int[] { -1, -1 };
    }</code></pre>
<p>💡2차원 배열은 그대로 Arrays.sort(arr)를 사용하면 <code>java.lang.ClassCastException: class [I cannot be cast to class java.lang.Comparable</code>라는 <span style="color: red;"><strong>런타임 에러</strong></span>가 발생하는데, 이유는 1차원 배열은 앞뒤의 데이터만 비교해야하는 것과 달리, <strong>2차원 배열은 row, col 등 여러 개를 비교</strong>해줘야 하기 때문이다.
👉따라서, 람다식을 활용하여 Comparable 인터페이스를 구현하여 sort의 인자로 넣는다.(∵람다식은 일급객체로 취급하기 때문에 함수의 인자로 넣을 수 있음)</p>
<p>💡위의 리턴문에서 o1과 o2의 위치를 바꾸면 내림차순 정렬로 구현이 가능하다.</p>
<hr />
<p>📚참고자료
🔗2차원 배열 정렬하기: <a href="https://angelplayer.tistory.com/452">https://angelplayer.tistory.com/452</a></p>