---
layout: post
title: "자주사용하는 리눅스 명령어 및 쉘 스크립트"
date: 2016-10-13 09:00:00 +0900
categories: linux
---


## Linux Command

### NETWORK

#### traceroute (Linux)
~~~
sudo traceroute  10.24.143.95  -T -p 10003

~~~

#### tracert, tracetcp
 - [Tracetcp 사용법 - 별도프로그램 download](http://byungsalta.tistory.com/194)

~~~
tracert -d 10.161.221.136 (window)
tracetcp 10.161.221.136:3306 -n
~~~

### view Log File

#### cat
~~~
cat /etc/shadow | wc -l
- option        
	-c : 문자수만 보여줌
    -m : 캐릭터수만 보여줌
    -l : 라인수만 보여줌
    -w : 단어수만 보여줌
    -L : 가장 긴줄 한줄만 보여줌
~~~

#### grep
~~~
용량이 큰 로그 파일에서 2011-03-08 10:20:XX 가 들어있는 부분까지만 bbb.txt로 내보내기
cat 파일명 | grep "^2011-03-08 10:20:[0-9][0-9]" >> bbb.txt
~~~

### vim

#### 이미 읽은 파일 인코딩 변경
~~~
:e ++enc=UTF-8
~~~

### 압축

#### command
~~~
[압축하기]
tar -cvf 압축파일명.tar 압축파일
gzip -f 압축파일명.tar 

[특정폴더 제외]
tar -cvf someDIR --exclude logs 

[압축풀기]
gzip -d 압축파일명
tar -xvf 파일명.tar

[소유권및 퍼미션을 유지한 채로 압축풀기]
tar czvpf filename.tar.gz public_html/
tar xzvpf filename.tar.gz public_html/

c : 압축
z : tar 압축후 gzip압축
v : verbose 압축과정을 출력
p : 소유권등 퍼미션을 그대로 유지
f : 내가 지정한 파일명으로 압축
~~~

### FIND

#### Search Keyword
~~~
현재 디렉토리(하위포함) 에서 JAVA라는 문구가 들어간 파일을 알고 싶어용
find . -name "*.sh" | xargs grep JAVA
~~~

### File

#### File Delete
~~~
[6일 이전 파일 삭제 방법]
/usr/bin/find -mtime +6 > deleteFile.txt
/bin/cat deleteFile.txt | /usr/bin/xargs /bin/rm -f
~~~

#### Remote Copy
~~~
rcp account@server-host:/home/.../file .
~~~

### MONITORING

#### Memory
~~~
SAR -R
~~~

#### DISK
~~~
[전체용량]
df -h

[디렉토리 용량]
du -sh 디렉토리명

du -h --max-depth=1
du -sh *
~~~

### Execute

#### xargs
~~~
ps -ef | grep java | awk {print $2} | xargs kill -9
~~~

## Shell Script

### Date

#### make "yyyyMMdd" Date String
~~~
THIS_DATE=`date --date "$THIS_DATE 1 day" +%Y%m%d`
~~~

#### using while loop
~~~
while [ $THIS_DATE -le $ED_DATE ]; do
    
	echo "=======target Date : $THIS_DATE ==========="

	sleep 0.5

    THIS_DATE=`date --date "$THIS_DATE 1 day" +%Y%m%d`
done
~~~
### String

#### String cut
~~~
ST_DATE="20160801"

THIS_DATE=$ST_DATE

YEAR=${THIS_DATE:0:4} 0 index 부터 4자리
MONTH=${THIS_DATE:4:2}
DATE=${THIS_DATE:6:2}
~~~

#### Argument Check
~~~ script
if [ $# -eq 0 ];
then
	echo "usage : ./grep.sh searchKeyword yyyyMMdd  or ./grep.sh searchKeyword"
    exit;
fi

if [ $# -eq 2 ];
then
    LOG_DATE=$2
fi
~~~
