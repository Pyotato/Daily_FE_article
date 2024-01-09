# 🗣️ 브라우저는 당신이 몰랐으면 하는 67가지 이상한 디버깅 트릭들


- `원문 : [67 Weird Debugging Tricks Your Browser Doesn't Want You to Know](https://alan.norbauer.com/articles/browser-debugging-tricks#user-content-fn-1)`
- `브라우저 디버거를 최대한 활용할 수 있는 유용하지만 당연하지는 않는 팁들을 담았습니다. 개발자 툴에 대한 중상 이상의 이해도를 지닌 독자들을 위한 글입니다.`

> 목차
>
> - [Advanced Conditional Breakpoints](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1)
>   - [Logpoints / Tracepoints](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1)
>     - [Watch Pane](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1-1)
>     - [Tracing Callstacks](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-1-2)
>   - [Changing Program Behavior](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-2)
>   - [Quick and Dirty Performance](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-3)
>   - [Profiling](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-4)
>   - [Using Function Arity](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5)
>     - [Break on Number of Arguments](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5-1)
>     - [Break on Function Arity Mismatch](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-5-2)
>   - [Using Time](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-6)
>     - [Skip Page Load](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-6-1)
>     - [Skip N Seconds](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-7-1)
>   - [Using CSS](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-7)
>   - [Even Calls Only](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-8)
>   - [Break on Sample](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-9)
>   - [Never Pause Here](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-10)
>   - [Automatic Instance IDs](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-11)
>   - [Programmatically Toggle](https://github.com/Pyotato/Daily_FE_article/blob/main/debugging/67_debugging_tricks.md#1-12)
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

<h2 id="1">Advanced Conditional Breakpoints</h2>

<h3 id="1-1">Logpoints / Tracepoints</h3>
<h4 id="1-1-1">Watch Pane</h4>
<h4 id="1-1-2">Tracing Callstacks</h4>
<h3 id="1-2">Changing Program Behavior</h3>
<h3 id="1-3">Quick and Dirty Performance</h3>
<h3 id="1-4">Profiling</h3>
<h3 id="1-5">Using Function Arity</h3>
<h4 id="1-5-1">Break on Number of Arguments</h4>
<h4 id="1-5-2">Break on Function Arity Mismatch</h4>
<h3 id="1-6">Using Time</h3>
<h4 id="1-6-1">Skip Page Load</h4>
<h4 id="1-6-2">Skip N Seconds</h4>
<h3 id="1-7">Using CSS</h3>
<h3 id="1-8">Even Calls Only</h3>
<h3 id="1-9">Break on Sample</h3>
<h3 id="1-10">Never Pause Here</h3>
<h3 id="1-11">Automatic Instance IDs</h3>
<h3 id="1-12">Programmatically Toggle</h3>
<h2 id="2">monitor() class Calls</h2>
<h3 id="2-1">From a Specific Instance</h3>
<h2 id="3">Call and Debug a Function</h2>
<h2 id="4">Pause Execution on URL Change</h2>
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
