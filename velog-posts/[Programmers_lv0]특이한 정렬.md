<h1 id="📜문제-해석">📜문제 해석</h1>
<ol>
<li>이때 n으로부터의 거리가 같다면 &quot;더 큰 수&quot;를 앞에 오도록 배치</li>
<li>정수가 담긴 배열 numlist와 정수 n이 주어질 때 numlist의 원소를 n으로부터 가까운 순서대로 정렬한 배열을 return</li>
</ol>
<p>🚧제약 조건</p>
<ol>
<li>numlist는 중복된 원소를 갖지 않습니다.</li>
<li>1 ≤ n ≤ 10,000, 1 ≤ numlist의 원소 ≤ 10,000 </li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>1번 문제 요구사항에 의해 numlist로부터 n까지의 거리를 계산한다.</li>
</ol>
<ul>
<li><code>for i in numlist: abs(n - i)</code> // 거리는 0 이상을 가지므로</li>
</ul>
<ol start="2">
<li>더 큰 수를 앞에 오도록 배치하기 위해, numlist와 거리 배열에 대한 <strong>mapping 관계</strong>가 있어야함</li>
</ol>
<ul>
<li>이를테면, 거리가 1인 원소 3과 5가 있을 때, 더 큰 수는 5이므로 5를 앞에 배치한다.
2-1. 딕셔너리는 단방향 해시함수를 가진다. 그렇다면, 역으로 거리가 같을 때 <strong>더 큰 수를 앞에 배치</strong>하기 위해서는...?<ul>
<li>(num, length) 튜플로 저장하고, <code>if 거리가 같으면: swap을 이용해서 더 큰 num을 앞에 배치</code>
2-2. 절댓값은 양수 또는 음수 2개의 값밖에 갖지 않기 때문에 stable한 원소는 2개 </li>
<li><strong>컨트롤 제트의 아이디어</strong>를 활용해서 prev로 이전의 값 저장 후, 다음 순회하는 값과 비교</li>
</ul>
</li>
</ul>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(numlist, n):
    num_len_arr = [(i, abs(n - i)) for i in numlist] # [num, length]

    # n과 가까운 거리를 기준으로 오름차순 정렬
    len_sorted_ls = sorted(num_len_arr, key=lambda num: num[1])
    # len_ls를 순회하는데 거리가 같으면 더 큰 수 앞에 배치
    for i in range(len(len_sorted_ls)):
        prev_len = len_sorted_ls[i][1]
        # list index out of range 문제 핸들링
        if i == len(len_sorted_ls) - 1:
            break
        # 거리가 같으면 더 큰 수 앞에 배치
        if prev_len == len_sorted_ls[i + 1][1]:
            # 스왑
            temp = len_sorted_ls[i]
            len_sorted_ls[i] = len_sorted_ls[i + 1]
            len_sorted_ls[i + 1] = temp
    return [num for num, len in len_sorted_ls]</code></pre>
<ul>
<li><code>sorted(iterable, key, reverse: boolean)</code>에서 iterable 객체에 있는 원소에 대한 key를 기준으로 iterable 객체를 정렬할 수 있다. 나는 여기서 거리를 기준으로 정렬을 해서 len_sorted_ls라는 새로운 리스트에 할당했다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/e1f7c97b-ebda-497e-b256-6754ac5c4a63/image.png" /></p>
<p>🤔Edge case를 계속해서 고민했더니, 거리가 같아도 <strong>이미 큰 수가 앞에 있으면 swap을 하기 때문에 거기에서 오류가 발생</strong>했던 것이다.</p>
<p>👇따라서 수정한 코드는 아래와 같고, 성공적으로 제출을 하였다.</p>
<h1 id="💻수정한-코드">💻수정한 코드</h1>
<pre><code class="language-python">def solution(numlist, n):
    num_dist = [(i, abs(n - i)) for i in numlist]  # [num, length]

    # n과 가까운 거리를 기준으로 오름차순 정렬
    dist_sorted_ls = sorted(num_dist, key=lambda num: num[1])
    # len_ls를 순회하는데 거리가 같으면 더 큰 수 앞에 배치
    for idx, (num, dist) in enumerate(dist_sorted_ls):
        # list index out of range 문제 핸들링
        if idx == len(dist_sorted_ls) - 1:
            break

        next_dist = dist_sorted_ls[idx + 1][1]
        next_num = dist_sorted_ls[idx + 1][0]
        # 거리가 같고 작은 수가 앞에 있으면 스왑
        # 거리가 같은데 이미 큰 수가 앞에 있으면 스왑 x
        if dist == next_dist and next_num &gt; num:
            # 스왑
            dist_sorted_ls[idx], dist_sorted_ls[idx + 1] = dist_sorted_ls[idx + 1], dist_sorted_ls[idx]
    return [num for num, dist in dist_sorted_ls]</code></pre>
<p>💡핵심은, <code>if dist == next_dist and next_num &gt; num:</code>로 거리가 같고 <strong>작은 수가 앞에 있을 때만 스왑</strong>을 시켜주는 것이였다.</p>
<ul>
<li>추가로, <code>enumerate()</code>를 이용해서 리스트_튜플을 복잡하게 인덱싱해서 비교하는 것이 아니라, value를 현재값으로 저장하고, idx를 활용해서 비교하게 수정하였다.</li>
</ul>
<p>💡파이썬의 튜플 언패킹을 활용해서 스왑을 구현하였다.</p>
<ul>
<li><code>a, b = b, a</code></li>
</ul>
<p>위의 복잡한 로직을 sorted(iterable, key)를 이용해서 다음과 같이 간단하게 구현할 수도 있다.</p>
<h1 id="🤖sorted를-이용한-코드-구현">🤖sorted()를 이용한 코드 구현</h1>
<pre><code class="language-python">def solution(numlist, n):
    # Sort by distance (ascending), then by number (descending) for equal distances
    return sorted(numlist, key=lambda x: (abs(x - n), -x))</code></pre>
<ul>
<li>key의 람다 x는 numlist의 원소를 key로 설정한 값이고, 먼저 거리를 기준으로 정렬한 다음, 더 큰 수를 앞에 배치하기 위해 원소에 마이너스를 씌워서 <code>[-5, -3]</code>처럼 앞에 오게 하였다.</li>
</ul>
<hr />
<p>📚참고자료
🔗sorted의 key 함수: <a href="https://docs.python.org/ko/3.13/howto/sorting.html">https://docs.python.org/ko/3.13/howto/sorting.html</a>
🔗파이썬 스왑: <a href="https://rshday.tistory.com/entry/Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC-swap-%EB%91%90-%EA%B0%9C%EC%9D%98-%EB%B3%80%EC%88%98%EB%A5%BC-%EA%B5%90%ED%99%98%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95">https://rshday.tistory.com/entry/Python-%ED%8C%8C%EC%9D%B4%EC%8D%AC-swap-%EB%91%90-%EA%B0%9C%EC%9D%98-%EB%B3%80%EC%88%98%EB%A5%BC-%EA%B5%90%ED%99%98%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95</a></p>