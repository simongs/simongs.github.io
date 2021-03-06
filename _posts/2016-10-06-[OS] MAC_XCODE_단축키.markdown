---
layout: post
title: "[ETC] MAC 사용팁과 XCODE 단축키"
date:   2016-10-02 17:45:49 +0900
categories: ETC
---

## MAC 사용팁

#### MAC Symbols
![mac symbols](http://www.mfk.co.kr/media/kunena/attachments/legacy/images/201009_MacOSX_Symbol.jpg)

#### Safari에서 영어단어 보기
~~~
특정 단어를 focusing 한 후에 3 fingers touch를 수행
~~~

#### Screen Zoon in-out
~~~
트랙패드에서 두 손가락으로 드래그하는 동안 Control(^) 키를 눌러 확대하십시오.
트랙패드에서 두 손가락으로 아래로 드래그하는 동안 Control 키를 눌러 축소하십시오.
~~~

#### Safari address 창
~~~
Command + L
~~~

#### MAC Mission Control
~~~
How to Use Multi Desktop 
http://zosolution.com/166
http://zosolution.com/191
~~~

#### MAC window 전체화면
~~~
Ctrl + Command + F
~~~

#### 프로그램 강제종료
~~~
Ctrl + Option + Esc
~~~

#### 특정 프로그램에서 터치바를 F1-F12를 노출하고 싶은 경우
- How to use function keys on MacBook Pro with Touch Bar
    기
    - https://support.apple.com/el-gr/HT207240

#### Finder

##### Command + Delete = File 삭제

##### 숨김파일 표시 방법
~~~
defaults write com.apple.finder AppleShowAllFiles -bool true
killall Finder
~~~

## MAC Application

#### Jumpcut
clip board (copy history)

#### Magner
- Window move & arrange Program
- window Key + allow key 
- 유료구매 필요

#### alfred 3
hot key

#### BetterTouchTool (BTT)
 - 한영전환 , 유료구매 필요
 - 터치바 개선
    - BTT로 터치바 개선하기
    - https://www.clien.net/service/board/cm_mac/12438693

#### OneNote 
 - shared Note

### LightShot
 - 화면 캡쳐 프로그램
 - 클라우드에 바로 업로드 후 Image Url 생성

#### Iterm2
advanced terminal program (with another setting)

 - zsh
 - solarized
 - oh-my-zsh

#### 기타등등
- 아래 Reference 를 통해서 주로 쓰는 프로그램 정보
- 유용한 어플이 있는지 참조하자. 
~~~
anaconda
android-file-transfer
android-studio
atom
bettertouchtool
boostnote
calibre
clipy
coconutbattery
discord
dropbox
firefox
google-chrome
hyper
iterm2
karabiner-elements
keka
keyboard-maestro
libreoffice
mactex
mamp
microsoft-remote-desktop-beta
nvalt
owncloud
skim
skype
speedcrunch
upterm
vagrant
vlc
~~~
 
##### Reference
 - [[MAC] 명령행 작업 좀더 편리하게 하기!](http://redgolems.tistory.com/30)
 - [[맥 유틸리티] Brew Cask - 커맨드라인에서 어플을 설치하자!](https://busy.org/@paul9/brew-cask)
 - [Mac에 Python 3.x 설치 (package)](http://dejavuqa.tistory.com/60)

## MAC Evironment Setting

#### setting .vimrc
 - [변정훈님 블로그 참고](https://blog.outsider.ne.kr/518)

~~~
set nocompatible " 오리지날 VI와 호환하지 않음
set autoindent  " 자동 들여쓰기
set cindent " C 프로그래밍용 자동 들여쓰기
set smartindent " 스마트한 들여쓰기
set wrap
set nowrapscan " 검색할 때 문서의 끝에서 처음으로 안돌아감
set nobackup " 백업 파일을 안만듬
set visualbell " 키를 잘못눌렀을 때 화면 프레시
set ruler " 화면 우측 하단에 현재 커서의 위치(줄,칸) 표시
set shiftwidth=4 " 자동 들여쓰기 4칸
set number " 행번호 표시, set nu 도 가능
set fencs=ucs-bom,utf-8,euc-kr.latin1 " 한글 파일은 euc-kr로, 유니코드는 유니코드로
set fileencoding=utf-8 " 파일저장인코딩
set tenc=utf-8      " 터미널 인코딩
set expandtab " 탭대신 스페이스
set hlsearch " 검색어 강조, set hls 도 가능
set ignorecase " 검색시 대소문자 무시, set ic 도 가능
set tabstop=4 "  탭을 4칸으로
set lbr
set incsearch "  키워드 입력시 점진적 검색
syntax on "  구문강조 사용
filetype indent on "  파일 종류에 따른 구문강조
set background=dark " 하이라이팅 lihgt / dark
colorscheme desert  "  vi 색상 테마 설정
set backspace=eol,start,indent "  줄의 끝, 시작, 들여쓰기에서 백스페이스시 이전줄로
set history=1000 "  vi 편집기록 기억갯수 .viminfo에 기록

" When editing a file, always jump to the last cursor position
autocmd BufReadPost *
\ if ! exists("g:leave_my_cursor_position_alone") |
\ if line("'\"") > 0 && line ("'\"") <= line("$") |
\ exe "normal g'\"" |
\ endif |
\ endif
~~~



## XCODE

#### XCODE에서 Clean
~~~
Press ⌘ + SHIFT + K to clean your project, and then first error should go away.
~~~
 
#### XCODE에서 Build
~~~
If the error isn’t immediately showing, 
hit ⌘ + SHIFT + U to build the app, and the error should appear
~~~

#### Test Execution 방법
~~~
Now we don’t have any errors standing in our way, 
let run our test! (⌘ + U).
~~~

#### Open Quickly
~~~
⌘ + SHIFT + O
~~~

#### Class Move (implementation)
~~~
option + Mouse left click
~~~

#### 타 블로그 정리 글
출처 : http://geekcoders.tistory.com/entry/Max-OS-X-XCode-기본-단축키-맥-단축키

~~~
XCode 

 - 커맨드 + Shift + K  : 프로젝트 클린
 - 커맨드 + B  : 프로젝트 빌드
 - 커맨드 + R  : 프로젝트 실행
 - 커맨드 + I  : 프로젝트 프로파일링 빌드
 - 커맨드 + . : 실행중인 앱 강제 종료
 - 커맨드 + , : XCode 프로퍼티창 열기
 # 해당 창에 Key Bindings 탭에서 모든 단축키를 변경할 수 있습니다. 단, 꼬이면 답이 없습니다..

 - 커맨드 + Shift + O : 프로젝트 내 파일 / 클래스 / 함수 검색 ( 매우 유용 )
 - 커맨드 + Shift + F : 파인드 창으로 캐럿 강제 이동
 - 커맨드 + F : 현재 창 검색
 - 커맨드 + E : 현재 선택된 텍스트 블럭을 모든 텍스트에디트에 복사 ( ?.. 설명을 잘 못하겠네요... )
 - 커맨드 + G : 현재 페이지에 텍스트에디트에 적힌 문구 순차 검색 ( 위 커맨드 + E 와 활용도 매우 높습니다 )
 - 커맨드 + Shift + G : 현재 페이지에 텍스트에디트에 적힌 문구 역순차 검색
 - 커맨드 + alt + Enter : 현재창 이중 분할
 - 커맨드 + Enter : 현재창을 단일창으로 변경

 - 커맨드 + 0 : 왼쪽에 프로젝트 파인더 및 검색 등등 윈도우 숨기기 & 열기
 - 커맨드 + 1 : 프로젝트 파인더 열기
 - 커맨드 + 2 : 프로젝트 하이라키 창 열기
 - 커맨드 + 3 : 프로젝트 검색 창 열기
 - 커맨드 + 4 : 프로젝트 워닝 및 에러 창 열기
 - 커맨드 + 5 : 테스트 타겟창 열기
 - 커맨드 + 6 : 디버그 세션 창 열기
 - 커맨드 + 7 : 프로젝트에 걸려있는 모든 브레이크 포인트를 보여주는 창 열기
 - 커맨드 + 8 : 빌드 관련 히스토리 ?

 - 커맨드 + \ : 브레이크 포인트 걸기
 - 커맨드 + Y : 브레이크 포인트 비활성화 / 활성화
 - 커맨드 + Shift + Y : 하단 디버그 세션 창 열기 / 닫기 
 - 커맨드 + / : 해당라인 주석 걸기 ( 다중 라인도 가능 )
 - 컨트롤 + 커맨드 + 좌우 화살표 : 이전/이후 페이지 이동
 - 커맨드 + [ ] : 해당 방향으로 들여쓰기
 - 컨트롤 + I : 선택된 텍스트블록의 들여쓰기 올바르게(?) 적용

디버깅 관련
 - F6 : 다음 라인 ( Step Over )
 - F7 : 현재 라인 내부 진입 ( Step Into )
 - F8 : 현재 함수에서 나가기 ( Step Out )
 - 커맨드 + Shift + Y : 실행 중 멈춤 ( Pause ) & 멈춤 상태에서 재 진행

소스 컨트롤 관련
 - 커맨드 + Shift + C : 커밋창 열기
 - 커맨드 + Shift + X : 업데이트 하기

iOS Simulator
 - 커맨드 + S : 현재 시뮬레이터에 뜬 화면 캡쳐
~~~

