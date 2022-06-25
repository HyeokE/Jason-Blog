---
title: React V18 Suspense
date: 2022-06-25 23:06:64
category: React
thumbnail: { thumbnailSrc }
draft: false
---

실험적 기능으로만 지원하던 `Suspense`가 v18부터 정식 지원하기 시작했습니다~! 🎉

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
import Posts from "./Posts";

function User({ resource }) {
    const user = resource.user.read();

    return (
        <div>
            <p>
                {user.name}({user.email}) 님이 작성한 글
            </p>
            <Suspense fallback={<p>글목록 로딩중...</p>}>
                <Posts resource={resource} />
            </Suspense>
        </div>
    );
}

export default User;