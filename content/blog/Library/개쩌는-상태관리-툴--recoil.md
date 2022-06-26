---
title: 멋진 상태관리 툴 Recoil
date: 2022-06-26 16:06:68
category: Library
thumbnail: { thumbnailSrc }
draft: false
---

![](https://images.velog.io/images/jhjeong00/post/6fc0e493-e33c-4525-addb-4418268e65cf/Screen%20Shot%202022-01-04%20at%202.56.10%20PM.png)

기존에 자주 사용하는 `Redux`는 사용자의 명령(`action`)을 `Reducer`->`Store`로 전달하여 상태를 변화시키고 그 값을 Component에 반영하는 원리인데 나는 `React-Redux`를 사용하면서 `React에서 쓰는게 좀 어색한데?/React스럽지 않은데?`라고 생각했다.

React는 hook을 사용하면서 컴포넌트에서 useState로 상태를 직접 변경하는데
**"상태관리를 기존의 useState와 비슷하게 할 수는 없을까."**
라는 의문이 들었고 토스에서 사용하는 상태관리 라이브러리인 Recoil을 찾았다.



## 이거다..!
---

[Recoil 공식문서](https://recoiljs.org/ko/docs/introduction/motivation)를 보면 제작자의 동기가 나와있는데
>우리는 API와 의미 및 동작을 가능한 React답게 유지하면서 이것을 개선하고자 한다.


Recoil의 상태변화는 atom으로부터 selector를 통해 컴포넌트로 이동하는 방식이다.
atom은 컴포넌트가 Subscribe할 수 있는 상태의 단위이며 Selector는 atom상태값을 동기/비동기방식으로 변환한다.

## Atoms

---

Redux와 달리 Recoil은 Atom으로 상태를 관리한다.

`Atom`이 업데이트 되면 `atom`을 구독한 컴포넌트는 새로운 값을 받아 재렌더링된다.

`atom`은 다음과 같이 사용한다.
```js
const fontSizeState = atom({
  key: 'fontSizeState',
  default: 14,
});
```
Atom은 고유한 키가 필요한데 이 키는 전역적으로 고유해야하며 Component가 atom을 구독할 때 사용된다.

위 예시처럼 기본값도 가진다.
`useState`와 매우 유사하다.

컴포넌트에서 atom을 사용하려면 `useRecoilState`라는 Hook을 사용한다.
간단하게 말해 전역으로 사용할 수 있는`useState`이다.

```js
function FontButton() {
  const [fontSize, setFontSize] = useRecoilState(fontSizeState);
  return (
    <button onClick={() => setFontSize((size) => size + 1)} style={{fontSize}}>
      Click to Enlarge
    </button>
  );
}
```

버튼을 누르면 글꼴의 크기가 1증가하며 fontSizeState를 사용하는 다른 컴포넌트의 글꼴도 같이 변화한다.


## Selectors

---

`Selector`는 `atom`이나 다른 `selector`를 입력으로 받는 함수이다.

상위 atom, `selector`가 업데이트되면 하위 `selector`도 재실행된다.
컴포넌트들은 `selectors`를 구독할 수 있고 `selector`가 변경되면 컴포넌트도 재렌더링된다.

`Selectors`는 상태를 기반으로 하는 파생 데이터를 계산하는 데 사용된다. 최소한의 상태만 `atoms`에 저장하고 다른 모든 데이터는 `selectors`에 명시한 함수를 통해 효율적으로 계산함으로써 쓸모없는 상태의 보존을 방지한다.

```js
const fontSizeLabelState = selector({
  key: 'fontSizeLabelState',
  get: ({get}) => {
    const fontSize = get(fontSizeState);
    const unit = 'px';

    return `${fontSize}${unit}`;
  },
});
```

전달되는 `get` 인자를 통해 `atoms`와 다른 `selectors`에 접근할 수 있다.
참조했던 다른 `atoms`나 `selectors`가 업데이트되면 이 함수도 다시 실행된다.

위 예시에서 `selector`는 `fontSizeState`라는 하나의 `atom`에 의존성을 갖는다.

```js
function FontButton() {
  const [fontSize, setFontSize] = useRecoilState(fontSizeState);
  const fontSizeLabel = useRecoilValue(fontSizeLabelState);

  return (
    <>
      <div>Current font size: ${fontSizeLabel}</div>

      <button onClick={setFontSize(fontSize + 1)} style={{fontSize}}>
        Click to Enlarge
      </button>
    </>
  );
}
```

Selector는 useRecoilValue를 통해 읽을 수 있다.

---

>이젠 Recoil 하세요~




