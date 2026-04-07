# 9장 ROS 2 도구와 CLI 명령어

## 9.1 ROS 도구 개요

ROS를 사용하는 핵심 이유 중 하나는 **로봇 개발에 필요한 다양한 개발 도구**가 있기 때문이다.
ROS 도구는 형태에 따라 4가지로 분류된다.

| 분류 | 설명 |
|---|---|
| Command-Line Tools | CLI 기반, 로봇 접근 및 거의 모든 ROS 기능 조작 |
| RQt | Qt 기반 GUI 프레임워크 |
| RViz | 3차원 시각화 툴 |
| Gazebo | 3차원 시뮬레이터 |

---

## 9.2 ROS 2 CLI 개요

**ROS 2 CLI (Command Line Interface)**: 터미널에서 텍스트를 입력하여 사용하는 명령어.
`ros2cli`라는 이름으로 20여 개가 제공된다.

```bash
ros2 [verbs] [sub-verbs] [options] [arguments]
```

- `ros2` : ros2cli 고유의 entry-point
- `verbs` : `run`, `launch`, `node` 등 특정 툴 선택
- `sub-verbs` : `node info`, `node list` 등 세부 명령
- 탭(tab) 자동완성 지원 → 외우지 않고도 빠른 사용 가능

```bash
# 도움말 확인
$ ros2 -h
$ ros2 node -h
$ ros2 node info -h
```

---

## 9.3 CLI 종류

### 실행 명령어

| 명령어 | 인자 | 기능 |
|---|---|---|
| `ros2 run` | `<package> <executable>` | 특정 패키지의 노드 실행 |
| `ros2 launch` | `<package> <launch-file>` | 런치 파일로 0개~복수개의 노드 실행 |

```bash
$ ros2 run turtlesim turtlesim_node
$ ros2 launch demo_nodes_cpp talker_listener.launch.py
```

---

### 정보 명령어

| 명령어 | 주요 sub-verbs | 기능 |
|---|---|---|
| `ros2 pkg` | `create / list / executables / prefix / xml` | 패키지 생성·조회 |
| `ros2 node` | `info / list` | 노드 정보 조회 |
| `ros2 topic` | `echo / hz / bw / delay / info / list / pub / type / find` | 토픽 조회·발행 |
| `ros2 service` | `call / find / list / type` | 서비스 조회·요청 |
| `ros2 action` | `info / list / send_goal` | 액션 조회·전송 |
| `ros2 interface` | `list / show / package / packages / proto` | 인터페이스 조회 |
| `ros2 param` | `get / set / list / delete / describe / dump` | 파라미터 관리 |
| `ros2 bag` | `record / play / info` | 데이터 기록·재생 |

```bash
$ ros2 topic echo /turtle1/cmd_vel
$ ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 255, b: 255, width: 10}"
$ ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.5708}"
$ ros2 param set /turtlesim background_r 148
$ ros2 bag record /turtle1/cmd_vel
```

> ROS 2 Foxy 이후 `msg`, `srv` 명령어는 사용 불가 → `ros2 interface`로 대체

---

### 기능 보조 명령어

| 명령어 | 기능 |
|---|---|
| `ros2 daemon` | daemon 시작/정지/상태 확인 |
| `ros2 doctor` | ROS 설정·네트워크·패키지 버전·rmw 잠재 문제 확인 |
| `ros2 wtf` | `ros2 doctor`의 alias (WTF: Where's The Fire) |
| `ros2 lifecycle` | 노드 라이프사이클 상태 조회·전환 |
| `ros2 component` | 컴포넌트 노드 실행·관리 |
| `ros2 multicast` | 멀티캐스트 수신·전송 |
| `ros2 security` | 보안키 및 보안 허가 파일 생성·관리 |

```bash
$ ros2 doctor -r
$ ros2 lifecycle list lc_talker
$ ros2 component load /ComponentManager composition composition::Talker
```

---

# 정리

| 형태 | 도구 | 핵심 용도 |
|---|---|---|
| CLI | ros2cli (20여 개) | 노드·토픽·서비스·액션·파라미터 등 전반 제어 |
| GUI | RQt | 그래프, 플롯 등 시각화 |
| 시각화 | RViz | 센서 데이터·로봇 외형 3D 시각화 |
| 시뮬레이터 | Gazebo | 물리 엔진 기반 3D 시뮬레이션 |

ROS 2 CLI는 외워서 사용하는 것이 아닌, **필요할 때 탭 자동완성과 `-h` 옵션을 활용**하여 체득하는 것이 핵심이다.

---

> **📌 Jazzy 업데이트 노트 (공식 문서 기준, 2026.04 확인)**
>
> **`ros2 bag`**: 기본 스토리지 포맷이 **MCAP**으로 변경됨 (SQLite3 → MCAP).
> 서비스 데이터 녹화·재생 기능이 새로 추가됨.
> ```bash
> $ ros2 bag record --service /turtle1/set_pen   # 서비스 데이터 녹화
> $ ros2 bag record --all-services               # 전체 서비스 녹화
> $ ros2 bag play --publish-service-requests bag_file  # 서비스 재생
> $ ros2 bag record --topic_types sensor_msgs/msg/Image  # 타입으로 필터링
> ```
> bag 파일 자체에 메타데이터가 내장되어 별도 YAML 파일 불필요.
>
> **`ros2 action`**: `type` sub-verb 추가.
> ```bash
> $ ros2 action type /turtle1/rotate_absolute  # 액션 타입 바로 확인
> ```
>
> **`ros2 launch`**: `--log-file-name` 인자 추가로 로그 파일명 지정 가능.
>
> **Gazebo**: Jazzy의 권장 시뮬레이터는 **Gazebo Harmonic** (구 Ignition Gazebo).
> 연동 패키지: `gazebo_ros_pkgs` → `gz_*_vendor` + `ros_gz` 패키지로 전환.
