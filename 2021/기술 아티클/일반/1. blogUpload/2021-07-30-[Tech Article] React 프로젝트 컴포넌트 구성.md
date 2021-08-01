# 📃 React 프로젝트 컴포넌트 구성 

항상 React 프로젝트를 할 때 컴포넌트 구성에 대해 많은 고민이 있었다.  
또한 나중에 하게 될 `Next.js`의 규칙을 조금은 알 수 있다고 생각하여 정리.

**이 글에서의 말..**

- 프로젝트가 시작된지 얼마 되지 않은 경우에 소스는 비교적 일관성을 가지고 있음.  
   하지만, 시간이 지남에 따라 여러 사람이 참여하고,  
   비슷한 새로운 프로젝트가 만들어지면서 점점 일관성이 떨어지게 됨.  
   (문서나 리뷰 과정이 있으면 비교적 낫지만, 완전히 방지하기는 어려움)
- React 자체는 UI 구성을 위한 **라이브러리** 이기 때문에 구성에 아무 제약이 없음  
  하지만 이 글을 쓴 카카오스타일은..
  - 일부 프로젝트에서는 `Next.js`를 쓰고 있음, 이는 **프레임워크** 이기에 여러가지 규칙이 있음.

## **[1]** 라우트

`Next.js`에서는 라우트를 **pages** 디렉토리에서 정하고 있음.  
이 글의 저자의 회사는 **pages**를 프로젝트 루트에 두지 않고, _`src` 밑에 모아두는 것을 선택_

`Next.js`가 아닌 일반 React 프로젝트에서도 마찬가지로 **pages** 밑에 두지만,  
라우트 연결은 수동으로 해야 함

**예시) 비 Next.js 프로젝트에서의 라우터 설정 예**

```tsx
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom';
import HomePage from 'pages';
import LoginPage from 'pages/login';
import SignupPage from 'pages/signup';
import QnaQuestionMainPage from 'pages/qna/questions';
import QnaQuestionNewPage from 'pages/qna/questions/new';
import QnaQuestionDetailPage from 'pages/qna/questions/[question_id]';
import ChatRoomMainPage from 'pages/chat/rooms';
import ChatRoomDetailPage from 'pages/chat/rooms/[room_id]';

function App() {
  return (
    <Router>
      <Switch>
        <Route exact path="/">
          <HomePage />
        </Route>
        <Route exact path="/login">
          <LoginPage />
        </Route>
        <Route exact path="/signup">
          <SignupPage />
        </Route>
        <Route exact path="/qna/questions">
          <QnaQuestionMainPage />
        </Route>
        <Route exact path="/qna/questions/new">
          <QnaQuestionNewPage />
        </Route>
        <Route path="/qna/questions/:question_id">
          <QnaQuestionDetailPage />
        </Route>
        <Route exact path="/chat/rooms">
          <ChatRoomMainPage />
        </Route>
        <Route path="/chat/rooms/:room_id">
          <ChatRoomDetailPage />
        </Route>
      </Switch>
    </Router>
  );
}

export default App;
```

- `Next.js`에서는 같은 위치에 페이지 파일이 있으면 자동으로 라우트 설정이 됨.

## **[2]** 페이지를 실제 구성하는 컴포넌트

간단한 뷰는 페이지 파일에 넣을 수 있겠지만 복잡한 경우 여러 컴포넌트로 나누어야 함.  
이를 **pages** 밑에 두면 `Next.js` <u>라우트로 인식</u> 되므로 별개의 위치에 둬야 함!

따라서 이를 **components** 디렉토리에 두기로 하고  
이때 RESTful한 주소를 갖도록 위치한 페이지와 달리 도메인별로 묶어서 구성

- **QnaQuestionMainPage(**`/pages/qna/questions/index.tsx`**)** 에 대응하는 컴포넌트는  
  `/components/qna/question/main/index.tsx`에 위치.  
  (컴포넌트명은 **QnaQuestionMain**)
- **ChatRoomDetailPage(**`/pages/chat/rooms/[room_id]/index.tsx`**)**에 대응하는 컴포넌트는  
  `/components/chat/room/detail/index.tsx`에 위치  
  (컴포넌트명은 **ChatRoomDetail**)

파일명을 컴포넌트 이름과 일치시킬지 여부를 내부에서 논의할 결과 진입점은 `index.tsx`로 통일.

**예시) QnaQuestionMainPage의 구조**

```tsx
import { FC } from 'react';
import QnaQuestionMain from 'components/qna/question/main';

const QnaQuestionMainPage: FC = () => {
  return <QnaQuestionMain />;
};

export default QnaQuestionMainPage;
```

## **[3]** 데이터 가져오기

Next.js 프로젝트와 비 Next.js 프로젝트는 데이터를 가져오는 시점이 다름.

- `Next.js`는 클라이언트에 내보내기 전에 React와 무관하게 데이터를 가져옴.
- **`비`**` Next.js` 프로젝트는 클라이언트 로딩이 끝난 후 데이터를 가져와야 함.

어느 경우에든 비슷하게 구성하기 위해 데이터 가져오는 것을 `fetchData`라는  
함수로 분리하여 컴포넌트쪽에 두고, 각 컴포넌트는 상위 페이지로 부터 데이터를 props로 받아옴.  
이렇게 구성하면..?

- 장점: 컴포넌트에 대한 **스토리북** 구성시 <u>데이터를 다르게 부여하기 편하다</u>
- 단점: 데이터가 고정이 아닌 경우(정렬 옵션등에 의해 페이지 이동 없이 바뀌어야 하는 경우),  
  <u>상위 페이지로 이를 전달하는 방법을 고민해 봐야</u> 한다는 점

**예시) 위 규칙에 따라 작성된 컴포넌트 내용**

`QnaQuestionMain.tsx` (컴포넌트)

```tsx
interface Question {
  id: string;
  title: string;
  date: number;
}

export interface Props {
  total_count: number;
  question_list: Question[];
}

const QnaQuestionMain: FC<Props> = (props) => {
  return (
    <div>
      {props.question_list.map((question) => (
        <QuestionView question={question} key={question.id} />
      ))}
    </div>
  );
};

export default QnaQuestionMain;

export async function fetchData(context: GetServerSidePropsContext | undefined): Promise<Props> {
  const result = await fetch(...);
  return {
    total_count: result.total_count,
    question_list: result.question_list,
  };
}
```

`QnaQuestionMainPage.tsx` (페이지 / Next.js 프로젝트)

페이지의 `getServerSideProps` 메소드에서 컴포넌트에서 만든 `fetchData`를 호출

```tsx
import type { GetServerSideProps } from 'next';
import QnaQuestionMain, { fetchData, Props } from 'components/qna/question/main';

export const getServerSideProps: GetServerSideProps<Props> = async (context) => {
  return {
    props: await fetchData(context),
  };
};

const QnaQuestionMainPage: FC<Props> = (props) => {
  return <QnaQuestionMain {...props} />;
};

export default QnaQuestionMainPage;
```

`QnaQuestionMainPage.tsx` (페이지 / **비** Next.js 프로젝트)

페이지의 `useEffect` 안에서 `fetchData`를 호출

```tsx
import QnaQuestionMain, { fetchData, Props } from 'components/qna/question/main';

const QnaQuestionMainPage: FC = () => {
  const [props, setProps] = useState<Props>({
    total_count: 0,
    question_list: [],
  });

  useEffect(() => {
    const run = async () => {
      setProps(await fetchData());
    };
    run();
  }, []);

  return <QnaQuestionMain {...props} />;
};

export default QnaQuestionMainPage;
```

## **[4]** 자잘한 규칙

**1.** 컴포넌트를 내보낼 때는 `default export`를 사용

- 장점: 내부적으로 컴포넌트 이름을 바꿔도 사용하는 쪽에 영향이 없다.
- 단점
  - 정의하는 쪽과 사용하는 쪽의 이름을 다르게 줄 수 있어서 찾기 어려워짐!
  - 스토리북에서 컴포넌트 Props를 제대로 인식하지 못하는 작은 문제가 있음

**2.** 상대 경로와 절대 경로를 참조해야 할 경우

- 상대 경로 참조
  - 한 페이지 컴포넌트를 작게 쪼갠 경우 (가까울 경우)
- 절대 경로 참조
  - 멀리 떨어진 컴포넌트를 참조할 경우  
    (**pages** 에서 **components** 까지 간다던가..)
- 이렇게 구성해야 파일을  
  `/pages/qna/questions/index.tsx` -> `/pages/questions/index.tsx`로 옮겨도  
  import 변경이 필요 없음.

**+** 절대 경로 세팅할 때 참고 (with Craco)

- `@/components/qna/question/main` 처럼 앞에 `@/`가 붙는 형태  
  (`@/*` 라는 글자를 `src/*` 로만 지정해주면 됨)
- `components/components/qna/question/main` 처럼 앞에 특정 폴더들이 붙는 형태  
  (절대 경로로 쓰고자하는 폴더들을 설정해주면 됨)
- 이 글 저자의 회사는.. 2번째꺼를 사용하고 있다함!

---

### 참고자료

- [kakao style : React 프로젝트 컴포넌트 구성](https://devblog.croquis.com/ko/2021-07-29-1-react-component-organization/)
