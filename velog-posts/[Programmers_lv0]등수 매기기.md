<p>📢먼저 본인은 <strong>원래 배열 순서를 유지하면서 등수를 매기는 아이디어</strong>를 생각하지 못했기 때문에, 이와 비슷한 &quot;진료 순서 정하기&quot; 문제를 다시 복기하려고 한다.
자세한 내용은 아래의 링크를 참조할 것👇
<a href="https://velog.io/@csj0209/Programmerslv0%EC%A7%84%EB%A3%8C%EC%88%9C%EC%84%9C-%EC%A0%95%ED%95%98%EA%B8%B0">https://velog.io/@csj0209/Programmerslv0%EC%A7%84%EB%A3%8C%EC%88%9C%EC%84%9C-%EC%A0%95%ED%95%98%EA%B8%B0</a></p>
<p>간단히 코드 설계를 정리하면:</p>
<ol>
<li>응급도가 높은 순서로 내림차순 정렬한다</li>
<li>정렬한 리스트에 대해 진료 순서를 index + 1로 정한다</li>
<li>딕셔너리를 이용해서 진료 순서를 불러온다.
💡딕셔너리는 해시테이블을 기반으로 만들어진 자료구조이기 때문에 <strong>순서에 상관없이 Key로만 Value에 접근</strong>할 수 있다.</li>
</ol>
<hr />
<h1 id="📜문제-해석">📜문제 해석</h1>
<ol>
<li>영어 점수와 수학 점수의 평균 점수를 기준으로 </li>
<li>학생들의 등수를 매기려고 합니다.
영어 점수와 수학 점수를 담은 2차원 정수 배열 score가 주어질 때, <strong>영어 점수와 수학 점수의 평균을 기준</strong>으로 매긴 등수를 담은 배열을 return</li>
</ol>
<p>🚧제약 조건</p>
<ol>
<li>0 ≤ score[0], score[1] ≤ 100</li>
<li>1 ≤ score의 길이 ≤ 10</li>
<li>score의 원소 길이는 2입니다.</li>
<li>score는 중복된 원소를 갖지 않습니다.</li>
</ol>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<ol>
<li>먼저 각 학생의 평균 점수를 계산한다.</li>
<li>문제의 조건이 평균 점수를 기준으로 등수를 매기는 것이라서, 평균 점수로 만든 리스트를 내림차순 정렬한다.</li>
<li>등수를 매기기 위한 딕셔너리를 만들고, &quot;진료순서 정하기&quot;와 비슷하게 for문을 작성한다.
3-1. 딕셔너리에 없을 때만 Value를 정하고, 그렇지 않으면 넘긴다.</li>
<li><strong>평균 점수를 기준</strong>으로 딕셔너리를 사용해서 학생들의 등수를 리턴</li>
</ol>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-python">def solution(score):
    # 1. 각 학생의 평균 점수를 계산
    score_avg = [(e + m) / 2 for e, m in score]
    # 2. 내림차순 정렬
    score_avg_sorted = sorted(score_avg ,reverse=True)
    # 3. 등수를 매기기위한 딕셔너리
    rank_map = {} # {score_val: idx + 1}

    for idx, score_val in enumerate(score_avg_sorted):
        # 중복 처리(동일한 key가 있으면 value는 업데이트되기 때문)
        if score_val not in rank_map:
            rank_map[score_val] = idx + 1
    # 평균 점수를 기준으로 학생들의 등수를 리턴
    return [rank_map[score_val] for score_val in score_avg]</code></pre>
<ul>
<li>딕셔너리 네이밍은 편하게 ~map으로 작성한다.</li>
<li>마지막의 return문에서 <strong>평균 점수를 기준</strong>으로 하기 때문에 평균 점수를 구한 score_avg에서 순회하게 한다.<ul>
<li>&quot;진료순서 정하기&quot;의 emergency 배열이 내림차순 정렬하기 전의 score_avg 배열과 같다고 생각하면 쉬움</li>
</ul>
</li>
</ul>