---
title: React V18 Suspense
date: 2022-06-25 23:06:64
category: React
thumbnail: { thumbnailSrc }
draft: false
---

실험적 기능으로만 지원하던 `Suspense`가 v18부터 정식 지원하기 시작했습니다~! 🎉 와~~

## Suspense란?

컴포넌트의 랜더링을 진행할 때 `loading`의 역할을 선언적으로 처리할 수 있도록 도와주는 기능이에요.

서버에서 데이터를 가져오는 기능의 경우 `async`로 진행되는데 일반적으로 외부 라이브러리를 통해 `loading` 처리를 하는 경우가 많습니다.

Suspense는 컴포넌트가 필요한 데이터를 가져올 때 데이터가 아직 로드되니 않았다는 것을 알려줘요.

## Suspense 사용법

```tsx
    <Suspense fallback={<Loading />}>
      <MemberCard />
    </Suspense>
```
컴포넌트를 위 처럼 Suspense로 감싸주게 되면 컴포넌트의 랜더링을 MemberCard를 불러오는 데이터를 가져오는 동안 중단하고 
그 작업이 끝나기 전까지는 fallback 속성 안에 있는 컴포넌트를 보여주게 돼요.

```tsx
import React, { Suspense } from "react";

function MemberSection({ memberListData }) {
    
    return (
        <div>{
            memberListData.map(member => (
                <Suspense fallback={<Loading />}>
                    <MemberCard member={member} />
                </Suspense>
            ))
        }</div>
    );
}

export default MemberSection;
```
위 예시처럼 사용할 수 있는 것이죠.

비동기처리하는 코드를 더 자세히 봅시다.

## 일반적인 비동기

```js

async function getUser() {
  const res = await apiClient.get<User>(`URL`)

  return res.data;
}
```

위 코드의 경우 비동기 상태에 따른 별도 처리가 필요해요.
try-catch로 감싸서 에러 핸들링 처리를 해봅시다.

```js
async function getUser() {
    try {
      const res = await apiClient.get<User>(`URL`)
      return res.data;
      
    } catch (error) {
      console.log(error);
    }
}
```

이 함수를 컴포넌트에서 사용하기 위해선 `useState`, `useEffect` 등의 리액트 hooks를 이용해 
리액트 컴포넌트의 상태로 데이터를 관리해야 해요.
```jsx
function useUser() {
    const [data, setData] = useState<User | null>(null)

    useEffect(() => {
        getUser().then(data => setData(data))
    }, [])
    return { data }
}
```

Hook으로 사용한다면 이렇게 작성할 수 있겠죠.

보통의 비동기 처리에 대한 값을 반환할 때는 loading, error 등의 비동기 처리에 따른 상태(state) 값들도 함께 전달하게 되는데, 
이 값에 대한 별도의 처리를 해야해요.

 
### 문제점 
1. 로딩 상태인지, 에러 상태인지 함수를 사용할 때마다 정의해줘야 한다.
2. 여러 에러들을 각각의 컴포넌트 또는 hooks에서 처리를 해줘야 하기 때문에 손이 많이 간다.

> "모든 컴포넌트에 대한 에러처리를 도와주는 기능이 있으면 좋겠다."

## 선언적으로 처리하기 (Suspense)

많이 알려진 서버 상태 관리툴(SWR, React-Query)는 Suspense 옵션을 지원해요.

React-Query와 SWR의 사용방법은 거의 비슷하니 SWR의 코드를 살펴봅시다.

```tsx
    import { useSWR } from 'swr'
    import { Suspense } from 'react'
    import { Loading } from './Loading'
    import { Error } from './Error'
    
    function MemberSection({ memberListData }) {
        const { data, error } = useSWR(`URL`, getUser, { suspense: true })
        
        return (
            <div>{
                memberListData.map(member => (
                    <Suspense fallback={<Loading />}>
                        <MemberCard member={member} />
                    </Suspense>
                ))
            }</div>
        )
    }
    
    export default MemberSection;
```

-> 데이터의 loading 부분은 Suspense가 맡아서 처리하게 되었어요.

>"에러핸들링도 선언적으로 가능할까?"


## 에러핸들링 선언적으로 처리하기 (ErrorBoundary)

로딩에 대한 처리를 Suspense가 했다면 에러에 대한 선언적처리는 ErrorBoundary가 담당해요.


아래는 React 공식 문서에서 소개하고 있는 ErrorBoundary 코드에요.

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error) {
    return { hasError: true }
  }

  componentDidCatch(error, errorInfo) {
    logErrorToMyService(error, errorInfo)
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>
    }

    return this.props.children
  }
}
```

사용하는 방법도 간단한데 단순히 async 컴포넌트를 감싸주면 돼요.

```jsx
<ErrorBoundary>
    <Suspense fallback={<Loading />}>
        <MemberCard member={member} />
    </Suspense>
</ErrorBoundary>
```

## 마치며

프로젝트를 하다보면 비동기처리를 할 일이 정말 많은데 Suspense를 이용해 선언적으로 처리할 수 있어요.

사용자의 경험 뿐 아니라 개발자의 경험에도 엄청난 경험이라고 생각해요.


### Reference
[React에서 선언적으로 비동기 다루기](https://jbee.io/react/error-declarative-handling-1/)

[React Suspense with the Fetch API](https://charles-stover.medium.com/react-suspense-with-the-fetch-api-a1b7369b0469)