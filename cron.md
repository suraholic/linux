## 리눅스에서 스케줄을 등록하는 방법  

## 1. cron 이란?  
cron은 유닉스 사용자로 하여금 특정한 명령이나 스크립트를 지정한 시간/날짜에 자동으로 실행하게 해주는 프로그램의 이름이다. 
주기적으로 시스템 백업 스크립트를 실행하는 등 시스템 관리자에 의해 많이 사용되며, 그 외에도 특정한 작업을 스케줄링하기 위해 사용될 수 있다. 

## 2. 시작하는 법     
ron은 일종의 데몬이다. 즉 한 번 실행되면 시스템에 상주하며 필요할 때마다 동작한다. 비슷한 예로 웹서버도 일종의 데몬으로 웹페이지에 대한 요청이 들어올 때만 작동한다. 데몬 크론은 crond라는 이름을 갖는데, 설정 파일 혹은 crontab에 기록된 날짜나 시간이 될 때까지 기다린 다음 동작을 시작한다.

대부분의 유닉스 배포판에서 crond는 이미 포함되어 있고, 시작 스크립트에 기록되어 있다. 만약 크론이 실행되고 있는지 살펴보려면 다음과 같이 확인할 수 있다. 

> ps aux | grep crond  

첫줄은 실행되고 있는 crond를 의미하며, 아랫 줄은 방금 실행한 검색 스크립트를 의미한다. 만약 데몬이 떠 있지 않다면 수동으로 kill 했거나 시작이 안된것이다. cron을 시작하려한다면 crond라는 한 줄을 시작 스크립트에 추가하자. 이 프로세스는 자동으로 백그라운드로 구동하기 때문에 별도로 &를 붙이지 않아도 된다. 이제 다음번 재부팅때부터 cron이 실행될 것이다. 만약 재부팅 없이 실행하려면 루트 권한으로 이를 시작해야 한다.

많은 데몬류 프로그램들은 설정 파일을 수정하고 나면, 수정된 내용을 반영하기 위해서 데몬을 재시작해야 할 필요가 있다. Vixie Cron의 경우, crontab 명령으로 설정을 변경하면 자동으로 설정 파일을 재로드하게 된다. 또 어떤 크론 버전들은 매 분마다 설정 파일을 새로 읽거나, 매 분마다 재시작하기도 한다.  

## 3. cron 사용하기  
/etc 디렉토리 내에서 다음과 같은 디렉토리들을 찾을 수 있을 것이다. cron.hourly, cron.daily, cron.weekly, cron.monthly. 이 디렉토리 내에 스크립트 파일을 저장해해두면 디렉토리에 따라서 매 시간, 매일, 매주, 매월등 특정 주기마다 해당 스크립트가 실행된다.  

이 보다 좀 더 유연한 동작을 원한다면 crontab(cron의 설정파일 이름이다.)을 편집한다. 주 설정 파일의 위치는 보통 /etc/crontab이다. 기본 레드햇 배포판에서 이 파일의 내용은 아래와 같이 생겼다. 

## crontab 사용하기  

<pre>
  SHELL=/bin/bash
  PATH=/sbin:/bin:/usr/sbin:/usr/bin 
  MAILTO=root 
  HOME=/ 

  #run-parts
  01  *  *  *  * root run-parts /etc/cron.hourly
  02  4  *  *  * root run-parts /etc/cron.daily
  22  4  *  *  0 root run-parts /etc/cron.weekly
  42  4  1  *  * root run-parts /etc/cron.monthly
</pre>

SHELL은 cron이 사용하는 쉘의 위치이다. PATH역시 쉘이 명령을 실행할 때 뒤져보는 경로들이다. 만약 /usr/cog/bin 아래에 있는 foo 스크립트를 실행해야 한다면 :/usr/cog/bin을 뒤에 추가해주면 좋을 것이다. HOME은 cron이 사용하는 홈 디렉토리가 된다.

<pre>
* * * * *  수행할 명령어
┬ ┬ ┬ ┬ ┬
│ │ │ │ │
│ │ │ │ │
│ │ │ │ └───────── 요일 (0 - 6) (0:일요일, 1:월요일, 2:화요일, …, 6:토요일)
│ │ │ └───────── 월 (1 - 12)
│ │ └───────── 일 (1 - 31)
│ └───────── 시 (0 - 23)
└───────── 분 (0 - 59)
</pre>

<pre>

* * * * * /root/every_1min.sh
 → 매 1분마다 /root/every_1min.sh 를 수행 (하루에 1440회[2])

15,45 * * * * /root/every_30min.sh
 → 매시 15분, 45분에 /root/every_30min.sh 를 수행 (하루에 48회[3])

*/10 * * * * /root/every_10min.sh
 → 10분마다 /root/every_10min.sh 를 수행 (하루에 144회[4])

0 2 * * * /root/backup.sh
 → 매일 02:00에/root/backup.sh 를 수행 (하루에 1회)

30 */6 * * * /root/every_6hours.sh
 → 매 6시간마다 수행(00:30, 06:30, 12:30, 18:30)

30 1-23/6 * * * /root/every_6hours.sh
 → 1시부터 매 6시간마다 수행(01:30, 07:30, 13:30, 19:30)

0 8 * * 1-5 /root/weekday.sh
 → 평일(월요일~금요일) 08:00

0 8 * * 0,6 /root/weekend.sh
 → 주말(일요일, 토요일) 08:00
</pre>

## 작업목록 확인  
> crontabl -l  
> crontabl -l -u testuser 

## 수동 등록 (편집)
> crontabl -e  
> crontab 명령어 입력을 위한 텍스트 에디터가 실행됩니다. 에디터를 통해 편집 후 저장을 하면 바로 적용이 됩니다.

## 삭제  
> crontabl -r  
> "crontab -e"을 통해 편집해도 되지만, 현재 사용자의 모든 crontab을 삭제하고 싶다면 위의 명령어를 사용하면 됩니다. 


<hr> 
* 발췌(출처) 

https://soooprmx.com/archives/6786 <https://soooprmx.com/archives/6786>  

https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%98%EB%B3%B5_%EC%98%88%EC%95%BD%EC%9E%91%EC%97%85_cron,_crond,_crontab <https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%98%EB%B3%B5_%EC%98%88%EC%95%BD%EC%9E%91%EC%97%85_cron,_crond,_crontab> 

http://includestdio.tistory.com/31 <http://includestdio.tistory.com/31> 


