# 클래스리스와 서브넷 마스크, 서브네팅
- 클래스풀의 단점을 보완하고자 등장
- 클래스로 나누는 것이 아닌 서브넷 마스크를 중심으로 어디까지가 네트워크 주소고 어디까지가 호스트 주소인지를 나눔
    - 서브네팅 : 네트워크를 나눈다는 의미
    - 서브넷 : 서브 네트워크, 쪼개진 네트워크
    - 서브넷 마스크 : 서브네트워크를 위한 비트 마스크
    참고로 현재 저희의 주소체계는 클래스리스

## 클래스리스
- 클래스 기반의 제한을 없애고 고정된 서브넷 마스크 대신 가변 길이 서브넷 마스크(VLSM)를 사용하여 네트워크와 호스트 부분을 동적으로 나누는 방법

## 서브넷마스크
- 네트워크 주소 부분만 모두 1, 호스트 주소 부분은 0으로 설정해 나눔
- 예 )
    - 192.168.50.112라면 
    - 한 옥텟 당 8비트니까 해당 네트워크 주소의 8비트 부분을 모두 1로, 호스트는 0으로 채움
        - 255.255.255.0
    - 여기에서 192.168.50.112과 255.255.255.0을 &(엔드 연산자)를 사용하면 192.168.50.0이 됨
        - 왜나면 각 네트워크 주소의 비트들은 1로, 호스트는 0으로 되어 있기 때문
            ![alt text](image-15.png)
![alt text](image-16.png)
- 맨 왼쪽은 서브넷 마스크의 값을 얼마로 할 껀 지 선정
- /20이라면 왼쪽부터 서브넷 마스크의 비트 값으로 비트 자리 20까지 1로 채우겠단 의미
    - 11111111.11111111.11110000.00000000 => 255.255.240.0
- /24라면?
    - 11111111.11111111.11111111.00000000 => 255.255.255.0 /24
- /25라면?
    - 11111111.11111111.11111111.10000000 => 255.255.255.128 /25

- 만약 10개의 사무실이 있으며 사무실당 12개의 장치를 연결해야 한다면?
    - 한 서브넷당 123.12.12.12/28로 16개의 네트워크를 확보해야 함. 16-2 = 14개 장치니까
    - 123.12.12.12
    - 255.255.255.
- 그러니까 할당된 기존 IP를 커스텀 하여 서브넷으로 바꾼 후 원하는 만큼 분할하여 사용
- 할당받은 IP 블록 내에서 원하는 만큼 서브넷으로 분할하여 네트워크 주소와 호스트 주소를 재설정 가능

## 클래스풀과 클래스 리스
- 클래스풀은 고정 길이 서브넷 마스크 방식을 사용하고, 클래스 리스는 가변 길이 서브넷 마스크 방식을 사용

## 공인 IP(public IP)와 사설 IP(private IP)와 NAT
![alt text](image-18.png)
- IP 주소의 부족을 공인 IP(public IP)와 사설 IP(private IP)로 나누고 중간에 NAT라는 기술을 통해 해결
- NAT(Network Address Translation)는 패킷이 트래픽 라우팅 장치를 통해 전송되는 동안 패킷의 IP 주소를 변경, IP 주소를 다른 IP 주소로 매핑하는 방법.
- NAT를 통해 내부 네트워크 IP가 노출되는 않는다는 점이 장점.
### 공유기와 NAT
- 실생활에서 인터넷 회선 하나를 개통하고 보통 공유기를 써서 wifi를 만들곤 하는데 이 때 여러 대의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하게 됨.
- 유튜브 서버의 응답 패킷이 집의 공인 IP(외부 인터페이스)를 통해 도착하면, NAT 라우터는 NAT 매핑 정보를 바탕으로 응답을 내부 사설 IP로 재매핑함. <br><br>
이때 NAT 라우터는 내부 LAN에서 해당 사설 IP와 연결된 스마트폰의 MAC 주소를 확인하기 위해 ARP를 사용합니다. 즉, ARP를 통해 올바른 내부 장치(스마트폰)의 MAC 주소를 확인하고, 그 주소로 데이터를 전달합니다.

### 예시
- 집 와이파이로 유튜브를 보다가 그대로 자연스럽게 집 밖으로 나가면 LTE가 되면서 무한 로딩이 되고, 새로고침이 필요해짐<br><br>
    1. 집에 있을 떈 
        - 영상 데이터를 요청할 때 사설 IP에서 집의 공인 IP로, 집의 공인 IP에서 유튜브 서버로 요청이 넘어가게 됨.
        - 이 단계에서 사설 IP에서 집의 공인 IP로 요청 데이터를 넘길 땐 이미 스마트폰의 ARP 테이블에 NAT 라우터(집의 공인 IP에 해당하는 인터페이스)의 MAC 주소가 등록되어 있으므로 추가 ARP 요청 없이 전송
        - 서버의 응답인 영상 데이터는 집의 공인 IP로 오게 되고, 집의 공인 IP에선 ARP를 통해 포트에 매핑된 사설 IP에서 내 스마트폰의 MAC 주소를 찾아 영상 데이터를 전달
    2. 밖으로 나오게 됐을 때
        - 집의 공인 IP와 사설 IP 간의 연결 유지 상태인 NAT 세션과 집의 공인 IP와 유튜브 서버와의 TCP 연결을 통해 영상 데이터의 요청/응답이 이루어졌지만, 밖으로 나오며 LTE 공인 IP로 변경되게 됨.
        - 당연히 집에서 내 스마트폰에 매핑되어 있던 사설 IP와 공인 IP의 NAT 매핑 관계를 종료됨.
        - 이 때, 스마트폰의 OSI 계층을 생각한다면
            - TCP 연결은 네트워크 계층의 정보를 토대로 연결됨(4-튜플 => 출발지 IP, 출발지 포트, 목적지 IP, 목적지 포트)
            - 따라서 내 스마트폰의 OSI 계층에서보면 네트워크 계층의 IP 주소(특히 출발지 IP)가 LTE 공인 IP로 변경된 부터 이미 TCP 만료를 의미
        - 따라서 유튜브가 새로고침 전에는 무한 로딩이 걸림. 
        - 그래서 새로고침을 통해 현재 네트워크(LTE)와 TCP 연결을 진행하여 영상 데이터에 대한 요청/응답이 다시 이루어짐.
- 반대 경우에도 마찬가지

## 참고
https://velog.io/@borab/IP-%EC%A3%BC%EC%86%8C%EC%99%80-Classful-Classless
https://jejoonlee.tistory.com/m/173
https://jeonghoon.netlify.app/Network/layer_3_ip/
https://jejoonlee.tistory.com/m/173
https://blog.naver.com/newyks/150182660566
https://m.blog.naver.com/seek316/222652148082
https://minjiwoo.kr/466