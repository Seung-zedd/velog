<p>여러 블로그 및 구글링해서 리액트 + 스프링부트 + SSL(https 적용) + nginx를 찾아본 결과, 다음과 같은 conf 파일로 종결이 났다.
🔎참고로, nginx를 정상적으로 설치했다는 가정하에, /etc/nginx/sites-available/에서 sudo vim test.conf로 다음과 같이 작성하면 악명 높은 Bad gateway 에러를 해결할 수 있다!!</p>
<h1 id="redirect-http---https">redirect http -&gt; https</h1>
<p>server {
  listen 80;
  server_name example.com;
  return 301 https://$host$request_uri;
}</p>
<p>server {
  listen 443 ssl http2;
  server_name example.com;</p>
<h1 id="ssl-인증서-적용하기">ssl 인증서 적용하기</h1>
<p>  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;</p>
<h1 id="https---react-project">https -&gt; react project</h1>
<p>  location / {</p>
<pre><code># proxy_pass http://localhost:5173;
root /home/ubuntu/app/git/[프론트 프로젝트 이름]/dist;
index index.html index.htm;
try_files $uri $uri/ /index.html;

proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;

# CORS 헤더 추가
add_header 'Access-Control-Allow-Origin' '*';
add_header 'Access-Control-Allow-Methods' 'GET, POST, DELETE, PATCH, PUT' always;
add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type' always;
add_header 'Access-Control-Allow-Credentials' 'true';
add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range'; # X-Requested-With: AJAX 통신 요청

# OPTIONS 요청에 대한 처리
if ($request_method = 'OPTIONS') {
    add_header 'Access-Control-Allow-Origin' 'https://example.com';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, DELETE, PATCH, PUT, OPTIONS';
    add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type';
    add_header 'Content-Length' '0';
    add_header 'Content-Type' 'text/plain charset=UTF-8';
    add_header 'Access-Control-Max-Age' 1728000;
    add_header 'Access-Control-Allow-Credentials' 'true';
    return 204;
}

proxy_buffer_size           128k;
proxy_buffers               4 256k;
proxy_busy_buffers_size     256k;</code></pre><p>  }</p>
<pre><code># convey client's request to WAS server</code></pre><p>  location ^~ /api {
    proxy_pass <a href="http://localhost:8080">http://localhost:8080</a>;
  }</p>
<p>}</p>
<h1 id="https-외의-경로로-들어오면-https로-리다이렉트-시킴이렇게-했더니-5173-포트로-입장-불가능">https 외의 경로로 들어오면 https로 리다이렉트 시킴(이렇게 했더니 5173 포트로 입장 불가능</h1>
<p>)
server {
    if ($host = example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot</p>
<p>}</p>
<p>⚠️소셜 로그인의 oauth 경로는 네임 서버 자체에서 처리하므로 api 경로를 안붙여도 된다!</p>
<p>여기서, 실제 배포한 도메인에서 /api/crawl/** 로 api fetch를 하기 위해서는, 추가로</p>
<p>Document docs = Jsoup.connect(url).get();
코드 위에 SSL 인증서를 비활성화시키는 setSSL()을 호출하는 서비스마다 추가해줘야 한다.이렇게 하면, 배포한 도메인에서도 정상적으로 api fetch를 통해 크롤링 데이터를 JSON 형식으로 가져온 것을 확인할 수 있다.</p>
<p>아직 끝난게 아니다!
배포한 도메인은 SSL 인증서를 설정할 때, aws ec2 서버에 인증서를 설치한 것이 아니라, 도메인 이름으로 인증서를 파일로 저장했기 때문에, 프론트단에서 api fetch를 할 때, 탄력적 ip 주소가 아닌 &quot;도메인 이름&quot;으로 경로를 작성해줘야 한다.</p>
<p>🔎추가로, www 서브도메인도 non-www 도메인으로 리다이렉트 시키고 싶으면 다음과 같이 작성하면 된다 👇</p>
<p>server {
    listen              80;
    server_name         <a href="http://www.yourdomain.com">www.yourdomain.com</a> yourdomain.com;
    return              301 <a href="https://yourdomain.com$request_uri">https://yourdomain.com$request_uri</a>;
}</p>
<p>server {
    listen              443 ssl;
    server_name         <a href="http://www.yourdomain.com">www.yourdomain.com</a>;
    ssl_certificate     /path/to/certificate.crt;
    ssl_certificate_key /path/to/private/key.pem;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    return              301 <a href="https://yourdomain.com$request_uri">https://yourdomain.com$request_uri</a>;
}</p>
<p>server {
    listen              443 ssl;
    server_name         yourdomain.com;
    ssl_certificate     /path/to/certificate.crt;
    ssl_certificate_key /path/to/private/key.pem;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;</p>
<pre><code># do the proper handling of the request</code></pre><p>}
⚠️주의할 점은, let's encrypt로 SSL 인증서를 발행할 때, example.com과 <a href="http://www.example.com">http://www.example.com</a> 둘다 적용을 해야한다는 것이다.
😅본인은 www 서브도메인은 적용 안했기 때문에 저 url을 입력하면 당연히 404 Not found 에러가 뜬다.</p>
<p>📑참고
도메인 내 https 설정 실패 참고:</p>
<p><a href="https://parkjeongwoong.github.io/articles/Failure/0">https://parkjeongwoong.github.io/articles/Failure/0</a></p>
<p>redirect www to Non-www with Nginx:</p>
<p><a href="https://serverfault.com/questions/258378/remove-www-and-redirect-to-https-with-nginx">https://serverfault.com/questions/258378/remove-www-and-redirect-to-https-with-nginx</a></p>
<p>setSSL() 메서드 참고:
<a href="https://velog.io/@jakeseo_me/JSOUP-SSL-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EB%AC%B8%EC%A0%9C%EA%B0%80-%EB%B0%9C%EC%83%9D%ED%95%A0-%EB%95%8C">https://velog.io/@jakeseo_me/JSOUP-SSL-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EB%AC%B8%EC%A0%9C%EA%B0%80-%EB%B0%9C%EC%83%9D%ED%95%A0-%EB%95%8C</a></p>