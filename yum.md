## yum 에러 : loaded plugins fastestmirror langpacks 

### yum update나 패키지 install 시 'loaded plugins: fastestmirror' 에러가 날 경우 

1. Make sure the RPM database is functional. 
<pre>
cp -arv /var/lib/rpm /var/lib/rpm.bak
rm -f /var/lib/rpm/__db*
/usr/lib/rpm/rpmdb_verify /var/lib/rpm/Packages
</pre>

2. Remove the external repos and test again  
<pre>
mkdir /etc/yum.repos.d/old
mv /etc/yum.repos.d/*repo /etc/yum.repos.d/old
rm -rf /var/cache/yum/*
yum repolist -v
</pre>

3. yum 명령어 다시 실행  

<hr>

* 발췌(출처) : <https://www.lesstif.com/pages/viewpage.action?pageId=14745755>
* 처리 Red Hat Customer 포털에 있는 내용대로 하면 됨. 
-> https://access.redhat.com/solutions/876853 
