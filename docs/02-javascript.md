# 02. Javascript

- Javascript는 웹페이지의 보조적인 기능을 수행하기 위해 브라우저에서 동작하는 경량 프로그래밍 언어이다.
- 브라우저에 따라 웹페이지가 정상적으로 동작하지 않는 '크로스 브라우징 이슈'를 해결하기 위해 ECMA International에서 Javascript의 표준 사양이 발표되었다.
  - ECMAScript 1 (ES1)
- HTML5와 함계 ES5가 출시되었고, ES6부터 범용 프로그래밍 언어로서의 기능이 대거 추가되었다.

## Javascript 역사

- Ajax : 서버와 브라우저가 비동기 방식으로 데이터를 교환할 수 있는 통신 기느
  - XMLHttpRequest
  - 변경이 필요 없는 부분은 다시 렌더링하지 않고, 필요한 데이터만 서버로부터 전송받아 필요한 부분만 한정적으로 렌더링하는 방식
  - 이로 인해 화면을 전환할 때 마다 새 HTML이 로드되어 화면이 깜빡이는 문제가 수정되고, 브라우저에서도 빠른 성능과 부드러운 화면 전환이 가능해졌다.
- JQuery : Javascript로 DOM을 더욱 쉽게 제어할 수 있게 만든 프레임워크
- V8 Javascript Engine
  - 구글의 V8 engine은 javascript를 더욱 빠르게 실행한다.
  - V8 엔진에 의해 서버에서 수행되던 로직들이 클라이언트로 이동하여 프론트엔드 개발이 주목받는 계기가 됐다.
- Node.js : 구글 V8 engine으로 빌드된 javascript runtime environment
  - 과거에 javascript는 브라우저에 내장된 engine에 의해서만 동작했다.
  - Node.js는 javascript를 브라우저 이외의 환경에서도 동작할 수 있도록 js engine을 독립시킨 실행 환경이다.
  - Server side application을 개발할 때 사용된다.
  - Frontend/Backend가 같은 javascript 언어를 사용하게 되어 학습 시간을 줄이는 효과가 있다.
  - Node.js는 비동기 I/O를 지원하고 단일 스레드 이벤트 루프 기반으로 동작하여 request 성능이 좋아 SPA(Single Page Application)에 적합하다.
- SPA Framework : 변경에 유연하면서 확장하기 쉬운 application architecture를 구축하는 프레임워크
  - Angular, React, Vue.js, Svelte 등

## ECMAScript

- Javascript = ECMAScript + Client Side Web API
- ECMAScript
  - Javascript의 핵심 문법을 규정하는 표준 사양 (ECMA-262)
  - 각 브라우저는 ECMAScript 사양을 준수해서 브라우저에 내장되는 js engine을 구현한다.
- Client Side Web API
  - DOM, BOM
  - Canvas
  - XMLHttpRequest, fetch
  - requestAnimationFrame, SVG, Web Storage, Web Component, Web Worker 등

## Javascript 특징

- 웹 브라우저에서 동작하는 유일한 프로그래밍 언어
- 컴파일 작업을 하지 않는 인터프리터 언어
- 인터프리터는 속도가 느리므로, 인터프리터와 컴파일러의 장점을 결합해서 만든 javascript engine을 통해 속도 문제를 해결한다.
  - 크롬의 V8
  - 파이어폭스의 SpiderMonkey
  - 사파리의 JavaScriptCore
  - 마이크로소프트 엣지의 Chakra
- Javascript는 일부 소스코드가 컴파일되어 실행되지만, 실행 파일이 생성되지 않고 인터프리터의 도움이 필요하므로 컴파일 언어는 아니다.
- 일부 구형 브라우저나 실행 환경에서 ES6+ 를 지원하지 않을 수 있으므로, Babel 같은 트랜스파일러를 사용해서 ES6+ 사양 js 코드를 ES5 이하로 다운그레이드 해야 하는 경우도 있다.
