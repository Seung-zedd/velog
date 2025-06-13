<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42576">https://school.programmers.co.kr/learn/courses/30/lessons/42576</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<p>parameter: 마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion
문제 요구사항: 완주하지 못한 선수의 이름을 return하라.(단, 한 명의 선수의 이름만 리턴하면 된다.)</p>
<p>🚧제약 조건</p>
<ol>
<li><p>마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
=&gt; $$O(10^5)$$, 그런데 해시맵은 조회/삽입/삭제가 $$O(1)$$만 걸림
즉, 이중 for문을 사용하지 말라는 의미!
but, sort를 사용해서 $$O(nlogn) = 10^7$$이라서 성공은 할 수 있음. 그런데 간당간당함
=&gt; 최종적으로 해시를 사용!</p>
</li>
<li><p>completion의 길이는 participant의 길이보다 1 작습니다.
=&gt; uncompleted = participant.length - completion.length</p>
</li>
<li><p>참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.</p>
</li>
<li><p>참가자 중에는 &quot;동명이인&quot;이 있을 수 있습니다.</p>
</li>
</ol>
<ul>
<li>🔖동명이인(同名異人): 같은 이름을 가진 다른 사람(2명 이상이 될 수 있다는 말)<ul>
<li>Key: participant, Value: completion으로 저장하는데, 만약에 같은 Key 이름을 가진 사람이 둘다 완주를 했다면...?
hash collision이 발생! -&gt; 추가적인 장치가 필요
먼저 $$O(N)$$으로 participant를 순회해서 count가 2 이상일 떄만 flag를 설정해서 케이스 분류</li>
</ul>
</li>
</ul>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p><img alt="" src="https://velog.velcdn.com/images/csj0209/post/2c5017d0-bbbe-43bd-9cf4-278dd8fe7b22/image.png" /></p>
<ul>
<li>먼저 명단을 매칭하기 위해 참가자, 완주자 배열을 정렬한다.</li>
<li>참가자 배열로 for문을 돌리면 IndexOfOutBound 예외가 터지기 때문에 완주자 배열로 순회하게 하였다.<ul>
<li>이때, 해시맵에 &lt;참가자, (완주자, count)&gt;를 삽입하는데, 참가자 명단이 이미 있으면 count를 증가시킨다.</li>
</ul>
</li>
<li>위의 사진과 같이** 완주하지 못한 경우를 2가지**로 나눌 수가 있는데, 동명이인이 아닌 사람이 완주하지 못한 경우와 동명이인 중 한 사람만 완주하지 못한 경우로 나눌 수가 있다.<ul>
<li>이때, 완주자 배열의 길이로 map을 연산했기 때문에 for문이 끝난 이후에 끝 명단을 map에 채운다.</li>
</ul>
</li>
</ul>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class MarathonSolution {
    public String solution(String[] participant, String[] completion) {
        // 명단을 매칭하기 위해 먼저 정렬을 함
        Arrays.sort(participant, (s1, s2) -&gt; s1.compareTo(s2));
        Arrays.sort(completion, (s1, s2) -&gt; s1.compareTo(s2));

        // {part: (comp, count)}
        HashMap&lt;String, Mark&gt; map = new HashMap&lt;&gt;();
        String answer = &quot;&quot;;

        // 명단 작성
        // 완주자 명단이 참가자 명단보다 1 작으므로 완주자 명단으로 순회
        for (int i = 0; i &lt; completion.length; i++) {
            // 중간에 구멍
            if (completion[i].isEmpty()) {
                answer = participant[i];
                break;
            }

            // 중간에 구멍이 없으면 map을 채움
            else if (!map.containsKey(participant[i])) {
                map.put(participant[i], new Mark(completion[i], 1));
            } else {
                Mark mark = map.get(participant[i]);
                mark.count++; // 이미 참가자 명단에 있으면 count를 증가
            }

        }

        // 끝 명단을 map에 채움(단, 참가자 마지막 명단과 완주자 마지막 명단이 같을 때만)
        if (!map.containsKey(participant[participant.length - 1]) &amp;&amp; participant[participant.length - 1].equals(completion[completion.length - 1])) {
            map.put(participant[participant.length - 1], new Mark(completion[completion.length - 1], 1));
        }

        // 동명이인 중에 완주를 못한 경우
        if (!map.isEmpty()) {
            for (Mark mark : map.values()) {
                if (mark.count &gt; 1) {
                    answer = mark.comp;
                }
            }
        }

        // 맨 끝에 구멍
            if (!participant[participant.length - 1].equals(completion[completion.length - 1])) {
            answer = participant[participant.length - 1];
        }
            // 참가자 수가 1명인 경우(==완주자 수 0명)
        if (completion.length == 0) {
            answer = participant[0];
        }

        return answer;

    }

    private class Mark {
        String comp;
        int count;

        public Mark(String comp, int count) {
            this.comp = comp; // 완주자 명단
            this.count = count;
        }
    }
}</code></pre>
<ul>
<li>이전 LCA문제의 커스텀 클래스를 활용해서 맵의 Value에 count도 넣게 하였다.</li>
</ul>
<p>🚩그런데, 위의 코드는 정렬 알고리즘과 해시맵을 둘다 사용했을 뿐 아니라 불필요하게 조건문이 많고 동명이인 중에 완주하지 못한 사람을 찾기 위해 또 for문을 사용해서 시간복잡도를 높혔다.</p>
<p>🔨따라서 위의 코드는 폐기하고, <strong>정렬 후 비교</strong> 아이디어만 가져와서 정렬 알고리즘만으로 풀 수가 있다.</p>
<h1 id="🤖수정된-코드-구현정렬">🤖수정된 코드 구현(정렬)</h1>
<pre><code class="language-java">import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        Arrays.sort(participant);
        Arrays.sort(completion);

        for (int i = 0; i &lt; completion.length; i++) {
            if (!participant[i].equals(completion[i])) {
                return participant[i];
            }
        }
        // 마지막 사람이 완주하지 못한 경우
        return participant[participant.length - 1];
    }
}</code></pre>
<ul>
<li>완주자 배열까지 순회하는데, String은 참조 타입이라 리터럴 값을 비교할 때는 <code>equals()</code>를 사용해야 한다.</li>
<li>for문을 빠져나가고 마지막 사람만 완주하지 못했으면 참가자의 마지막 인덱스에 있는 값을 리턴한다.</li>
</ul>
<p>💡<code>Arrays.sort()</code>는 <strong>기본이 오름차순</strong>이기 때문에 굳이 compareTo로 Comparator 인터페이스를 오버라이딩하지 않아도 된다.</p>
<p>⏰정렬을 하기 때문에  $$O(nlogn)$$가 걸린다.</p>
<hr />
<p>그런데, 문제의 카테고리가 해시라서 해시맵을 사용해서 풀 수도 있다. 즉, <strong>해시맵은 순서가 존재하지 않고</strong> 오로지 key를 통해서 hash function을 통해 value를 조회할 수 있기 때문에 굳이 <strong>정렬을 사용하지 않아도 된다.</strong></p>
<h1 id="🤖수정된-코드-구현해시맵">🤖수정된 코드 구현(해시맵)</h1>
<pre><code class="language-java">import java.util.*;

public class MarathonHashSolution {
    public String solution(String[] participant, String[] completion) {
        HashMap&lt;String, Integer&gt; map = new HashMap&lt;&gt;();
        // 참가자 명단을 카운팅
        for (String p : participant) {
            map.put(p, map.getOrDefault(p, 0) + 1);
        }
        // 완주자 명단을 카운팅에서 빼준다.
        for (String c : completion) {
            map.put(c, map.get(c) - 1);
        }
        // 값이 1로 남은 사람이 완주하지 못한 선수
        for (Map.Entry&lt;String, Integer&gt; e : map.entrySet()) {
            if (e.getValue() == 1) {
                return e.getKey();
            }
        }
        return &quot;&quot;;
    }

}</code></pre>
<ol>
<li>참가자 명단을 카운팅하는데, <code>map.getOrDefault(key, 0) + 1</code>으로 참가자 명단이 없으면 0에 1을 더하고, 그렇지 않으면 <strong>이미 있는 참가자 명단의 값에 1을 더함으로써 동명이인을 처리</strong>하였다.</li>
<li>완주자 명단을 카운팅에서 빼주는데, 동명이인의 완주자 명단을 처리하기 위해 map의 value에 <code>map.get(c) - 1</code>로 1을 빼준다.</li>
<li>값이 1로 남은 사람이 완주하지 못한 선수이므로, entry의 키를 리턴한다.</li>
</ol>
<p>🔖<code>map.getOrDefault(Object key, V defaultValue)</code>: 키에 매핑되는 값이 없으면 설정한 defaultValue를 리턴하고, 그렇지 않으면 키의 값을 리턴한다.</p>
<h1 id="📝깨달은-점">📝깨달은 점</h1>
<p>순서로 비교하기 위해서는 정렬을, <strong>순서와 상관없이</strong> 참가자 명단과 완주자 명단을 각각 카운팅하기 위해서는 <strong>해시맵</strong>을 사용한다는 것을 알았다.</p>
<p>❓풀어보면 좋은 문제: <a href="https://school.programmers.co.kr/learn/courses/30/lessons/120869">입문 - 외계어 사전</a></p>
<hr />
<p>📚참고자료
🔗<code>getOrDefault()</code>를 사용하는 이유, entrySet() vs keySet()의 속도 차이: <a href="https://betterdev.tistory.com/11">https://betterdev.tistory.com/11</a></p>