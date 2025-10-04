<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/87390">https://school.programmers.co.kr/learn/courses/30/lessons/87390</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>정수 n, left, right가 주어집니다. 다음 과정을 거쳐서 1차원 배열을 만들고자 합니다.</p>
<pre><code class="language-txt">1. n행 n열 크기의 비어있는 2차원 배열을 만듭니다.

2. i = 1, 2, 3, ..., n에 대해서, 다음 과정을 반복합니다.
    2.1. 1행 1열부터 i행 i열까지의 영역 내의 모든 빈 칸을 숫자 i로 채웁니다.

3. 1행, 2행, ..., n행을 잘라내어 모두 이어붙인 새로운 1차원 배열을 만듭니다.

4. 새로운 1차원 배열을 arr이라 할 때, arr[left], arr[left+1], ..., arr[right]만 남기고 나머지는 지웁니다.</code></pre>
<p>정수 n, left, right가 매개변수로 주어집니다. 주어진 과정대로 만들어진 1차원 배열을 return 하도록 solution 함수를 완성해주세요.</p>
<h2 id="🚧제약조건">🚧제약조건</h2>
<ul>
<li>1 ≤ n ≤ 10^7</li>
<li>0 ≤ left ≤ right &lt; n^2</li>
<li>right - left &lt; 10^5</li>
</ul>
<hr />
<p>난, 제약조건 최대 데이터 개수를 보고, 이 문제는 2차원 배열로 초기화하는 방식으로 풀면 망한다는 것을 알았다. 따라서, <strong>1차원 배열로 초기화하되, 2번 과정의 규칙을 파악</strong>해서 1차원 배열에 값을 저장해야 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/3d8cd366-ed44-4f9a-b06e-e429d630a312/image.jpg" />
위의 노트 풀이와 같이, 2차원 배열 형태의 값은 군수열로 채워진다는 것을 알 수 있다. 그리고 군수열로부터 얻은 정보들은 다음과 같다.</p>
<p>📢편의를 위해 인덱스는 1부터 시작하고, 0 인덱스는 더미 슬롯으로 지정한다.</p>
<ol>
<li>2차원 배열의 총 원소 개수: $$1 + 3 + 5 + ... + 2n - 1 ≒ n^2$$ (∵등차수열의 합)</li>
<li>시작 인덱스: $$1, 2, 5, ..., ≒ n^2 - 2n + 2$$</li>
<li>마지막 인덱스: $$1, 4, 9, ..., ≒ n^2$$</li>
</ol>
<p>위의 정보들을 바탕으로 2번 과정까지를 코드로 구현하면 아래와 같다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public int[] solution3(int n, long left, long right) {
        // 1, 3, 5, ..., 2k-1 씩 i의 개수가 커짐
        // A.P. = n^2
        int[] oriArr = new int[n * n + 1]; // 0 인덱스는 더미 슬롯

        // 2. 1행 1열부터 i행 i열까지의 영역 내의 모든 빈 칸을 숫자 i로 채웁니다.
        // n은 행(군)을 의미
        for (int i = 1; i &lt; n + 1; i++) {
            // 개수만큼 반복
            for (int j = i * i - 2 * i + 2; j &lt;= i * i; j++) {
                oriArr[j] = i;
            }
        }

        /*// 3. 1행, 2행, ..., n행을 잘라내어 모두 이어붙인 새로운 1차원 배열을 만듭니다.
        int[] newArr = new int[n * n + 1];

        for (int i = 1; i &lt; n + 1; i++) {

            // 대각성분의 인덱스를 기준


            // newArr의 인덱스
            for (int j = 3 * i - 2; j &lt;= 3 * i; j++) {

            }
        }*/

        return oriArr;
    }</code></pre>
<hr />
<p>😵‍💫대각성분으로 행을 기준으로 newArr에 값을 할당하려고 하면, 마찬가지로 n번 만큼 순회를 또 해야하기 때문에 과감하게 포기하고 AI의 도움을 받았다.</p>
<h1 id="📜수정된-문제-해석">📜수정된 문제 해석</h1>
<ol>
<li><p>n×n 가상 배열을 특정 규칙으로 채운 뒤, 이를 행 순서로 1차원 전개한 배열 arr에서 구간 <code>arr[left..right]</code>만 반환한다.</p>
</li>
<li><p>규칙 요약: 가상의 좌표를 0-index로 보면, (row, col) 위치의 값은 <code>max(row, col) + 1</code> 이다.</p>
</li>
<li><p>전개 인덱스 k는 가상 좌표 (row, col) = (k / n, k % n)와 대응한다.</p>
</li>
</ol>
<h2 id="🤔1차원-배열로-나타냈을-때의-row-col-공식">🤔1차원 배열로 나타냈을 때의 row, col 공식</h2>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/1ca0c4d7-aa64-42dc-9606-140409fbd540/image.jpg" />
그러니까 결국 <strong>2차원 배열은 col들을 row만큼 순회하는 구조</strong>고, 배열은 zero-index based이기 때문에 반복 == '%' 연산자, row == '/' 연산자라는 것을 쉽게 이해할 수 있다.</p>
<h2 id="🚧제약조건에서-파악해야-할-사항">🚧제약조건에서 파악해야 할 사항</h2>
<ul>
<li><code>1 ≤ n ≤ 10^7</code>: 2차원 배열을 생각하면 망함!</li>
<li><code>0 ≤ left ≤ right &lt; n^2</code>: 인덱스는 long 연산으로 안전하게 처리. k = left + i는 long 사용 권장(오버플로우 방지)</li>
<li><code>right - left &lt; 10^5</code>: 반환 구간의 길이는 최대 $$10^5$$고, 이 구간만 $$O(1)$$ 계산으로 채워야 함. (e.g. arithmathic operator를 통한 값 저장 또는 return 문)</li>
</ul>
<h1 id="⚙️수정된-코드-설계">⚙️수정된 코드 설계</h1>
<ol>
<li><p>전체 1차원 배열을 초기화하지 말고, 결과 배열 크기가 
$$left &lt;= x &lt;= right$$이므로 right - left + 1로 1차원 배열 크기를 설정한다.</p>
</li>
<li><p>i = 0...len-1에 대해 전개 인덱스 k를 left + i로 잡는다.
👉이때, left와 right는 long 타입이기 때문에 k도 마찬가지로 long 타입으로 설정한다.</p>
</li>
<li><p>k를 2D 좌표로 변환: row = k / n, col = k % n</p>
</li>
<li><p>값 규칙 적용: val = Math.max(row, col) + 1 (∵zero-indexed 기준으로 대각성분은 모두 i로 채워야 한다.)</p>
</li>
</ol>
<h1 id="💻수정된-코드-구현">💻수정된 코드 구현</h1>
<pre><code class="language-java">public int[] solution(int n, long left, long right) {
        // left &lt;= x &lt;= right의 닫힌 구간
        int[] answer = new int[(int) (right - left + 1)];

        for (int i = 0; i &lt; answer.length; i++) {
            long k = i + left; // 인덱스 k 설정
            int row = (int) (k / n);
            int col = (int) (k % n);

            // 값을 채움(zero-indexed 기준)
            // 대각성분은 row, col이 같고 i = 1부터 시작하기 때문에 1을 더함
            answer[i] = Math.max(row, col) + 1;
        }

        return answer;
    }</code></pre>
<ul>
<li>left, right는 long 타입인데, 리턴 타입은 <code>int[]</code>를 요구하기 때문에 int로 캐스팅하였다.</li>
<li>마찬가지로 row, col을 설정할 때도 int로 캐스팅하였다.</li>
</ul>
<h1 id="✍️교훈">✍️교훈</h1>
<ol>
<li><strong>2차원 배열</strong>은 결국 &quot;1차원 배열의 col들을 row만큼 순회한 구조&quot;이기 때문에 <strong>인덱스로 쉽게 접근</strong>할 수 있다.
1.1. 문제의 요구사항을 잘 파악하면 굳이 1차원 배열 전체에 값을 넣지 않고도 주어진 요구사항에 맞게 변형된 배열을 리턴할 수 있다.<br /></li>
<li>문제에서 주어진 int형 n과, long형 left, right의 <strong>파라미터를 유심히 살펴야 오버플로우를 방지</strong>할 수 있다.</li>
</ol>