# 🗣️ 브라우저는 당신이 몰랐으면 하는 67가지 이상한 디버깅 트릭들


- 원문 : [67 Weird Debugging Tricks Your Browser Doesn't Want You to Know](https://alan.norbauer.com/articles/browser-debugging-tricks#user-content-fn-1)
- `브라우저 디버거를 최대한 활용할 수 있는 유용하지만 당연하지는 않는 팁들을 담았습니다. 개발자 툴에 대한 중상 이상의 이해도를 지닌 독자들을 위한 글입니다.`

> 목차
>
> - [Advanced Conditional Breakpoints (조건부 중단점 고급 활용법)](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1)
>   - [Logpoints / Tracepoints (중단점)](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1)
>     - [Watch Pane (감시창)](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1-1)
>     - [Tracing Callstacks (호출 스택 추적)](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1-2)
>   - [Changing Program Behavior](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-2)
>   - [Quick and Dirty Performance Profiling](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-3)
>   - [Using Function Arity](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-4)
>     - [Break on Number of Arguments](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-4-1)
>     - [Break on Function Arity Mismatch](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-4-2)
>   - [Using Time](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5)
>     - [Skip Page Load](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5-1)
>     - [Skip N Seconds](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5-1)
>   - [Using CSS](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-6)
>   - [Even Calls Only](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-7)
>   - [Break on Sample](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-8)
>   - [Never Pause Here](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-9)
>   - [Automatic Instance IDs](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-10)
>   - [Programmatically Toggle](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-11)
> - [monitor() class Calls](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#2)
>   - [From a Specific Instance](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#2-1)
> - [Call and Debug a Function](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#3)
> - [Pause Execution on URL Change](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#4)
> - [Debugging Property Reads](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#5)
> - [Use copy()](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#6)
> - [Debugging HTML/CSS](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7)
>   - [Inspect the DOM with JS Disabled](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-1)
>   - [Inspect an Elusive Element](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-2)
>   - [Record Snapshots of the DOM](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-3)
>   - [Monitor Focused Element](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-4)
>   - [Find Bold Elements](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-5)
>     - [Just Descendants](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-5-1)
>   - [Reference Currently Selected](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-6)
>   - [Element](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-7)
>     - [Previous Elements](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-7-1)
>     - [Get Event Listeners](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-7-2)
>   - [Monitor Events for Element](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#7-8)

<h2 id="1">Advanced Conditional Breakpoints : 조건부 중단점 고급 활용법</h2>
> 사이드 이펙트를 예상치 못한 곳에 표현식을 사용한다면 조건부 중담점과 같은 기본 기능의 효능을 최대화할 수있습니다.   
<h3 id="1-1">Logpoints / Tracepoints</h3>

* 예를 들어 `console.log`를 중단점(breakpoint)에 사용할 수 있습니다. 로그포인트 (logpoint)는 실행 중단을 하지 않고도 콘솔로그를 할 수 있는 지점입니다. `Microsoft Egde`는 로그포인트가 내장된 지 꽤 되었지만, `Chrome`은 v73이 돼서야 추가됐고, `FireFox`는 내장된 기능은 아닙니다. 
하지만 조건부 중단점을 활용해서 모든 브라우저에서 시뮬레이션이 가능합니다.

![Logpoints / Tracepoints](https://alan.norbauer.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fconditional-breakpoint-console-log.2d18d3e4.gif&w=828&q=75)

실행할 함수 줄이 몇번 실행된 건지 알고 싶다면 `console.log` 대신 `console.count`를 사용하실 수 있습니다.

> 💡2020 5월 업데이트 : 모든 주요 브라우저들([Chrome Logpoints](https://developer.chrome.com/blog/new-in-devtools-73/#logpoints), [Edge Tracepoints](https://learn.microsoft.com/en-us/archive/microsoft-edge/legacy/developer/#breakpoints), [Firefox Logpoints](https://firefox-source-docs.mozilla.org/devtools-user/debugger/set_a_logpoint/index.html))이 직접 logpoint/tracepoint를 지원합니다.

<h4 id="1-1-1">Watch Pane</h4>
`console.log`를 감시창(Watch Pane)에서도 활용할 수 있습니다. 

* 예를 들면 애플리케이션이 매번 디버거에서 중단될 떄 로컬스토리지 스냅샷을 버릴(dump)려면, `console.table(localStorage)` watch를 생성할 수 있습니다. 

![Watch Pane](https://alan.norbauer.com/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fconsole-table-in-watch.03919d55.png&w=640&q=75)

* 또는 DOM 변경이 발생한 후 표현식이 실행되도록 DOM 변경 중단점을 `Element Inspector`에서 설정할 수 있습니다. 
![image](https://github.com/Pyotato/Daily_FE_article/assets/102423086/5f11aa5d-b651-49fc-9d20-4f2d78d94502)

그 뒤 감시 표현식을 추가 (예를 들어 DOM 스냅샷을 기록하기 위해 `(window.doms = window.doms || []).push(document.documentElement.outerHTML`)하면 됩니다. 이를 통해 DOM 하위트리에서 변경 사항이 발생하면 디버거가 실행을 중단하고 새로운 DOM 스냅샷이 window.doms 배열 마지막에 추가됩니다. (DOM 변경 중단지점이라는 이름에서 알 수 있듯이, 무조건 실행 중단을 합니다.)

<h4 id="1-1-2">Tracing Callstacks : 호출 스택 추적</h4>

로딩 스피너를 `보여주는 함수`와 `숨겨주는 함수`가 있다고 합시다. 하지만 코드 어딘가에 스피너를 `보여주는 함수`는 있지만 대응하는 `숨겨주는 함수`가 없다고 합시다. 
어떻게 짝이 없는 함수 소스를 찾을 수 있을까요? `console.trace`함수를 조건부 중단점에 넣어 `보여주는 함수`에 넣고, 함수를 실행한다면 `보여주는 함수` 메소드를 가장 마지막에 호출한 스택을 추적해서 찾을 수 있습니다. 

![Tracing Callstacks](https://github.com/Pyotato/Daily_FE_article/assets/102423086/299a79d0-9120-4801-acc9-4f0d2b25efb2)


<h3 id="1-2">Changing Program Behavior : 프로그램 동작 변경하기</h3>

프로그램 동작에 사이드 이펙트가 있는 표현식을 활용해서 브라우저에 즉각 프로그램 동작을 변경할 수 있습니다.

예를들어, `function getPerson(id)`라는 함수의 매개변수 id를 오버라이드 할 수 있습니다. id=1가 true로 평가되기 떄문에, 조건부 중단점은 디버거를 중단시킬 것입니다. 이를 방지하기 위해서 `false`를 표현식에 추가할 수 있습니다.

<h3 id="1-3">Quick and Dirty Performance Profiling: 손쉽게 성능 체크</h3>

조건부 중단점 평가시간같은 걸로 성능 프로파일링을 저하해서는 안됩니다. 하지만 어떤 기능을 실행하는데 얼마나 걸리는지 손쉽게 알고 싶다면 `console timing API`를 조건부 중단점에 사용하면 됩니다. 

중단점 시작점에 `console.time('label')`로 조건을 설정하고 종료지점에 `console.timeEnd('label')` 조건을 설정합니다. 매번 실행성능 (속도)체크를 할 때, 브라우저는 콘솔에 실행하는데 걸리는 시간을 체크할 것입니다.

![Quick and Dirty Performance](https://github.com/Pyotato/Daily_FE_article/assets/102423086/2d6fefd7-3c4a-4940-8927-881feea31463)

<h3 id="1-4">Using Function Arity : 함수의 항수 활용 </h3>

<h4 id="1-4-1">Break on Number of Arguments : 인자 개수에 따라 중단하기</h4>

함수가 3개의 인자로 호출될 때만 중단하도록 하게 (`arguments.callee.length === 3`) 함으로써 필수가 아닌 (옵션인) 매개변수를 지닌 오버로드된 함수를 디버깅할 때 활용할수 있습니다.  

![Break on Number of Arguments](https://github.com/Pyotato/Daily_FE_article/assets/102423086/eb8bd276-5c9a-4369-a288-f63d5f27ea5e)

<h4 id="1-4-2">Break on Function Arity Mismatch : 함수의 항수가 불일치 할 때 중단하기</h4>

함수가 호출될 때 인자 개수가 불일치한다면 중단 (`arguments.callee.length != arguments.length`)함으로써 함수 호출 위치의 버그를 찾을 때 유용합니다. 

<h3 id="1-5">Using Time : 시간 활용하기</h3>
<h4 id="1-5-1">Skip Page Load : 페이지 로드 후 몇 초 동안은 중단 불가</h4>
페이지가 로드되고 5초가 지나고 나서야 (`performance.now() > 500`) 중단하게 함으로써 초기 페이지 로드 후에 실행 중단을 하도록하게 하고 싶을 때 유용합니다.

<h4 id="1-5-2">Skip N Seconds : N초 후에만 중단 가능</h4>
중단점이 5초 동안은 실행 중단을 하지 못하도록 하고 그 이후로는 (`window.baseline = window.baseline || Date.now(), (Date.now() - window.baseline)>5000`) 중단하게 함으로써 콘솔을 통해 `window.baseline = Date.now()` 원하는 시간으로 counter를 리셋할 수 있습니다.

<h3 id="1-6">Using CSS : CSS 활용하기</h3>
계산된 CSS 값(document body가 빨강 배경색을 지닐 떄만 실행을 중단하게 하기)을 활용
할 수 있습니다 : `window.getComputedStyle(document.body).backgroundColor === rgb(255,0,0)`
<h3 id="1-7">Even Calls Only : 짝수 호출만 중단</h3>
실행할 함수 줄이 번갈아서 한번만 중단되도록 하기 (짝수나 홀수번째 줄에서만 중단하도록) : `window.counter = window. counter || 0, window.counter % 2 ===0`
<h3 id="1-8">Break on Sample: 랜덤하게 중단</h3>
랜덤 샘플의 줄이 실행될 때만 중단하도록 하기 (10번 실행 중 1번 중단하도록 하기 : `Math.random() < 0.1 `)
<h3 id="1-9">Never Pause Here : 절대 중단하지 않을 지점 설정</h3>
  `📌CHROME ONLY FEATURE`
  코드 블록에서 오른쪽 클릭을 하고 `Never Pause Here` 선택시, 크롬 브라우저는 조건부 중단점을 `false`으로 설정해서 생성하므로써 디버거 절대로 이 줄에서 중단하지 않도록 합니다.
 XHR 중단점에서 줄을 제외시키거나, 던져진 예외를 무시하고 싶을 경우 등에서 유용합니다.  
  
<h3 id="1-10">Automatic Instance IDs : 자동 ID 인스턴스 할당하기</h3>
조건부 중단점을 클래스 생성자로 설정하면 클래스의 모든 인스턴스에 유일한 ID를 자동으로 할당합니다. `(window.instances = window.instances || []).push(this)`

이 유일 ID 를 불러올 때는 `window.instances.indexOf(instance)`를 활용하면 됩니다.

<h3 id="1-11">Programmatically Toggle : 프로그래밍적으로 토글</h3>
전역 불리언 게이트를 통해 하나 이상의 조건부 중단점을 생성하고

![Programmatically Toggle](https://github.com/Pyotato/Daily_FE_article/assets/102423086/3a9ced0b-ad60-4f43-bc5e-bc195c825c2e)

프로그래밍적으로 불리언이 토글되도록 할 수 있습니다. 아래는 그중 몇가지 방법들입니다.

* 수동적으로 콘솔을 활용
```js
window.enableBreakpoints = true;
```
* 다른 중단점을 활용
![image](https://github.com/Pyotato/Daily_FE_article/assets/102423086/dc995c5f-1a3c-473e-9496-c2ae05bc3459)
* 콘솔의 타이머를 활용
```js
setTimeout(()=>window.enableBreakpoints = true),5000);
```

<h2 id="2">monitor() class Calls : monitor()클래스 함수 호출</h2>
  `📌CHROME ONLY FEATURE`
크롬의 `monitor` 커맨드라인 메소드를 활용해서 쉽게 모든 클래스 호출 메소드들을 추적할 수 있습니다. 

Dog 라는 클래스가 있다고 합시다.

```js
class Dog{
  bark(count){
      /*...*/
  }
}
```
Dog의 모든 인스턴스들이 호출된 모든 회수를 알고 싶다면 커맨드라인에 아래와 같이 추가하면

```js
var p = Dog.prototype;
Object.getOwnPropertyNames(p).forEach((k) => monitor(p[k]));
```

결과 : `function bark called with arguments: 2`

콘솔에 로그만 하는 대신, 메소드 호출 시 실행 중단을 원한다면 `monitor` 대신 `debug`를 사용할 수 있습니다. 

<h3 id="2-1">From a Specific Instance : 특정 인스턴스을 통해</h3>
  `📌CHROME ONLY FEATURE`
 만약 클래스는 모르는 인스턴스가 있을 경우, 아래를 커맨드라인에 추가하면 

```js
var p = instance.constructor.prototype;
Object.getOwnPropertyNames(p).forEach((k) => monitor(p[k]));
```
Dog 클래스뿐만 아니라 모든 인스턴스의 모든 클래스에서 위와 같은 기능을 하는 함수를 작성하고 싶다면 유용합니다.
 
<h2 id="3">Call and Debug a Function : 함수 호출과 Debugger 또는 debug() 호출하기</h2>

함수를 호출하기 전에 콘솔에서 디버깅을 하고 싶다면 `debugger`를 호출 할 수 있습니다. 

```js
function fn(){
/*...*/
}

```

콘솔에 `debugger; fn(1);`를 입력한 후 -> `step into next function call (다음 함수 호출 단계로 넘어가면)` fn 함수의 구현체를 디버깅할 수 있습니다.

fn의 정의를 찾아서 수동으로 중단점을 추가하고 싶지 않을 경우나, fn이 동적으로 함수에 종속되어 소스를 모를 경우 유용합니다. 

크롬에서는 추가적으로 debug(fn)을 커맨드라인에 호출할 떄마다 fn 내부에서 실행을 중단하도록 설정하는 옵션이 있습니다.

<h2 id="4">Pause Execution on URL Change : URL이 변경될 경우 실행 중단</h2>
SPA(single-page application)에서 URL이 변경(라우팅 이벤트 발생)되기 전에 실행을 중단하게 할 수 있습니다. 

```js
const dbg = ()=>{debugger;};
history.pushState = dbg;
history.replaceState = dbg;
window.onhashchange = dbg;
window.onpopstate = dbg;
```
페이지 이동 실행중단을 하지 않는 dgb의 버젼을 만드는 독자의 몫이다.

또한, `window.location.replace/assign`과 같이 직접 페이지를 할당하여 페이지가 언로드 되어, 디버깅할게 없어지는 경우에 대한 처리를 해주지 않았다는 점에 유의해야한다.

만약 redirect 하기 이전의 (그리고 redirect 시의 상태를 디버그하고 싶다면 크롬의 debug()를 통해 유사한 메소드를 디버그할 수 있다. 

```js
debug(window.location.replace);
debug(window.location.assign);
```

<h2 id="5">Debugging Property Reads</h2>
<h2 id="6">Use copy()</h2>
<h2 id="7">Debugging HTML/CSS</h2>
<h3 id="7-1">Inspect the DOM with JS Disabled</h3>
<h3 id="7-2">Inspect an Elusive Element</h3>
<h3 id="7-3">Record Snapshots of the DOM</h3>
<h3 id="7-4">Monitor Focused Element</h3>
<h3 id="7-5">Find Bold Elements</h3>
<h4 id="7-5-1">Just Descendants</h4>
<h3 id="7-6">Reference Currently Selected</h3>
<h3 id="7-7">Element</h3>
<h4 id="7-7-1">Previous Element</h4>
<h4 id="7-7-2">Get Event Listeners</h4>
<h3 id="7-8">Monitor Events for Element</h3>
