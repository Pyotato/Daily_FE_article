# 리액트로 프록시 디자인 패턴 사용하는 법

- 원문 : [Using The Proxy Design Pattern with React](https://blog.bitsrc.io/proxy-design-pattern-with-react-c0b465980fbf)
- `리액트 환경에서 프록시 디자인 패턴을 사용하는 방법에 대해 탐구하는 글입니다.`

<img width="1064" alt="image" src="https://github.com/Pyotato/Daily_FE_article/assets/102423086/596056cb-4b62-409d-9d65-c10bbf2b2648">


개발자로써 한번쯤은 '디자인 패턴들'이라는 용어를 접해봤을 것입니다.
이 용어는 코딩 마스터의 영역으로 가기 위해 알아야하는 개념이므로 단순히 짚고 넘어갈 수 없는 개념이죠.
다양한 패턴들은 소프트웨어 개발, 코드 관리와 유지, 확장성을 위한 귀중한 툴 역할을 합니다.

여러 디자인 패턴들 중에 `프록시 디자인 패턴`은 1995년 `구조적 디자인 패턴`으로 처음 등장했고 현재까지 다양한 시나리오의 애플리케이션에 적용되어 왔습니다.

이 아티클에서는 프록시 디자인 패턴에 대해 집중적으로 탐구합니다. `프록시 디자인 패턴의 쓰임`과 `리액트 애플리케이션 구조와 기능성을 강화하는 적용법`에 대해 알아봅시다.

## 목차

> - [프록시 디자인 패턴 이해하기]()
>   - [왜 객체에 대한 제어권을 갖아야할까?]()
>   - [프록시 디자인 패턴 활용의 장점]()
> - [자바스크립트에서 프록시 객체란?]()

<h2 id='1'>프록시 디자인 패턴 이해하기</h2>

<img width="861" alt="image" src="https://github.com/Pyotato/Daily_FE_article/assets/102423086/b5f5b039-bcad-479d-979f-2bded5efe919">


프록시 디자인 패턴은 하나의 객체의 대체제나 플레이스홀더를 제공할수 있도록 해줍니다.
대체나 플레이스홀더를 제공하게 되면, 프록시는 원본 객체에 대한 제어권을 지니게 되고, 원본 객체에 요청이 전달된 전후의 동작들을 실행가능하게 합니다.

<h3 id='1-1'>왜 객체에 대한 제어권을 갖아야할까?</h3>

특정 함수들이 연속적으로 동작해서 굉장히 큰 객체들이 생성되고, 시스템에 부담을 주며, 과다한 네트워크 리소스를 쓰는 상황이 있다고 예를 들어보겠습니다. 이런 상황이라면 주기적으로 실행하거나 조건부적으로 작동하도록 해야하겠지만, 연속적으로 동작하다보면 전반적인 성능을 헤칠 수도 있습니다. <a href="#" id="ex1">시나리오 예 1</a>

이와 같은 상황에 프록시 디자인 패턴이 유용할 수 있습니다. 프록시 디자인 패턴은 원본 서비스 객체의 인터페이스를 미러링하는 프록시 클래스를 만들어서 사용하면 됩니다.

<h3 id='1-2'>프록시 디자인 패턴 활용의 장점</h3>

프록시 객체를 도입하면 클래스의 메인 로직을 실행하기 전후의 로직을 실행해 볼 수 있습니다.

예를 들어, [위의 예시]()과 같은 경우 메인 객체에서 다루는 데이터를 캐시할 프록시 객체를 만들어 사용하면, 복잡한 연산의 반복을 최소화할 수 있고, 성능과 자원을 효율성을 높일 수 있습니다.

데이터 캐싱뿐만 아니라 프록시 디자인 패턴들은 아래와 같은 장점들을 지닙니다.

- **보안 강화**: 인증과 권한 체크를 도입해서 민감한 정보나 작업들이 권한이 있는 유저들에게만 접근가능하도록 할 수 있습니다.
- **자원 소모량 감소**: lazy loading, 캐싱, 프록시를 통해 접근 통제를 하면 애플리케이션의 자원소모를 줄일 수 있습니다.
- **네트워크 최적화**: 분산된 시스템에서 프록시 디자인 패턴은 원격 객체에 대한 플레이스 홀더 역할을 함으로써 네트워크 커뮤니케이션을 최적화할 수 있습니다. 네트워크 대기 시간의 영향을 최소화하는데 도움을 줄 수 있습니다.
- **동시성 컨트롤**: 프록시를 통해 공유자원에 대한 접근을 컨트롤 할 수 있는 방법들을 도입할 수 있습니다. 동시에 공유자원에 접근하려는 경쟁상태(race condition)를 방지해서 데이터의 일관성을 유지할 수 있습니다.

<h2 id='2'>자바스크립트에서 프록시 객체란?</h2>

자바스크립트의 프록시 객체는 다른 프로그래밍 언어의 핵심 개념을 기반으로 만들어졌지만 자바스크립트의 동적인 특징을 반영한 고유한 특성들도 지니고 있습니다.

다른 언어들이 갖고 있는 전통적인 프록시 객체와 달리 자바스크립트의 프록시 객체는 객체와 상호작용하거나 객체를 조작하는 방식이 매우 유연하고 동적입니다.

자바스크립트에서의 프록시 객체는 다음과 같이 정의할 수 있습니다 :

```js
const proxy = new Proxy(target, handler);
```

`target`은 프록시를 감싸는 객체를 가리키고, `handler`는 하나 이상의 "트랩"(타겟 객체에 수행될 동작들을 가로채거나 변형시켜줄 함수들)을 지닌 객체입니다.

<h3 id='2-1'>사용 예</h3>

```js
const targetObject = {
  message: "Hello, Proxy!",
};

const handler = {
  get: function (target, prop) {
    console.log(`Accessing property: ${prop}`);
    return target[prop];
  },
};

const proxy = new Proxy(targetObject, handler);
console.log(proxy.message);

// Accessing property: message;
// Hello, Proxy!
```

이 예시에서 `get` 트랩을 통해 JS 프록시 객체의 기본 사용법을 적용해봤습니다. 더 자세한 사용법은 [여기로](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy).

💡 JS 프록시 객체에 대한 기본적인 이해를 지니고 있다면 다음 섹션을 이해하는데 도움이 됩니다.

<h2 id='3'>리액트에서 프록시 디자인 패턴 사용하는 방법</h2>

[리액트 프록시 예시](https://bit.cloud/wulfridng/react-proxy-examples)

프록시 디자인 패턴과 자바스크립트에서의 프록시 객체에 대한 이해를 했을 거라고 생각합니다. 이제 리액트에서 프록시를 직접 다뤄봅시다!

리액트는 앱의 전반적인 반응성과 성능최적화를 위한 내장된 기능들이 제공됩니다. 이러한 기능들과 더불어 프록시 디자인 패턴을 활용한다면 객체간의 상호작용과 데이터 접근에 대한 컨트롤을 강화해서 성능을 더욱 향상시킬 수 있을 겁니다.

<h3 id='3-1'>보안 강화</h3>

프록시 디자인 패턴은 리액트 앱에 객체간의 상호작용과 민감한 데이터 접근을 위한 여러 레이어의 컨트롤을 제공함으로써 보안을 강화할 수 있습니다ㅏ. 아래의 간단한 예시를 통해 이 개념을 살펴볼 수 있습니다.

```jsx
import { useEffect, useState } from "react";

const fetchUserData = () => {
  // Place the network request here
  console.log("Data fetching...");

  return {
    username: "john_doe",
    password: "user_password",
  };
};

const useSecureUserData = () => {
  const [userData, setUserData] = useState(null);

  const isUserAdmin = () => {
    // Implement your authorization logic here
    // For simplicity, assume logged user is admin
    return true;
  };
};

useEffect(() => {
  const userProxy = new Proxy(fetchUserData(), {
    get(target, prop) {
      // 유저가 비밀번호에 접근할 권한이 있는 지 체크
      if (prop === "password" && !isUserAdmin()) {
        console.warn("Unauthorized access to password!");
        return null; // Return null or handle unauthorized access
      }

      return target[prop];
    },
  });
  setUserData(userProxy);
}, []);

export const UserProfile = () => {
  const userData = useSecureUserData();
  if (!userData) {
    return <div>Loading</div>;
  }
  return (
    <div>
      <p>Username : {userData.username}</p>
      <p>Password : {userData.password}</p>
    </div>
  );
};
```

위의 예시에서 `useSecureUserData` 커스텀 훅은 유저 데이터를 가져오고 유저 데이터를 리턴하기 전에 프록시 객체를 만들어서 유저 데이터를 확인합니다. `fetchUserData`는 실제 객체를 리턴하고 `userProxy`가 프록시 객체입니다. 마지막으로 `UserProfile` 컴포넌트가 프록시 객체를 호출하는 클라이언트 역할을 합니다.

이 예시에서는 접근 관리를 프록시 객체를 통해서 하지만, HOC(Higher-Order Component), 컨텍스트API, 라우터 가드 등이 리액트에서 더 흔하고 가끔은 더 전통적인 관리 방식입니다. 애플리케이션의 특수한 요구사항이나 접근 컨트롤의 정도에 따라 선택을 하면 됩니다.

<h3 id='3-2'>효율적인 데이터 관리</h3>

자원을 많이 잡아먹고 성능을 저하하는 네트워크 요청이 많은 애플리케이션이라면 fetch한 데이터를 캐시하는 것이 하나의 해결책이 될 수 있습니다. 리액트에는 데이터 캐싱 방법들이 다양하게 존재하고, 프록시 객체를 사용하는 방법도 그중 하나입니다.

다음의 예시에서는 프록시를 객체를 생성해서 네트워크 응답을 캐시하도록 하고 추가적 네트워크 요청없이 데이터를 재사용하는 경우를 보겠습니다.

```tsx
import React from "react";

const fetchUserData = () => {
  // 여기에 네트워크 요청 코드 작성
  console.log("Data fetching");

  return [
    {
      id: "0001",
      name: "John Doe",
      email: "john@mail.com",
    },
  ];
};

const cachedUserData = new Proxy([], {
  get: function (target: any) {
    if (!target.data) {
      // 캐시된 데이터가 없다면 fetch하기
      target.data = fetchUserData();
    } else {
      console.log("Returning cached data...");
    }

    return target.data;
  },
});

export function UserList() {
  const [state, setState] = React.useState(
    [] as { id: string; name: string; email: string }[]
  );

  return (
    <div>
      {state.map((user) => (
        <div>
          <p>Name:{user.name}</p>
          <p>Email:{user.email}</p>
        </div>
      ))}
      <button onClick={() => setState(cachedUserData.data)}>Click me</button>
    </div>
  );
}
```

위의 API는 객체에 이미 데이터가 있다면 보내지지 않습니다. 데이터가 없다면 데이터를 fetch하고 프록시 객체를 통해 캐시를 합니다. "Click me" 버튼을 클릭하면 [결과](https://bit.cloud/wulfridng/react-proxy-examples/components/data-management/user-list)의 콘솔에서 캐시가 어떻게 동작하는 지 더 자세히 알 수 있습니다.

위와 같은 단순한 상황에서는 데이터 상태를 관리하고 매번 랜더를 할 때 네트워크 요청을 방지할 수 있어서 성능 문제를 완화할 수 있습니다. 뿐만 아니라 애플리케이션 내에 프록시 데이터 객체는 애플리케이션 데이터 캐시에 여러 방면으로 도움이 됩니다.

<h3 id='3-3'>효율적인 데이터 관리 : 복잡한 연산 데이터 캐시</h3>

```jsx
const createExpensiveFunction = () => {
  // Replace this with your actual expensive function
  const expensiveFunction = (arg) => {
    console.log(`Calculating expensive value for ${arg}`);
    return arg * 2;
  };

  const cache = new Map();

  const handler = {
    apply(target, thisArg, argumentList) {
      const arg = argumentsList[0];
      if (!cache.has(arg)) {
        cache.set(arg, expensiveFunction(arg));
      } else {
        console.log("Returning cached data");
      }

      return new Proxy(expensiveFunction, handler);
    },
  };
};

const memorizedFunction = createExpensiveFunction();

export const MemorizeExpensiveFunction = () => {
  const items = [1, 2, 1, 1, 5];
  return (
    <div>
      {items.map((item, index) => (
        <div key={`${item}-${index}`}>
          <p>Item: {item}</p>
          <p>Expensive Value: {memorizedFunction(item)}</p>
        </div>
      ))}
    </div>
  );
};
```

위의 예시는 복잡한 연산을 하는 함수의 결과를 캐시하는 방법을 보여줍니다. `createExpensiveFunction`은 프록시 객체를 리턴합니다. 인풋 인자들을 체크하고, 같은 인풋으로 함수가 호출된다면 캐시된 결과를 가져옵니다. 반면에 캐시되지 않은 인자로 호출이 된다면 `expensiveFunction`를 실행하고 결과를 캐시에 저장합니다.

<h3 id='3-4'>그 외의 사용 예시들</h3>

위에서 언급한 경우 이외에도 프록시 디자인 패턴들을 사용해서 개발에 유용한 경우가 있습니다. 대표적으로,

- 추상화와 코드 재사용성 : 프록시는 복잡한 데이터 접근 패턴이나 API상호작용을 추상화하여 코드를 단순화시키고, 코드 반복을 줄이고 코드의 재사용을 가능하게 합니다.
- 데이터 확인 & 정제: 프록시는 컴포넌트의 로직에 이르기 이전에 사용자 입력값을 확인하는데 사용할 수 있습니다. 이를 통해 데이터를 정제할 수있고, XSS공격으로부터 방어를 하고 전반적인 애플리케이션의 보안을 강화할 수 있습니다.
- 성능 모니터링과 최적화: API 호출에 걸리는 시간이나 데이터 접근 패턴들의 성능 지표를 모니터링하는데 프록시를 적용해 성능 병목지점을 찾고 해결하는데 도움이 될 수 있습니다.

[BitScope](https://bit.cloud/wulfridng/react-proxy-examples)에서 위의 경우들에 대한 예시를 확인할 수 있습니다.

<h2 id='4'>마무리를 지으며</h2>

프록시 디자인 패턴과 자바스크립트 프록시 객체는 리액트 애플리케이션을 강화하는데 강력한 툴들을 제공합니다. 성능 최적화부터 민감한 데이터 보호, 효율적 데이터 관리를 넘어, 프록시는 다양한 상황에 적절한 해결책을 제시합니다.

실용적인 경우 몇가지를 확인했지만, 이상적인 해결책은 애플리케이션이 요구사항에 따라 다르다는 걸 명심해야합니다. 리액트는 개발자들이 애플리케이션에 맞게 최선의 선택을 할 수 있도록 유연함을 지녔습니다.

프록시 디자인 패턴에 대해 탐구하면서 프록시 디자인의 장점들(리액트 개발 경험을 극대화해줄 간소화된 코드, 개선된 보안성 그리고 성능 최적화)을 기억해보세요.

---

## 읽은 후 생각

- chatgpt API를 사용했던 프로젝트가 있었는데, 해당 프로젝트는 응답시간(평균 24s)이 너무 길거나, 토큰을 사용해야 했다(유료)는 점에서 이 프록시 디자인 패턴을 활용해보면 좋을 거 같다는 생각이 들었다.
- 예를 들어, 입력값으로 비슷한 질문에 대한 데이터가 있다면 API요청을 하기 보다는 먼저 캐시된 데이터를 가져오는게 더 효율적인 자원을 사용하는 방식일 것 같다.
- 아니면, 프로젝트의 특성상 로드맵을 만드는 (편집 기능이 들어간) 사이트였기 때문에 새로고침이나 수정 (랜더 발생) 마다 API를 다시 호출하는 것은 비효율적일 거 같다는 생각이 들었다.
- 또한 반복된 동작을 해야한다면 프록시 디자인 패턴을 한번 사용해 보면 좋을 거 같다. 예를 들어 반복적으로 호출되는 코드가 있다면 호출되는 부분에서 코드를 매번 작성하는 것보다는 프록시 객체를 활용해서 반복코드를 줄여볼 수 있을 거 같아 코드가 더 간결해질 거 같다. 예를 들면 위의 `get`에서와 같이 다른 연속적인 동작을 하기 전에 먼저 체크하고 다음 동작은 실행하도록하면 조건에 맞지 않으면 빠른 리턴을 하기 때문에 코드가 깔끔해지고, 해당 체크 코드를 재사용할 수 있을 거 같다.
- 프록시 디자인 패턴 말고 다른 디자인 패턴에는 뭐가 있을까?
