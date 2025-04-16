<h1 id="해시테이블hash-table">해시테이블(Hash Table)</h1>
<p>👉해시함수 h를 사용하여 <strong>key-value 쌍의 데이터</strong>를 입력받고, h에 <strong>key값을 입력으로 넣어 얻은 해시값 h(k)를 위치로 지정</strong>하여 key-value 데이터 쌍을 저장한다.
👍삽입, 삭제, 검색의 시간복잡도는 모두 <code>O(1)</code>이다.</p>
<p>주소화 방식은 크게 Direct-address Table과 Hash Table 2가지로 나뉜다.</p>
<h2 id="direct-address-table직접-주소화-테이블">Direct-address Table(직접 주소화 테이블)</h2>
<p>👉key값이 k인 데이터를 index k 위치에 저장하는 방식으로, ArrayList를 떠올리면 된다.</p>
<p>🚩그런데, 이러한 테이블 방식은 다음과 같은 문제점이 있다.</p>
<ol>
<li><p>불필요한 공간 낭비
인덱스를 기반으로 메모리에 저장하기 때문에 사용하지 않은 공간은 낭비가 될 수 있다.</p>
</li>
<li><p>Key값으로 문자열이 올 수 없다.
왜냐하면 인덱스는 int형만 허용하기 때문</p>
</li>
</ol>
<p>🔨위와 같은 문제점들을 해결하기 위해, Hash Table이라는 자료구조가 생겨났고, 해시 테이블은 다음과 같은 특징을 가진다</p>
<h2 id="해시-테이블의-특성">해시 테이블의 특성</h2>
<ol>
<li><p>모든 데이터에 <strong>key값은 무조건 존재</strong>해야 한다.</p>
</li>
<li><p>key값은 unique하기 때문에 <strong><span style="color: red;">중복되는 key값이 있어서는 안된다.</span></strong>
👉만약 duplicate key가 존재할 경우, 해시 테이블에서 collision을 일으켜서 예상치 못하게 기존의 value가 업데이트될 수 있다.</p>
<p>🔎해시 테이블에서 collision이 발생하면, 아래와 같이 open-addressing을 사용해서 해결할 수도 있지만, 보통은 linear-probing을 하기 때문에 free-slot(or space)를 탐색하기 위해 최악의 경우 O(N)이 걸릴 수 있다.
<img alt="" src="https://velog.velcdn.com/images/csj0209/post/89be522b-8365-41c8-afab-283d97339ed3/image.png" /></p>
</li>
</ol>
<h1 id="⏰시간복잡도-비교">⏰시간복잡도 비교</h1>
<p>Hash Table과 Doubly LinkedList, 그리고 Array의 시간복잡도를 아래의 표를 참고해서 비교해보자.</p>
<table>
<thead>
<tr>
<th>연산 / 구조</th>
<th>Hash Table</th>
<th>Doubly LinkedList</th>
<th>Array (Dynamic)</th>
</tr>
</thead>
<tbody><tr>
<td>access</td>
<td>O(1) (key)</td>
<td>O(N)</td>
<td>O(1) (index)</td>
</tr>
<tr>
<td>insert_front</td>
<td>❌</td>
<td>O(1)</td>
<td>O(N)</td>
</tr>
<tr>
<td>insert_back</td>
<td>❌</td>
<td>O(1)</td>
<td>O(1)</td>
</tr>
<tr>
<td>insert_at</td>
<td>❌</td>
<td>O(N) (노드 탐색)</td>
<td>O(N) (shift)</td>
</tr>
<tr>
<td>remove_front</td>
<td>❌</td>
<td>O(1)</td>
<td>O(N) (shift)</td>
</tr>
<tr>
<td>remove_back</td>
<td>❌</td>
<td>O(1)</td>
<td>O(1) amortized or O(N) if resizing needed</td>
</tr>
<tr>
<td>remove_at</td>
<td>❌</td>
<td>O(N) (노드 탐색)</td>
<td>O(N) (shift)</td>
</tr>
<tr>
<td>- <strong>해시 테이블</strong>은 위에서 말했다시피, 해시함수 h에 key값을 입력으로 넣어 얻은 해시값 h(k)를 위치로 지정하기 때문에 <strong>순서의 개념이 없어서</strong> 삽입/삭제, 검색의 시간복잡도는 O(1)이 된다.</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody></table>
<h1 id="자바의-해시테이블-hashmap">자바의 해시테이블: HashMap</h1>
<p>해시맵은 다음과 같이 생성할 수 있다:</p>
<pre><code class="language-java">HashMap&lt;Key, Value&gt; map = new HashMap&lt;&gt;();</code></pre>
<p>💡기본적으로 Primitive data type은 허용하지 않기 때문에, Wrapper class 또는 class type을 사용해야 한다.</p>
<p>그런데, 해시맵은 키-값 쌍의 개수에 따라 동적으로 크기가 증가하기 때문에 overhead가 크다. 따라서 초기에 저장할 데이터 개수를 알고 있다면 추가적으로 argument에 다음과 같이 _initialCapacity_와 _load factor_를 설정할 수도 있다.</p>
<pre><code class="language-java">HashMap&lt;Key, Value&gt; map = new HashMap&lt;&gt;(1000, 0.7f);</code></pre>
<p>🔖initialCapacity: 초기에 저장할 데이터 개수
🔖load factor: 몇 퍼센트 이상 해시 버킷이 차면, 해시 버킷의 크기를 2배의 크기로 resizing한다.</p>
<p>🔎실무에서 웹 서비스를 개발할 때는 대량의 데이터를 저장하거나 <strong>동시성, 메모리 최적화가 중요</strong>할 때 initialCapacity, loadFactor를 조정해서 리사이징 횟수를 줄이고 성능 최적화가 중요하다.
👉즉, 코테에서는 1초에 10^8개의 데이터를 연산 처리하기 때문에 필요없음!</p>
<h2 id="💻해시맵의-메서드-종류">💻해시맵의 메서드 종류</h2>
<p><code>void clear()</code>: 해시맵의 모든 요소들을 지운다.
<code>boolean isEmpty()</code>: 해시맵이 비었는지 확인
<code>boolean containsKey(Object Key)</code>: 인자로 주어진 Key가 해시맵에 존재하는지 판단하여 boolean값을 반환한다.
🔎파이썬의 딕셔너리 in 연산자와 비슷하게 동작하고, <strong>key를 기반으로 탐색하기 때문에 O(1)</strong>이 걸린다.</p>
<p><code>boolean containsValue(Object value)</code>: 인자로 주어진 Value를 가진 Key가 해시맵에 존재하는지를 판단
⚠️value가 존재하는지 확인하기 위해 모든 값을 탐색하기 때문에 O(N)가 걸리므로 주의할 것!</p>
<p><code>Set&lt;Map.Entry&lt;K, V&gt;&gt; entrySet()</code>: 해시맵의 모든 요소들을 파이썬의 <code>dict.items()</code>와 같이 'Key=Value'로 묶어서 Set으로 반환한다.
<code>Set&lt;K&gt; keySet()</code>: 해시맵의 모든 요소의 key만 묶어서 Set으로 반환한다.
<code>Collection&lt;V&gt; values()</code>: 해시맵의 모든 요소의 value만 묶어서 반환한다.
<code>V get(Object key)</code>: 인자로 주어진 key와 매핑되는 Value를 반환한다.</p>
<ul>
<li><p>만약 해시맵에 key가 없으면 <span style="color: red;"><strong>null을 반환</strong></span>
<code>V put(K key, V value)</code>: 인자로 주어진 key=value 쌍을 해시맵에 추가한다.</p>
<pre><code class="language-java">HashMap&lt;String, Integer&gt; map = new HashMap&lt;&gt;();
      map.put(&quot;사과&quot;, 1);
      map.put(&quot;바나나&quot;, 2);
      map.put(&quot;포도&quot;, 3);
      map.put(&quot;사과&quot;, 4);
      map.put(&quot;복숭아&quot;, 5);</code></pre>
</li>
<li><p>만약 해시맵에 key가 이미 존재하면, 나중에 put된 value로 업데이트된다.</p>
</li>
</ul>
<p><code>V remove(Object key)</code>: 해시맵에 인자의 key가 있으면 key=value 쌍을 제거하고 value를 반환한다.</p>
<ul>
<li>주어진 <span style="color: red;"><strong>key가 없으면 null을 반환</strong></span>한다.</li>
</ul>
<p><code>V replace(K key, V value)</code>: 해시맵에 기존에 존재하던 key=oldValue를 새로운 key=value로 바꾼다.</p>
<ul>
<li>만약 해시맵에 key가 없으면 null을 반환한다.</li>
</ul>
<p><code>void forEach(BiConsumer&lt;? super K,? super V&gt; action)</code>: forEach를 사용하여 해시맵의 각 key=value쌍에 접근할 수 있는데 보통 <strong>람다식을 사용</strong>해서 다음과 같이 접근할 수 있다.</p>
<pre><code class="language-java">map.forEach((key, value) -&gt; {
            System.out.println(&quot;key = &quot; + key);
            System.out.println(&quot;value = &quot; + value);
        });</code></pre>
<p>🔎keySet(), entrySet(), values()로 만든어진 것들도 위와 같이 접근이 가능하다.</p>
<p><code>V getOrDefault(K Key, V defaultValue)</code>: key가 존재하면 key에 매핑되는 value를 반환하고, key가 존재하지 않으면 defaultValue를 반환한다.</p>
<ul>
<li>기존의 <code>map.get(K key)</code>는 해당 key가 존재하지 않을 경우 <span style="color: red;"><strong>null을 반환</strong></span>
🔎파이썬의 <code>dict.get(key, default_value)</code>와 동일하다.</li>
</ul>
<p><code>V putIfAbsent(K key, V value)</code>: <strong>key가 해시맵에 없을 때에만 put이 진행</strong>된다. 만약 기존의 key가 해시맵에 존재한다면, 그 key에 매핑되는 value를 반환한다.
💡기존에 저장한 key에 매핑되는** value를 업데이트하고 싶지 않을 때** 사용한다.</p>
<p><code>V computeIfAbsent(K key, Function&lt;? super K, ? extends V&gt; mappingFunction)</code>: 해시맵에 key가 없으면 mappingFunction이 호출되서 <strong>key=value 값을 해시맵에 추가</strong>할 수 있다. 만약 해시맵에 key가 있으면 mappingFunction은 호출되지 않고 기존의 value를 반환한다.</p>
<pre><code class="language-java">map.computeIfAbsent(&quot;감&quot;, key -&gt; 40);
System.out.println(&quot;map = &quot; + map); // {감=40}</code></pre>
<p><code>V computeIfPresent(K key, BiFunction&lt;? super K, ? super V, ? extends V&gt; remappingFunction)</code>: 해시맵에 key가 있으면, remappingFunction을 호출해서 <strong>key=value 값을 해시맵에 갱신</strong>할 수 있다.</p>
<pre><code class="language-java">HashMap&lt;String, Integer&gt; map = new HashMap&lt;&gt;();
        map.put(&quot;사과&quot;, 1);
        map.put(&quot;바나나&quot;, 2);
        map.put(&quot;포도&quot;, 3);
        map.put(&quot;사과&quot;, 4);
        map.put(&quot;복숭아&quot;, 5);

        map.computeIfAbsent(&quot;딸기&quot;, k -&gt; 40);
        map.computeIfPresent(&quot;복숭아&quot;, (k, v) -&gt; v + 50000);
        System.out.println(map); // {복숭아=50005, 포도=3, 사과=4, 바나나=2, 딸기=40}</code></pre>
<h3 id="🤔putifabsent-vs-computeifabsent-차이점">🤔putIfAbsent() vs computeIfAbsent() 차이점</h3>
<p><code>computeIfAbsent()</code>: 키가 없으면 값을 얻기 위하여 호출하는 mappingFunction이 있다.
<code>putIfAbsent()</code>: value를 바로 가져오기 때문에 <code>computeIfAbsent()</code>보다 <span style="color: red;"><strong>오버헤드가 크다.</strong></span></p>
<pre><code class="language-java">var theKey = &quot;Fish&quot;;        

// key가 존재하여도 callExpensiveMethodToFindValue()가 호출된다.
productPriceMap.putIfAbsent(theKey, callExpensiveMethodToFindValue(theKey)); 

// key가 존재한다면 callExpensiveMethodToFindValue()가 결코 호출되지 않는다. 
productPriceMap.computeIfAbsent(theKey, key -&gt; callExpensiveMethodToFindValue(key));</code></pre>
<p>🔎<code>putIfAbsent()</code>: 타임어택이 있는 코테에서 사용해도 무난
<code>computeIfAbsent()</code>: 실무에서는 <span style="color: red;"><strong>오버헤드가 곧 성능과 직결</strong></span>되기 때문에 <strong>캐시 구현, DB/API 조회 결과 저장</strong> 같은 상황에서 사용하면 <strong>효율성이 극대화</strong>된다.</p>


<p>📝그런데, 코테에서 주로 사용하는 해시맵은 다음과 같다.</p>
<p>Java HashMap 메서드 | Python 딕셔너리 대응
|---|---|
containsKey(key) | if key in dict:
keySet() | dict.keys()
values() | dict.values()
entrySet() | dict.items()
getOrDefault(key, defaultValue) | dict.get(key, defaultValue)
putIfAbsent(key, value) | dict.setdefault(key, value) (또는 if key not in dict: dict[key] = value)</p>
<hr />
<p>📚참고자료
🔗해시맵 메서드 및 사용법: <a href="https://gre-eny.tistory.com/97#Set%-CMap-Entry%-CK%-C%--V%-E%-E%--entrySet--">https://gre-eny.tistory.com/97#Set%-CMap-Entry%-CK%-C%--V%-E%-E%--entrySet--</a></p>