---
title: yarn berry 모노레포 도전기#1
date: 2022-07-16 00:07:44
category: Bundler
thumbnail: { thumbnailSrc }
draft: false
---

GDSC DJU에서 프론트엔드가 관리하는 사이트/서비스는 아래와 같아요.

>[GDSC DJU Web](https://gdsc-dju.web.app/)
[GDSC DJU Shared](https://gdsc-dju-shared.web.app/)
GDSC DJU admin (추후 분리)
[Hey teddy bear](https://hey-teddy.vercel.app/)
GDSC DJU Blog(개발중)
gds (gdsc design system) 개발예정


## 모노레포를 결심한 이유는 다음과 같습니다.
- 비슷한 디자인
- 비슷한 컴포넌트
- 디자인 에셋의 중복
- 디자인 시스템의 추후 추가 예정
- 여러 레포지토리의 관리가 어려움
- 대부분의 라이브러리가 중복됨
- 프론트엔드 개발자 분들의 원활한 피드백을 위해

## 기존 패키지 매니징에 대한 불만

제가 모든 프로젝트에서 npm을 사용하지 않는 이유는 다음과 같아요.

- 보안 이슈 (유저 데이터 유출)
- 라이브러리를 다운받는 속도가 느려요

yarn은 기존의 npm에 대한 문제를 모두 해결한 노드 패키지 매니저에요.

- 보안 빵빵
- 라이브러리에 대한 캐싱으로 속도 개선

## yarn 대신 모노레포를 선택하는 이유

기존의 npm, yarn을 이용한 프로젝트 패키지 매니징은 node modules를 이용해서 진행되었어요.

### 비효율적인 의존성 검색

NPM은 패키지를 찾기 위해서 상위 디렉토리의 node_modules 폴더를 탐색해요.

예를 들어 `/gdsc/library/buttons` 라는 폴더에서 패키지를 불러온다면 상위 폴더부터 존재하는 모든 node모듈을 찾아요. 상당히 비효율적이죠.

### 환경에 따라 달라지는 동작

NPM은 패키지를 찾지 못하면 상위 디렉토리의 node_modules 폴더를 계속 검색해요. 이 특성 때문에 어떤 의존성을 찾을 수 있는지는 해당 패키지의 상위 디렉토리 환경에 따라 달라집니다.

### 비효율적인 공간차지

NPM에서 만든 node_modules는 매우 큰 공간을 차지해요.

간단한 프로젝트도 엄청난 용량을 차지하죠,,

### 유령 의존성

예를 들어 의존성 트리가 왼쪽의 모습을 하고 있다고 가정할 때 왼쪽의 A (1.0)과 B(1.0)은 두 번 설치되므로 디스크 공간을 낭비해요.

그러나 오른쪽 처럼 트리가 변경되면서 패키지1에서 B(1.0)라이브러리를 불러올 수 있어요.
![](https://velog.velcdn.com/images/jhjeong00/post/b5c62c38-089c-44ae-ba7b-a8edff7a6b50/image.png)
위와 같이 의존하고 있지 않은 라이브러리에 dependency를 걸 수 있는 현상을 유령 의존성이라고 해요.

유령 의존성 현상이 발생할 때, package.json에 명시하지 않은 라이브러리를 조용히 사용할 수 있게 되는데 다른 의존성을 package.json 에서 제거했을 때 같이 사라지기도 합니다.

이런 특성은 의존성 관리 시스템을 혼란스럽게 만들어요.

## Plug’n’Play (PnP)

Yarn Berry는 위에서 언급한 문제를 새로운 PnP 전략을 이용하여 해결해요
yarn berry는 node-modules를 생성하지 않아요.

.yarn/cache 폴더에 의존성 정보가 저장되고 .pnp.cjs에 의존성을 찾을 수 있는 정보가 저장돼요.

.pnp.cjs를 이용하면 어떤 패키지가 어떤 라이브러리에 의존하고 있는지 각 라이브러리는 어디에 위치하는지를 알 수 있어요.

## 앞으로의 계획
디자인 시스템은 아직 기획단계이기 때문에 분리되어있는 프로젝트를 먼저 작업한 후 공통 모듈을 분리할 예정이에요.

reperence : https://toss.tech/article/node-modules-and-yarn-berry