# WebSocket
- 진행했던 DevPals 프로젝트에 실시간 알림 기능/채팅 기능 추가를 위한 공부
- 기본 개념부터 차근차근 쌓기.

# WebSocket이란?
- 기존의 http 방식은 요청과 응답을 통해 데이터를 송수신.<br><br>
문제는 이렇게 되면 서버는 클라이언트가 요청하기 전까진 먼저 데이터를 보낼 수 없어 클라이언트는 항상 서버에 무결성을 확인하기 위해 지속적으로 요청을 보내는 비효율적인 문제 발생. => 웹 소켓으로 해결
- 웹 앱과 서버 간 지속적인 연결을 제공하는 "프로토콜" -> 양방향 통신
- http는 단일성 연결이지만, WebSocket은 한 번 열리면 계속 유지됨.


# WebSocket의 특징
- 실시간 통신 : 웹 애플리케이션의 실시간 업데이트(채팅, 주식 거래 등)
- 효율성 : 오버 헤드가 낮음.<br><br> <blockquote>오버 헤드는 함수를 실행하는 시간이 아닌 함수를 호출하는, 즉 어떤 동작을 수행하기 위한 기타 과정이 포함되는 기간을 말함.</blockquote>
- 서버에서 클라이언트로 직접 푸시 : 클라이언트가 서버로의 지속적인 폴링 없이 데이터를 관리 가능. -> 리소스 사용을 최소화 하며 반응성을 높임 <br><br> <blockquote>폴링이란 react query 같은 곳에서 데이터의 동기화 및 충돌 회피를 위해 검사 및 서버로 요청하는 것을 말함</blockquote>
- 낮은 지연 시간 : 초기 핸드쉐이크 과정 이후 지속적을로 열려 있으므로 지연 시간 단축
- 양방향 통신 : 클라이언트/서버 모두 데이터 송수신 가능
- 유연성 : WebSocket은 포트 80과 443을 통해 통신하므로, 대부분 방화벽 및 프록시 서버에게 차단되지 않음
- 이벤트 기반 : 클라이언트/서버 간 데이터 전송이 이벤트 기반으로 이루어져 실시간 업데이트 및 상호작용을 구현할 수 있음.

# 웹소켓 동작 원리

## 1. 핸드쉐이크
- 최초 클라이언트와 서버와의 연결은 http 요청을 통해 각 소켓들을 연결하고, 이후로 통신되는 데이터들은 http가 아닌 소켓을 통해 제어하지만, 직접 제어하는 것이 아닌 WebSocket 프로토콜을 통해 간접 제어.
- 즉, 채팅 기능이 활성화 된다면 해당 채팅 기능은 http로 최초 서버와 첫 연결이 동작되지만, 이후의 연결은 http 프로토콜에서 WebSocket 프로토콜로 동작하게 됨.

## 2. 연결 유지
- 핸드 쉐이크 이후 TCP 기반 연결이 http가 아닌 소켓을 통해 지속적으로 유지됨.

## 3. 데이터 교환
- 연결이 확립되면 클라이언트와 서버 간에 데이터를 교환할 수 있음.<br> 데이터는 메시지의 형태로 주고받으며, 이 메시지는 이벤트 기반으로 동작. <br>클라이언트 또는 서버는 언제든지 메시지를 보낼 수 있으며, 상대방은 이에 응답할 수 있음.

## 4. 연결 종료
- 웹소켓 연결을 종료하려면 클라이언트 또는 서버가 종료 프레임을 보냄. <br>이 프레임은 연결을 명시적으로 닫는 데 사용되며, 연결이 비정상적으로 종료되는 경우에는 네트워크 문제 또는 다른 이유로 인해 연결이 끊어진 것으로 판단함.

# React에서 사용

## WebSocket 연결
- 서버
```js
import WebSocket from 'ws';
const PORT = 1234;

const wss = new WebSocket.Server({ port: PORT }); 

wss.on('connection', (ws: WebSocket) => {
  console.log('새로운 클라이언트가 연결되었습니다.');

  ws.on('message', (message: string) => {
    console.log('메시지를 받았습니다: %s', message);
  });
  
  ws.send('hi from server');

  ws.on('close', () => {
    console.log('클라이언트 연결이 종료되었습니다.');
  });


  ws.on('error', (error: any) => {
    console.log(error);
  });
});

console.log(`WebSocket 서버가 ${PORT} 포트에서 실행 중입니다.`);
```

- 클라이언트
```jsx
  const [webSocket, setWebSocket] = useState<WebSocket | null>(null);

  useEffect(() => {
    const socket = new WebSocket('ws://localhost:1234/ws');

    socket.onopen = () => {
      console.log('WebSocket 연결이 열렸습니다.');
    };

    socket.onmessage = (event) => {
      console.log('서버로부터 메시지를 받았습니다:', event.data);
    };

    socket.onclose = () => {
      console.log('WebSocket 연결이 닫혔습니다.');
    };

    setWebSocket(socket);

    return () => {
      socket.close();
    };
  }, []);


  const handleSend = () => {
    if (webSocket && webSocket.readyState === WebSocket.OPEN) {
      webSocket.send(input);
      setInput('');
    } else {
      console.error('웹소켓 연결이 열려있지 않습니다.');
    }
  };
```