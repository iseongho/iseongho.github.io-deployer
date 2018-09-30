---
title: 리액트(React) 앱의 성능을 최적화시킨 비결
intro: 똑같은 앱을 만들더라도 더욱 최적화된 앱을 만들어 봅시다.
date: 2018-09-30 18:44:18
tags: [react, js, optimization, performance]
category: javascript
---
리액트(React)의 App 속도를 향상하게 만드는 몇 가지의 방법들을 숙지하고 있습니까? 요즘 사용되는 PC의 성능이면 별다른 문제가 없지만, 출시된 지 오래된 안드로이드 폰이라면 어떨까요? 렌더링 성능 이슈가 발생할 겁니다. 똑같은 앱을 만들더라도 더 성능이 좋은 앱을 만들어 봅시다.

# Component vs PureComponent vs Functional Component

리액트(React)에서 컴포넌트를 만드는 방법에는 세 가지가 있습니다. 모두 다 특징이 있으며 어느 상황에서 어떤 방법을 사용해야 가장 최적화를 잘 할 수 있는지 알아보겠습니다.

## Component

```jsx
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>Hello, world!</div>
    );
  }
}

export default App;
```

React를 확장(extends)해서 사용하는 클래스 기반의 Component입니다. 세 가지의 방법 중 유일하게 선택적으로 shouldComponentUpdate라는 라이프 사이클을 사용합니다. shouldComponentUpdate로 리렌더링(re-render)이 되는 상황을 정해줄 수 있지만 shouldComponentUpdate를 별도로 사용하지 않는다면 App의 Props와 State가 변경되는 모든 상황에 리렌더링(re-render)이 됩니다. 별도의 설정없이는 가장 많이 리렌더링(re-render)이 일어날 수 있는 방식입니다.

## PureComponent

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  render() {
    return (
      <div>Hello, world!</div>
    );
  }
}

export default App;
```

React를 확장(extends)해서 사용하는 클래스 기반의 PureComponent입니다. 기본적으로 Component와 똑같이 작동합니다만, 한 가지 다른 점이 있습니다. 오로지 자신의 Props와 State가 변경될 시에만 리렌더링(re-render)가 되도록 shouldComponentUpdate가 적용돼있다고 생각하시면 됩니다.

## Functional Component

```jsx
import React from 'react';

const App = () => (
  <div>Hello, world!</div>
);

export default App;
```

React의 함수 기반 Component입니다. 클래스 기반의 컴포넌트들 두 개와 달리 State, Ref, 라이프 사이클 함수들을 사용할 수 없습니다. 그렇다면 클래스 기반의 컴포넌트들 보다 빠를까요? 아직 React에서 Functional Component 최적화 작업이 이루어지지 않아서 Component와 같이 작동하며 똑같은 성능을 냅니다. 미래에 최적화 작업이 이루어진다면 더 빨라지겠죠. 다만 this를 bind할 필요가 없고 간단하여 더 가독성 좋다는 장점이 있습니다.

## 상황에 따른 컴포넌트 선언 방법

성능만 본다면 PureComponent > Functional Component >= Component입니다. 미래에 Functional Component가 최적화가 된다면 충분히 바뀔 수 있습니다.

- Component
  - 복잡한 컴포넌트다.
  - 앱이 리렌더링(re-render)될 때 자신도 같이 돼야 한다.
  - this, ref 사용이 필요하다.
- PureComponent
  - 자식, 아이템 컴포넌트다.
  - 자신의 Props와 State가 변할 때만 리렌더링(re-render)이 일어나면 된다.
  - this, ref 사용이 필요하다.
- Functional Component
  - 해당하는 게 없다.
  - 간단한 컴포넌트다.
  - Props만으로 다 해결이 가능하다.
  - 지금 가장 가독성 좋고, 최적화될 날을 기다리며 사용한다.

# shouldComponentUpdate

```jsx
shouldComponentUpdate(nextProps, nextState) {
  return true;
}
```

React의 함수 기반 Component에서 사용할 수 있는 함수입니다. 이 함수는 새로운 Props, State가 추가되거나 변경될 시마다 불리며, 리렌더링(re-render) 여부를 정해줄 수 있습니다. 앱의 사용자에게 보여질 화면이 리렌더링(re-render)이 필요한 경우에만 true를 반환(return)해주고 아닌 경우엔 false를 반환(return)해주시면 됩니다.

# 불필요한 변수와 함수 재생성

Render가 될 때마다 선언되지 않은 타입의 변수들을 재생성하게 됩니다. 정적인 변수와 함수는 render 함수 밖으로 빼둡시다.

## 변수

나쁜 예제:

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  render() {
    return (
      <div
        style={{
          backgroundColor: 'black',
          color: 'pink',
        }}
      >
        Hello, world!
      </div>
    );
  }
}

export default App;
```

위의 코드는 Render가 될 때마다 style의 Object를 재생성하게 됩니다.

---

좋은 예제:

```jsx
import React, { PureComponent } from 'react';

const style = {
  backgroundColor: 'black',
  color: 'pink',
};

class App extends PureComponent {
  render() {
    return (
      <div style={ style }>
        Hello, world!
      </div>
    );
  }
}

export default App;
```

정적인 변수를 render 밖으로 잘 빼뒀습니다.

## 함수

나쁜 예제:

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  state = {
    count: 1,
  };

  render() {
    const { count } = this.state;

    return (
      <button
        onClick={ () => {
          this.setState({ count: count + 1 });
        } }
      >
        { count }
      </button>
    );
  }
}

export default App;
```

위의 코드는 Render가 될 때마다 함수를 재생성하게 됩니다.

---

좋은 예제:

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  state = {
    count: 1,
  };

  onClickButton = () => {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    const { count } = this.state;

    return (
      <button onClick={ this.onClickButton }>
        { count }
      </button>
    );
  }
}

export default App;
```

미리 선언된 함수를 잘 사용합니다.

# State vs This

React 초보면 흔히 저지를 수 있는 실수로, 변경되는 모든 값을 State로 지정하는 겁니다. 이것이 왜 실수인지 알아봅시다.

## State

shouldComponentUpdate 함수를 사용하지 않은 경우 State가 변경되는 경우 렌더링(re-render)이 됩니다. 또한 State의 값은 변경될 시 렌더링(re-render)돼야 하는 것들로 구성되는 게 맞습니다.

## This

`this.boolean === true`와 같이 this에 변수를 선언해줄 수 있습니다. 값이 변해도 렌더링(re-render)이 되지 않습니다.

## 예제

count를 Submit하는 컴포넌트입니다. 

나쁜 예제:

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  state = {
    count: 1,
  };

  onClickPlusButton = () => {
    this.setState({ count: this.state.count + 1 });
  }

  onClickSubmit = () => {
    const { onSubmit } = this.props;
    onSubmit(this.state.count);
  }

  render() {
    return (
      <div>
        <button onClick={ this.onClickPlusButton }>
          Plus
        </button>
        <button onClick={ this.onClickSubmit }>
          Submit
        </button>
      </div>
    );
  }
}

export default App;
```

사용자 입장에선 count가 변해도 count를 보여주지 않기 때문에 렌더링(re-render)을 할 필요가 없습니다.

---

좋은 예제:

```jsx
import React, { PureComponent } from 'react';

class App extends PureComponent {
  count = 1;

  onClickPlusButton = () => {
    this.count = this.count + 1;
  }

  onClickSubmit = () => {
    const { onSubmit } = this.props;
    onSubmit(this.count);
  }

  render() {
    return (
      <div>
        <button onClick={ this.onClickPlusButton }>
          Plus
        </button>
        <button onClick={ this.onClickSubmit }>
          Submit
        </button>
      </div>
    );
  }
}

export default App;
```

불필요한 렌더링(re-render) 없이 Plus Button을 누른 횟수를 제출하는 컴포넌트를 완성했습니다. 렌더링(re-render)이 일어나지 않아도 `this.count` 값은 제대로 올라가고 있습니다.

# 결론

리액트(React)를 최적화하는 여러 가지의 방법을 알아보았습니다. 스스로 작성했던 코드 중 잘못된 코드가 있었나요? 그럼 Refactoring해보는 게 어떨까요? 똑같은 앱이라도 더 안좋은 환경에서 잘 돌아가도록 해봅시다. 또한 앞으로 만들 App은 최적화를 시켜서 작성해봅시다. Programmer로서의 가치를 높여봅시다.
