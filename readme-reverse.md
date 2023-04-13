**Reverse Proxy 구성하기 (Nginx, UFW, Nodejs를 사용하여)**
------------------------------------------------------

####가. 개념도 
      ##Client(localhost:3000)  <---------->  Internet <--------> UFW, Nginx <-----> Local Server(localhost:8080)
         
      localhost:3000으로 접근하면, 방화벽을 지나 Nginx 서버가 localhost:8080으로 요청을 전달하고, 
      해당 서버에서 실행 중인 웹 애플리케이션이 반환한 응답을 클라이언트에게 전달해줍니다.


####나. 용어정리
   1. Nginx
      무료 오픈 소스 소프트웨어로 개발된 고성능 웹 서버, 기존의 아파치 웹 서버와 같은 역할을 수행하고
      메모리 사용량도 적고 CPU 부하도 적어 리버스 프록시 서버로서도 사용되며, 로드 밸런싱, 웹 애플리케이션 방화벽 등의 기능도 제공함.
<br>      
   2. UFW(Uncomplicated Firewall)
      Ubuntu 및 Debian 기반 시스템에서 기본적으로 제공되며 쉽게 사용할 수 있는 방화벽 구성 도구이며,
      사용자가 직접 규칙을 작성할 수도 있고, 서버 보안을 강화하기 위해 많은 시스템 관리자들이 사용하고 있음.
<br>
   3. Nodejs 
      서버 사이드 어플리케이션을 개발할 수 있는 오픈 소스 크로스 플랫폼이며, JavaScript 런타임으로서 개발자들이 쉽게 접근 가능함.   
   

<br>
####다. 필요 프로그램 설치,환경 설정 및 테스트
   1. Reverse Proxy 역할을 할 서버 설치하기(Nginx) 
      - Kali Linux에서 APT 패키지 색인을 업데이트합니다.
         ```sudo apt update```
      - Nginx를 설치합니다.
         ```sudo apt install nginx```
      - Nginx 서비스를 시작하고 부팅시 자동으로 실행되도록 구성합니다.
         ```sudo systemctl start nginx```
         ```sudo systemctl enable nginx```
      - Nginx가 실행되는지 명령어로 확인
         ```sudo systemctl status nginx```
 <br>
   2. UFW 방화벽 설정을 구성합니다. 
      - UFW가 없을 시 설치한다
         ```sudo apt-get update```
         ```sudo apt-get install ufw```
         ```sudo ufw enable```
         ```sudo ufw allow 8000, 8080, 3000```
         ```sudo ufw allow 'Nginx HTTP' ```
         ```sudo ufw status 로 방화벽 작동확인```
<br>
   3. Reverse Proxy를 위한 환경 설정작업을 한다. 
      - nginx 설정 파일(/etc/nginx/nginx.conf)을 수정
         <pre><code>
         upstream app {
               server 127.0.0.1:8080
            }
         server {
            listen 3000;
            server_name localhost;
            location / {
               proxy_pass http://localhost:8080;
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            }
         }
         </code></pre>
      - 수정 후 구문 오류를 체크한다
         ```sudo nginx -t ```
      - 재시작을 한다.
         ```sudo systemctl restart nginx```
<br>
   4. 로컬 테스트를 위한 sample App 작성
      - 해당 api가 작동할 디렉토리를 만든 후 시작한다. (/var/www/html/api)
      - Nodejs, NPM(Node Package Manager), Expressjs를 설치 
        ```sudo apt-get update```
        ```sudo apt-get install nodejs```
        ```sudo apt-get install npm```
        ```sudo npm install express```
      - 테스트로 작동할 app.js를 아래와 같이 작성한다. 
         <pre><code>
         const express = require('express');
         const app = express();
         app.get('/', (req, res) => {
         res.send('My Hello World! Local listening port 8080');
         });
         app.listen(8080, () => {
         console.log('Local Server listening on port 8080!');
         });
         </code></pre>
         
   5. 로컬 환경하에서 테스트
      - 위에서 설정한 대로 (localhost:3000)으로 접근해 보면, nginx 서버가 localhost:8080으로 요청을 전달하고,
         해당 서버에서 실행 중인 웹 애플리케이션이 반환한 응답을 클라이언트에게 전달해줍니다. 
      - 아래와 같이 Sample App을 실행한다.
        ```node app.js```
      - 웹 브라우저에 입력 (http://localhost:3000/) 하면 다음과 같이 홈페이지 연결됨
        ```My Hello World! Local listening port 8080```
