<p><a href="https://school.programmers.co.kr/learn/courses/30/lessons/42579">https://school.programmers.co.kr/learn/courses/30/lessons/42579</a></p>
<h1 id="📜문제-해석">📜문제 해석</h1>
<ol>
<li>&quot;장르 별&quot;로 가장 많이 재생된 노래를 2개씩 모아 베스트 앨범으로 출시한다.</li>
</ol>
<p>-&gt; 장르 별로 grouping한 다음, 재생횟수를 내림차순 정렬?
2. 노래는 고유 번호(=인덱스)로 구분</p>
<p>1) 재생 횟수가 많은 노래가 있는 &quot;장르&quot;를 먼저 수록
2) 장르 내에서 &quot;재생횟수&quot;가 많은 것을 먼저 수록
2-1) if 장르 내에서 재생 횟수가 같으면: &quot;인덱스&quot;가 낮은 노래를 먼저 수록</p>
<p>(정리)수록 순서: 장르 별 재생 횟수 &gt; 장르 내 재생횟수(재생횟수 같으면 인덱스 오름차순 정렬)</p>
<p>📌문제 요구사항: 노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호(인덱스)를 순서대로 return하라.</p>
<p>🚧제약 조건</p>
<ol>
<li>genres[i]는 고유번호가 i인 노래의 장르입니다.</li>
<li>plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.</li>
<li>genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.</li>
</ol>
<p>-&gt; 최대 $$10^4$$인데, 위의 수록 기준 대로 이중 for문을 사용하면 시간 초과! =&gt; <strong>&quot;해시맵&quot;</strong>을 사용하자. &amp;&amp; $$nlogn == 10^6$$이므로 필요하다면 <strong>&quot;정렬&quot;</strong>도 사용 가능</p>
<ol start="4">
<li>장르 종류는 100개 미만입니다.</li>
<li>장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.</li>
<li>⚠️모든 장르 별 재생 횟수는 unique하다.(e.g. classic 별 재생 횟수 != pop 별 재생 횟수)</li>
</ol>
<h2 id="입출력-예시">입출력 예시</h2>
<p><code>genres = [&quot;classic&quot;, &quot;pop&quot;, &quot;classic&quot;, &quot;classic&quot;, &quot;pop&quot;]</code><br /><code>plays = [500, 600, 150, 800, 2500]</code>라고 하자.
위의 수록 기준대로 처리를 한다면 다음과 같다:</p>
<ol>
<li>장르 별로 grouping한 다음, 재생횟수를 내림차순 정렬</li>
</ol>
<p>-&gt; <code>{&quot;pop&quot;: 3100}</code>, <code>{&quot;classic&quot;: 1450}</code>이 되므로, 내림차순 정렬하면 <code>{&quot;pop&quot;, &quot;classic&quot;}</code>이 된다.
2. 장르 내에서 &quot;재생횟수&quot;가 많은 것을 먼저 수록
-&gt; <code>{&quot;pop&quot;: {(2500, 4),  (600, 1)}</code>
🔎튜플의 우값은 <code>plays[i]</code>의 인덱스를 의미
<code>{&quot;classic&quot;: {(800, 3), (500, 0), (150, 2)}</code></p>
<ol start="3">
<li>가장 많이 재생된 노래를 <strong>2개</strong>씩 모아 베스트 앨범으로 출시</li>
</ol>
<p>-&gt; {4, 1, 3, 0}</p>
<h1 id="⚙️코드-설계">⚙️코드 설계</h1>
<p>이건 딱봐도 grouping과 sorting을 적절히 사용해서 풀으라는 문제기 때문에 SQL 문제에 더 적합하다 생각했다. 따라서 이전 문제 해석의 베스트 앨범 수록 과정을 쿼리로 풀면 다음과 같다.</p>
<blockquote>
<ol>
<li>장르 그룹화 및 장르별 정렬: GROUP BY genre... ORDER BY SUM(plays) DESC</li>
<li>장르 내 노래 정렬: ORDER BY playCount DESC, index ASC</li>
<li>상위 2개 선택: LIMIT 2</li>
</ol>
</blockquote>
<p>하지만, 문제가 해시 카테고리로 분류되어 있으니 해시맵으로 풀 경우에는 총합 집계용(장르 별 재생횟수) map, 데이터 그룹핑용(장르 내 재생횟수)mapMusic 2개의 해시맵이 필요하다.
💡이게 핵심인데, 위 2개 해시맵의 공통키인 <code>genre[i]</code>를 통해 map에서 mapMusic으로 전환해서 정렬 연산 처리가 가능하다.</p>
<h1 id="💻코드-구현">💻코드 구현</h1>
<pre><code class="language-java">public class BestAlbumSolution {
    public int[] solution(String[] genres, int[] plays) {
        // 장르 별 재생횟수를 위한 map
        Map&lt;String, Integer&gt; map = new HashMap&lt;&gt;();
        for (int i = 0; i &lt; genres.length; i++) {
            map.merge(genres[i], plays[i], Integer::sum);
        }

        // 1. 재생횟수를 기준으로 내림차순 정렬
        List&lt;Map.Entry&lt;String, Integer&gt;&gt; sortedGenres = new ArrayList&lt;&gt;(map.entrySet());
        sortedGenres.sort((e1, e2) -&gt; e2.getValue().compareTo(e1.getValue()));

        // 장르 내 재생횟수를 위한 mapMusic(장르 내 재생횟수가 다른 곡들이 있으므로 List&lt;Music&gt;으로 Value를 설정)
        Map&lt;String, List&lt;Music&gt;&gt; mapMusic = new HashMap&lt;&gt;();
        for (int i = 0; i &lt; genres.length; i++) {
            mapMusic.computeIfAbsent(genres[i], k -&gt; new ArrayList&lt;&gt;()).add(new Music(plays[i], i));
        }

        // 노래 고유번호를 담기 위한 리스트(장르 종류가 100개 미만으로 랜덤해서 정적 배열로 초기화 불가)
        List&lt;Integer&gt; answer = new ArrayList&lt;&gt;();

        // 2. 장르 내 재생횟수 내림차순 정렬(if 재생횟수 같으면 인덱스 오름차순 정렬)
        for (Map.Entry&lt;String, Integer&gt; e : sortedGenres) {
            String genre = e.getKey();

            List&lt;Music&gt; musicList = mapMusic.get(genre);
            musicList.sort((m1, m2) -&gt; {
                if (m1.playCount == m2.playCount) {
                    return m1.index - m2.index;
                } else {
                    return m2.playCount - m1.playCount;
                }
            });

            // 3. 각 장르 별로 최대 2곡의 고유 번호를 정답 리스트에 추가
            for (int i = 0; i &lt; Math.min(musicList.size(), 2); i++) {
                answer.add(musicList.get(i).index);
            }

        }

        return answer.stream().mapToInt(i -&gt; i).toArray();

    }

    private static class Music {
        int playCount;
        int index;

        public Music(int playCount, int index) {
            this.playCount = playCount;
            this.index = index;
        }


    }

}</code></pre>
<h2 id="📘자바-문법-정리">📘자바 문법 정리</h2>
<p>위에서 사용한 여러 가지 자바 문법들을 분석해보자.</p>
<p>🔖<code>Map.merge()</code>(instead of <code>Map.put(key, map.getOrDefault(key, int value))</code>): <strong>Map에 키(key)가 존재할 때와 존재하지 않을 때의 동작을 한 줄로 처리</strong>해주는 매우 편리한 메소드다.</p>
<p>if <code>genres[i]</code>라는 키가 map에 없으면: <code>plays[i]</code>를 값으로 하여 새로운 항목을 추가한다. (e.g., map.put(&quot;pop&quot;, 500))</p>
<p>else if <code>genres[i]</code>라는 키가 map에 있으면: 기존 값과 새로 들어온 값(<code>plays[i]</code>)을 <code>Integer::sum</code> 함수를 사용해 더한 결과로 기존 값을 대체한다.
🔎<code>Integer::sum</code>: 이것은 '메소드 참조(Method Reference)'라는 문법이다. <code>(기존값, 새값) -&gt; Integer.sum(기존값, 새값)</code>과 동일한 람다 표현식을 더 짧게 쓴 것이다. 즉, 두 숫자를 더하는 기능을 merge 메소드에 전달하는 것을 의미한다.</p>
<p>🔖<code>sortedGenres.sort(...)</code>: 리스트를 정렬하는 메소드
🔖<code>(e1, e2) -&gt; e2.getValue().compareTo(e1.getValue())</code>: 파라미터로 전달하는 e1, e2는 Map.Entry의 객체고, <strong>e2를 앞에 두었기 때문에 내림차순</strong>으로 정렬된다.
🔎<code>compareTo</code>는 앞의 값이 크면 양수, 같으면 0, 작으면 음수를 반환한다.</p>
<pre><code class="language-java">// 1. 재생횟수를 기준으로 내림차순 정렬
List&lt;Map.Entry&lt;String, Integer&gt;&gt; sortedGenres = new ArrayList&lt;&gt;(map.entrySet());
sortedGenres.sort((e1, e2) -&gt; e2.getValue().compareTo(e1.getValue()));</code></pre>
<p>🔖<code>Map.computeIfAbsent()</code>: 값이 없을 때만 mappingFunction이 호출되서 key=value 값을 해시맵에 추가해준다.</p>
<ul>
<li>자세한 내용은 <a href="https://velog.io/@csj0209/%ED%95%B4%EC%8B%9C%ED%85%8C%EC%9D%B4%EB%B8%94Hash-Table#%ED%95%B4%EC%8B%9C%EB%A7%B5%EC%9D%98-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%A2%85%EB%A5%98">해시맵의 메서드 종류</a>를 참고할 것!<pre><code class="language-java">Map&lt;String, List&lt;Music&gt;&gt; mapMusic = new HashMap&lt;&gt;();
for (int i = 0; i &lt; genres.length; i++) {
  mapMusic.computeIfAbsent(genres[i], k -&gt; new ArrayList&lt;&gt;()).add(new Music(plays[i], i));
}</code></pre>
</li>
<li>genres[i]라는 키가 mapMusic에 없으면, <code>k -&gt; new ArrayList&lt;&gt;()</code> 람다식을 실행하여 새로운 ArrayList를 생성하고, 그 리스트를 값으로 하여 mapMusic에 추가한다. 그리고 그 새로 생성된 리스트를 반환한다.<ul>
<li>genres[i]라는 키가 mapMusic에 이미 있으면, 아무것도 하지 않고 기존에 있던 Value인 <code>List&lt;Music&gt;</code>를 그대로 반환한다.</li>
</ul>
</li>
<li><code>.add(new Music(plays[i], i))</code>: computeIfAbsent가 반환한 <code>List&lt;Music&gt;</code>(새로 만들었거나, 기존에 있던 것)에 Music 객체를 추가한다.</li>
</ul>
<p>🔖Stream API : 데이터 처리를 메서드 체이닝을 활용해서 선언적으로 해줘서 복잡한 연산들을 처리해주는 기능</p>
<pre><code class="language-java">return answer.stream().mapToInt(i -&gt; i).toArray();</code></pre>
<ul>
<li><code>.mapToInt(i -&gt; i)</code>: 스트림의 각 요소(Integer 타입의 i)를 int 타입으로 변환(언박싱, unboxing)하여 새로운 IntStream을 생성한다.</li>
<li><code>.toArray()</code>: 최종 연산에 해당하고, IntStream의 모든 요소들을 <code>int[]</code> 배열로 만들어서 반환헤준다.</li>
</ul>
<hr />
<p>스트림API를 사용해서 풀어보자.</p>
<h1 id="💻코드-구현스트림-api">💻코드 구현(스트림 API)</h1>
<pre><code class="language-java">import java.util.*;
import java.util.stream.*;

public class BestAlbumStreamSolution {
    public int[] solution(String[] genres, int[] plays) {

        // 1. 초기 데이터 스트림 생성: genres, plays, index를 합쳐 Music 객체 스트림으로 변환
        return IntStream.range(0, genres.length)
                .mapToObj(i -&gt; new Music(genres[i], plays[i], i))

                // 2. 장르 별 그룹핑
                // Map&lt;String, List&lt;Music&gt;&gt; 생성
                .collect(Collectors.groupingBy(Music::getGenre))

                // 3. 그룹핑된 Map을 다시 스트림으로 변환하여 장르 별 정렬 준비
                .entrySet().stream()

                // 4. 장르 정렬 (ORDER BY SUM(plays) DESC)
                // 각 장르(entry)의 총 재생 횟수를 계산해서 내림차순으로 정렬
                .sorted((e1, e2) -&gt; Integer.compare(
                        e2.getValue().stream().mapToInt(Music::getPlay).sum(),
                        e1.getValue().stream().mapToInt(Music::getPlay).sum()
                ))

                // 5. 각 장르 내에서 노래 정렬 후, 상위 2곡 추출하고 하나의 스트림으로 합치기
                .flatMap(e -&gt; e.getValue().stream()

                        // 5-1. 장르 내 노래 정렬 (ORDER BY playCount DESC, index ASC)
                        .sorted(Comparator.comparing(Music::getPlay).reversed().thenComparing(Music::getIndex))
                        // 5-2. 상위 2개 선택 (LIMIT 2)
                        .limit(2)
                )

                // 6. 최종 결과에서 노래의 고유 변호(index)만 추출
                .mapToInt(Music::getIndex)
                // 7. int 배열로 변환하여 변환
                .toArray();

    }


    private static class Music {
        String genre;
        int play;
        int index;

        public Music(String genre, int play, int index) {
            this.genre = genre;
            this.play = play;
            this.index = index;
        }

        // 스트림에서 사용하기 편하도록 getter 함수 생성

        public String getGenre() {
            return genre;
        }

        public int getPlay() {
            return play;
        }

        public int getIndex() {
            return index;
        }
    }
}</code></pre>