---
title: 템플릿 엔진 Pug(Jade) 배우기
date: 2018-08-05 23:07:18
tags: [node, js, template_engine, pug, jade, express]
---
Pug는 요즘 가장 핫한 Node Express Engine입니다.
Pug를 통해 HTML 코드의 생산성을 증진하고, 동적 + 정적인 View를 만들기 위해 공부해봅시다.

# Node Express Engine Pug의 장점

- HTML을 간단하게 표현해서 가독성이 좋다.
- 마크업에 문법보다 코드량이 적어 생산성이 좋아진다.
- JS 연산 결과를 쉽게 보여줄 수 있다.
- 정적인 부분과 동적인 부분을 따로 할 수 있다.
- 타 Express Engine보다 Google Trend 수치가 높다.

# Pug는 Jade였다

Pug는 기존에 Jade라는 이름을 가지고 있었습니다. 하지만 이미 상표권이 있는 이름이였기 때문에 Pug라는 이름으로 바꾸게 됩니다.

# Pug와 HTML 코드의 차이

백 번 듣는 것보다 한 번 보는 게 낫겠죠. 직접 코드를 보고 차이점에 대해 생각해봅시다.

## Pug

```js
doctype html
html(lang='ko')
    head
        title Pug tutorial
        style.
            .title {
                color: blue;
            }
        script.
            console.log('This is log');
    body
        #header
            h1.title Header
        #main
            div Main
            ul
                - var items = [1, 2, 3, 4, 5]
                each item in items
                    li= item
            pre
                | This is
                | multiline
        #footer
            p Footer
```

## HTML

```html
<!DOCTYPE html>
<html lang="ko">
    <head>
    <title>Pug tutorial</title>
    <style>
        .title {
            color: blue;
        }
    </style>
    <script>
        console.log('This is log');
    </script>
    </head>
    <body>
        <div id="header">
            <h1 class="title">Header</h1>
        </div>
        <div id="main">
            <div>Main</div>
            <ul>
                <li>1</li>
                <li>2</li>
                <li>3</li>
                <li>4</li>
                <li>5</li>
            </ul>
            <pre>
                This is
                multiline
            </pre>
        </div>
        <div id="footer">
            <p>Footer</p>
        </div>
    </body>
</html>
```

### 차이점에 대하여

HTML보다 Pug 코드가 훨씬 적다는 게 눈으로 보입니다. 따라서 Pug를 접하시게 된다면 처음에는 문법을 익히느라 시간이 필요하겠지만 장기적으로 봤을 땐 빠른 개발이 가능할 것입니다.

# 문법 특징

- 마크업과 달리 닫는 태그가 없다. * `<div></div>`
- 들여쓰기로 종속성을 구별한다.
- 태그를 선언하려면 태그 이름만 쓰면 된다. * div { content }
- ID와 Class를 선언하려면 CSS의 기본 선택자를 사용하면 된다. * .container
- 태그 없이 ID나 Class만 선언하면 자동으로 div 태그를 사용한다.
- 태그에 속성을 주고 싶으면 괄호를 이용한다. * html(lang='ko')
- 여러 줄을 사용하고 싶으면 |를 사용한다.
- Script나 CSS로 태그를 사용할 땐 끝에 .을 입력한다. * script., style.
- 템플릿 안에서 Javascript를 사용하려면 앞에 -(Hyphen)을 입력한다.

# 문법 요약

Pug 작성에 있어서 자주 사용될 것 같은 문법을 모아봤습니다.

## Doctype 지정

HTML엔 정말 다양한 Doctype이 있고, Custom Doctype도 존재합니다. 하지만 자주 사용되는 HTML5에 대해서만 작성해두겠습니다.

### HTML5

작성

```js
doctype html
```

결과물

```html
<!DOCTYPE html>
```

## Block 생성

작성

```js
p foo
```

결과물

```html
<p>foo</p>
```

## 속성

괄호를 이용하여 속성을 주입해줍니다.

### 단일 속성

작성

```js
a(href='google.com') Google
```

결과물

```html
<a href="google.com">Google</a>
```

### 다중 속성

작성

```js
input(
  type='checkbox'
  name='agreement'
  checked
)
```

결과물

```html
<input type="checkbox" name="agreement" checked="checked" />
```

## Conditionals 조건문

여러 종류의 조건문에 대해 다뤄보겠습니다.

### if문

작성

```js
- var user = { description: 'foo bar baz' }
- var authorised = false
#user
  if user.description
    h2.green Description
    p.description= user.description
  else if authorised
    h2.blue Description
    p.description.
      User has no description,
      why not add one...
  else
    h2.red Description
    p.description User has no description
```

결과물

```html
<div id="user">
  <h2 class="green">Description</h2>
  <p class="description">foo bar baz</p>
</div>
```

### unless문

if의 반대 조건일 경우 true가 실행되는 조건문입니다.

이것과

```js
unless user.isAnonymous
  p You're logged in as #{user.name}
```

이것은 동일한 조건입니다.

```js
if !user.isAnonymous
  p You're logged in as #{user.name}
```

## Case 조건문

템플릿에서 자바스크립트 코드를 사용하기 위해 앞에 -(Hyphen)을 입력합니다.

작성

```js
- var friends = 10
case friends
  when 0
    p you have no friends
  when 1
    p you have a friend
  default
    p you have #{friends} friends
```

결과물

```html
<p>you have 10 friends</p>
```

### OR문과 Break

- OR문을 사용하고 싶다면 when 밑에 줄에 다시 when 조건을 걸면 됩니다.
- Break는 - break라고 입력합니다.

작성

```js
- var friends = 0
case friends
  when 0
  when 1
    p you have very few friends
    - break
  default
    p you have #{friends} friends
```

결과물

```html
<p>you have very few friends</p>
```

### Element 조건문

작성

```js
- var friends = 1
case friends
  when 0: p you have no friends
  when 1: p you have a friend
  default: p you have #{friends} friends
```

결과물

```html
<p>you have a friend</p>
```

## 반복문

템플릿에서 자바스크립트 코드를 사용하기 위해 앞에 -(Hyphen)을 입력합니다.

### for문

작성

```js
- for (var x = 0; x < 3; x++)
  li item
```

결과물

```html
<li>item</li>
<li>item</li>
<li>item</li>
```

### each문

작성

```js
-
  var list = ["Uno", "Dos", "Tres",
          "Cuatro", "Cinco", "Seis"]
each item in list
  li= item
```

결과물

```html
<li>Uno</li>
<li>Dos</li>
<li>Tres</li>
<li>Cuatro</li>
<li>Cinco</li>
<li>Seis</li>
```

## 마크업 코드를 내용물로 사용하기

그냥 작성하면 아래와 같이 꺽쇠가 `&lt;`와 `&gt;`로 변합니다.

작성

```js
p
  = 'This code is <escaped>!'
```

결과물

```html
<p>This code is &lt;escaped&gt;!</p>
```

'='을 '!='로 고쳐주면 마크업 태그를 그대로 사용합니다.

작성

```js
p
  != 'This code is <strong>not</strong> escaped!'
```

결과물

```html
<p>This code is <strong>not</strong> escaped!</p>
```

## Mixin 변수

템플릿 단위로 재사용 가능한 변수를 사용해봅시다. mixin으로 변수를 선언하고 '+{변수 이름}'으로 사용합니다.

작성

```js
// 선언
mixin list
  ul
    li foo
    li bar
    li baz

// 사용
+list
+list
```

결과물

```html
<ul>
  <li>foo</li>
  <li>bar</li>
  <li>baz</li>
</ul>
<ul>
  <li>foo</li>
  <li>bar</li>
  <li>baz</li>
</ul>
```

## Includes(템플릿 파일들 불러오기)

include { 파일 경로 }만 적어주시면 됩니다.

```js
html
  include includes/head.pug
  body
    h1 My Site
```

# 템플릿을 나누어 블로그 만들기

위의 Includes 설명만으론 이해가 안 될 수 있을 겁니다. Includes를 익히기 위해 블로그를 개발해봅시다.

## 생성해야 할 파일들

- blog.pug
- includes/header.pug
- includes/footer.pug

## 뼈대 (blog.pug)

```js
doctype html
html
  title My Blog
  body
    include includes/header.pug // 헤더를 불러옵니다.
    p#main Welcome to my blog. // 헤더와 푸터 사이의 내용을 입력합니다.
    include includes/footer.pug // 푸터를 불러옵니다.
```

## 헤더 (header.pug)

```js
head
  title My Blog
```

## 푸터 (footer.pug)

```js
footer#footer
  p Copyright (c) anonymous
```

## 결과물

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Blog</title>
  </head>
  <body>
    <h1>My Blog</h1>
    <p id="main">Welcome to my blog.</p>
    <footer id="footer">
      <p>Copyright (c) anonymous</p>
    </footer>
  </body>
</html>
```

- include한 header.pug의 'My Blog'가 잘 보입니다.
- blog.pug에 입력해둔 'Welcome to my blog.'가 잘 보입니다.
- include한 footer.pug의 'Copyright (c) anonymous'가 잘 보입니다.

# 마치면서

해당 강좌에서 사용된 프로젝트는 [github/pug-tutorial](https://github.com/iseongho/pug-tutorial)에 올려뒀습니다.