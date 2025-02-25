# Node.js

생성일: 2023년 3월 14일 오후 11:47
summary: node.js는 웹 서버가 아니라 자바스크립트 런타임이다
tags: Node.js, 자바스크립트

# Node.js

## Node.js란 무엇인가?

Node.js(이하 노드)는 **Chrome V8 Javascript 엔진으로 빌드된 Javascript 런타임**이다. 런타임이란 **특정 언어로 만든 프로그램들을 실행할 수 있는 환경**을 의미한다. 노드의 주된 쓰임이 서버 어플리케이션 개발이라 오해가 있지만 기본적으로 노드는 자바스크립트 어플리케이션을 실행하기 위한 환경이다.

기존에는 자바스크립트 프로그램을 웹 브라우저 위에서만 실행할 수 있었다. 하지만 2008년 구글이 V8 엔진을 사용하여 크롬을 출시했고 매우 빠른 속도로 동작하였기에 브라우저 외의 환경에서 자바스크립트를 실행하기 위한 프로젝트가 시작되었고, 라이언 달에 의해 Node.js가 발표되었다.

### 이벤트 루프

노드는 이벤트 기반으로 동작한다. 브라우저 혹은 노드가 아래와 같은 요소를 제공하며 이벤트 루프를 지원한다.

- 이벤트 루프: 이벤트 발생시 호출할 콜백 함수들을 관리하고, 호출된 콜백 함수의 실행 순서를 결정하는 역할을 담당한다. 종료될 때까지 이벤트 처리를 위한 작업을 반복하므로 루프라고 부른다.
- 백그라운드: 타이머나 이벤트 리스너들이 대기하는 곳이다. 여러 작업이 동시에 실행될 수 있다.
- 태스크 큐: 이벤트 발생 후, 백그라운드에서는 태스크 큐로 타이머나 이벤트 리스너의 콜백 함수를 보낸다. 콜백 들은 보통 완료된 순서대로 줄을 서있지만 특정한 경우에는 순서가 바뀌기도 한다.

### 논 블로킹 I/O

이벤트 루프를 잘 활용하면 오래 걸리는 작업을 효율적으로 처리할 수 있다. 노드는 I/O 작업을 백그라운드로 넘겨 동시에 처리할 수 있다.

### 싱글 스레드

자바스크립트 코드는 동시에 실행될 수 없다. 스레드가 하나뿐인 환경이기 때문이다. (노드 프로세스가 내부적으로 여러개의 스레드를 생성하기는 하지만 직접 제어 가능한 스레드는 하나뿐이다) 예외적으로 암호화, 파일 입출력, 압축 등을 수행할때 `스레드 풀(Thread Pool)`을 사용하여 멀티스레드로 동작하는 경우도 존재한다. 또한 노드 12부터는 `워커 스레드(Worker Thread)` 기능이 제공되어 직접 다수의 스레드를 다루는 방법도 추가되었다.

### 서버로서의 노드

서버 프로그램은 기본적으로 I/O 요청이 많이 발생하므로, 논 블로킹 방식의 노드가 서버로서 장점이 있다. 하지만 CPU 부하가 큰 작업에는 적절하지 않을 수 있다. 따라서 개수는 많지만 크기는 작은 데이터를 실시간으로 주고 받는 서비스에 적합하다. 예를 들어 실시간 채팅 어플리케이션이나 주식 차트, JSON 데이터를 제공하는 API 서버에 노드가 많이 사용된다. 그러나 이미지나 비디오 처리, 대규모 데이터 처리처럼 CPU를 많이 사용하는 작업을 위한 서버로는 적합하지 않다. 또한 싱글 스레드 방식이므로 하나 뿐인 스레드가 에러로 인해 멈출 수 있으므로 잘 관리할 필요가 있다.

### 서버 외의 노드

노드는 자바스크립트 런타임이므로 용도가 서버에만 한정되지 않는다. 사용 범위는 웹, 모바일, 데스크탑 어플리케이션 개발에 까지 넓어졌다. 노드 기반으로 돌아가는 대표적인 웹 프레임워크로는 앵귤러, 리액트, 뷰 등이 있다. 모바일 개발도구로는 리액트 네이티브, 데스크탑 개발 도구로는 일렉트론이 대표적이다.

## Node.js의 기능

### 모듈 시스템

여러 파일에 걸쳐 재사용되는 함수나 변수를 모듈로 만들어두면 편리하다. ES2015에서 표준 모듈 시스템 문법이 생겼지만(import, export) Node.js에서의 기본 모듈 시스템 활용은 아래 코드와 같다. 노드 또한 버전9부터 표준 모듈 시스템 사용이 가능해졌다.

```jsx
// var.js
const odd = “홀수”;
const even = “짝수”;

module.exports = {
	odd,
	even,
};
```

```jsx
// func.js
const { odd, even } = require(‘./var’);

function checkOdd(num) {
	return num % 2 ? odd : even;
}

module.exports = checkOdd;
```

### 노드 내장 객체

### global 객체

브라우저의 window와 같은 **전역 객체**이다. 전역 객체이므로 모든 파일에서 접근할 수 있다. 또한 window 객체와 마찬가지로 global도 생략할 수 있다. 예를 들어 console.log() 역시 원형은 global.console.log()이다. 함수 선언문 내부의 this는 global 객체를 가리킨다. (최상위 스코프의 this는 module.exports를 가리킨다)

### console 객체

- console.time(레이블)
- console.log(내용)
- console.error(에러내용)
- console.table(배열)
- console.dir(객체, 옵션)
- console.trace(레이블)

### 타이머 객체

- setTimeout(콜백 함수, 밀리초)
- setInterval(콜백 함수, 밀리초)
- setImmediate(콜백 함수)

위 타이머 함수들은 아이디를 반환한다. 그 아이디를 이용해서 타이머를 취소할 수 있다.

- clearTimeout(아이디)
- clearInterval(아이디)
- clearImmediate(아이디)

### __filename, __dirname

현재 파일의 경로와 파일명을 제공하는 키워드이다.

### process.env

시스템 환경변수를 저장하는 내장 객체이다. 환경변수를 넣는 방법은 운영체제마다 차이가 있지만 `dotenv` 라이브러리를 사용하여 통일할 수 있다.

```jsx
const secretId = process.env.SECRET_ID;
```

### process.nextTick(콜백)

process.nextTick()은 setImmediate()나 setTimeout()보다 먼저 실행된다. 이벤트 루프에서 태스크 큐처럼 `마이크로태스트 큐`가 별도로 존재하며 이는 태스크 큐보다 우선시 된다. nextTick 함수나 resolve된 Promise가 이 마이크로태스크 큐에 존재하게 된다.

### 노드 내장 모듈 사용하기

### os 모듈

브라우저의 자바스크립트는 운영체제의 정보를 가져올 수 없지만, 노드는 os 모듈을 통해 정보를 가져올 수 있다.

```jsx
const os = require(‘os’);
console.log(os.arch());
console.log(os.cpus().length);	// 코어의 개수
```

### path 모듈

path.sep: 경로의 구분자입니다. 윈도는 \, POSIX는 /입니다.

- path.delimiter: 환경 변수의 구분자입니다. process.env.PATH를 입력하면 여러 개의 경로가 이 구분자로 구분되어 있습니다. 윈도는 세미콜론(;)이고, POSIX는 콜론(:)입니다.
- path.dirname(경로): 파일이 위치한 폴더 경로를 보여줍니다.
- path.extname(경로): 파일의 확장자를 보여줍니다.
- path.basename(경로, 확장자): 파일의 이름(확장자 포함)을 표시합니다. 파일의 이름만 표시하고 싶다면 basename의 두 번째 인수로 파일의 확장자를 넣으면 됩니다.
- path.parse(경로): 파일 경로를 root, dir, base, ext, name으로 분리합니다.
- path.format(객체): path.parse()한 객체를 파일 경로로 합칩니다.
- path.normalize(경로): /나 \를 실수로 여러 번 사용했거나 혼용했을 때 정상적인 경로로 변환합니다.
- path.isAbsolute(경로): 파일의 경로가 절대경로인지 상대경로인지를 true나 false로 알립니다.
- path.relative(기준경로, 비교경로): 경로를 두 개 넣으면 첫 번째 경로에서 두 번째 경로로 가는 방법을 알립니다.
- path.join(경로, …): 여러 인수를 넣으면 하나의 경로로 합칩니다. 상대경로인 ..(부모 디렉터리)과 .(현 위치)도 알아서 처리합니다.
- path.resolve(경로, …): path.join()과 비슷하지만 차이가 있습니다. 차이점은 다음에 나오는 Note에서 설명합니다.

Note ≡ join과 resolve의 차이
path.join과 path.resolve 메서드는 비슷해 보이지만 동작 방식이 다릅니다. /를 만나면 path.resolve는 절대경로로 인식해서 앞의 경로를 무시하고, path.join은 상대경로로 처리합니다. 코드로 보면 이해하기 쉽습니다.
path.join('/a', '/b', 'c');  /*  결과 : /a/b/c/ */
path.resolve('/a', '/b', 'c');  /*  결과 : /b/c */

### url 모듈과 querystring 모듈

```jsx
const url = require('url');

const { URL } = url;
const myURL = new URL('<http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor>');
console.log('new URL():', myURL);
console.log('url.format():', url.format(myURL));
console.log('------------------------------');
const parsedUrl = url.parse('<http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor>');
console.log('url.parse():', parsedUrl);
console.log('url.format():', url.format(parsedUrl));
```

```jsx
const url = require('url');
const querystring = require('querystring');

const parsedUrl = url.parse('<http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript>');
const query = querystring.parse(parsedUrl.query);
console.log('querystring.parse():', query);
console.log('querystring.stringify():', querystring.stringify(query));
```

### 워커 스레드

노드에서 멀티 스레드 방식으로 작업하는 방법이다.

```jsx
const {
  Worker, isMainThread, parentPort,
} = require('worker_threads');

if (isMainThread) { // 부모일 때
  const
  worker = new Worker(__filename);
  worker.on('message', message => console.log('from worker', message));
  worker.on('exit', () => console.log('worker exit'));
  worker.postMessage('ping');
} else { // 워커일 때
  parentPort.on('message', (value) => {
    console.log('from parent', value);
    parentPort.postMessage('pong');
    parentPort.close();
  });
}
```

### child_process 모듈을 이용하여 파이썬 코드 실행하기

```jsx
const spawn = require('child_process').spawn;

var process = spawn('python', ['test.py']);

process.stdout.on('data', function(data) {
  console.log(data.toString());
}); // 실행 결과

process.stderr.on('data', function(data) {
  console.error(data.toString());
}); // 실행 >에러
```

### fs 모듈

- 버퍼와 스트림

```jsx
const buffer = Buffer.from('저를 버퍼로 바꿔보세요');
console.log('from():', buffer);
console.log('length:', buffer.length);
console.log('toString():', buffer.toString());

const array = [Buffer.from('띄엄 '), Buffer.from('띄엄 '), Buffer.from('띄어쓰기')];
const buffer2 = Buffer.concat(array);
console.log('concat():', buffer2.toString());

const buffer3 = Buffer.alloc(5);
console.log('alloc():', buffer3);
```

```jsx
const zlib = require('zlib');
const fs = require('fs');

const readStream = fs.createReadStream('./readme4.txt');
const zlibStream = zlib.createGzip();
const writeStream = fs.createWriteStream('./readme4.txt.gz');
readStream.pipe(zlibStream).pipe(writeStream);
```

- 기타 메서드
- fs.access(경로, 옵션, 콜백): 폴더나 파일에 접근할 수 있는지를 체크합니다. 두 번째 인수로 상수들(constants를 통해 가져옵니다)을 넣었습니다. F_OK는 파일 존재 여부, R_OK는 읽기 권한 여부, W_OK는 쓰기 권한 여부를 체크합니다. 파일/폴더나 권한이 없다면 에러가 발생하는데 파일/폴더가 없을 때의 에러 코드는 ENOENT입니다.
- fs.mkdir(경로, 콜백): 폴더를 만드는 메서드입니다. 이미 폴더가 있다면 에러가 발생하므로 먼저 access 메서드를 호출해서 확인하는 것이 중요합니다.
- fs.open(경로, 옵션, 콜백): 파일의 아이디(fd 변수)를 가져오는 메서드입니다. 파일이 없다면 파일을 생성한 뒤 그 아이디를 가져옵니다. 가져온 아이디를 사용해 fs.read나 fs.write로 읽거나 쓸 수 있습니다. 두 번째 인수로 어떤 동작을 할 것인지를 설정할 수 있습니다. 쓰려면 w, 읽으려면 r, 기존 파일에 추가하려면 a입니다. 앞의 예제에서는 w를 했으므로 파일이 없을 때 새로 만들 수 있었습니다. r이었다면 에러가 발생했을 것니다.
- fs.rename(기존 경로, 새 경로, 콜백): 파일의 이름을 바꾸는 메서드입니다. 기존 파일 위치와 새로운 파일 위치를 적으면 됩니다. 꼭 같은 폴더를 지정할 필요는 없으므로 잘라내기 같은 기능을 할 수도 있습니다.

### 이벤트 이해하기

events 모듈을 사용하면 됩니다. myEvent라는 객체를 먼저 만듭니다. 객체는 이벤트 관리를 위한 메서드를 가지고 있습니다.

- on(이벤트명, 콜백): 이벤트 이름과 이벤트 발생 시의 콜백을 연결합니다. 이렇게 연결하는 동작을 이벤트 리스닝이라고 부릅니다. event2처럼 이벤트 하나에 이벤트 여러 개를 달아줄 수도 있습니다.
- addListener(이벤트명, 콜백): on과 기능이 같습니다.
- emit(이벤트명): 이벤트를 호출하는 메서드입니다. 이벤트 이름을 인수로 넣으면 미리 등록해뒀던 이벤트 콜백이 실행됩니다.
- once(이벤트명, 콜백): 한 번만 실행되는 이벤트입니다. myEvent.emit('event3')을 두 번 연속 호출했지만 콜백이 한 번만 실행됩니다.
- removeAllListeners(이벤트명): 이벤트에 연결된 모든 이벤트 리스너를 제거합니다. event4가 호출되기 전에 리스너를 제거했으므로 event4의 콜백은 호출되지 않습니다.
- removeListener(이벤트명, 리스너): 이벤트에 연결된 리스너를 하나씩 제거합니다. 리스너를 넣어야 한다는 것을 잊지 마세요. 역시 event5의 콜백도 호출되지 않습니다.
- off(이벤트명, 콜백): 노드 10 버전에서 추가된 메서드로, removeListener와 기능이 같습니다.
- listenerCount(이벤트명): 현재 리스너가 몇 개 연결되어 있는지 확인합니다.

```jsx
const EventEmitter = require('events');

const myEvent = new EventEmitter();
myEvent.addListener('event1', () => {
  console.log('이벤트 1');
});
myEvent.on('event2', () => {
  console.log('이벤트 2');
});
myEvent.on('event2', () => {
  console.log('이벤트 2 추가');
});
myEvent.once('event3', () => {
  console.log('이벤트 3');
}); // 한 번만 실행됨

myEvent.emit('event1'); // 이벤트 호출
myEvent.emit('event2'); // 이벤트 호출

myEvent.emit('event3'); // 이벤트 호출
myEvent.emit('event3'); // 실행 안 됨

myEvent.on('event4', () => {
  console.log('이벤트 4');
});
myEvent.removeAllListeners('event4');
myEvent.emit('event4'); // 실행 안 됨

const listener = () => {
  console.log('이벤트 5');
};
myEvent.on('event5', listener);
myEvent.removeListener('event5', listener);
myEvent.emit('event5'); // 실행 안 됨

console.log(myEvent.listenerCount('event2'));
```

### 예외 처리하기

### uncaughtException

try~catch가 에러처리의 기본이다. 하지만 최후의 수단으로 처리되지 못한 에러를 처리하는 방법이 uncaughtException 처리이다. 다만 권장되지 않는 방법으로 단순 에러내용을 기록하고 process.exit()으로 프로세스를 종료하는 것이 좋다. 대신 운영중인 서버가 에러로 인해 종료되었을 때 자동으로 재시작하도록 운영하도록 하자.

```jsx
process.on('uncaughtException', (err) => {
  console.error('예기치 못한 에러', err);
});

setInterval(() => {
  throw new Error('서버를 고장내주마!');
}, 1000);

setTimeout(() => {
  console.log('실행됩니다');
}, 2000);
```

### 자주 발생하는 에러들

- ReferenceError: 모듈 is not defined: 모듈을 require했는지 확인합니다.
- Error: Cannot find module 모듈명: 해당 모듈을 require했지만 설치하지 않았습니다. npm i 명령어로 설치하세요.
- Error: Can't set headers after they are sent: 요청에 대한 응답을 보낼 때 응답을 두 번 이상 보냈습니다. 요청에 대한 응답은 한 번만 보내야 합니다. 응답을 보내는 메서드를 두 번 이상 사용하지 않았는지 체크해보세요.
- FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory: 코드를 실행할 때 메모리가 부족하여 스크립트가 정상 작동하지 않는 경우입니다. 코드가 잘못되었을 확률이 높으므로 코드를 점검해보세요. 만약 코드는 정상이지만 노드가 활용할 수 있는 메모리가 부족한 경우라면 노드의 메모리를 늘릴 수 있습니다. 노드를 실행할 때 node --max-old-space-size=4096 파일명과 같은 명령어를 사용하면 됩니다. 4096은 4GB를 의미합니다. 원하는 용량을 적으면 됩니다.
- UnhandledPromiseRejectionWarning: Unhandled promise rejection: 프로미스 사용 시 catch 메서드를 붙이지 않으면 발생합니다. 항상 catch를 붙여 에러가 나는 상황에 대비하세요.
- EADDRINUSE 포트 번호: 해당 포트 번호에 이미 다른 프로세스가 연결되어 있습니다. 그 프로세스는 노드 프로세스일 수도 있고 다른 프로그램일 수도 있습니다. 그 프로세스를 종료하거나 다른 포트 번호를 사용해야 합니다.
- EACCES 또는 EPERM: 노드가 작업을 수행하는 데 권한이 충분하지 않습니다. 파일/폴더 수정, 삭제, 생성 권한을 확인해보는 것이 좋습니다. 맥이나 리눅스 운영체제라면 명령어 앞에 sudo를 붙이는 것도 방법입니다.
- EJSONPARSE: package.json 등의 JSON 파일에 문법 오류가 있을 때 발생합니다. 자바스크립트 객체와는 형식이 조금 다르니 쉼표 같은 게 빠지거나 추가되지는 않았는지 확인해보세요.
- ECONNREFUSED: 요청을 보냈으나 연결이 성립하지 않을 때 발생합니다. 요청을 받는 서버의 주소가 올바른지, 꺼져 있지는 않은지 확인해봐야 합니다.
- ETARGET: package.json에 기록한 패키지 버전이 존재하지 않을 때 발생합니다. 해당 버전이 존재하는지 확인하세요.
- ETIMEOUT: 요청을 보냈으나 응답이 일정 시간 내에 오지 않을 때 발생합니다. 역시 요청을 받는 서버의 상태를 점검해봐야 합니다.
- ENOENT: no such file or directory: 지정한 폴더나 파일이 존재하지 않는 경우입니다. 맥이나 리눅스 운영체제에서는 대소문자도 구별하므로 확인해봐야 합니다.