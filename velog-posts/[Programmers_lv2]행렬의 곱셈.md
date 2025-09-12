<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/12949">https://school.programmers.co.kr/learn/courses/30/lessons/12949</a></p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>행렬의 곱을 단순하게 성분(인덱스로 접근)으로 접근하면, OutofIndex 에러가 나기 때문에 (행벡터) x (열벡터) 로 풀었다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class MatrixMulSolution {
    public int[][] solution(int[][] arr1, int[][] arr2) {
        // 1. 행렬 곱의 size = (m x k) x (k x n) = m x n
        int m = arr1.length;
        int n = arr2[0].length;
        int[][] answer = new int[m][n]; // 리턴할 정답 배열

        for (int i = 0; i &lt; m; i++) {
            for (int j = 0; j &lt; n; j++) {
                for (int k = 0; k &lt; Math.min(arr1.length, arr2.length); k++) {
                    answer[i][j] += arr1[i][k] * arr2[k][j];
                }
            }
        }

        return answer;
    }
}</code></pre>
<ul>
<li>위의 3중 for문은 (행벡터) x (열벡터)를 할 때, 각각의 성분을 곱해서 더할 때도 반복문이 필요하기 때문에 Index 에러를 안 내기 위해서 arr1, arr2 중 길이가 적은 값을 조건으로 설정하였다.</li>
</ul>
<p>그런데, 정확히 절반만 테스트 케이스를 통과하였다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/405db8df-e8ed-480e-a786-a50227d07328/image.png" /></p>
<hr />
<p>그래서 풀이 코드를 봤더니 다음과 같이 작성이 되어있다.</p>
<pre><code class="language-java">public class MatrixMulSolution {
    public int[][] solution(int[][] arr1, int[][] arr2) {
        // 1. 행렬 곱의 size = (m x k) x (k x n) = m x n
        int r1 = arr1.length;
        int c1 = arr1[0].length;
        int r2 = arr2.length;
        int c2 = arr2[0].length;
        int[][] answer = new int[r1][c2]; // 리턴할 정답 배열

        for (int i = 0; i &lt; r1; i++) {
            for (int j = 0; j &lt; c2; j++) {
                // 내부 차원에서의 성분 곱
                for (int k = 0; k &lt; c1; k++) {
                    answer[i][j] += arr1[i][k] * arr2[k][j];
                }
            }
        }

        return answer;
    }
}</code></pre>
<ul>
<li>2차원 행렬이 주어졌으면, 제일 먼저 <strong>행과 열을 변수에 저장</strong>할 것</li>
</ul>
<h2 id="🤔왜-k의-조건문이-c1이-되어야-하는가">🤔왜 k의 조건문이 c1이 되어야 하는가?</h2>
<p>세 번째 반복문의 k는 두 행렬이 “겹치는” 내부 차원(공유 차원)을 따라 도는 인덱스이기 때문에, 그 길이인 c1(= arr1의 열 수 = arr2의 행 수)을 상한으로 둬야하기 때문이다.</p>
<p>아래의 그림을 보면 행렬의 곱셈에 대한 이해를 명확하게 할 수가 있다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/45b07025-41f2-4a0c-9df2-5ffdcb203cc4/image.png" />
즉, 행렬 곱에 대한 크기는 외항에 해당하는 (m x n)이 되고, 공유 차원이 되는 k를 기준으로 곱한다.</p>
<h1 id="✍️깨달은-점">✍️깨달은 점</h1>
<ol>
<li>곱할 수 있는 행렬만 주어졌고, (m x k) 크기의 행렬 A와 (k x n) 크기의 행렬 B가 있을 때, <strong>행렬 곱에 대한 크기는 외항</strong>에 해당하는 (m x n)이다.</li>
<li>i번째 행과 j번째 열의 성분을 각각 곱해서 더하기 위해서는 <strong>내항에 해당하는 k</strong>를 기준으로 곱한다.</li>
</ol>