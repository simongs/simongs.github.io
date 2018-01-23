---
layout: post
title: "[SHELL] Favorite Linux Command & Shell Script"
date: 2016-10-13 09:00:00 +0900
categories: LINUX
---

* Table of Contents
{:toc}

## Linux Command

### BASIC

~~~
문장의 처음/끝 이동
    - Ctrl+A, Ctrl+E
단어 단위 이동
    - Ctrl+방향키
화면정리 (Clear)        
    - Ctrl+L
스크롤 멈춤/재시작
    - Ctrl+S, Ctrl+Q
현재 디렉토리 위치 확인
    - PWD
자신의 ID 정보 확인
    - ID    
현재 접속해있는 서버의 이름/OS 정보 확인
    - uname, uname -a
    - hostname (서버 호스트명 ex.tebalp_web001)
히스토리 보기
    - history
바로 직전 히스토리 재실행
    - !!
바로 직전 명령의 마지막 Argument
    - !$
파일의 종류를 판단하는 명령어
    - file
사용자변수
    - 보통 소문자로 명명
    - x=1
    - x="hello"
    - x=hello world
사용자변수 가져오기
    - echo x
    - echo $x
    - echo ${x}
    - echo "$x"
Evaluation (수학적 값을 연산)
    - backquote(`) 또는 $( )로 둘러싸기
    - date_str=`date +"%Y%m%d"`
    - date_str=$(date +"%Y%m%d")
    - cmd="ls –alF"
    - eval $cmd
환경변수 (export)
    - 환경변수는 evn 명령어를 통해서 확인가능
    - sub-shell에서 상속받아 사용할 수 있도록 변수를 공개
    - 사용자 변수를 환경 변수로 전환
    - export var1=2
File Permission
    - TYPE(1) + OWNER(3) + GROUP(3) + OTHERS(3)
    - TYPE :: Directory, Symbolic Link 등등
    - 3자리는 READ + WRITE + EXECUTE 권한 의미
파일 소유자 변경하기
    - chown 소유자ID 파일
Comparison operator
    - integer
        - -eq -ne -gt -ge -lt -le
    - string
        - < <= > >= == = != -z –n
        - zero length? null?
        주의사항
    - -eq 연산자는 정수 연산자
    - ==, = 연산자는 문자열 연산자
    - 변수가 정의되지 않았거나 공백을 포함할 수 있으므로 quotation 필요
    - x=“he llo”
    - if [ ”$x” == “he llo” ]; then echo $x; fi
입출력 리다이렉트
    - 표준 입력(standard input) – 0번
        - 키보드 입력이 프로세스로 들어가는 통로
    - 표준 출력(standard output) – 1번
        - 프로세스가 출력한 내용이 화면으로 나오는 통로
    - 표준 에러(standard error) – 2번
        - 프로세스에서 발생한 에러가 화면으로 나오는 통로
    - ex) ls /lkjfslfjds 2>&1 | sort

Script File
    - #!
        - 어떤 인터프리터 프로그램을 사용할 것인가를 지정
    - #!/bin/bash
    - #!/usr/bin/env python
        - 권장하는 방식
    - command-line arguments
        - $1, $2
        - $#
    - exit status
        - $?
사용자설정
    - Bourne shell
        - ~/.profile
    - BASH
        - login shell
            - .bashrc
        - non-login shell
            - .bash_profile
추천설정
    export TERM=xterm-256color
    export LC_ALL=ko_KR.utf8
    export LANG=ko_KR.utf8
    export GREP_OPTIONS=’--color=always’
    export CLICOLOR=1
    export LSCOLORS=GxFxCxDxBxegedabagaced
    export PS1=“[\u@\h] \W\$ ”
    export LESS=‘-EMR’
    export EDITOR=vim
    export PAGER=“less $LESS”
    export PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:$HOME/bin    

~~~

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

#### connection Log 확인
~~~
netstat -an | grep "연동서버 IP"
~~~

##### option 설명
 - -a : 통신중만 아니고 통신 개시 전의 것도 모두 포함
 - -n : IP주소나 포트 번호를 번호로 표시합니다.
 
##### connection 상태  
* ESTABLISHED 접속 동작이 끝나고 데이터 통신 중임을 표시
* LISTENING 상대로부터 접속을 기다리는 상태
* TIME_WAIT

### About Log

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

대소문자 구분하지 않고 검색하기
> grep -i 'content_id' umon.log  

앞에 줄번호 붙이기
> grep -n "content"

바이너리 파일 grep
> grep -a 'test' binary.log

특정 문자열이 포함된 ROW는 출력하지 않는다.
> grep -v 'test' binary.log

~~~

#### sed

Stream Edior 
대화식 에디터가 아닌 흘러 들어오는 텍스트를 그대로 편집하는 프로그램
VIM은 특정 파일을 오픈하여 실시간으로 편집하는 스크린 에디터이다.
ED 라는 라인 에디터도 존재하지만 사용률은 미미하다.

~~~
start line - end line 까지 로그 자르기 (라인수 기준)
> sed -n '100,200p' catalina.log > temp

특정문자가 포함된 문자열 삭제 (0000000000가 들어간 줄은 삭제하고 출력한다.)
> sed '/0000000000/d' test.txt

특정문자를 신규문자로 치환한다. (문서내의 addrass -> address 로 변경)
> sed 's/addrass/address/' test.txt 

특정문자가 포함된 문자열만 삭제하지 않는다 (0000000000이 들어간 문자열만 출력한다.)
> sed '/0000000000/!d' test.txt

공백라인을 삭제한다.
> sed '/^$/d' test.txt

한 줄 안에 포함된 모든 문자를 치환한다.
> sed "s/test/test2/g"

대소문자 구분을 하지 않는다
> sed "s/Windows XP/Windows/i"
> sed "s/Widdows XP/Windows/gi" // 두가지 옵션을 같이 설정할 때
~~~



#### head
~~~
특정 파일 앞의 3바이트만 확인하기
> head -n 3 lib_monitor_cmn.js | xxd -p
~~~

#### 중복처리
~~~
특정 파일안에 중복된 ROW 가 존재하는지 확인해보자
ex)데이터식별자(1) USER_ID(50) + 카드번호(10) + 기타정보

cut -b2-61 test.txt | sort | uniq -c | sort -nr | head -n 10
~~~

### vim

#### 이미 읽은 파일 인코딩 변경
~~~
:e ++enc=UTF-8
~~~

#### 자주쓰는 단축키 정보
~~~
^ : 커서를 라인 처음으로 위치시킨다. (정규표현식에서는 맨 앞의 문자를 의미)
$ : 커서를 라인 끝으로 위치시킨다. (정규표현식에서는 맨 뒤의 문자를 의미)

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

#### 마지막 수정일이 1일 이내의 파일을 찾고 싶은 경우
~~~
[localhost /home/user]# find ./* -mtime -1
./bloc_10850.out
./bloc_10850.out.2014.03.05
~~~

#### 마지막 수정일이 7일 이상된의 파일을 찾아서 지우고 싶다.
~~~
[localhost /home/user]# find ./* -mtime +7 -exec rm -rf {} \;
~~~

#### 7일 ~ 15일 사이에 생성된 파일을 찾고 싶다.
~~~
find . -ctime +7 and -ctime +15
~~~

#### 30일이 지난 access 파일이나 error 파일 검색
~~~
find . -ctime +30 and \( -name "*access*" or -name "*error*" \)
~~~

#### ctime, mtime, atime 의 차이 
~~~
atime - accesstime (접근시간), grep 명령어로도 갱신
mtime - 최종변경시간 - 파일 내용에 대한 변경 시간.
ctime - changed time, 이름, 퍼미션, 디렉토리 이동등 파일에 대한 변경 시간.
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

#### diff
~~~
특정 두 디렉토리 비교 명령어
diff -r org_dir chg_dir
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

#### sub directory 작업
()이 없으면 logs 까지 내려갔다가 data 디렉토리를 갈때 logs에서 가려고 한다.
() 이렇게 묶는 기법을 서브쉘이라고 한다.
하지만 왠만하면 함수를 사용해라
~~~
for dir in logs data users
do
    (
        cd $dir
        files=$(find . -name "*.bak");
        for file in $files;
        do
            rm $file;
        done
    )
done
~~~

### String

#### String cut
{% highlight ruby %}
### ===============================================
### Simple bash scripting for String cut
### Credit: simongs
### ===============================================
ST_DATE="20160801"

THIS_DATE=$ST_DATE

# 0 index 부터 4자리
YEAR=${THIS_DATE:0:4} 
MONTH=${THIS_DATE:4:2}
DATE=${THIS_DATE:6:2}
### ===============================================
### END
### ===============================================
{% endhighlight %}

#### Argument Check
~~~ ruby
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

## Example

### Example 1

{% highlight ruby %}
servers=(
"server1"
)

servers2=(
"server2"
)

directories=(
"/home/test1/service/log"
)


for SERVER in "${servers2[@]}"
do
        echo "========= ${SERVER} ===============\n\n"

        for DIR in "${directories[@]}"
        do
                ssh irteam@${SERVER} "mkdir -p ${DIR}"
        done
done
{% endhighlight %}

### Example 2
특정 디렉토리 하위의 모든 파일들을 찾아서 각 파일들의 LINE 수를 구하여라

~~~
find . -type f -print | xargs wc -l
~~~

# 정규표현식

## grep

~~~
-E : 확장 정규표현식

특정 검색어가 있는 문자열 검색 하기
> grep -E "(검색어1|검색어2|검색어3)" catalina.log

숫자만 있는 문자열
> 의미 : 0~9까지의 문자가 1개 이상이다.
> [0-9]+

시작하는 문자만 변경
>  - 라라라 ->  * 라라라 로 변경
> s/^( +)-/\1*/

끝나는 문자만 변경
> s/a$/b


~~~

## VIM

~~~
정규표현식을 활용한 치환
> 문서 내의 반환중 혹은 회수중 문자열를 회수완료 문자열로 치환
> (명령어 prompt에서) :%s/\v(반환중|회수중)/회수완료/
~~~

## SED

리눅스용 GNU SED는 -r 옵션으로 정규표현식 사용
MAC OS 나 BSD의 BSD SED는 -E 옵션으로 정규표현식 사용

~~~
문자열 구성 : prePart + "사용중" + postPart
변경하고 싶은 문자열 : prePart + "회수완료" + postPart + "파기예정"

sed -r -e "s/(Windows XP.+)사용중(.+)"/\1회수완료\2파기예정/"
~~~


