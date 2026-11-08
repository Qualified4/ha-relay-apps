# SmartThings LAN Relay

한국어 · [English](README.en.md)

SmartThings 기기의 상태를 Home Assistant로 전달하고, HA에서 차량·온수매트 제어 명령을 보낼 수 있게 연결하는 앱입니다. **AEB 대신 자체 SmartThings Edge 드라이버와 Rules를 사용합니다.** 실행 중에는 SmartThings OAuth 토큰을 저장하거나 REST API를 반복 조회하지 않습니다.

## 구성

```text
상태: 기기/클라우드 → SmartThings Rules → Edge 허브 → LAN Relay 앱 → MQTT → HA
제어: HA → MQTT → LAN Relay 앱 → Edge 허브 → SmartThings Rules → 기기/클라우드
```

허브와 HA 사이에서 인증된 로컬 HTTP 통신을 사용합니다. 클라우드 기기의 인터넷·SmartThings·차량 서비스 의존성은 남습니다.

## 주요 기능

- 세탁기·차량·온수매트의 단순 값과 중첩 객체·배열 상태 수신, MQTT 자동 발견
- 차량 명령 버튼과 JSON 명령, 카탈로그로 정의하는 공조·잠금·온수매트 전원/좌우 난방 등의 MQTT 제어
- 여러 속성을 묶은 view 문서(예: 전체 전원 + 좌/우 switch)로 실제 난방 상태 해석
- 로컬 공조 온도·작동 시간·김서림 설정 저장. 설정 변경만으로 차량을 작동시키지 않음
- 최신 상태 캐시와 재연결 시 재전송, 중복 명령 방지
- 영구 중계 키 인증, 허브 IP·중계 기기 허용 목록
- Home Assistant Green의 `aarch64`, Intel/AMD HAOS의 `amd64` 지원

## 설치 전 확인

Home Assistant OS 또는 Supervisor 환경, SmartThings Edge 지원 허브, 설정된 Mosquitto 앱과 HA MQTT 통합이 필요합니다.

**앱 설치만으로 연결이 완료되지 않습니다.** 자체 Edge 드라이버 설치와 기기별 Rules·카탈로그 구성이 필요합니다. 구성 도구와 드라이버 소스는 비공개 저장소에서 관리하며, 공개 설치 저장소만으로는 초기 구성을 할 수 없습니다. 현재 검증 범위는 구성된 세탁기·차량·나비엔 온수매트(MateWmFloatDouble)이며 모든 SmartThings 기기의 자동 호환을 보장하지 않습니다.

공개 설치 저장소를 HA의 **설정 → 앱 → 앱 스토어 → 저장소**에 추가하세요.

```text
https://github.com/Qualified4/ha-relay-apps
```

앱을 설치한 뒤 허브 IP, 중계 기기 ID, 인증 키를 설정합니다. 자세한 절차는 [사용 설명서](DOCS.md)를 참고하세요. 공개 이미지를 사용하므로 설치에 GitHub 토큰은 필요하지 않습니다.

## 장애와 보안

허브 heartbeat가 약 90초 동안 없으면 엔티티를 사용할 수 없음으로 표시합니다. 이는 LAN 중계 연결 상태이며, 가전·클라우드가 정상이라는 뜻은 아닙니다. 장애 중 중간 변화는 합쳐질 수 있고 복구 시 최신 상태를 동기화합니다. 제어 시간 초과는 결과 불명으로 취급하며 자동 재시도하지 않습니다.

중계 포트 `18088/tcp`는 내부 LAN 전용입니다. 중계 키는 자동 만료되지 않으며 앱과 Edge 양쪽에서 함께 변경합니다. 앱 백업에는 키와 상태 캐시가 들어가므로 비공개로 보관하세요.

소스 Git 저장소는 비공개지만 공개 컨테이너 안의 실행 코드는 추출할 수 있습니다. 개인 기기 ID·IP·키·상태는 공개 저장소나 이미지에 넣지 않고 로컬 설정과 `/data`에서 관리합니다.
