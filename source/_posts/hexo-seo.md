---
title: Hexo SEO - 하루 만에 검색 엔진 최적화 시키기
intro: Hexo로 만들어진 블로그를 빠르게 검색 엔진 최적화 시키는 방법
date: 2018-08-26 17:27:39
tags: [hexo, seo, github-pages]
category: hexo
---
Hexo와 같이 개인이 직접 만들어 운영하는 블로그는 직접 검색 엔진 최적화(영어: search engine optimization, SEO)를 시켜줘야 합니다. 나름 시간을 많이 투자해야하는 작업인데 다행히도 Hexo는 SEO와 관련된 플러그인이 많이 있습니다. 해당 글에선 그 플러그인들을 이용한 SEO에 대해 다뤄보겠습니다.

# 플러그인 설치 및 사용

## [hexo-auto-canonical](https://github.com/HyunSeob/hexo-auto-canonical)

자동으로 표준 링크를 생성해줍니다.

설치

```console
npm install --save hexo-auto-canonical
```

사용

HTML의 HEAD 태그 안에 넣어줍니다.

ejs인 경우

```js
<%- autoCanonical(config, page) %>
```

jade인 경우

```js
| !{ autoCanonical(config, page) }
```

## [hexo-autonofollow](https://github.com/liuzc/hexo-autonofollow)

블로그의 포스들에 있는 외부 링크들에 자동으로 nofollow 속성을 추가해줍니다.

설치

```console
npm install hexo-autonofollow --save
```

사용

_config.yml 파일에 추가합니다.

```yml
nofollow:
    enable: true
    exclude:
    - exclude1.com
    - exclude2.com
```

- enable - 플러그인을 사용합니다. 기본값은 false입니다.
- exclude - 들어오지 못하게 할 hostname

## [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)

자동으로 Atom 1.0 또는 RSS 2.0 피드를 생성해줍니다.

설치

```console
npm install hexo-generator-feed --save
```

사용

_config.yml 파일에 추가합니다. 피드의 종류를 정할 수 있는데 네이버는 Atom을 지원하지 않음으로 RSS2를 권장합니다.

```yml
feed:
  type: rss2
  path: rss2.xml
  limit: 20
```

- type - 피드의 종류 (atom/rss2)
- path - 피드가 생성될 경로 (Default: atom.xml/rss2.xml)
- limit - 최신 포스트의 개수 설정 (0 또는 false 입력 시 모든 포스트)

## [hexo-generator-seo-friendly-sitemap](https://github.com/ludoviclefevre/hexo-generator-seo-friendly-sitemap)

설치

```console
npm install hexo-generator-seo-friendly-sitemap --save
```

사용

```yml
sitemap:
  path: sitemap.xml
  tag: false
  category: false
```

- path - sitemap이 생성될 경로
- tag - sitemap에 tag 포함 여부
- category - sitemap에 category 포함 여부

## [hexo-generator-robotstxt](https://github.com/leecrossley/hexo-generator-robotstxt)

자동으로 robots.txt 파일을 생성해줍니다.

설치

```console
npm install hexo-generator-robotstxt --save
```

사용

_config.yml 파일에 추가합니다.

```yml
robotstxt:
  useragent: "*"
  allow:
    - /
  sitemap: https://username.github.io/sitemap.xml
```

결과

```html
User-agent: *
Allow: /
Sitemap: https://username.github.io/sitemap.xml
```

# 검색 엔진에 등록하기

SEO 초기 세팅이 끝났으니 그걸 검색 엔진들에게 알려줘야 합니다! 간혹 사이트의 소유권 인증하라고 제공받은 파일을 사이트에 올리라고 하는데 public 폴더 안에 넣어주시고 `hexo deploy`해주시면 됩니다!

## 구글

[구글 웹마스터 도구](https://www.google.com/webmasters/tools/home?hl=ko)에서 구글의 SEO를 관리할 수 있습니다.

- 본인의 사이트인 것을 인증해야 등록이 가능합니다
- Search Console > 크롤링 > Sitemap에서 /sitemap.xml과 /rss2.xml을 등록해야 합니다.

## 네이버

[네이버 웹마스터 도구](https://webmastertool.naver.com/)에서 네이버의 SEO를 관리할 수 있습니다.

- 본인의 사이트인 것을 인증해야 등록이 가능합니다.
- 요청 > (사이트맵 제출/RSS 제출)에서 (sitemap.xml/rss2.xml)을 등록해줍니다.

## 다음

[다음 검색등록](https://register.search.daum.net/index.daum)에서 다음의 SEO를 관리할 수 있습니다.

- 간단히 사이트의 주소와 이메일만으로 등록할 수 있습니다.