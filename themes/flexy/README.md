# Flexy(Custom)

Flexy는 [Hexo](https://hexo.io/)를 위한 블로그이며, 제작에 [Flexbox](https://philipwalton.github.io/solved-by-flexbox/)를 이용했습니다.

## Demo

[이성호의 블로그](https://iseongho.github.io)에서 실사용 중입니다.

## Usage

[Hexo deployer](https://github.com/iseongho/iseongho.github.io-deployer)에서 Subtree를 이용하여 사용합시다.

```console
git remote add custom-flexy https://github.com/iseongho/custom-flexy.git
git subtree add --prefix themes/flexy/ custom-flexy master
npm un -S hexo-renderer-ejs
npm i -S hexo-renderer-jade
```

그 후 Root의 `_config.yml`에서 `theme`를 `flexy`로 바꿔줍니다.

## Features

- Written in Jade and Stylus
- Uses Flexbox
- Responsive
- Mobile first
- CSS3 animations in header
- Uses Stylus Nib
- normalize.css
- Schema.org markup

In the header, the `transform3d()` function is used, which triggers GPUs to render the animation. This makes the page very quick to render, as the only background image is a 2.8K big SVG. It runs without using any JavaScript out of the box.

It's mobile first, so the media queries in the `source/styles/screen.styl` target the landscape screens.

## Post Variables

In the homepage post listing, you can use the `post.intro` variable to define an introduction. To do this, add the `intro` variable to your YAML in your Markdown file. Comments can be toggled with the `post.comments` boolean variable.

```md
title: The Rise of the Autobots
intro: A look at the upward march of automation into the vehicles around us.
date: 2015-05-28 13:45:00
comments: false
---
```

## Articles Page

In your root's `_config.yml`, set `filename_case` to `1`. Create a new `Articles` page by giving the `hexo new page 'Articles'` command. `layout/_content.jade` will output a yearly summary of posts.

## Fonts

기본적으로 제주 고딕체를 사용하며, Code는 Fira Mono 글꼴을 사용합니다. 위치는 `source/fonts` 디렉토리의 `WOFF`, `WOFF2` 포맷의 파일들입니다.

## Forked from

원본의 Flexy 테마가 궁금하시다면 [sjaakvandenberg/flexy](https://github.com/sjaakvandenberg/flexy)에서 알아보는 것을 추천합니다.