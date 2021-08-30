# Git Commit Template 내 입맛대로 만들기 🤪

## 적용하기 전에

VSCode에서 커밋할 때 이번 글에서 적용할 Git Commit Template를 보고싶다면..?  
VSCode를 Git 사용 시 기본 에딧으로 설정해주세요!  
👉 [[VSCode] VSCode에서 Git 사용시 기본에딧 VSCode로 설정하는 법](https://codi-rano.tistory.com/139)

## How to set

먼저 git template에 적용할 `gitmessage.txt`를 만들기

**✨ `gitmessage.txt` 예시**

```
Subject line (try to keep under 50 characters)

Multi-line description of commit,
feel free to be detailed.

[Ticket: X]
```

<br/>

이렇게 다 만들고나면 설정을 해줘야하는데..  
터미널 열고 난 후 아래와 같이 쓰고 적용!

```sh
$ git config --global commit.template ~/.gitmessage.txt
$ git commit
```

> 특별히 주의할 점이라한다면, 만든 `gitmessage.txt`를 절대 경로로 지정해줘야 할 수도 있다!  
> `gitmesasge.txt`를 만든 곳에서 위 명령을 입력해서 적용했지만  
> Commit을 하려고 하는 순간 **_파일을 찾을 수 없다며 커밋조차 안되는 마법_** 이 발생할 수도 있다!!

- 이 문제를 해결하기 위해선 `gitmessage.txt`가 있는 _절대 경로_ 까지 입력해주세요!!!  
   지금 내 터미널이 있는 가르키고 있는 절대경로를 보려면..? `pwd`

<br/>

설정 한 후에 Commit을 할 때 편집기에선 아래와 같은 내용이 나옴!

```
Subject line (try to keep under 60 characters)

Multi-line description of commit,
feel free to be detailed.

[Ticket: X]
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
# modified:   lib/test.rb
#
~
~
".git/COMMIT_EDITMSG" 14L, 297C
```

<br/>

위에 있는 건 순전히 예시일 뿐...  
다들 커밋메세지 쓰실 때 Commit Type 쓰시잖아요? 그렇죠?  
입맛에 따라 적용해주세요!

제가 적용한 `gitmessage.txt`는..

```
##########################(DESCRIPTION START)##########################
# --- Commit 예시  ---

# [이슈번호] Commit타입: :이모지: 본문
# 예시 👉   [#1] feat: :sparkles: 사이드바 스켈레톤 코드 구현
# [❗️] 상황에 따라 적용하여 커밋메세지 쓰기! (이모지나 이슈번호 없어도 됨)

# --- Commit Message Table  ---

# | Commit 종류    | 이모지                | Commit 타입 |
# | -------------- | --------------------- | ----------- |
# | Initial commit | 🎉 `:tada:`           | init:       |
# | New Feature    | ✨`:sparkles:`        | feat:       |
# | Bug Fix        | 🐛`:bug:`             | fix:        |
# | Refactor Code  | 🔨 `:hammer:`         | refactor:   |
# | Styling UI     | 🎨`:art:`             | style:      |
# | Documentation  | 📚 `:books:`          | docs:       |
# | Tests          | 🚨 `:rotating_light:` | test:       |
# | Chore          | ⚙️`:gear:`            | chore:      |


# --- Commit Type Description  ---

#   feat    : 기능 (새로운 기능)
#   fix     : 버그 (버그 수정)
#   refactor: 리팩토링
#   style   : 스타일 (코드 형식, 세미콜론 추가: 비즈니스 로직에 변경 없음)
#   docs    : 문서 (문서 추가, 수정, 삭제)
#   test    : 테스트 (테스트 코드 추가, 수정, 삭제: 비즈니스 로직에 변경 없음)
#   chore   : 빌드 업무 수정, 패키지 매니저 수정, 기타 변경사항

##########################(DESCRIPTION END)##########################
```

이렇게 작성했습니다!  
저는 VSCode에서 Commit 할 때는 아래와 같이 나옵니다!

<img src="https://user-images.githubusercontent.com/33610315/131132587-ec18a8ca-71ef-4019-8c35-08b47aa40a35.png" width="600"/>

만약 이 템플릿을 쓰신다면  
메시지를 작성할 땐, 맨 위 공백 (한 줄) 그 곳에서부터 작성하면 됩니다!


</br>

이 작업을 하면서 다양한 git commit 이모지가 있다는 것도 알게 되었고, (아래 참고자료 봐주세요!!)  
이제 커밋 할 때마다 커밋 타입 찾으러 안가도 됩니다!! "행복 😆"  

---

### 참고자료

- [8.1 Git맞춤 - Git 설정하기](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)
- [github(parmentf): Git Commit message Emoji](https://gist.github.com/parmentf/035de27d6ed1dce0b36a)
