<h1 id="📜문제-해석">📜문제 해석</h1>
<p>3을 저주의 숫자라고 생각하기 때문에 3의 배수와 &quot;숫자 3&quot;을 사용하지 않습니다. 3x 마을 사람들의 숫자는 다음과 같습니다.</p>
<table>
<thead>
<tr>
<th>10진법</th>
<th>3x 마을에서 쓰는 숫자</th>
<th>10진법</th>
<th>3x 마을에서 쓰는 숫자</th>
</tr>
</thead>
<tbody><tr>
<td>1</td>
<td>1</td>
<td>6</td>
<td>8</td>
</tr>
<tr>
<td>2</td>
<td>2</td>
<td>7</td>
<td>10</td>
</tr>
<tr>
<td>3</td>
<td>4</td>
<td>8</td>
<td>11</td>
</tr>
<tr>
<td>4</td>
<td>5</td>
<td>9</td>
<td>14</td>
</tr>
<tr>
<td>5</td>
<td>7</td>
<td>10</td>
<td>16</td>
</tr>
</tbody></table>
<p>정수 n이 매개변수로 주어질 때, n을 3x 마을에서 사용하는 숫자로 바꿔 return하는 함수를 만들어라.</p>
<p>🚧제약 조건</p>
<ol>
<li>1 ≤ n ≤ 100</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>처음에는 위의 표를 이용해서 딕셔너리로 초기화해야 겠다고 생각했다. 그런데, n = 15일 경우에 이전의 값인 <code>dict_3x[14]</code>의 value가 없어서 에러가 발생했기 때문에 자연스럽게 Bottom-up DP가 생각났다.</p>
<p>먼저 저주의 숫자 3을 피하기 위해서는 다음과 같은 케이스 분류가 필요하다.
조건 1. 3의 배수 =&gt; <code>if n % 3 == 0:</code>
조건 2. 숫자 3을 사용하지 x =&gt; <code>if '3' in str(n)</code></p>
<p>이걸로는 부족해서 직접 노트에다가 1부터 19까지 {decimal_val: 3x_val}을 나열했는데, 9 -&gt; 12(X), 13(X), 14(O)와 같이 <strong>조건 1)이거나 조건 2)일 경우에 1씩 더한다</strong>는 것을 발견했다.
👉OR 조건으로 처리</p>
<p>위의 알고리즘을 코드로 구현하면 다음과 같다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(n):
    dict_3x = {1: 1, 2: 2}

    for i in range(3, n + 1):
        dict_3x[i] = dict_3x[i - 1] + 1

        while dict_3x[i] % 3 == 0 or &quot;3&quot; in str(dict_3x[i]):
            dict_3x[i] += 1
    return dict_3x[n]</code></pre>
<ul>
<li><code>while dict_3x[i] % 3 == 0 or &quot;3&quot; in str(dict_3x[i]):</code>이렇게 작성한 이유는 19 -&gt; 30(X), 31(X), ..., 39(X), 40(O)이 되기 때문에 while 반복문으로 처리를 했다.</li>
</ul>