## API 사용해보기 - NCP Maps
- Naver Cloud는 네이버에서 만든 클라우드 서비스
- 일반적인 클라우트 서비스의 기능에 더해 Naver 지도, Clova등 다양한 기능을 API 형태로 제공
- Naver Cloud Platform에서 제공하는 지도 API를 이용해 사용자의 위치를 기반으로 하는 기능을 만드는 연습을 할 예정
- https://www.ncloud.com/
- 네이버 아이디로 회원가입 진행
- 결제 수단 등록 (첫 등록 시 100000원 크래딧 증정)
  - 등록하지 않으면 대부분의 기능이 막혀있음

## 네이버 지도 API 사용을 위한 AI-NAVER API Application 등록 및 Web Dynamic Map 설정
- https://console.ncloud.com/dashboard
  - 네이버 지도 API는 `Service -> AI Naver API`에서 찾을 수 있음
  - 찾으면 `+ Application 등록` 버튼 클릭
  - 사용할 네이버 API 목록이 나옴
    - Web Dynamic Map: 사용자가 사용할 수 있는, 흔히 보는 네이버 지도를 HTML에 추가하는 API 입니다.
    - Directions 5: 두 지점 사이의 이동 경로를 알려주는 길찾기 API 입니다.
    - Geocoding: 입력한 주소의 좌표를 포함한 상세 정보를 제공하는 API입니다.
    - Reverse Geocoding: 좌표를 기준으로 주소를 알려주는 API 입니다.

- 하단에는 `서비스 환경 등록` 창이 있음
- `Web 서비스 URL`에 `http://localhost:8080`등록
  - Web Dynamic Map에서 허가된 host에서만 지도를 사용할 수 있도록 하기 위함
- `인증정보` 버튼을 누르면
  - API를 쓰기 위한 Client ID와 Client Secret 확인 가능
  - Naver API를 쓰기 위해 반드시 알고 있어야 하는 내용
  - Client ID를 주어진 Skeleton Code의 `/src/main/resources/static/map.html` 8번째 줄의 `<client-id>`항목에 대체
- 코드 실행 후 `http://localhost:8080/map.html` 으로 이동하면 지도 확인 가능

## GeoLocation 사용을 위한 인증키 설정
- GeoLocation은 네이버 지도 API와 별도로 설정하게 되며, 인증을 위해 필요로 하는 API Key (Access Key, Secret Key) 도 다름
- NCP 홈페이지에서 마이페이지 - 인증키 관리 메뉴 확인
- 등록된 API 인증키 목록 확인 가능

## GeoLocation 이용 신청
- GeoLocation은 `Services -> Application Services`에서 확인 가능
- 처음 사용시 서비스 이용 신청을 해야 함
- 약관을 확인하고, 월별 사용 한도를 설정 (수정 가능)
  - 월별 1000번의 호출까지 무료

## 요구 사항
- 주어진 Skeleton 코드의 `com.example.ncpmaps.NaviController`를 확인하면 4개의 Handler Method가 존재
```java
@RestController
@RequestMapping("navigate")
@RequiredArgsConstructor
public class NaviController {

    // 두 좌표를 받아 이동경로를 반환하는 메서드
    @PostMapping("points")
    public NaviRouteDto withPoints(
            @RequestBody
            NaviWithPointsDto dto
    ) {
        throw new ResponseStatusException(HttpStatus.NOT_IMPLEMENTED);
    }

    // 하나의 좌표를 입력받아, 주소를 반환하는 메서드
    @PostMapping("get-address")
    public RGeoResponseDto getAddress(
            @RequestBody
            PointDto point
    ) {
        throw new ResponseStatusException(HttpStatus.NOT_IMPLEMENTED);
    }

    // 하나의 좌표와 주소를 입력받아, 좌표에서
    // 주소검색 결과 위치로의 이동경로를 반환하는 메서드
    @PostMapping("start-query")
    public NaviRouteDto withQuery(
            @RequestBody
            NaviWithQueryDto dto
    ) {
        throw new ResponseStatusException(HttpStatus.NOT_IMPLEMENTED);
    }

    // 두 IP 주소를 입력받아 이동경로를 반환하는 메서드
    @PostMapping("ips")
    public NaviRouteDto withIpAddresses(
            @RequestBody
            NaviWithIpsDto dto
    ) {
        throw new ResponseStatusException(HttpStatus.NOT_IMPLEMENTED);
    }
}
```
- 이들은 `map.html`의 각각의 버튼의 기능을 완성하기 위한 메서드
- 정상적을 동작할 경우 지도에 경로가 표시되거나 `alert`로 주소를 반환하는 기능
---
- 💡 각 컨트롤러에 해당하는 기능을 NCP의 API를 활용하여 완성하는 것이 목표 💡
  - `withPoints`: 두 점의 정보를 입력받아 그 사이의 이동경로를 반환 
    - `**두 점 경로 구하기**` 버튼을 누르면 실행
  - `getAddress`: 하나의 좌표를 입력받아 주소를 반환
    - `**중심점 주소**` 버튼을 누르면 실행되며, 지도의 중심점의 좌표를 전달
  - `withQuery`: 하나의 좌표와 검색 주소를 입력받아 좌표에서 주소 검색 결과까지의 위치로 이동
    - **`중심점에서 좌표까지 (가장 근접한 결과로)`** 버튼을 누르면 실행되며, 그 상단의 `찾는 곳` 입력창의 내용을 주소로 검색
  - `withIpAddresses`: 두 IP 주소를 입력받아 그 사이의 이동경로를 반환
    - `**경로 구하기**` 버튼을 누르면 실행되며, `시작 IP`와 `도착 IP` 입력창의 내용이 전달

## 사용할 API
이 프로젝트에서 활용할 API

### Directions 5
- 출발지와 목적지 간의 경로에 대한 정보를 반환하는 API
- `https://naveropenapi.apigw.ntruss.com/map-direction/v1/driving` 로 요청을 보냄
- [Directions 5 개요](https://api.ncloud-docs.com/docs/ai-naver-mapsdirections)
```java
curl "https://naveropenapi.apigw.ntruss.com/map-direction/v1/driving?start={출발지}&goal={목적지}&option={탐색옵션}" \
	-H "X-NCP-APIGW-API-KEY-ID: {애플리케이션 등록 시 발급받은 client id 값}" \
	-H "X-NCP-APIGW-API-KEY: {애플리케이션 등록 시 발급받은 client secret값}" -v
```
인증을 위해 필요한 Header
- Request Header
  - `X-NCP-APIGW-API-KEY-ID`: Application을 생성할때 발급받은 Client ID
  - `X-NCP-APIGW-API-KEY`: Application을 생성할때 발급받은 Client Secret

    
- Query Parameters
  - `start`: 경도와 위도를 `,` 로 연결한 시작지점을 나타내는 좌표값 (예시: `127.12345,37.12345`)
  - `goal`: 경도와 위도를 `,` 로 연결한 도착지점을 나타내는 좌표값

### Geocoding
- 주소 검색어(지번, 도로명)을 입력받아, 상세 주소와 좌표를 응답하는 API
- https://api.ncloud-docs.com/docs/ai-naver-mapsgeocoding-geocode
```java
curl -G "https://naveropenapi.apigw.ntruss.com/map-geocode/v2/geocode" \
    --data-urlencode "query={주소}" \
    --data-urlencode "coordinate={검색_중심_좌표}" \
    -H "X-NCP-APIGW-API-KEY-ID: {애플리케이션 등록 시 발급받은 client id값}" \
    -H "X-NCP-APIGW-API-KEY: {애플리케이션 등록 시 발급받은 client secret값}" -v
```
인증을 위해 필요한 Header
- Request Header
  - `X-NCP-APIGW-API-KEY-ID`: Application을 생성할때 발급받은 Client ID
  - `X-NCP-APIGW-API-KEY`: Application을 생성할때 발급받은 Client Secret

  
- Query Paremters
  - `query`: 검색 주소

### Reverse Geocoding
- 좌표를 입력받아, 주소 정보로 반환하는 API
- https://api.ncloud-docs.com/docs/ai-naver-mapsreversegeocoding-gc
```java
curl "https://naveropenapi.apigw.ntruss.com/map-reversegeocode/v2/gc?coords={입력_좌표}&sourcecrs={좌표계}&orders={변환_작업_이름}&output={출력_형식}" \
	-H "X-NCP-APIGW-API-KEY-ID: {애플리케이션 등록 시 발급받은 client id값}" \
	-H "X-NCP-APIGW-API-KEY: {애플리케이션 등록 시 발급받은 client secret값}" -v
```
인증을 위해 필요한 Header
- Request Header
    - `X-NCP-APIGW-API-KEY-ID`: Application을 생성할때 발급받은 Client ID
    - `X-NCP-APIGW-API-KEY`: Application을 생성할때 발급받은 Client Secret


- Query Paremters
  - `coords`: 경도와 위도를 `,` 로 연결한, 주소를 알고싶은 좌표 (예시: `127.12345,37.12345`)
  - `output`: 출력 형식을 정의하는 값으로, `xml` 또는 `json`을 선택 가능 
    - 요청에 필수적이진 않지만, JSON을 활용함으로 `json`을 지정
  

### GeoLocation
- IP를 입력받아 위치정보를 반환하는 API
- https://api.ncloud-docs.com/docs/ai-application-service-geolocation-geolocation


인증을 위해 필요한 Header
- Request Header
  - `x-ncp-apigw-timestamp`: 요청을 보낸 시점의 Unix Time (1970.01.01 00:00:00 부터 지금까지 흐른 밀리초)
  - `x-ncp-iam-access-key`: 인증키 관리에서 발급받은 Access Key
  - `x-ncp-apigw-signature-v2`: 인증키 관리에서 Access Key와 Secret Key를 포함해 만드는 시그니처
  - 시그니처는 요청할 떄 시간 정보를 활용하여 생성해주어야 함 (https://api.ncloud-docs.com/docs/common-ncpapi)


Query Parameters

- `ip`: 지역정보를 알고싶은 IP
- `ext`: 추가정보 포함 여부를 `t` (포함), `f` (비포함)으로 지정
  - 좌표 정보를 활용하고 싶다면, `t`를 사용
- `responseFormatType`: 출력 형식을 정의하는 값으로, `xml` 또는 `json`을 선택 가능
  - 요청에 필수적이진 않지만, JSON을 활용함으로 `json`을 지정