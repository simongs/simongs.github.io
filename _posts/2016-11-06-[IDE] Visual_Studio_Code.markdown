---
layout: post
title: "[IDE] Visual Studio Code TIPS"
date:   2016-11-06 09:00:00 +0900
categories: IDE
---

#### Reference
 - [Visual Site Code](https://code.visualstudio.com) 
 - [공식 키보드 단축키 리스트-windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

#### 자주쓰는 단축키

주로 쓰는 단축키는 아래와 같이 정리합니다.

Action | ShortCut | Description
------------- | ------------- | -------------
라인복사 |Shift + Alt + arrow key (방향키) | 라인을 복사합니다.
줄편집	|Ctrl + Shift + Alt + arrow key(방향키)| 세로로 편집을 가능하도록 합니다.
탐색기 탭 열기|Ctrl + B| 좌측 탐색기를 엽니다.
편집 윈도우 이동 | Ctrl + num key(숫자키) | 1번 윈도우, 2번 윈도우 포커스 이동 
TAB 이동 (in 윈도우 내) | Ctrl + Tab, Ctrl + Page Up | 열린 윈도우 내의 TAB 이동 
우측 윈도우로 현재 TAB 복사 | Ctrl + \ | 현재 열린 파일을 우측 윈도우로 복사한다
Markdown Preview | Ctrl + Shift + B | Auto-Open Markdown Preview 플러그인
편집기 TAB 닫기 | Ctrl + W | 현재 열린 편집기를 닫습니다.
후행 Trim() | Ctrl + K, Ctrl + X | 선택된 문자열의 후행 공백을 제거합니다.

#### Github 연동하기

탐색기에서 폴더를 열 때 github에서 다운로드 받은 디렉토리를 엽니다.
에디터에서 자동적으로 연결이 됩니다.
VSC 에서 바로바로 Commit & Push 가 가능합니다.

#### 특정 문자를 개행문자로 변경하기
![My helpful screenshot](/assets/img/2018/20161106_VSC_2018-01-17_14;48;48.PNG)

#### 단축키 변경 (modify keybindings.json)

##### WINDOWS 
파일 > 기본설정 > 바로가기 키 를 선택한 후 수정하면 `keybindings.json` 파일이 생성됩니다.
아래와 같이 재정의를 합니다.

~~~json
// 키 바인딩을 이 파일에 넣어서 기본값을 덮어씁니다.
[
    // 키 바인딩을 이 파일에 넣어서 기본값을 덮어씁니다.
[
    // 라인 삭제 재정의 및 키 바인딩 삭제 (- 표기)
    { "key": "ctrl+shift+d", "command": "editor.action.deleteLines", "when": "editorTextFocus && !editorReadonly" },
    { "key": "ctrl+shift+d", "command": "-workbench.view.debug" },
    
    // 라인복사 재정의 및 키 바인딩 삭제 (- 표기)
    { "key": "ctrl+alt+down", "command": "editor.action.copyLinesDownAction", "when": "editorTextFocus && !editorReadonly" },
    { "key": "ctrl+alt+up",   "command": "editor.action.copyLinesUpAction", "when": "editorTextFocus && !editorReadonly" },
    { "key": "ctrl+alt+up",   "command": "-editor.action.insertCursorAbove", "when": "editorTextFocus" },
    { "key": "ctrl+alt+down", "command": "-editor.action.insertCursorBelow", "when": "editorTextFocus" },

    // 문서 formatting (- 표기)
    { "key": "ctrl+shift+f", "command": "editor.action.formatDocument", "when": "editorHasDocumentFormattingProvider && editorTextFocus && !editorReadonly" },
    { "key": "ctrl+shift+f", "command": "-search.action.focusActiveEditor","when": "searchInputBoxFocus && searchViewletVisible" },
    { "key": "ctrl+shift+f", "command": "-workbench.action.findInFiles", "when": "!searchInputBoxFocus"},
    { "key": "ctrl+shift+h", "command": "-workbench.action.replaceInFiles"},
    { "key": "ctrl+shift+h", "command": "editor.action.startFindReplaceAction" },
    
    // 문서내 검색
    { "key": "ctrl+h", "command": "workbench.view.search", "when": "!searchViewletVisible"},
    { "key": "ctrl+h", "command": "-editor.action.startFindReplaceAction"},
    { "key": "ctrl+shift+f", "command": "-workbench.view.search", "when": "!searchViewletVisible"},

    // 문서 언어 변경 (- 표기)
    { "key": "ctrl+l", "command": "workbench.action.editor.changeLanguageMode"},
    { "key": "ctrl+k m", "command": "-workbench.action.editor.changeLanguageMode"},
    
    // VSC 명령어 Command 열기
    { "key": "alt+oem_3", "command": "workbench.action.showCommands"},
    { "key": "f1", "command": "-workbench.action.showCommands"},
    { "key": "ctrl+oem_3", "command": "-workbench.action.terminal.toggleTerminal" },
    { "key": "ctrl+shift+c", "command": "workbench.action.terminal.toggleTerminal"},
    
    // VSC Console 열기
    { "key": "ctrl+shift+c", "command": "-workbench.action.terminal.openNativeConsole", "when": "!terminalFocus" },
    
    // 대소문자 변경 단축키 설정
    { "key": "ctrl+shift+u", "command": "editor.action.transformToUppercase"},
    { "key": "ctrl+shift+u", "command": "-workbench.action.output.toggleOutput"},
    { "key": "ctrl+shift+alt+u", "command": "editor.action.transformToLowercase"},
    { "key": "ctrl+shift+alt+oem_5", "command": "workbench.action.splitEditor"},
    { "key": "ctrl+oem_5", "command": "-workbench.action.splitEditor"},
    { "key": "ctrl+shift+alt+oem_6", "command": "workbench.action.toggleEditorGroupLayout"},
    { "key": "shift+alt+1", "command": "-workbench.action.toggleEditorGroupLayout"}
]
]
~~~

~~~json
// 설정을 이 파일에 넣어서 기본 설정을 덮어씁니다.
{
    "files.autoSave": "afterDelay",
    "git.confirmSync": false,
    "workbench.statusBar.visible": true,
    "window.zoomLevel": 0,
    "editor.detectIndentation": false
}
~~~
