---
title: React v18 주요 변경점
date: 2022-07-20 00:07:44
category: Bundler
thumbnail: { thumbnailSrc }
draft: false
---
# React v18 주요 변경점

## Automatic Batching
React v18 부터 상태 업데이트를 하나로 통합해서 배치처리를 한 후 리렌더링을 진행합니다.

### 리렌더링 관련 성능 개선
과거 React v17 에서는 이벤트 핸들러 내부에서 발생하는 상태 업데이트만 배치처리를 지원했습니다.
하지만 이벤트 핸들러 내부에 fetch()등 과 같은 콜백을 받아 처리하는 메소드가 존재할 경우 내부의 콜백이 모두 완료된 후에는 Automatic Batching이 처리되지 않았습니다.
```js
function handleClick(){
  setIsFetching(false);
  setError(null);
  setFormStatus('success');
}
```
쉽게 말해 위 예시처럼 state를 변경하는 함수가 3가지면 3번의 rerender가 발생했는데 18버전부터는 자동으로 한번만 묶어서 처리해준다.

>단 다른 컴포넌트에서 실행되는 이벤트는 별개로 처리된다.

## Suspense on the Server

서버 사이드 렌더링은 서버에서 HTML을 렌더해준다.
하나의 컴포넌트를 렌더할 때 서버에서 데이터를 받아오는 동안 대기해야했다.
이는 전체적인 앱 성능에 영향을 준다고 한다.

Suspense가 서버 렌더링에도 지원을 해줌으로서 데이터를 받아오는 동안 fallback을 출력해줌으로서 이를 해결했다.
![](https://velog.velcdn.com/images/jhjeong00/post/f6f384cc-0571-4d7a-a9c4-5201d2160ee3/image.png)


## Concurrent Feature
### createRoot

새로 나온 리액트 18에서는 ReactDOM.render가 아니라, createRoot를 사용해야 한다

### useTransition

`  const [isPending, startTransition] = useTransition();`
isPending은 지연되고 있음, startTransition은 지연시켜야할 함수를 인자로 받는다.

기존에 검색 결과 등에서 사용했던 setTimeout, Debounce ,Throttle 기능들을 대체할 수 있다.
일단 setTimeout과는 다르게 스케쥴링에 포함이 되지 않아 더 빠르다.
즉시 실행되고 동기적이지만 리액트가 렌더링할때 transition으로 입력된 값을 활용한다.
useTransition의 경우, isPending 상태값을 받아 렌더링 결과를 분기 처리할 수 있다.

>Reference: [React 18 for app developers
](https://youtu.be/ytudH8je5ko)