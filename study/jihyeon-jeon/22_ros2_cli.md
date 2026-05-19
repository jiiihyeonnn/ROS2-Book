# 2장 ROS 2 CLI

## 2.1 ROS 2 CLI

ROS 2 CLI의 기본 개념과 종류는 이전 챕터(9장 ROS 2 도구와 CLI 명령어)에서 다루었다. 이 장에서는 실습 예제를 중심으로 각 명령어의 동작을 확인한다.

```bash
ros2 [verbs] [sub-verbs] [options] [arguments]
```

### 실행 명령어

| ros2cli + verbs | arguments | 기능 |
|---|---|---|
| `ros2 run` | `<package> <executable>` | 특정 패키지의 특정 노드 실행 (executable에 따라 복수 노드 가능) |
| `ros2 launch` | `<package> <launch-file>` | 특정 패키지의 런치 파일 실행 (0개~복수개의 노드) |

### 정보 명령어

| ros2cli + verbs | sub-verbs | 기능 |
|---|---|---|
| `ros2 pkg` | `create` | 새로운 ROS 2 패키지 생성 |
| | `executables` | 지정 패키지의 실행 파일 목록 출력 |
| | `list` | 사용 가능한 패키지 목록 출력 |
| | `prefix` | 지정 패키지의 저장 위치 출력 |
| | `xml` | 지정 패키지의 패키지 정보 파일(xml) 출력 |
| `ros2 node` | `info` | 실행 중인 노드 중 지정한 노드의 정보 출력 |
| | `list` | 실행 중인 모든 노드의 목록 출력 |
| `ros2 topic` | `bw` | 지정 토픽의 대역폭 측정 |
| | `delay` | 지정 토픽의 지연시간 측정 |
| | `echo` | 지정 토픽의 데이터 출력 |
| | `find` | 지정 타입을 사용하는 토픽 이름 출력 |
| | `hz` | 지정 토픽의 주기 측정 |
| | `info` | 지정 토픽의 정보 출력 |
| | `list` | 사용 가능한 토픽 목록 출력 |
| | `pub` | 지정 토픽의 토픽 발행 |
| | `type` | 지정 토픽의 토픽 타입 출력 |
| `ros2 service` | `call` | 지정 서비스의 서비스 요청 전달 |
| | `find` | 지정 서비스 타입의 서비스 출력 |
| | `list` | 사용 가능한 서비스 목록 출력 |
| | `type` | 지정 서비스의 타입 출력 |
| `ros2 action` | `info` | 지정 액션의 정보 출력 |
| | `list` | 사용 가능한 액션 목록 출력 |
| | `send_goal` | 지정 액션의 액션 목표 전송 |
| `ros2 interface` | `list` | 사용 가능한 모든 인터페이스 목록 출력 |
| | `package` | 특정 패키지에서 사용 가능한 인터페이스 목록 출력 |
| | `packages` | 인터페이스 패키지들의 목록 출력 |
| | `proto` | 지정 패키지의 프로토타입 출력 |
| | `show` | 지정 인터페이스의 형태 출력 |
| `ros2 param` | `delete` | 지정 파라미터 삭제 |
| | `describe` | 지정 파라미터 정보 출력 |
| | `dump` | 지정 파라미터 저장 |
| | `get` | 지정 파라미터 읽기 |
| | `list` | 사용 가능한 파라미터 목록 출력 |
| | `set` | 지정 파라미터 쓰기 |
| `ros2 bag` | `info` | 저장된 rosbag 정보 출력 |
| | `play` | rosbag 재생 |
| | `record` | rosbag 기록 |

### 기능 보조 명령어

| ros2cli + verbs | sub-verbs / options | 기능 |
|---|---|---|
| `ros2 extensions` | `-a`, `-v` | ros2cli의 extension 목록 출력 |
| `ros2 extension_points` | `-a`, `-v` | ros2cli의 extension point 목록 출력 |
| `ros2 daemon` | `start` / `status` / `stop` | daemon 시작 / 상태 확인 / 정지 |
| `ros2 multicast` | `receive` / `send` | multicast 수신 / 전송 |
| `ros2 doctor` | `hello`, `-r`, `-rf`, `-iw` | ROS 설정·네트워크·패키지 버전·rmw 잠재 문제 확인 |
| `ros2 wtf` | (doctor와 동일) | ros2 doctor의 alias (WTF: Where's The Fire) |
| `ros2 lifecycle` | `get` / `list` / `nodes` / `set` | 라이프사이클 정보 출력 / 상태천이 목록 / 노드 목록 / 상태 전환 |
| `ros2 component` | `list` / `load` / `standalone` / `types` / `unload` | 컨테이너·컴포넌트 목록 / 실행 / 중지 |
| `ros2 security` | `create_key` / `create_keystore` / `create_permission` / `generate_artifacts` / `generate_policy` / `list_keys` | 보안키·저장소·허가 파일 생성 및 목록 출력 |

---

## 2.2 ROS 2 CLI 실습

> 별도 언급이 없는 한 `turtlesim_node`와 `turtle_teleop_key` 노드를 실행 중인 상태에서의 결과이다.

```bash
$ ros2 run turtlesim turtlesim_node
$ ros2 run turtlesim turtle_teleop_key
```

---

### 01) ros2 run

특정 패키지의 특정 노드를 실행한다. executable에 따라 복수 노드도 실행 가능하다.

```bash
$ ros2 run turtlesim turtlesim_node
$ ros2 run turtlesim turtle_teleop_key
```

---

### 02) ros2 launch

런치 파일을 실행하여 0개~복수개의 노드를 한 번에 실행한다. 다른 패키지의 런치 파일을 내부에서 불러오는 것도 가능하다.

```bash
$ ros2 launch demo_nodes_cpp talker_listener.launch.py
[INFO] [launch]: All log files can be found below /home/robot/.ros/log/...
[INFO] [talker-1]: process started with pid [4184]
[INFO] [listener-2]: process started with pid [4186]
[talker-1] [INFO]: Publishing: 'Hello World: 1'
[listener-2] [INFO]: I heard: [Hello World: 1]
```

---

### 03) ros2 pkg

패키지 정보를 확인하거나 새 패키지를 생성한다.

```bash
# 새 패키지 생성
$ ros2 pkg create my_first_ros_rclpy_pkg --build-type ament_python --dependencies rclpy std_msgs

# 패키지 내 실행 파일 목록 확인
$ ros2 pkg executables turtlesim
turtlesim draw_square
turtlesim mimic
turtlesim turtle_teleop_key
turtlesim turtlesim_node

# 설치된 모든 패키지 목록 확인
$ ros2 pkg list
action_msgs
action_tutorials_cpp
...

# 패키지 저장 위치 확인
$ ros2 pkg prefix turtlesim
/opt/ros/foxy

# package.xml 내용 확인
$ ros2 pkg xml turtlesim
<package format="3">
  <name>turtlesim</name>
  <version>1.2.5</version>
  ...
```

---

### 04) ros2 node

실행 중인 노드의 정보를 조회한다.

```bash
# 실행 중인 모든 노드 목록
$ ros2 node list
/teleop_turtle
/turtlesim

# 특정 노드의 상세 정보 (구독/발행 토픽, 서비스 등)
$ ros2 node info /turtlesim
/turtlesim
  Subscribers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /turtle1/cmd_vel: geometry_msgs/msg/Twist
  Publishers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /rosout: rcl_interfaces/msg/Log
  ...
```

---

### 05) ros2 topic

토픽의 구성, 대역폭, 지연시간, 인터페이스 형태 등의 정보를 조회하거나 직접 발행·수신한다.

> `delay`는 Header 인터페이스를 포함한 토픽에서만 사용 가능하다.

```bash
# 대역폭 측정
$ ros2 topic bw /turtle1/cmd_vel
1.73 KB/s from 100 messages
    Message size mean: 0.05 KB min: 0.05 KB max: 0.05 KB

# 지연시간 측정 (Header 포함 토픽)
$ ros2 topic delay /image
average delay: xxx
    min: xxxs max: xxxs std dev: xxxs window: 1090

# 토픽 데이터 출력
$ ros2 topic echo /turtle1/cmd_vel
linear:
  x: 1.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0

# 특정 인터페이스 타입을 사용하는 토픽 이름 조회
$ ros2 topic find geometry_msgs/msg/Twist
/turtle1/cmd_vel

# 토픽 발행 주기 측정
$ ros2 topic hz /turtle1/cmd_vel
average rate: 33.212
min: 0.029s max: 0.089s std dev: 0.00126s window: 2483

# 토픽 정보 조회 (타입, 발행/구독 수)
$ ros2 topic info /turtle1/cmd_vel
Type: geometry_msgs/msg/Twist
Publisher count: 1
Subscriber count: 1

# 토픽 목록 확인
$ ros2 topic list
/parameter_events
/rosout
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose

# 토픽 목록 + 인터페이스 타입 함께 확인
$ ros2 topic list -t
/parameter_events [rcl_interfaces/msg/ParameterEvent]
/rosout [rcl_interfaces/msg/Log]
/turtle1/cmd_vel [geometry_msgs/msg/Twist]
/turtle1/color_sensor [turtlesim/msg/Color]
/turtle1/pose [turtlesim/msg/Pose]

# 토픽 한 번 발행 (테스트용)
$ ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist \
  "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"

# 토픽 1Hz 주기로 계속 발행
$ ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist \
  "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"

# 토픽의 인터페이스 타입 확인
$ ros2 topic type /turtle1/cmd_vel
geometry_msgs/msg/Twist
```

---

### 06) ros2 service

서비스의 정보를 조회하거나 직접 서비스를 요청해볼 수 있다.

```bash
# 서비스 요청 (call)
$ ros2 service call /turtle1/set_pen turtlesim/srv/SetPen \
  "{r: 255, g: 255, b: 255, width: 10}"
requester: making request: turtlesim.srv.SetPen_Request(r=255, g=255, b=255, width=10, off=0)
response:
turtlesim.srv.SetPen_Response()

# 특정 인터페이스 타입의 서비스 이름 조회
$ ros2 service find std_srvs/srv/Empty
/clear
/reset

# 서비스 목록 확인
$ ros2 service list
/clear
/kill
/reset
/spawn
/turtle1/set_pen
/turtle1/teleport_absolute
/turtle1/teleport_relative

# 서비스 목록 + 인터페이스 타입 함께 확인
$ ros2 service list -t
/clear [std_srvs/srv/Empty]
/kill [turtlesim/srv/Kill]
/reset [std_srvs/srv/Empty]
/spawn [turtlesim/srv/Spawn]
/turtle1/set_pen [turtlesim/srv/SetPen]
/turtle1/teleport_absolute [turtlesim/srv/TeleportAbsolute]
/turtle1/teleport_relative [turtlesim/srv/TeleportRelative]

# 서비스의 인터페이스 타입 확인
$ ros2 service type /clear
std_srvs/srv/Empty
```

---

### 07) ros2 action

액션의 정보를 조회하거나 직접 액션 목표를 전달할 수 있다.

```bash
# 액션 서버/클라이언트 정보 확인
$ ros2 action info /turtle1/rotate_absolute
Action: /turtle1/rotate_absolute
Action clients: 1
    /teleop_turtle
Action servers: 1
    /turtlesim

# 액션 목록 확인
$ ros2 action list
/turtle1/rotate_absolute

# 액션 목록 + 인터페이스 타입 함께 확인
$ ros2 action list -t
/turtle1/rotate_absolute [turtlesim/action/RotateAbsolute]

# 액션 목표 전달
$ ros2 action send_goal /turtle1/rotate_absolute \
  turtlesim/action/RotateAbsolute "{theta: 1.5708}"
Waiting for an action server to become available...
Sending goal:
     theta: 1.5708

Goal accepted with ID: b991078e96324fc994752b01bc896f49

Result:
    delta: -1.5520002841949463

Goal finished with status: SUCCEEDED
```

---

### 08) ros2 interface

토픽·서비스·액션에서 사용하는 인터페이스의 정보를 조회한다.

```bash
# 전체 인터페이스 목록 (msg, srv, action)
$ ros2 interface list
Messages:
    action_msgs/msg/GoalInfo
    ...
Services:
    action_msgs/srv/CancelGoal
    ...
Actions:
    action_tutorials_interfaces/action/Fibonacci
    ...

# 특정 패키지의 인터페이스 목록 확인
$ ros2 interface package turtlesim
turtlesim/srv/TeleportAbsolute
turtlesim/srv/SetPen
turtlesim/msg/Color
turtlesim/action/RotateAbsolute
turtlesim/msg/Pose
turtlesim/srv/Spawn
turtlesim/srv/TeleportRelative
turtlesim/srv/Kill

# 인터페이스를 담고 있는 패키지 목록
$ ros2 interface packages
action_msgs
action_tutorials_interfaces
...

# 인터페이스의 기본 형태(프로토타입) 확인
$ ros2 interface proto geometry_msgs/msg/Twist
"linear:
  x: 0.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0
"

# 인터페이스 구조 확인
$ ros2 interface show geometry_msgs/msg/Twist
Vector3 linear
Vector3 angular

$ ros2 interface show geometry_msgs/msg/Vector3
float64 x
float64 y
float64 z
```

---

### 09) ros2 param

파라미터를 읽고 쓰거나 삭제·저장하는 명령어이다.

```bash
# 파라미터 삭제
$ ros2 param delete /turtlesim background_b
Deleted parameter successfully

# 파라미터 상세 정보 확인
$ ros2 param describe /turtlesim background_b
Parameter name: background_b
  Type: integer
  Description: Blue channel of the background color
  Constraints:
    Min value: 0
    Max value: 255
    Step: 1

# 파라미터를 yaml 파일로 저장
$ ros2 param dump /turtlesim
Saving to: ./turtlesim.yaml

# 파라미터 값 읽기
$ ros2 param get /turtlesim background_g
Integer value is: 86

# 모든 파라미터 목록 확인
$ ros2 param list
/teleop_turtle:
  scale_angular
  scale_linear
  use_sim_time
/turtlesim:
  background_b
  background_g
  background_r
  use_sim_time

# 파라미터 값 설정
$ ros2 param set /turtlesim background_r 148
Set parameter successful
```

---

### 10) ros2 bag

토픽을 파일로 저장하거나 재생하는 명령어이다.

```bash
# 특정 토픽 저장
$ ros2 bag record /turtle1/cmd_vel
[INFO]: Opened database 'rosbag2_2021_01_25-08_03_24/..._0.db3' for READ_WRITE.
[INFO]: Subscribed to topic '/turtle1/cmd_vel'

# 모든 토픽 저장
$ ros2 bag record -a
[INFO]: Subscribed to topic '/turtle1/cmd_vel'
[INFO]: Subscribed to topic '/rosout'
...

# 저장된 rosbag 파일 정보 확인
$ ros2 bag info rosbag2_2021_01_25-08_03_24
Files:             rosbag2_2021_01_25-08_03_24_0.db3
Bag size:          16.5 KiB
Storage id:        sqlite3
Duration:          3.545s
Start:             Jan 25 2021 08:03:26.399
End:               Jan 25 2021 08:03:29.944
Messages:          32
Topic information: Topic: /turtle1/cmd_vel | Type: geometry_msgs/msg/Twist | Count: 32

# rosbag 재생
$ ros2 bag play rosbag2_2021_01_25-08_03_24
[INFO]: Opened database '..._0.db3' for READ_ONLY.
```

---

### 11) ros2 extensions

> ros2cli 개발용으로 일반적으로 잘 사용되지 않는다.

```bash
$ ros2 extensions
ros2action.verb
  info: Print information about an action
  list: Output a list of action names
  send_goal: Send an action goal
ros2bag.verb
  info: Print information about a bag to the screen
  play: Play back ROS data from a bag
  record: Record ROS data to a bag
...
```

---

### 12) ros2 extension_points

> ros2cli 개발용으로 일반적으로 잘 사용되지 않는다.

```bash
$ ros2 extension_points
ros2action.verb: The extension point for 'action' verb extensions
ros2bag.verb: The extension point for 'bag' verb extensions
ros2cli.command: The extension point for 'command' extensions
...
```

---

### 13) ros2 daemon

ROS 2는 DDS/RTPS(Real-Time Publish Subscribe — DDS의 하위 통신 프로토콜) 기반의 분산 발견 프로세스(중앙 서버 없이 네트워크의 노드들이 서로를 자동으로 탐색하는 메커니즘)를 사용하므로 노드를 검색하는 데 시간이 걸릴 수 있다. daemon(백그라운드에서 지속적으로 실행되며 요청을 처리하는 서비스 프로세스)은 ROS 그래프 정보를 캐싱(자주 쓰는 정보를 미리 저장해 빠르게 응답하는 기법)하여 ros2cli 툴의 응답 속도를 높인다. `ros2 topic list` 등 ros2cli 명령어를 실행하면 자동으로 시작된다.

> 열악한 네트워크 환경에서 daemon이 실행되면 통신 상태가 나빠질 수 있다.

```bash
$ ros2 daemon start
The daemon has been started  (또는 The daemon is already running)

$ ros2 daemon status
The daemon is running  (또는 The daemon is not running)

$ ros2 daemon stop
The daemon has been stopped  (또는 The daemon is not running)
```

---

### 14) ros2 multicast

ROS 2 DDS 테스트용 명령어로 UDP 멀티캐스트(하나의 송신자가 특정 그룹의 수신자 전체에게 동시에 패킷을 전송하는 방식) 송수신을 테스트할 때 사용한다.

```bash
# 수신 대기
$ ros2 multicast receive
Waiting for UDP multicast datagram...
Received from 192.168.1.10:35134: 'Hello World!'

# 패킷 전송
$ ros2 multicast send
Sending one UDP multicast datagram...
```

---

### 15) ros2 doctor

ROS 2 개발 환경의 잠재적 문제(설정, 네트워크, 패키지 버전, rmw 미들웨어 등)를 확인한다.

| 옵션 | 의미 | 동작 |
|---|---|---|
| `hello` | 네트워크 연결 확인 | 멀티캐스트 통신 테스트 |
| `-r` | report | 체크한 모든 항목 출력 |
| `-rf` | report-fail | 체크 실패 항목만 출력 |
| `-iw` | include-warnings | 경고 항목 포함 출력 |

```bash
# 네트워크 연결 확인
$ ros2 doctor hello
MULTIMACHINE COMMUNICATION SUMMARY
Topic: /canyouhearme, Published Msg Count: 10
...

# 전체 보고서 출력
$ ros2 doctor -r
   NETWORK CONFIGURATION
...
   PLATFORM INFORMATION
system           : Linux
...
   RMW MIDDLEWARE
middleware name    : rmw_fastrtps_cpp
...
   ROS 2 INFORMATION
distribution name      : foxy
...

# 실패 항목만 출력
$ ros2 doctor -rf
.../topic.py: 56: UserWarning: Subscriber without publisher detected on /turtle1/cmd_vel.
...
All 3 checks passed

# 경고 항목 포함 출력
$ ros2 doctor -iw
...
1/3 check(s) failed
Failed modules: topic
```

---

### 16) ros2 wtf

`ros2 doctor`의 alias이다. WTF는 **Where's The Fire**의 약자이며 사용 방법은 `ros2 doctor`와 완전히 동일하다.

---

### 17) ros2 lifecycle

ROS 2에서 새롭게 도입된 lifecycle은 노드의 상태를 정밀하게 제어하는 개념이다.

**노드의 4가지 상태**: Unconfigured → Inactive → Active → Finalized

> 하기 예제는 `ros2 run lifecycle lifecycle_talker` 실행 후의 결과이다.

```bash
# lifecycle 상태 확인
$ ros2 lifecycle get
/lc_talker: unconfigured [1]

# 가능한 상태 천이 목록 확인
$ ros2 lifecycle list /lc_talker
- configure [1]
    Start: unconfigured
    Goal: configuring
- shutdown [5]
    Start: unconfigured
    Goal: shuttingdown

# lifecycle 노드 목록 확인
$ ros2 lifecycle nodes
/lc_talker

# 상태 천이 트리거
$ ros2 lifecycle set /lc_talker configure
Transitioning successful
```

---

### 18) ros2 component

컴포넌트는 노드를 공유 라이브러리(`.so` / `.dll` 파일 — 여러 프로세스에서 동시에 로드해 사용할 수 있는 코드 모음) 형태로 컨테이너(컴포넌트를 실행하는 그릇 역할의 노드 프로세스)에 동적으로 로드하는 ROS 2 기능이다.

> 하기 예제는 `ros2 launch composition composition_demo.launch.py` 실행 후의 결과이다.

```bash
# 실행 중인 컨테이너와 컴포넌트 목록
$ ros2 component list
/my_container
  1  /talker
  2  /listener

# 컨테이너에 컴포넌트 추가 실행
$ ros2 component load /my_container composition composition::Talker
Loaded component 3 into '/my_container' container node as '/talker'

# 표준 컨테이너로 컴포넌트 단독 실행
$ ros2 component standalone composition composition::Talker
[INFO]: Publishing: 'Hello World: 1'
[INFO]: Publishing: 'Hello World: 2'
...

# 사용 가능한 컴포넌트 목록 확인
$ ros2 component types
teleop_twist_joy
  teleop_twist_joy::TeleopTwistJoy
logging_demo
  logging_demo::LoggerConfig
  logging_demo::LoggerUsage
...

# 컴포넌트 실행 중지
$ ros2 component unload /my_container 1
Unloaded component 1 from '/my_container' container node
```

---

### 19) ros2 security

SROS2 유틸리티로 DDS-Security를 ROS 2에서 사용하기 위한 보안 관련 명령어이다.

```bash
# 보안키 저장소 생성
$ ros2 security create_keystore demo_keys
creating keystore: demo_keys
creating new CA key/cert pair
...

# 보안키 생성
$ ros2 security create_key demo_keys /talker_listener/talker
$ ros2 security create_key demo_keys /talker_listener/listener

# 보안 허가 파일 생성
$ ros2 security create_permission demo_keys /talker_listener/talker policies/sample.policy.xml
$ ros2 security create_permission demo_keys /talker_listener/listener policies/sample.policy.xml

# 보안 정책 파일 생성 (대상 노드가 실행 중이어야 함)
$ ros2 security generate_policy ./test.policy.xml

# 정책 파일로 보안키 및 허가 파일 일괄 생성
$ ros2 security generate_artifacts -k demo_keys -p test.policy.xml

# 보안키 목록 출력
$ ros2 security list_keys demo_keys
```

---

### 20) ros2 env (신규 CLI 예시)

`ros2 env`는 기본 제공 명령어가 아닌 사용자가 직접 작성한 커스텀 CLI 예시이다. (하기 2.5절에서 제작 방법 설명)

```bash
$ ros2 env list -a
ROS_VERSION        = 2
ROS_DISTRO         = foxy
ROS_PYTHON_VERSION = 3
ROS_DOMAIN_ID      = 7
RMW_IMPLEMENTATION = rmw_fastrtps_cpp
```

---

## 2.3 ROS 2 CLI의 빠른 실행

자주 사용하는 ROS 2 CLI 명령어를 `~/.bashrc`에 `alias`로 등록하면 빠르게 실행할 수 있다.

> `alias`는 명령어를 다른 문자열로 치환하는 Unix 셸 기능이다.

```bash
# ~/.bashrc에 추가

alias tn='ros2 run turtlesim turtlesim_node'
alias tt='ros2 run turtlesim turtle_teleop_key'

alias testpub='ros2 run demo_nodes_cpp talker'
alias testsub='ros2 run demo_nodes_cpp listener'
alias testpubimg='ros2 run image_tools cam2image'
alias testsubimg='ros2 run image_tools showimage'

alias rt='ros2 topic list'
alias re='ros2 topic echo'
alias rn='ros2 node list'
```

**사용 예시**

```bash
$ rt
/parameter_events
/rosout
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose

$ re /turtle1/pose
x: 5.544444561004639
y: 5.544444561004639
theta: 0.0
linear_velocity: 0.0
angular_velocity: 0.0
```

---

## 2.4 CLI 명령어에서 ROS arguments 사용하기

`ros2 run` 또는 `ros2 launch` 실행 시 `--ros-args` 옵션을 사용하여 ROS arguments를 함께 전달할 수 있다.

```bash
ros2 run <package> <node> --ros-args [ROS arguments]
```

**자주 사용하는 ROS arguments**

| argument | 기능 |
|---|---|
| `-r __ns:=<namespace>` | 네임스페이스(토픽·노드 이름 앞에 붙는 경로 접두어 — `/robot1/cmd_vel`에서 `/robot1`이 네임스페이스) 설정 |
| `-r __node:=<name>` | 노드 이름 변경 |
| `-r <원래이름>:=<바꿀이름>` | 토픽/서비스/액션 이름 재매핑(실행 시점에 이름을 다른 이름으로 교체하는 기능 — 코드 수정 없이 연결 대상을 바꿀 수 있음) |
| `-p <파라미터>:=<값>` | 파라미터 값 설정 |
| `--params-file <파일>` | yaml 파라미터 파일 적용 |

**예제**

```bash
# 네임스페이스, 노드 이름, 토픽 이름을 모두 변경하여 실행
$ ros2 run demo_nodes_cpp talker --ros-args \
  -r __ns:=/demo \
  -r __node:=my_talker \
  -r chatter:=my_topic

# 네임스페이스, 노드명, 토픽 재매핑, 파라미터 변경을 한 번에 적용
$ ros2 run turtlesim turtlesim_node --ros-args \
  -r __ns:=/tutorial \
  -r __node:=my_turtle \
  -r turtle1/cmd_vel:=cmd_vel \
  -p background_b:=50

# 파라미터를 yaml 파일로 저장 후 로드하여 실행
$ ros2 param dump /turtlesim
Saving to:  ./turtlesim.yaml

$ cat turtlesim.yaml
turtlesim:
  ros__parameters:
    background_b: 50
    background_g: 86
    background_r: 69
    use_sim_time: false

$ ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml
```

---

## 2.5 신규 ROS 2 CLI 작성 방법

ros2cli는 기본 명령어 외에도 사용자가 원하는 명령어를 추가할 수 있다. 여기서는 `ros2 env`라는 신규 CLI를 예제로 작성 방법을 설명한다. `ros2 env`는 ROS 관련 환경 변수를 읽고 쓰는 명령어이다.

### 패키지 구조

`ros2env` 패키지는 일반적인 ROS 2 파이썬 패키지 형태이며, `ros2cli`의 확장 구조를 따른다.

```
ros2env/
├── ros2env/
│   ├── api/
│   │   └── __init__.py    # 실제 실행 로직
│   ├── command/
│   │   └── env.py         # 'ros2 env' 진입점
│   └── verb/
│       ├── list.py        # 'ros2 env list' verb
│       └── set.py         # 'ros2 env set' verb
└── setup.py
```

### setup.py — entry_points 설정

`entry_points`(Python 패키지가 외부에서 호출 가능한 진입점을 플러그인 형태로 등록하는 메커니즘)를 통해 `ros2 env`, `ros2 env list`, `ros2 env set` 명령어로 등록된다.

```python
entry_points={
    'ros2cli.command': [
        'env = ros2env.command.env:EnvCommand',
    ],
    'ros2cli.extension_point': [
        'ros2env.verb = ros2env.verb:VerbExtension',
    ],
    'ros2env.verb': [
        'list = ros2env.verb.list:ListVerb',
        'set = ros2env.verb.set:SetVerb',
    ],
},
```

### command/env.py — 진입점

`CommandExtension`을 상속받아 `ros2 env` 명령어로 동작하도록 한다.

```python
from ros2cli.command import add_subparsers_on_demand
from ros2cli.command import CommandExtension


class EnvCommand(CommandExtension):
    """Various env related sub-commands."""

    def add_arguments(self, parser, cli_name):
        self._subparser = parser
        add_subparsers_on_demand(
            parser, cli_name, '_verb', 'ros2env.verb', required=False)

    def main(self, *, parser, args):
        if not hasattr(args, '_verb'):
            self._subparser.print_help()
            return 0
        extension = getattr(args, '_verb')
        return extension.main(args=args)
```

### verb/list.py — sub-verb 구현

옵션(`-a`, `-r`, `-d`)에 따라 서로 다른 API를 호출한다.

```python
from ros2env.api import get_all_env_list
from ros2env.api import get_dds_env_list
from ros2env.api import get_ros_env_list
from ros2env.verb import VerbExtension


class ListVerb(VerbExtension):
    """Output a list of ROS environment variables."""

    def add_arguments(self, parser, cli_name):
        parser.add_argument('-a', '--all', action='store_true',
            help='Display all environment variables.')
        parser.add_argument('-r', '--ros-env', action='store_true',
            help='Display the ROS environment variables.')
        parser.add_argument('-d', '--dds-env', action='store_true',
            help='Display the DDS environment variables.')

    def main(self, *, args):
        if args.ros_env:
            message = get_ros_env_list()
        elif args.dds_env:
            message = get_dds_env_list()
        else:
            message = get_all_env_list()
        print(message)
```

### api/\_\_init\_\_.py — 실제 실행 로직

환경 변수를 읽어 문자열로 반환한다.

```python
import os


def get_ros_env_list():
    ros_version = os.getenv('ROS_VERSION', 'None')
    ros_distro = os.getenv('ROS_DISTRO', 'None')
    ros_python_version = os.getenv('ROS_PYTHON_VERSION', 'None')
    return 'ROS_VERSION        = {0}\nROS_DISTRO         = {1}\nROS_PYTHON_VERSION = {2}\n'.format(
        ros_version, ros_distro, ros_python_version)


def get_dds_env_list():
    ros_domain_id = os.getenv('ROS_DOMAIN_ID', 'None')
    rmw_implementation = os.getenv('RMW_IMPLEMENTATION', 'None')
    return 'ROS_DOMAIN_ID      = {0}\nRMW_IMPLEMENTATION = {1}\n'.format(
        ros_domain_id, rmw_implementation)


def get_all_env_list():
    return get_ros_env_list() + get_dds_env_list()


def set_ros_env(env_name, env_value):
    os.environ[env_name] = env_value
    value = os.getenv(env_name, 'None')
    return '{0} = {1}'.format(env_name, value)
```

### 실행 결과

```bash
$ ros2 env list -a
ROS_VERSION        = 2
ROS_DISTRO         = foxy
ROS_PYTHON_VERSION = 3
ROS_DOMAIN_ID      = 7
RMW_IMPLEMENTATION = rmw_fastrtps_cpp

$ ros2 env list -r
ROS_VERSION        = 2
ROS_DISTRO         = foxy
ROS_PYTHON_VERSION = 3

$ ros2 env list -d
ROS_DOMAIN_ID      = 7
RMW_IMPLEMENTATION = rmw_fastrtps_cpp
```

ros2cli는 기본 제공 명령어 외에도 사용자가 원하는 명령어를 자유롭게 추가할 수 있다. 추가적인 커스텀 CLI 제작 시 [ros2cli 리포지토리](https://github.com/ros2/ros2cli)를 참고하자.

---

## 정리

| 명령어 | 주요 용도 |
|---|---|
| `ros2 run` / `launch` | 노드 및 런치 파일 실행 |
| `ros2 node` | 노드 목록·정보 조회 |
| `ros2 topic` | 토픽 조회·발행·측정 |
| `ros2 service` | 서비스 조회·요청 |
| `ros2 action` | 액션 조회·목표 전달 |
| `ros2 interface` | 인터페이스 타입 조회 |
| `ros2 param` | 파라미터 읽기·쓰기·저장 |
| `ros2 bag` | 토픽 데이터 기록·재생 |
| `ros2 daemon` | CLI 응답 속도 향상을 위한 캐싱 데몬 |
| `ros2 doctor` | 개발 환경 문제 진단 |
| `ros2 lifecycle` | 노드 라이프사이클 상태 관리 |
| `ros2 component` | 컴포넌트 노드 동적 로드·언로드 |
| `--ros-args` | 실행 시 네임스페이스·이름·파라미터 동적 설정 |

ROS 2 CLI는 외워서 쓰는 것이 아니라 **탭 자동완성과 `-h` 옵션을 활용하여 체득**하는 것이 핵심이다. GUI보다 빠르고 간결하게 작업할 수 있으므로 의도적으로 자주 사용해보자.
