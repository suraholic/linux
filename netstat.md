## 사용중인 포트 확인  

> netstat -antup

<pre>
-a : 모든 소켓 표시
-t : TCP 만 표시
-u: UDP 만 표시
-n: 호스트, 포트 번호 등의 이름 확인을 하지 않고 숫자로 표시
-p: 소켓을 사용하고 있는 프로세스의 ID 표시
-r: 라우팅 테이블 표시
-l : 연결 대기 상태인 소켓만 표시
</pre>

<hr>

## lsof 사용법 

lsof 는 list open files 의 약자로 시스템에서 열린 파일 목록을 알려주고 사용하는 프로세스, 디바이스 정보, 파일의 종류등 상세한 정보를 출력해 준다.

리눅스와 유닉스는 추상화된 파일 시스템(VFS - Virtual File System)을 사용하므로 일반 파일, 디렉터리, 네트워크 소켓, 라이브러리, 심볼릭 링크 등도 모두 파일로 처리되며 lsof 에서 상세한 정보를 확인할 수 있다.



유닉스마다 고유의 lsof 와 비슷한 용도의 명령어가 있지만 명령어와 옵션이 제각각이고 출력 정보가 상이하여 OS 가 바뀌면 사용하기가 힘들다.

lsof 는 리눅스, AIX, Solaris, FreeBSD, Mac OS X 등 종류에 상관없이 일관된 옵션과 출력 형식을 갖는 장점이 있다.  

### FILE 
옵션없이 lsof를 실행하면 모든 열린 파일 정보를 출력한다.
<pre>
lsof
  
COMMAND    PID      USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
init         1      root  cwd       DIR              253,0     4096          2 /
init         1      root  rtd       DIR              253,0     4096          2 /
init         1      root  txt       REG              253,0   150352       3932 /sbin/init

1. COMMAND : 실행한 명령어
2. PID : process id
3. USER : 실행한 사용자
4. FD: File Descriptor, 파일의 종류. 
   a. cwd: current working directory
   b. rtd: root directory
   c. mem : memory-mapped file
   d. txt: program text (code and data);
5. TYPE: 파일 종류
   a. DIR: 디렉터리
   b. CHR:  character special file
   c. REG: regular file
   d. unix: 유닉스 도메인 소켓 (MySQL 등이 사용하는 소켓으로 로컬 프로세스에서만 사용 가능하며 TCP/UDP 보다 속도가 매우 빠름)
6. DEVICE : 장치 번호
7. SIZE/OFF: 파일의 크기나 오프셋
8. NODE: 노드 번호
9. NAME:  파일명
</pre>

* 특정 사용자의 열린 파일 출력
> lsof -u lesstif  

* 특정 파일을 사용하는 프로세스 보기 
> lsof /var/log/httpd/access_log 

* 특정 디렉터리 내 열린 파일 보기 
> lsof +D /tmp 

* 특정 포트를 사용하는 프로세스 정보 보기  (-i 옵션뒤에 프로토콜과 포트를 명시해 주면 된다.)
> lsof -i TCP:22  

* 특정 명령어가 사용하는 포트 
> lsof -c httpd  

* 포트 범위 지정  
> lsof -i TCP:22-80 

* service name 대신 port 번호 표시  
> lsof -i TCP -P | grep java | grep LISTEN  

* IPv4 또는 IPv6 포트만 표시 
> lsof -i 4 

* 열린 모든 네트워크 포트 표시  (-i 뒤에 프로토콜을 명시하면 해당 프로토콜 관련 포트만 표시한다.) 
>  lsof -i  
>  lsof -i TCP    
>  lsof -i UDP 

* 특정 사용자 제외 
> lsof -p 123 

* 특정 사용자가 오픈한 모든 프로세스 중지  
> lsof -t -u lesstif  
> kill -KILL `lsof -t -u lesstif` 

<hr>
* 발췌(출처) : 
https://www.lesstif.com/pages/viewpage.action?pageId=20776078 <https://www.lesstif.com/pages/viewpage.action?pageId=20776078>

