# 🤔 VSCode에서 Git 사용시 기본에딧 VSCode로 설정하는 법

## How to set

명령어 줄에서 `git config --global -e` 실행하여 `.gitconfig` 파일에 아래 내용 추가

```sh
[core]
	editor = code --wait
[diff]
    tool = default-difftool
[difftool "default-difftool"]
    cmd = code --wait --diff $LOCAL $REMOTE
```

아래 예시에서 VSCode를 편집기로 사용가능

-   git rebase HEAD~1
-   git commit
    -   commit 메세지를 VSCode로 작성 가능

## **+** 만약 위 코드들을 작업하는 게 불편하다면..?

-   명령어 줄에서  
    `git config --global core.editor "code --wait"`  
    를 입력하면 **[core]** 에 있는 설정 값을 바로 `.gitconfig`에 입력할 수 있음.  
    (위 명령어를 입력하면 `.gitconfig` 파일을 VSCode로 수정하는 것이 가능해지기에.)

-   그 후에 **[diff]** & **[difftool "default-difftool"]** 에 있는 설정 값을  
    명령어 줄에서  
    `git config --global -e`  
    사용하여 입력 후 저장 -> 닫기

### 참고자료

-   [VS Code 를 Git 편집기로 활용](https://rottk.tistory.com/entry/VS-Code-를-Git-편집기로-활용)
