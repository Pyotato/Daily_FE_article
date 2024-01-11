# 유저가 페이지를 벗어나도 확실하게 HTTP 요청 보내는 법

- 원문 : [Reliably Send an HTTP Request as a User Leaves a Page](https://css-tricks.com/send-an-http-request-on-page-exit/)

유저가 다른 페이지로 이동하거나 form을 제출하면서 HTTP 요청을 '/log'에 데이터와 함께 보내야할 경우가 있습니다. 아래와 같이 링크를 클릭헸을 때 외부 서비스에 정보를 보내애하는 경우를 고려해봅시다.

```html
<a href="/some-other-page" id="link">Go to Page</a>

<script>
  document.getElementById("link").addEventListener("click", (e) => {
    fetch("/log", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ some: "data" }),
    });
  });
</script>
```

위의 경우서는 특별히 복잡한 일이 진핻되고 있지는 않습니다. 링크는 `e.preventDefault()`가 쓰이지 않기 때문에 평소처럼 작동하도록 되어 있지만, 클릭을 하면 평소 동작 이전에 POST 요청이 일어납니다. 어떤 응답을 기다릴 필요가 없이 단순히 어떤 서비스로 **전송되기만** 원하는 상황입니다.

얼핏보기에는 이 요청이 동기적으로 동작하기를 기대하실 수도 있습니다. 서버가 성공적으로 요청을 처리하면 다른 페이지로 이동할 것이라고 기대할 수도 있지만, 아쉽게도 항상 그런 결과를 가져오지는 않는다는 것을 알 수 있습니다.

<h2 id="1">브라우저는 열린 HTTP요청 보존을 장담할 수 없다</h2>

브라우저 페이지를 종료하는 상황이 발생하면 진행중인 HTTP 요청이 성공적으로 종료될 거라는 보장을 할 수없습니다. 페이지의 생명주기 상태에 대한 자세한 내용은 [여기서]() 알아볼 수 있습니다. 네트워크 연결, 애플리케이션의 성능, 그리고 외부 서비스의 설정 자체 등, 요청 수행의 확실성에 영향을 주는 요인들은 다양합니다.

따라서 중대한 결정에 영향을 미치는 민감한 데이터를 다루는 로그에 잠재적인 문제를 일으킬 수 있습니다. 이러한 불확실성에 대한 부가설명을 위해 위의 코드를 포함한 페이지를 지닌 작은 Express 애플리케이션을 아래와 같이 설정했습니다. 링크가 클릭될 때, 브라우저는 `/other`로 페이지 이동을 하게 되는데, 이동 직전에 POST 요청이 발생합니다.

위의 모든 일이 발생 중에 브라우저의 네트워크 탭을 열고 'Slow 3G'를 사용하고 있다고 가정해 봅시다. 페이지가 로드 된 후 로그를 비우면 별 특이점은 없어보입니다:

![Alt text](https://i0.wp.com/css-tricks.com/wp-content/uploads/2022/02/initial-load-1.png?resize%253D1536%252C727%2526ssl%253D1)

하지만 링크를 누르자마자 문제가 생깁니다. 페이지 이동이 발생하자 요청이 취소됩니다.

![Alt text](https://i0.wp.com/css-tricks.com/wp-content/uploads/2022/02/request-failed-1.gif?resize%253D1000%252C472%2526ssl%253D1)

이런 상황에서는 외부 서비스가 실제로 요청을 처리했는지에 대한 확신을 할 수 없게 됩니다. 이 동작은 `window.location`으로 이동을 해줄 경우에도 동일하게 발생한다는 걸 수 있습니다.

```js
document.getElementById('link').addEventListener('click', (e) => {
+ e.preventDefault();
  // Request is queued, but cancelled as soon as navigation occurs.
  fetch("/log", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      some: 'data'
    }),
  });

+ window.location = e.target.href;
});

```

페이지가 어떻게 언제 이동하는 것과 무관하게, 활성화된 페이지가 종료된다면 완료되지 않는 요청들이 버려질 위험에 노출됩니다.

<h2 id="2">취소되는 이유</h2>

요청이 취소되는 근본적인 이뉴는 XHR 요청(`fetch`나 `XMLHttpRequest`)이 비동적으로 동작하고 차단 불가능하(non-block)기 때문입니다. 요청이 큐에 추가되면, 실제 요청 작업은 브라우저 레벨의 API에게 넘겨져서 처리됩니다.

메인 쓰레드를 건드리지 않는 이 방식은 성능의 측면에서는 이는 매우 좋은 동작 방식입니다. 하지만 페이지가 '종료(terminated)' 상태에 진입하게 되면 요청이 버려질 위험성도 있습니다. 구글이 이 생명주기 상태를 요약한 양상을 [여기서](https://developer.chrome.com/docs/web-platform/page-lifecycle-api#states) 자세히 확인 가능합니다.

> 페이지는 언로드되고 브라우저 메모리에서 삭제될 때 `종료` 상태에 놓이게 됩니다.
> 이 상태에서는 어떠한 [새로운 작업](https://html.spec.whatwg.org/multipage/webappapis.html#queue-a-task)도 시작할 수 없으며 진행중인 작업들도 너무 오래 실행된다면 종료될 수 있습니다.

즉, 브라우저는 페이지가 버려지면 더이상 그 페이지의 뒤에서 진행되거나 대기 중인 프로세스들 또한 더 이상 지속할 필요가 없다는 가정을 바탕으로 작동합니다.

<h2 id="3">해결 방안들 & 대안</h2>

가장 단순하게 이 문제를 방지할 수 있는 방법은 가능한한 유저 동작이 요청에 대한 응답을 리턴 받을 때까지 최대한 늦추는 것입니다. 과거에 이는 XMLHttpRequest에서 지원했던 [동기적 플래그](https://xhr.spec.whatwg.org/#synchronous-flag)를 통해 잘못된 방식으로 구현이 되었습니다. 이 방식은 메인 쓰레드를 완전히 막아서 심각한 성능 문제를 일으킵니다. [여기서](https://macarthur.me/posts/use-web-workers-for-your-event-listeners) 이벤트 리스너를 위해 웹워커를 사용하라는 아티클에서 이 주제에 대해 다뤘습니다. 사실 이 기능은 플랫폼에서 서서히 제거되고 있습니다. [크롬 v80+](https://developer.chrome.com/blog/chrome-80-deps-rems)은 이미 제거했습니다.

대신, 응답을 받을 때까지 유저의 동작을 미루는 방식으로 문제를 해결하고 싶다면 `Promise`를 사용해서 응답이 리턴될 때까지 기다리는 방식이 더 적절합니다. 응답을 받았다면 안전하게 동작을 실행하면 됩니다. 위에서의 상황에 대입해 코드를 재작성하자면,

```js
document.getElementById("link").addEventListener("click", async (e) => {
  e.preventDefault();

  // 응답이 올때까지 대기...
  await fetch("/log", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      some: "data",
    }),
  });

  // ... 그 다음에 페이지 이동!
  window.location = e.target.href;
});
```

이런 방식은 문제는 해결하지만, 심각한 단점들이 있습니다.

1. **유저가 예상한 동작과는 달리 딜레이가 발생해서 유저경험의 질을 낮춥니다.** 애플리케이션 분석 데이터를 수집하는 것은 미래의 사용자들과 비즈니스에 유익합니다. 하지만 현재의 사용자가 그 이점들을 위해 히생해야하는 상황이라면 이상적인 해결책은 아닙니다. 뿐만 아니라, 외부에 의존성으로 인한 대기시간 혹은 서비스 자체 내부의 문제로 인한 성능 문제들이 발생한다면 이 문제들은 곧 유저들이 직면해야할 문제들입니다. 분석 서비스로 인한 타임아웃이 유저가 고비용의 동작을 수행하지 못하게 한다면 모두가 피해를 입는 상황이 발생할 수도 있습니다.

2. **어떤 종료 동작들은 프로그래밍적으로 지연시킬 수 없는 상황이 있으므로 불안정합니다.** : 예를들어, `e.preventDefault()`은 브라우저 탭을 닫으려는 사용자의 행동을 지연시킬 수 없습니다. 최선은 유저의 데이터를 어느 정도 수집하겠지만 신뢰할만한 정도로 충분하지는 않을 겁니다.

<h2 id="4">브라우저가 중요한 요청 보존하도록 하기</h2>

다행히도, 유저의 경험을 희생하지 않고도 중요한 HTTP 요청을 보존하는 옵션들이 대부분의 브라우저에 탑재되어 있습니다.

<h3 id="4-1">Fetch의 <i>keepalive flag</i>사용하기</h3>

`fetch()`사용시 [keepalive flag](https://fetch.spec.whatwg.org/#request-keepalive-flag)가 `true`로 설정되어 있다면, 요청을 시작한 페이지가 종료되었더라도 대응하는 요청이 열린 상태로 유지될 것입니다. 위의 예시에 이를 구현하면 다음과 같습니다 :

```html
<a href="/some-other-page" id="link">Go to Page</a>

<script>
  document.getElementById("link").addEventListener("click", (e) => {
    fetch("/log", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        some: "data",
      }),
      keepalive: true,
    });
  });
</script>
```

링크를 클릭하고 페이지 이동이 발생했을 때, 요청 취소가 발생하지 않습니다.

![Alt text](https://i0.wp.com/css-tricks.com/wp-content/uploads/2022/02/request-succeeded.gif?resize%253D1000%252C472%2526ssl%253D1)

대신, `(unknown)`상태가 되는데, 이는 단순히 활성화된 페이지가 어떠한 응답을 받도록 되어 있지 않기 때문입니다.

위와 같이 단 한줄로 문제를 해결할 수 있습니다. 특히 흔히 사용되는 브라우저 API일 경우에 더욱 쉽게 적용할 수 있는 해결방안입니다.
하지만 특정적인 옵션을 지난 더 간단한 인터페이스를 찾고 있다면, 브라우저 지원을 통해 사실상 같은 문제를 해결할 수 있습니다.

<h3 id="4-2"><i>Navigator.sendBeacon()</i> 사용하기</h3>

`Navigator.sendBeacon()`함수는 단방향 요청을 보낼 때 (beacon)[https://w3c.github.io/beacon/#sec-processing-model] 사용됩니다. 간단한 구현은 아래와 같습니다. `POST`요청을 `stringified JSON` `Content-Type:text/plain`로 보내면 됩니다:

```js
navigator.sendBeacon(
  "/log",
  JSON.stringify({
    some: "data",
  })
);
```

하지만 이 API는 커스텀 헤더를 허용하지 않습니다. 따라서 데이터를 `application/json`으로 보내기 위해서는 `Blob`를 사용해서 살짝 변형을 해야합니다.

```html
<a href="/some-other-page" id="link">Go to Page</a>

<script>
  document.getElementById('link').addEventListener('click', (e) => {
    const blob = new Blob([JSON.stringify({ some: "data" })], { type: 'application/json; charset=UTF-8' });
    navigator.sendBeacon('/log', blob));
  });
</script>
```

결론적으로는 같은 결과가 나옵니다 : 다른 페이지로 이동하고도 요청이 완료되도록합니다. 하지만 `beacons`를 사용하는 것이 `fetch()`보다 좋은 점을 꼽자면 낮은 우선 순위를 지녔다는 점입니다. 설명을 위해 `fetch()`와 `keepalive`를 사용하고 동시에 `sendBeacon()`를 사용할 경우의 네트워크 탭을 비교해보겠습니다:

![Alt text](https://i0.wp.com/css-tricks.com/wp-content/uploads/2022/02/request-priorities.png?resize%253D1536%252C727%2526ssl%253D1)

디폴트로, `fetch()`는 '높은' 우선 순위를 지닙니다. 반면 `beacon`(ping 타입이라는 점을 주목해주세요!)는 '제일 낮은' 우선순위를 지녔습니다. 페이지가 기능하는데 핵심적이지 않은 요청들에 관해서 낮은 우선순위를 부여하면 좋습니다. [Beacon 사양](https://www.w3.org/TR/beacon/)을 보면 :

> 이 사양은 다른 시간에 민감한 동작들을 위한 자원 경쟁을 최소화하면서 이런 요청들이 처리되고 목적지에 도달하도록 보장하는 인터페이스를 정의한다.

다시말해, `sendBeacon()`는 유저 경험에 영향을 미치는 요청들을 방해하지 않도록 보장합니다.

<h3 id="4-3">명예의 전당: <i>ping</i>속성 활용하기</h3>

브라우저들의 `ping`속성 지원이 증가하고 있습니다. 링크 속성에 추가 시 작은 `POST`요청이 발생합니다.

```html
<a href="http://localhost:3000/other" ping="http://localhost:3000/log">
  Go to Other Page
</a>
```

이 요청 헤더들은 클릭(ping-from)되었던 페이지와 이동한 페이지(ping-to)의 href 값을 지니게 됩니다.

```js
headers: {
  'ping-from': 'http://localhost:3000/',
  'ping-to': 'http://localhost:3000/other'
  'content-type': 'text/ping'
  // ...other headers
},
```

이 방식은 `sendBeacon()`과 매우 유사하지만 몇가지 제약들이 있습니다.

1. **링크에 한에서만 사용가능합니다**: 따라서 버튼클릭이나 양식제출 등의 다른 상호작용이 동반된 데이터를 추적해야한다면 사용할 수 없습니다.
2. **브라우저 지원이 양호하지만 [최상은 아닙니다](https://caniuse.com/ping)**: 현재를 기준으로 FireFox는 디폴트로 적용되어있음을 명시하지 않습니다.
3. **요청과 함께 보내질 데이터 커스터마이징이 불가능합니다** : 위에서 언급된 바와 같이 `ping-*`과 몇몇의 페더 정보를 갖게 되는게 전부입니다.

<h2 id="5">결론: 어떤 방식을 채택해야하는가?</h2>

`fetch()`와 `keepalive`를 샤용하든, `sendBeacon`을 사용하든, 마지막 요청들을 보낼 때는 각각 장단점이 있을 수 밖에 없습니다. 상황에 따라 가장 적절한 경우를 따지기 위해 고려할 점들은 아래와 같습니다:

| `fetch()`와 `keepalive`가 좋은 선택지일 경우                                                                                                                                                 | `sendBeacon`가 좋은 선택지일 경우                                                                                                                                                                     |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🤔 요청과 함께 커스텀 헤더를 보내야할 경우<br/> 🤔 `POST`요청보다 `GET`을 하고 싶을 경우 <br/> 🤔 IE와 같은 예전 버젼의 브라우저들을 지원해야하는 경우와 이미 `fetch pollyfill`울 로드했다면 | 🤔 커스터마이징이 불필요한 간단한 서비스 요청을 해야하는 경우<br/> 🤔 더 우아하고 깔끔한 API를 원한다면 <br/> 🤔 애플리케이션 내의 우선순위가 높은 요청들과 경쟁하는 상황이 없음을 보장하고 싶은 경우 |

<h2 id="6">같은 실수 반복하지 않도록</h2>

페이지가 종료되면 브라우저가 요청을 처리하는 방식에 대해 탐구한 특별한 이유가 있습니다. 전에 팀이 양식이 제출하면서 특정 요청이 발생할 때 분석 로그의 특이점을 경험한 적이 있습니다. 급격하고 상당한 변화였는데, 이전 분석의 30% 감소가 발생했었죠.

이 문제를 파다보니 이 문제를 해결할 방안과 툴들을 발견하게 되었습니다. 이 글을 읽으시고 얻어가시는 게 있다면 이러한 챌린지의 뉘앙스들을 이해함으로써 우리가 겪었던 문제들을 겪지 않았으면 좋겠습니다. Happy logging!

<h2 id='7'>읽은 후 생각</h2>

- 페이지를 벗어나면 해당 페이지에서 했던 요청에 대한 응답을 보장할 수 없다는 점에서 왠지 전에 있던 프로젝트의 버그를 해결할 수 있는 방안을 제시한 아티클같아서 엄청 흥미로웠다.
- 메인페이지에서 챗지피티에 요청을 하면 요청을 하는데 대기 시간이 매우 길다. 메인 페이지에 대한 요청이 완료되어야 다음 페이지로 이동했는데 이 대기 시간이 사용자들이 경험에 그다지 좋은 영향을 미치지는 않았던 거 같다.
- 하지만 둘을 사용하기에는 애매하지 않을까 싶다. 일단 챗지피에 요청에는 커스터마이징이 필요하므로 fetch가 적당해보이지만 post 요청이라서 적절한지는 모르겠다.그리고 만약 사용자가 탭을 그냥 닫아도 토큰이 사용되고 비용이 발생한다.
- 다음에는 웹워커에 대해 파보는 시간을 가져야겠다!
