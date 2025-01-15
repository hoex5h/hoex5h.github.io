---
layout: post
current: post
cover: "assets/images/cover/svelte.png"
navigation: True
title: "[Svelte] Deploy Sveltekit"
date: 2025-01-14 02:40:00
tags:
    - [Svelte, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Project, ]
---

# [Svelte] Deploy Sveltekit


이번에 졸업 프로젝트 하면서 간단한 프론트가 필요해서, 예전에 써본 기억이 있던 Svelte 로 뚝딱 만들었다.


아주 짤막하게 만들면 되는 주제에 Sveltekit 이 재밌어 보여서 한 번 써봤다.


그냥저냥 재밌기만 한 줄 알았는데, 이게 웬걸..? 배포할 때 build 폴더가 나오질 않는 것이었다...


앞으로 자주 쓸 일은 없겠지만 혹여나 이번 같이 짤막하게 프론트를 만들 일이 생길 수도 있기에


배포하는 방법 정도는 적어두려고 한다.


[Sveltekit 공식문서](https://kit.svelte.dev/docs/building-your-app)


Sveltekit 은 단순하게 `npm run build` 만 하면 build 폴더가 뿅 하고 생기지 않는다.. 어째서??


> Before you can deploy your SvelteKit app, you need to adapt it for your deployment target.


SvelteKit 을 배포하려면, `adapt` 해야 한단다.. 이게 무슨 소리인가하니,


> Adapters are small plugins that take the built app as input and generate output for deployment.


어댑터를 하나 달아주어야 하는데, 이 어댑터가 바로 deployment 를 위한 output 을 만들어 준다고 한다.


Cloudflare, netlify, ... 등등 다양한 어탭터가 있는데, 나는 따로 서버에 직접 배포할 것이므로 Node 서버 adapter 를 선택했다.


[Sveltekit Node Server](https://kit.svelte.dev/docs/adapter-node)


막상 알고나면, 해야할 것은 별 거 없다.


`npm i -D @sveltejs/adapter-node`


커맨드를 통해서, adapter plugin 다운 받아 주시고, 프로젝트 루트 path 에 있는 `svelte.config.js` 에 들어간다.



```
text
import adapter from '@sveltejs/adapter-node';

export default {
	kit: {
		adapter: adapter()
	}
};


```



이후 위의 코드 처럼, 다운 받은 plugin 으로 부터 adapter 를 import 하고, kit 에 달아준다.


그 뒤로는 더욱 별 거 없다.


`npm run build` 하고, `npm ci --omit dev` 하고, `node build` 하면 된다!


아래는 아주 간단하게 작성한 Dockerfile 이다.



```
shell
FROM node:16.14.0 AS build

ARG PORT=3000

WORKDIR /app

COPY . ./

RUN npm install

RUN npm i -D @sveltejs/adapter-node

RUN npm run build

RUN npm ci --omit dev

EXPOSE ${PORT}

CMD node build



```


