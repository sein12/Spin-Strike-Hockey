# Spin Strike Hockey

Spin Strike Hockey는 회전하는 말렛으로 퍽을 치는 Unity 2D 에어하키 게임 프로토타입입니다. 말렛의 초록색 구역은 퍽을 강하게, 빨간색 구역은 약하게 튕기도록 설계했습니다. 퍽이 벽에 부딪힐 때마다 득점에 사용할 점수가 쌓입니다.

![Spin Strike Hockey 경기 화면](https://github.com/user-attachments/assets/1410b678-e120-4c38-ad24-f07225870990)

## 경기 규칙과 조작

1. 경기 타이머는 150초부터 시작합니다.
2. 퍽이 벽에 부딪히면 분홍색으로 표시되는 누적 점수가 1점씩 올라갑니다.
3. 골을 넣은 쪽의 점수에 누적분이 더해집니다. 누적 점수는 0으로 돌아가고 퍽과 말렛이 시작 위치에 놓입니다.
4. 제한 시간이 끝났을 때 흰색으로 표시되는 최종 점수가 높은 쪽이 승리하는 규칙입니다. 현재 씬에서는 타이머와 득점을 표시하며, 자동 경기 종료와 승자 표시는 구현이 남아 있습니다.

`InGame` 씬에서는 말렛을 마우스로 눌러 각자의 구역 안에서 드래그합니다. `InGame 1`의 네트워크 플레이어 프리팹은 방향키와 WASD 입력을 받습니다.

## Unity에서 열기

**환경** · Unity `2022.3.28f1` · C# · Firebase Authentication · Photon PUN 2

```bash
git clone https://github.com/sein12/Spin-Strike-Hockey.git
```

1. Unity Hub에서 복제한 `Spin-Strike-Hockey` 폴더를 프로젝트로 추가하고 `2022.3.28f1` 에디터로 엽니다.
2. 하키 코트를 보려면 `Assets/Scenes/InGame.unity`를 열고 **Play**를 누릅니다.
3. 로그인 화면부터 확인하려면 Firebase 프로젝트에 대상 플랫폼 앱을 등록하고 이메일/비밀번호 로그인을 활성화합니다. 해당 앱의 설정과 테스트 계정을 준비한 뒤 `Assets/Scenes/SignIn.unity`에서 **Play**를 누릅니다. 로그인에 성공하면 `Lobby`로 이동합니다.
4. Photon 연결 코드를 확인하려면 Unity의 **Window → Photon Unity Networking**에서 본인 프로젝트의 PUN App ID를 설정합니다. 로비 스크립트는 Photon에 연결하고, 무작위 방 입장에 실패하면 최대 2인 방을 생성하는 코드를 포함합니다.

위 절차는 소스와 씬 설정을 기준으로 정리했습니다. Unity 플레이와 빌드는 실행 검증이 필요합니다.

## 씬과 주요 코드

| 씬 | 구성 |
| --- | --- |
| [`SignIn`](Assets/Scenes/SignIn.unity) | [`AuthManager`](Assets/Scripts/sein/AuthManager.cs)가 Firebase 이메일·비밀번호 로그인을 처리합니다. |
| [`Lobby`](Assets/Scenes/Lobby.unity) | [`LobbyManager`](Assets/Scripts/sein/LobbyManager.cs)가 Photon 연결 상태와 방 입장 콜백을 처리합니다. |
| [`InGame`](Assets/Scenes/InGame.unity) | [`GameManager.cs`](Assets/Scripts/ju%20hwan/GameManager.cs)와 [`PuckControl`](Assets/Scripts/ju%20hwan/PuckControl.cs)이 타이머, 벽 충돌 점수, 골 판정과 퍽 재생성을 담당합니다. |
| [`InGame 1`](Assets/Scenes/InGame%201.unity) | [`GameManager1.cs`](Assets/Scripts/sein/GameManager1.cs)가 Photon 방의 플레이어 프리팹을 생성하고 [`PlayerController`](Assets/Scripts/sein/PlayerController.cs)가 이동과 위치·속도 전송을 처리합니다. |

### 현재 구현 상태

- 로비의 **Start** 버튼은 `OnJoinedRoom`을 직접 호출해 `InGame`을 여는 구성입니다. `Connect()`의 무작위 매칭 흐름과 `InGame 1`의 네트워크 플레이어 생성은 별도 코드로 구현되어 있습니다.
- `InGame`의 [`DetectPuck`](Assets/Scripts/ju%20hwan/DetectPuck.cs)은 퍽의 상대 방향으로 충돌 구역을 판별합니다. 현재 씬에는 이 컴포넌트의 퍽 참조가 비어 있어 구역별 반발을 확인하려면 연결 작업이 필요합니다.
- [`GameManager.cs`](Assets/Scripts/ju%20hwan/GameManager.cs)와 [`PlayerRotation.cs`](Assets/Scripts/ju%20hwan/PlayerRotation.cs) 등은 파일명과 `MonoBehaviour` 클래스명이 달라 컴포넌트 연결을 점검해야 합니다. `GameManager.cs`의 `UnityEditor.SearchService` 참조도 플레이어 빌드 전에 정리해야 합니다.

## 팀원 및 역할

| 팀원 | 담당 |
| --- | --- |
| 최세인 | 게임 시작 화면 제작, 게임 결과 공유와 멀티플레이를 위한 네트워크 구성 |
| 배주환 | 경기 화면 디자인, 경기 진행 구성, 게임 알고리즘 구현 |
