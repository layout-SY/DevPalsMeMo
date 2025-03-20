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