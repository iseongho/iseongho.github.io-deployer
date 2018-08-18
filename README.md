# iseongho.github.io-deployer

[이성호의 블로그](https://iseongho.github.io) 배포기입니다.

## Usage

기본적으로 [Hexo 공식 문서](https://hexo.io/ko/docs/)에 사용법이 기재돼있습니다.

### Install

- [Node.js](https://nodejs.org/ko/)
- [Git](https://git-scm.com/)

컴퓨터에 이미 이런 것들이 설치되어 있다면, 축하드립니다! 이제 npm을 이용하여 Dependencies를 설치하기만 하면 됩니다.

```console
npm install
```

### New post

아래의 커맨드로 새로운 문서를 발행합니다:

```console
hexo new <제목>
```

source/_posts 폴더에 <제목>.md 마크다운 문서가 생성됩니다.

```js
---
title: Hello World
date: 2018-07-29 15:49:05
tags:
---
<!-- 이곳에 내용을 작성합니다 -->
```

아래의 커맨드로 문서의 정적 리소스를 생성합니다:

```console
hexo generate
```

### Deploy

아래의 커맨드로 배포합니다:

```console
hexo deploy
```
