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

## response.write
> https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback

HTTP body의 날 것이라고 한다. 테스트 할 때 말고는 딱히 쓸 일은 없다.

```js
res.write('<html>');
res.write('<head><title>제목</title></head>');
res.write('<body><h1> 힘들게써야되지 ? </h1></body>');
res.write('</html>');
```


## response.end
> https://nodejs.org/api/http.html#http_response_end_data_encoding_callback

서버에 응답 헤더와 바디를 보냈음을 알리고 처리. 주로 응답 하기 직전 써주어야 함.

## 요청 분석하기

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