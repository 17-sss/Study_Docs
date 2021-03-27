---
title: 'Windows에서 VSCODE에 ZSH 적용 (보류)'
categories: [TIL, WSL, zsh]
date: 2021-03-27 16:48:00 +0900
tags: [공부, 검색]
comments: true
---

# Windows에서 VSCODE에 ZSH 적용 : ***보류***
git bash로만 작업하려니 적용안되는 사항도 있어서 `oh-my-zsh`을 적용해보고 싶어서 찾아봤다.   
(결국은 zsh도 잘 적용 안돼서 보류했지만..)

## **1.** WSL 설치 (우분투 설치)
- 먼저 '제어판 > 프로그램 > 프로그램 및 기능 - Windows 기능 켜기/끄기'를 선택하고 **Linux용 Windows 하위 시스템**을 체크 -> **재부팅** -> 기능설치 완료   
<img src="https://user-images.githubusercontent.com/33610315/112714462-c82abe00-8f1d-11eb-9d9d-07d537255e2f.png" width=500 />

- Windows Store 앱 -> Ubuntu 검색 -> Ubuntu 앱 설치   
    18.04, 20.04가 붙어있는 앱이 아닌 _Ubuntu_ 라고만 쓰여있는 것을 설치.

## **2.** ZSH 설치 (설치된 우분투 앱 실행 후 작업)
1. `sudo apt-get install zsh`
2. `nano ~/.bashrc`
    - 기본 쉘을 zsh로 변경하기위해, bashrc의 마지막 줄에 아래 코드를 입력하기.   
        ```bash
        if [ -t 1 ]; then
            exec zsh
        fi
        ```

## **3.** Oh-my-zsh 설치
1. zsh사용을 편리하게 해주는 **oh-my-zsh**를 설치.   
    이때 curl을 사용하려면 git이 있어야 하므로 git이 없으면 설치!
2. 아래 코드를 입력해 **oh-my-zsh** 설치
    ```bash
    curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
    ```

## **4.** 테마 설정
1. 흔히보고 핫한 agnoster 테마로 바꾸기위해, vi를 실행한다. (`nano ~/.zshrc`)
2. ZSH_THEME부분을 찾은 다음, 아래 코드처럼 변경 후 저장
    ```bash
    ZSH_THEME="agnoster"
    ```
3. 만약 폰트가 깨진다면 [Git - Powerline font](https://github.com/powerline/fonts) 에서   
    폰트 하나를 골라 설치해준다.   
    (난 AnonymousPro를 설치!)

### **5.** VSCODE에 Zsh 적용 (보류)
1. `Ctrl` + `,`를 눌러 설정으로 이동
2. `settings.json` 파일 수정
    ```json
    "terminal.integrated.shell.windows": "C:\\Users\\Administrator\\AppData\\Local\\Microsoft\\WindowsApps\\ubuntu.exe",
    "terminal.integrated.shellArgs.windows": ["-c", "zsh"],
    "terminal.integrated.fontFamily": "Anonymous Pro for Powerline",
    ```
3. 이렇게 수정하여 VSCode 터미널에 적용하였으나, npm 명령어든 안되는 게 너무 많음..   
    (환경변수가 겹치거나 설정을 바꿔야하나?)

---

### **+** 다시 옛날에 썼던 Cmder 적용 (최신으로 업데이트 후 적용)
- [윈도우에서 Cmder를 VSCode 기본 터미널로 사용하기](https://devcnix.tistory.com/10)
- 근데 또 pty 에러... 그냥 Git Bash 쓰자 ㅠㅠ

---

### 참고 자료
- [윈도우즈에서 리눅스 설치 - WSL](https://webdir.tistory.com/541)
- [VSCode 기본 터미널 Zsh+Oh-my-zsh로 바꾸는 법 Windows 10](https://devbull.xyz/vscode-teomineol-zshro-bagguneun-beob-windows-10/)
- [How to Install Oh My Zsh! on Windows 10 Home Edition](https://dev.to/vsalbuq/how-to-install-oh-my-zsh-on-windows-10-home-edition-49g2)
- [nano 에디터 사용법](https://swiftcoding.org/cli-and-nano-editor)
- [Oh-my-zsh 테마 & 그 밖에 추가 설정(MacOS)](https://velog.io/@hwang-eunji/터미널꾸미기-Oh-my-zsh-테마-그-밖에-추가-설정MacOS)
- [Windows 10 터미널 커스터마이징 (feat. WSL2, oh-my-zsh) ](https://mong9data.tistory.com/113)
- [WSL 파일이 위치한 경로 찾기](https://jootc.com/p/201901132508)
- [Visual Studio code(vscode) WSL 사용하기](https://evols-atirev.tistory.com/29)
