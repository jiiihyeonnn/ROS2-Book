# 10장 ROS의 종합 GUI 툴 RQt

## 10.1 RQt란?

**RQt** = ROS + Qt의 합성어.
플러그인 형태로 다양한 도구와 인터페이스를 구현할 수 있는 **GUI 프레임워크**이자 ROS의 **종합 GUI 툴박스**이다.

- ROS 1 Fuerte부터 기존의 rxbag, rxplot, rxgraph → `rqt_bag`, `rqt_plot`, `rqt_graph` 등 플러그인 형태로 통합
- Qt 기반 → 크로스 플랫폼(Linux, macOS, Windows), C++ 및 Python(PyQt, PySide) 지원
- ROS 2 Foxy 기준: **14개** 기본 설치 + Robot Tools 2개 추가 설치 가능

---

## 10.2 RQt 설치 및 실행

```bash
# 전체 설치
$ sudo apt install ros-foxy-rqt*
```

### 실행 방법 3가지

| 방법 | 명령어 | 설명 |
|---|---|---|
| 1 (권장) | `$ rqt` | Main Window 실행 후 Plugins 메뉴에서 플러그인 선택 |
| 2 | `$ ros2 run rqt_msg rqt_msg` | 특정 플러그인만 단독 실행 |
| 3 | `$ rqt_graph` / `$ rqt_topic` | 미리 지정된 단축 명령어 사용 |

---

## 10.3 RQt 플러그인 종류 (10카테고리 30여 개)

| 카테고리 | 주요 플러그인 | 기능 |
|---|---|---|
| Actions | Action Type Browser | Action 타입 데이터 구조 확인 |
| Configuration | Dynamic Reconfigure | 노드 파라미터 확인·변경 |
| Introspection | Node Graph, Process Monitor | 노드 관계 그래프, CPU·메모리 사용률 확인 |
| Logging | Console, Logger Level | rosout 메시지(DEBUG/INFO/WARN/ERROR/FATAL) 확인 |
| Miscellaneous | Python Console, Shell | 파이썬 콘솔, 쉘 구동 |
| Robot Tools | Diagnostic Viewer, Robot Steering | 로봇 디바이스 경고·에러, 속도 발행 |
| Services | Service Caller, Service Type Browser | 서비스 요청, 타입 구조 확인 |
| Topics | Message Publisher, Topic Monitor | 토픽 발행, 토픽 목록·정보 확인 |
| Visualization | Image View, Plot, RViz, TF Tree | 카메라 영상, 2D 데이터 플롯, 3D 시각화 |

---

## 10.4 주요 플러그인 사용 예시

### Node Graph
```bash
$ rqt_graph
# 또는 Plugins → Introspection → Node Graph
```
실행 중인 노드들의 관계와 토픽을 그래프로 표시. 노드는 타원, 토픽은 사각형.

---

### Topic Monitor
```
Plugins → Topics → Topic Monitor
```
토픽 목록 및 선택한 토픽의 이름·타입·대역폭·주기·값 확인. `ros2 topic`의 GUI 버전.

---

### Message Publisher
```
Plugins → Topics → Message Publisher
```
특정 토픽을 지정 주기로 발행. `ros2 topic pub`의 GUI 버전.

예: `/turtle1/cmd_vel`, 타입 `geometry_msgs/msg/Twist`, 주기 1Hz → linear.x=2.0, angular.z=1.0

---

### Service Caller
```
Plugins → Services → Service Caller
```
서비스 이름 입력 → Request 값 설정 → Call 버튼으로 서비스 요청. `ros2 service call`의 GUI 버전.

---

### Parameter Reconfigure
```
Plugins → Configuration → Dynamic Reconfigure
```
노드의 파라미터 값 확인·변경. `ros2 param`의 GUI 버전.

---

### Plot
```
Plugins → Visualization → Plot
```
토픽 데이터를 시간축(x)으로 2D 도식화. PyQtGraph(기본) / MatPlot / QwtPlot 선택 가능.
그래프를 png 또는 csv로 저장 가능.

---

### Image View
```
Plugins → Visualization → Image View
```
`sensor_msgs/msg/Image` 타입 토픽을 영상으로 표시.

```bash
$ ros2 run image_tools cam2image
# 카메라 없는 경우:
$ ros2 run image_tools cam2image --ros-args -p burger_mode:=true
```

---

### Console
```
Plugins → Logging → Console
```
모든 노드의 `rosout` 로그를 한 화면에서 확인. 메시지 종류(DEBUG/INFO/WARN/ERROR/FATAL), 노드, 타임스탬프, 위치 표시. Exclude/Highlight 필터 지원.

---

## 10.5 CLI vs GUI 비교

| 기능 | CLI | RQt 플러그인 |
|---|---|---|
| 토픽 조회 | `ros2 topic` | Topic Monitor |
| 토픽 발행 | `ros2 topic pub` | Message Publisher |
| 서비스 요청 | `ros2 service call` | Service Caller |
| 파라미터 변경 | `ros2 param` | Parameter Reconfigure |
| 인터페이스 확인 | `ros2 interface` | Message/Service/Action Type Browser |
| 노드 그래프 | `ros2 node list` | Node Graph |

CLI와 GUI는 같은 기능의 다른 인터페이스. **목적에 따라 편한 것을 선택**하면 된다.

---

# 정리

RQt는 단순한 도구 모음이 아닌 **플러그인 기반 GUI 프레임워크**로,
공통 API를 제공하여 누구나 새로운 RQt 플러그인을 쉽게 개발·통합할 수 있다.
ROS의 핵심 철학인 **재사용성**과 **Re-inventing 방지**를 GUI 도구에서도 실현한 것이다.

---

> **📌 Jazzy 업데이트 노트 (공식 문서 기준, 2026.04 확인)**
>
> **설치 명령어**: `ros-foxy-rqt*` → `ros-jazzy-rqt*`
> ```bash
> $ sudo apt install ros-jazzy-rqt*
> ```
>
> **Service Caller**: 공식 튜토리얼에서 가장 먼저 소개되는 핵심 플러그인으로 강조됨.
> `/spawn` 서비스로 거북이 추가, `/set_pen`으로 펜 색상·굵기 변경 등 실습에 적극 활용.
>
> **Dynamic Reconfigure**: Jazzy에서는 플러그인 이름이 **Parameter Reconfigure**로 통일됨.
> 메뉴 경로: `Plugins → Configuration → Parameter Reconfigure`
>
> **`rqt_msg` / `rqt_srv`**: Jazzy에서 deprecated. `ros2 interface` CLI 또는 **Message Type Browser** 플러그인 사용 권장.
>
> **RViz2 개선**: 트랜스폼 정규식(regex) 필터링, 구독 주기 시각화, DepthCloud·CameraInfo 디스플레이 타입 추가.
