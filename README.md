# SmartThings LAN Relay 앱 저장소

한국어 · [English](README.en.md)

SmartThings와 Home Assistant를 자체 Edge 드라이버·Rules·로컬 MQTT로 연결하는 앱의 설치 저장소입니다. 소스는 별도 비공개 저장소에서 관리하며, 여기에는 설치 정보와 사용 문서만 공개합니다.

## 설치

1. Home Assistant에서 **설정 → 앱 → 앱 스토어 → 저장소**를 엽니다.
2. `https://github.com/Qualified4/ha-relay-apps`를 추가하고 **SmartThings LAN Relay**를 설치합니다.
3. [사용 설명서](smartthings_relay/DOCS.md)에 따라 허브·중계 기기·MQTT를 구성합니다.

`aarch64`와 `amd64`를 지원하며 공개 사전 빌드 이미지를 사용하므로 GitHub 토큰은 필요하지 않습니다.

**앱만 설치해서는 사용할 수 없습니다.** 별도 Edge 드라이버와 기기별 Rules·카탈로그가 필요합니다. 초기 구성 도구는 비공개 소스 저장소에 있으며 모든 SmartThings 기기의 자동 호환을 보장하지 않습니다.

[앱 소개](smartthings_relay/README.md) · [사용 설명서](smartthings_relay/DOCS.md) · [English documentation](smartthings_relay/DOCS.en.md)

실제 허브 IP·기기 ID·인증 키·상태는 로컬 설정에만 보관하세요. 공개 이미지의 실행 코드는 추출할 수 있습니다.
