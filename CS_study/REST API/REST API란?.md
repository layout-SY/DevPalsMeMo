# REST API
- REST API란 RESTful한 API를 말하며 일련의 특징과 규칙 등을 지키는 API

## REST API의 특징

### Uniform-Interface
- API에서 자원들은 각각의 독립적인 인터페이스를 가지며 각각의 자원들이 url 자원식별, 표현을 통한 자원조작, Self-descriptive messages, HATEOAS 구조를 가지는 것
    - 독립적인 인터페이스란?
        - 서로 종속적이지 않은 인터페이스.<br><br>
            예 ) HTTP 명세나 HTML 명세가 변경 되어도 웹 페이지는 잘 작동함.

#### URL 자원 식별
- identification of resources를 말함. 자원은 URL로 식별 되어야 함.

#### 표현을 통한 자원 조각
- manipulation of resources through representations은 URL과 GET, DELETE, 등 HTTP 표준 메서드 등을 통해 자원을 조회, 삭제 등 작업을 설명할 수 있는 정보가 담겨야 하는 것을 말함.

#### Self-descriptive messages
- HTTP Header에 타입을 명시하고 각 메세지(자원)들은 MIME types에 맞춰 표현 되어야 함.<br><br>
    예 ) .json을 반환한다면 application/json으로 명시해줘야 함.
    - MIME types는 문서, 파일 등의 특성과 형식을 나타내는 표준(font/ttf, text/plain, text/csv 등) <br><br>
    예 ) json 타입의 데이터를 보낼 때 헤더의 'Content-Type' = 'application/json'을 명시해야 함.

#### HATEOAS 구조
- Hypermedia as the Engine of Application State는 하이퍼링크에 따른 다른 페이지를 보여줘야 하며 데이터 마다 어떤 URL에서 원했는 지 명시 해줘야 하는 것을 말함.
- 보통 href, links, link, url 속성 중 하나의 해당 데이터의 URL을 담아서 표기해야 함.

### Stateless
- REST API에서 제공해주는 서버는 세션을 해당 서버 쪽에 유지하지 않는다는 의미

### Cacheable
- HTTP는 어떤 로직을 구현하지 않아도 자동으로 캐싱됨.<br>
    새로고침하게 되면 304가 뜨며 원래 있던 js와 css 이미지 등을 불러옴
- HTTP 메서드 중 GET에 한정해 'Cache-Control:max-age=100' 이런 식으로 한정된 시간을 정할 수 있음.
- 캐싱된 데이터가 유효한지를 판단하기 위해 'Last-modified'와 'Etag'라는 헤더값을 씀.

### Client-Server 구조
- 클라이언트와 서버가 서로 독립적인 구조를 가져야함.(HTTP를 통해 가능한 구조)
- 서버에서 HTTP 표준만 지킨다면 웹에서는 그에 따른 화면이 잘 나타남
- 서버는 API를 제공하고 그 API에 맞는 비즈니스 로직을, 클라이언트는 HTTP로 받는 로직만 잘 처리하면 됨.

### Layered System
- 계층 구조로 나눠져 있는 아키텍쳐를 뜻함.
- WEB 기반 서비스를 하면 보통 이러한 시스템을 구축

## REST API의 URI 규칙
1. 동작은 HTTP 메소드로만, url에 해당 내용이 들어가면 안됨.<br>
    예 ) '/book/delete/1' 이런 식으로 하면 안됨.
2. .jpg, .png 등 확장자는 표시하지 말아야함
3. 동사가 아닌 명사로만 표기해야 함
4. 계층적인 내용을 담고 있어야 함.<br>
   예 ) '/집/아파트/전세' 이런 식으로
5. 대문자가 아닌 소문자를 쓰며 너무 길어 바(_, -)를 써야 할 경우 언더바가 아닌 그냥 바를 사용
6. HTTP 응답 상태코드를 적재적소에 활용함.<br>
    예 ) 성공 시 200, 리다이렉트 시 301 등