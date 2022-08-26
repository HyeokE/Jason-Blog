---
title: yarn berry 모노레포 도전기 #2
date: 2022-07-16 00:07:44
category: Frontend
thumbnail: { thumbnailSrc }
draft: false
---


# 서론

전 글에 이어서 모노레포 작업을 진행 중에 이슈를 발견했다.
빌드 도중 firebase 모듈을 불러오지 못하는 이슈인데 번들러를 변경해봐도 오류가 발생했다.
```
[vite]: Rollup failed to resolve import "@firebase/app" from "../../.yarn/__virtual__/@firebase-storage-virtual-cc77973ef1/0/cache/@firebase-storage-npm-0.9.9-68fe8fe822-99692bf66d.zip/node_modules/@firebase/storage/dist/index.esm2017.js".
```
검색해보니 PnP 방식을 사용했을 때의 오류라고 한다.
추후 firebase는 모두 제거를 할 예정이기 때문에 임시로 PnP를 비활성화하고 node_modules를 생성하는 방향으로 선택했다.

# 본론

프로젝트의 구조는 다음과 같다.
![](https://velog.velcdn.com/images/jhjeong00/post/ace8e80d-8f01-41ba-a30a-967429579112/image.png)

lint(prettier, eslint)는 rout에서 프로젝트 전체로 공유하고 Package 내부에는 각 도메인들과 디자인 시스템이 들어갈 예정이다.

CI/CD를 위해 프로젝트의 변경에 따라 Github Action을 작성했다.

```yaml
name: Deploy to GDSC DJU WEB Live Channel

on:
  push:
    branches:
      - master
    paths:
      - "packages/gdsc-dju-web/**" //위 경로가 포함된 파일이 변경되면 실행
      - ".github/workflows/gdsc-dju-web-deploy-live.yml"
  pull_request:
    branches:
      - master
    types:
      - closed
    paths:
      - "packages/gdsc-dju-web/**"
      - ".github/workflows/gdsc-dju-web-deploy-live.yml"
jobs:
  Deploy_Live_channel:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    env:
      working-directory: ./packages/gdsc-dju-web
    environment:
      name: GDSC DJU WEB Production
      url: https://gdsc-dju.web.app
    steps:
      - uses: actions/checkout@v2
      - name: Setting .env
        run: |
          echo "VITE_FIREBASE_API_KEY=${{ secrets.VITE_FIREBASE_API_KEY }}" >> .env
          echo "VITE_FIREBASE_AUTH_DOMAIN=${{ secrets.VITE_FIREBASE_AUTH_DOMAIN }}" >> .env
          echo "VITE_FIREBASE_DATABASE_URL=${{ secrets.VITE_FIREBASE_DATABASE_URL }}" >> .env
          echo "VITE_FIREBASE_PROJECT_ID=${{ secrets.VITE_FIREBASE_PROJECT_ID }}" >> .env
          echo "VITE_FIREBASE_STORAGE_BUCKET=${{ secrets.VITE_FIREBASE_STORAGE_BUCKET }}" >> .env
          echo "VITE_FIREBASE_MESSAGING_SENDER_ID=${{ secrets.VITE_FIREBASE_MESSAGING_SENDER_ID }}" >> .env
          echo "VITE_FIREBASE_APPID=${{ secrets.VITE_FIREBASE_APPID }}" >> .env
          echo "VITE_FIREBASE_MEASUREMENT_ID=${{ secrets.VITE_FIREBASE_MEASUREMENT_ID }}" >> .env
          cat .env
        working-directory: ${{ env.working-directory }}
      - name: install package
        run: yarn install
      - name: install sitemap package
        run: yarn add babel-cli babel-preset-es2015 babel-preset-react babel-register
        working-directory: ${{ env.working-directory }}
      - name: Project Build
        run: yarn build
        working-directory: ${{ env.working-directory }}
      - name: Generate sitemap
        run: node sitemap-builder.js
        working-directory: ${{ env.working-directory }}
      - uses: FirebaseExtended/action-hosting-deploy@v0
        id: run_firebase_deploy
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT_GDSC_DJU }}'
          channelId: live
          projectId: gdsc-dju
          entryPoint: './packages/gdsc-dju-web'
      - name: Discord Message Notify
        uses: appleboy/discord-action@0.0.3
        with:
          webhook_id: ${{ secrets.DISCORD_FRONT_WEBHOOK_ID }}
          webhook_token: ${{ secrets.DISCORD_FRONT_WEBHOOK_TOKEN }}
          color: "#4285f4"
          args: |
            gdsc-dju-web has been deployed to Live channel
            `Contributor` ${{github.actor}}
            `Event Status` ${{github.event_name}}
            `URL` https://gdsc-dju.web.app
            `Build Time` ${{steps.run_firebase_deploy.outputs.expire_time}}

```

# 결론

여러 레포에 나눠진 프로젝트를 하나로 뭉쳐보았다.
공통모듈까지 작성해야 모노레포의 장점을 완벽히 소화할 수 있을 것 같다.
여러 도메인을 관리하고 비슷한 디자인, 코드를 사용하신다면 한번쯤 고려해보는 것은 어떨까요?