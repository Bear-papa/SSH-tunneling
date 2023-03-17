* RPIVOT 구현을 위한 연구. 
 - Socks5 Reverse proxy
 - RPIVOT allows to tunnel traffic into an internal network pivot via socks 4. 
 - It works like ssh dynamic port forwarding but in the opposite direction.
* 어떻게 시작하는가?
 - Baby Step부터 시작하는걸로..
1. SSH port forwarding 구현
    1-1. Local port forwarding
     * https://iximiuz.com/en/posts/ssh-tunnels/
     * https://www.ssh.com/academy/ssh/tunneling-example
    1-2. Remote port forwarding
     * 상동
    1-3. Dynamic port forwarding
    
2. Socks5 proxy 구현 
    - ssh tunneling 이용해서
    - Dynamic port forwarding과 유사하여 1-3의 내용으로 갈음함
3. Reverse Proxy 구현
    1-1. Http Web server 구축이 필요한 상황(Nginx로 구성)
        https://nginx.org/en/docs/beginners_guide.html
        https://www.aosabook.org/en/posa/warp.html
        https://extrememanual.net/9976
        https://kscory.com/dev/nginx/install
        https://12bme.tistory.com/366

5. Socks5 reverse proxy 구현
