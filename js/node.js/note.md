# Node.js
updated 2021.08.22

## 개요
node.js 학습노트 

## 생명주기

```
node app.js 실행
    |
코드 해석, 변수 및 함수 등록
    |
[이벤트 루프] ----> 노드 어플리케이션
    |
이벤트 리스너가 등록되는 한 반복
```

## 시작하기

### http.createServer
> https://nodejs.org/api/http.html#http_http_createserver_options_requestlistener

서버 객체를 생성


```js
const http = require('http')
const server = http.createServer((req, res) => {
    ...
});
```

### server.listen
> https://nodejs.org/api/http.html#http_server_listen

HTTP 서버가 끊어지지 않게 유지하는 역할을 수행. 사용하지 않는다면 실행 후 종료되버린다.

```js
server.listen(3000);
```

### response.write
> https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback

HTTP body의 날 것이라고 한다. 테스트 할 때 말고는 딱히 쓸 일은 없다.

```js
res.write('<html>');
res.write('<head><title>제목</title></head>');
res.write('<body><h1> 힘들게써야되지 ? </h1></body>');
res.write('</html>');
```


### response.end
> https://nodejs.org/api/http.html#http_response_end_data_encoding_callback

서버에 응답 헤더와 바디를 보냈음을 알리고 처리. 주로 응답 하기 직전 써주어야 함.

## 요청 (request) 해석하기

express 를 사용하면 아래와 같은 코드를 쓰지 않아도 된다.
그러나, 어떻게 동작하는지 간단하게 알아둘 필요가 있다.   
예를 들어, 폼에서 이름이 name인 input 필드에 'yuu2dev' 를 입력한 후 전송해보자.   
```js
const server = (req, res) => {
    const buffer = [];
    // 버퍼에 데이터가 채워질 때마다 실행되는 이벤트
    req.on('data', chunk => {
        buffer.push(chunk);
    }),
    
    // 요청이 마무리 되었을 때 실행되는 이벤트
    req.on('end', () => {

        // chunk가 담긴 배열을 하나로 합쳐 새로운 버퍼를 만들어 문자열로 변환
        const parsed = Buffer.concat(buffer).toString();    
        
        // name=yuu2dev
        const [k, v] = parsed.split('=');
        console.log(k, v);
    });
}
```

## 디버깅 (Debugging)

많은 상품을 크롤링 한다던가 스택 큐가 실행 도중에 알 수 없는 에러로 인해 기대하는 결과 값이 나오지 않는다면 어쩔 수 없이 많은 시간을 할애해서 코드를 수정 해야 할 것이다.   
조금이라도 시간을 낭비하지 않기 위해서 콜스택 (Call Stack)에서 지점마다 어떤 값이 들어 있는지 확인 할 수만 있다면 내가 자주 사용하는 VsCode의 기능을 조금이나마 알 필요가 있을 것이다.   
https://code.visualstudio.com/docs/nodejs/nodejs-debugging

그리고 에러에 대한 설명을 잠깐 하자면 ...   

- 문법 에러
- 실행 에러
- 논리 에러

에러는 세가지로 구분 할 수 있다. 위에서 말한 예시는 논리 에러이다.   
이 논리에러는 간단하게 말하면 우리가 프로그램을 짜고 의도한대로 동작하지 않은 경우를 뜻한다.   

이 경우 에러 메시지를 출력하지 않아 발견하기 어렵다. vscode를 이용한다면 디버깅을 사용 할 수 있다. **Run -> Start Debugging -> Node.js** 를 통해서 실행 할 수 있다.      

만약 디버깅이 끝나고 exit 처리 되어 곤란하다면 설정 값을 추가해서 해소 할 수 있다.   
**프로젝트/.vsocde/launch.json** 에 아래와 같은 값을 추가해야 한다.

```json
"configurations": [
    {
        "type": "node",
        "request": "launch",
        "name": "Launch Program",
        // 진입점 설정
        "program": "${workspaceFolder}/app.js",
        "restart": true,
        // 노드몬을 사용한다면 설정
        "runtimeExecutable": "nodemon",
        // 디버그 콘솔이 아닌 터미널에서 볼 수 있음
        "console":"integratedTerminal",
    }
    ...
]
```




