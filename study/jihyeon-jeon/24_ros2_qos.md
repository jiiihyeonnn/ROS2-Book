# 4장 QoS (Quality of Service)

## 4.1 QoS (Quality of Service)

QoS는 DDS 기반의 ROS 2가 제공하는 통신 품질 제어 옵션이다. 데이터 전송의 신뢰성, 보관 방식, 주기 검사 등 다양한 측면을 세밀하게 설정할 수 있다.

> **RMW (ROS MiddleWare)**: DDS와 ROS 2 사이의 추상화 레이어. 실제 DDS 구현체(FastDDS, CycloneDDS 등)를 교체해도 ROS 2 코드가 동일하게 동작하도록 중간에서 변환을 담당한다. **RMW QoS Profile**은 RMW 수준에서 미리 정의된 QoS 설정 묶음이다.

---

### (1) History

| 옵션 | 설명 |
|---|---|
| `KEEP_LAST` | 정해진 메시지 큐 사이즈(depth)만큼의 데이터를 보관 |
| `KEEP_ALL` | 모든 데이터를 보관 (큐 사이즈는 DDS 벤더마다 다름) |

> `depth`: 메시지 큐의 사이즈 — `KEEP_LAST` 설정일 경우에만 유효

> **메시지 큐**: 전송 대기 중인 메시지를 순서대로 저장하는 버퍼. 새 메시지가 큐를 초과하면 가장 오래된 것부터 제거된다 (`KEEP_LAST` 기준).

---

### (2) Reliability

| 옵션 | 설명 |
|---|---|
| `BEST_EFFORT` | 전송 속도 중시. 네트워크 상태에 따라 유실이 발생할 수 있음 (UDP 방식) |
| `RELIABLE` | 신뢰성 중시. 유실이 발생하면 재전송을 통해 수신 보장 (TCP 방식) |

---

### (3) Durability

| 옵션 | 설명 |
|---|---|
| `TRANSIENT_LOCAL` | Subscription이 생성되기 **전**의 데이터도 보관 (Publisher에만 적용 가능) |
| `VOLATILE` | Subscription이 생성되기 전의 데이터는 무효 (기본값) |

---

### (4) Deadline

| 항목 | 설명 |
|---|---|
| `deadline_duration` | 정해진 주기 안에 데이터가 발신·수신되지 않으면 `EventCallback`을 실행 |

---

### (5) Lifespan

| 항목 | 설명 |
|---|---|
| `lifespan_duration` | 정해진 주기 안에 수신된 데이터만 유효로 판정, 기한 초과 데이터는 삭제 |

---

### (6) Liveliness

| 항목 | 설명 |
|---|---|
| `liveliness` | `AUTOMATIC` / `MANUAL_BY_NODE` / `MANUAL_BY_TOPIC` 중 선택 |
| `lease_duration` | 노드 또는 토픽의 생존을 확인하는 주기 |

---

### RMW QoS Profile

자주 사용하는 QoS 옵션의 세트 구성인 RMW QoS Profile은 다음과 같다.

| 항목 | Default | Sensor Data | Service | Action Status | Parameters | Parameter Events |
|---|---|---|---|---|---|---|
| Reliability | RELIABLE | BEST_EFFORT | RELIABLE | RELIABLE | RELIABLE | RELIABLE |
| History | KEEP_LAST | KEEP_LAST | KEEP_LAST | KEEP_LAST | KEEP_LAST | KEEP_LAST |
| Depth | 10 | 5 | 10 | 1 | 1,000 | 1,000 |
| Durability | VOLATILE | VOLATILE | VOLATILE | TRANSIENT_LOCAL | VOLATILE | VOLATILE |

---

## 4.2 Topic, Service, Action의 QoS 설정

### 4.2.1 Topic

Topic의 기본 QoS는 별도 설정이 없으면 RMW QoS Profile의 **Default** 설정을 사용한다 (Reliability: RELIABLE, History: KEEP_LAST, Depth: 10, Durability: VOLATILE).

**기본 사용 예시 (RCLPY / RCLCPP)**

```python
# Python (RCLPY)
qos_profile = QoSProfile(depth=10)
self.helloworld_publisher = self.create_publisher(String, 'helloworld', qos_profile)
```

```cpp
// C++ (RCLCPP)
auto qos_profile = rclcpp::QoS(rclcpp::KeepLast(10));
helloworld_publisher_ = this->create_publisher<std_msgs::msg::String>("helloworld", qos_profile);
```

가독성과 명확성을 위해 4가지 기본 옵션을 명시적으로 작성하는 것을 권장한다.

**RCLPY — 명시적 QoS 설정**

```python
# topic_service_action_rclpy_example/arithmetic/argument.py

QOS_RKL10V = QoSProfile(
    reliability=QoSReliabilityPolicy.RELIABLE,
    history=QoSHistoryPolicy.KEEP_LAST,
    depth=qos_depth,
    durability=QoSDurabilityPolicy.VOLATILE)

self.arithmetic_argument_publisher = self.create_publisher(
    ArithmeticArgument,
    'arithmetic_argument',
    QOS_RKL10V)
```

**각 옵션 설명**

| 옵션 | 설정값 | 의미 |
|---|---|---|
| Reliability | `RELIABLE` | 유실 시 재전송으로 수신 보장 |
| History | `KEEP_LAST` | 정해진 큐 크기만큼 보관 |
| Depth | `10` | 큐 사이즈 10 (KEEP_LAST일 때만 유효) |
| Durability | `VOLATILE` | Subscription 생성 전 데이터는 무효 |

**RCLCPP — 명시적 QoS 설정**

```cpp
// topic_service_action_rclcpp_example/src/arithmetic/argument.cpp

const auto QOS_RKL10V =
  rclcpp::QoS(rclcpp::KeepLast(qos_depth)).reliable().durability_volatile();

arithmetic_argument_publisher_ =
  this->create_publisher<ArithmeticArgument>("arithmetic_argument", QOS_RKL10V);
```

RCLCPP는 메서드 체이닝으로 한 줄에 간결하게 설정할 수 있다.

---

### 4.2.2 Service

서비스는 특별한 경우를 제외하고 기본 QoS(`qos_profile_services_default`)를 사용한다.

**RCLPY** — `create_service`의 기본 QoS

```python
# rclpy/node.py
def create_service(
    self,
    srv_type,
    srv_name: str,
    callback,
    *,
    qos_profile: QoSProfile = qos_profile_services_default,  # 기본값
    callback_group: CallbackGroup = None
) -> Service:
```

**RCLCPP** — `create_service`의 기본 QoS

```cpp
// rclcpp/node.hpp
template<typename ServiceT, typename CallbackT>
typename rclcpp::Service<ServiceT>::SharedPtr
create_service(
  const std::string & service_name,
  CallbackT && callback,
  const rmw_qos_profile_t & qos_profile = rmw_qos_profile_services_default,  // 기본값
  rclcpp::CallbackGroup::SharedPtr group = nullptr);
```

**`qos_profile_services_default` 상세**

```c
// rmw/qos_profiles.h
static const rmw_qos_profile_t rmw_qos_profile_services_default =
{
  RMW_QOS_POLICY_HISTORY_KEEP_LAST,         // History: KEEP_LAST
  10,                                        // Depth: 10
  RMW_QOS_POLICY_RELIABILITY_RELIABLE,      // Reliability: RELIABLE
  RMW_QOS_POLICY_DURABILITY_VOLATILE,       // Durability: VOLATILE
  RMW_QOS_DEADLINE_DEFAULT,                 // Deadline: {0, 0}
  RMW_QOS_LIFESPAN_DEFAULT,                 // Lifespan: {0, 0}
  RMW_QOS_POLICY_LIVELINESS_SYSTEM_DEFAULT, // Liveliness: SYSTEM_DEFAULT
  RMW_QOS_LIVELINESS_LEASE_DURATION_DEFAULT,
  false
};
```

---

### 4.2.3 Action

액션은 토픽과 서비스를 모두 사용하는 복합 형태이므로 QoS도 각각 별도로 설정된다.

**액션의 5가지 QoS 구성**

| 항목 | 기본 QoS |
|---|---|
| `goal_service_qos_profile` | `qos_profile_services_default` |
| `result_service_qos_profile` | `qos_profile_services_default` |
| `cancel_service_qos_profile` | `qos_profile_services_default` |
| `feedback_pub_qos_profile` | `QoSProfile(depth=10)` |
| `status_pub_qos_profile` | `qos_profile_action_status_default` |

**RCLPY** — `ActionServer` 기본 설정

```python
# rclpy/action/server.py
class ActionServer(Waitable):
    def __init__(
        self,
        node,
        action_type,
        action_name,
        execute_callback,
        *,
        goal_service_qos_profile=qos_profile_services_default,
        result_service_qos_profile=qos_profile_services_default,
        cancel_service_qos_profile=qos_profile_services_default,
        feedback_pub_qos_profile=QoSProfile(depth=10),
        status_pub_qos_profile=qos_profile_action_status_default,
        result_timeout=900
    ):
```

**RCLCPP** — `rcl_action_server_get_default_options` 기본 설정

```c
// rcl_action/action_server.h
// - goal_service_qos   = rmw_qos_profile_services_default
// - cancel_service_qos = rmw_qos_profile_services_default
// - result_service_qos = rmw_qos_profile_services_default
// - feedback_topic_qos = rmw_qos_profile_default
// - status_topic_qos   = rcl_action_qos_profile_status_default
```

---

## 4.3 QoS 실습

사용 패키지: `demo_nodes_cpp`, `image_tools`, `quality_of_service_demo`, `my_first_ros_rclpy_pkg`

---

### 4.3.1 History

History는 데이터를 몇 개나 보관할지 결정하는 옵션이다. `KEEP_ALL` 동작 확인을 위해 Durability를 `TRANSIENT_LOCAL`로 함께 설정한다.

**QoS 코드 변경 예시** (`helloworld_publisher.py`, `helloworld_subscriber.py`)

```python
# 기본 설정
qos_profile = QoSProfile(depth=10)

# KEEP_LAST 명시적 설정
qos_profile = QoSProfile(
    reliability=QoSReliabilityPolicy.RELIABLE,
    history=QoSHistoryPolicy.KEEP_LAST,
    depth=10,
    durability=QoSDurabilityPolicy.TRANSIENT_LOCAL)

# KEEP_ALL 설정
qos_profile = QoSProfile(
    reliability=QoSReliabilityPolicy.RELIABLE,
    history=QoSHistoryPolicy.KEEP_ALL,
    depth=10,
    durability=QoSDurabilityPolicy.TRANSIENT_LOCAL)
```

**실행 결과** — `KEEP_ALL`로 설정 후, 퍼블리셔를 한참 실행한 뒤 서브스크라이버를 늦게 시작해도 **이전 메시지 전부**를 수신

```bash
$ ros2 run my_first_ros_rclpy_pkg helloworld_publisher
[INFO]: Published message: Hello World: 0
...
[INFO]: Published message: Hello World: 660
(이하 생략)

$ ros2 run my_first_ros_rclpy_pkg helloworld_subscriber
[INFO]: Published message: Hello World: 0    ← 처음부터 전부 수신
...
[INFO]: Published message: Hello World: 660
(이하 생략)
```

`KEEP_LAST`였다면 `depth=10` 설정에 따라 최근 10개만 수신했을 것이다.

---

### 4.3.2 Reliability

`tc(traffic control)` 유틸리티로 10% 데이터 손실을 인위적으로 발생시켜 Reliability 옵션 차이를 확인한다.

```bash
# 10% 패킷 손실 설정
$ sudo tc qdisc add dev lo root netem loss 10%
```

> **tc (traffic control)**: Linux 네트워크 트래픽을 제어하는 커맨드라인 도구.
> **qdisc (Queuing Discipline)**: tc가 관리하는 큐잉 규칙 — 패킷을 어떻게 대기시키고 보낼지 정의한다.
> **netem (Network Emulator)**: qdisc의 일종으로 지연·손실·중복·순서 뒤섞임 등 네트워크 열악 조건을 소프트웨어로 재현한다.

**RELIABLE (listener)**

```bash
$ ros2 run demo_nodes_cpp listener         # Terminal 1
$ ros2 run demo_nodes_cpp talker           # Terminal 2
```

```bash
# listener 출력 — 손실 없이 전부 수신 (단, 손실 시점에서 잠시 멈춤)
[INFO]: I heard: [Hello World: 1]
[INFO]: I heard: [Hello World: 2]
...
[INFO]: I heard: [Hello World: 15]
```

손실이 발생해도 재전송으로 보장한다. 다만 재전송·ACK 처리로 인해 잠시 멈추는 현상이 나타난다.

> **ACK (Acknowledgement)**: 데이터를 정상 수신했음을 송신 측에 알리는 확인 신호. RELIABLE 모드에서는 수신 측이 ACK를 보내지 않으면 송신 측이 해당 패킷을 재전송한다.

**BEST_EFFORT (listener_best_effort)**

```bash
$ ros2 run demo_nodes_cpp listener_best_effort    # Terminal 1
$ ros2 run demo_nodes_cpp talker                  # Terminal 2
```

```bash
# listener_best_effort 출력 — 2번, 9번 유실
[INFO]: I heard: [Hello World: 1]
# 2번 손실
[INFO]: I heard: [Hello World: 3]
...
[INFO]: I heard: [Hello World: 8]
# 9번 손실
[INFO]: I heard: [Hello World: 10]
...
[INFO]: I heard: [Hello World: 15]
```

> `SensorDataQoS`는 내부적으로 `rmw_qos_profile_sensor_data`를 사용하며 Reliability가 `BEST_EFFORT`로 설정된다.

```bash
# 테스트 완료 후 반드시 손실 설정 해제
$ sudo tc qdisc delete dev lo root netem loss 10%
```

**Reliability 비교**

| 설정 | 손실 처리 | 특징 |
|---|---|---|
| `RELIABLE` | 재전송으로 보장 | 신뢰성 우선, 지연 발생 가능 |
| `BEST_EFFORT` | 손실 허용 | 속도 우선, 끊김 없음 |

---

### 4.3.3 Durability

Durability는 서브스크라이버가 생성되기 **전**의 데이터를 받을지 여부를 결정한다. `TRANSIENT_LOCAL` 설정 시 depth로 지정한 개수만큼 이전 데이터를 보관한다.

```python
# VOLATILE (기본값) — 이전 데이터 무효
qos_profile = QoSProfile(
    reliability=QoSReliabilityPolicy.RELIABLE,
    history=QoSHistoryPolicy.KEEP_LAST,
    depth=10,
    durability=QoSDurabilityPolicy.VOLATILE)

# TRANSIENT_LOCAL — depth만큼 이전 데이터 보관
qos_profile = QoSProfile(
    reliability=QoSReliabilityPolicy.RELIABLE,
    history=QoSHistoryPolicy.KEEP_LAST,
    depth=10,
    durability=QoSDurabilityPolicy.TRANSIENT_LOCAL)
```

**실행 결과** — `TRANSIENT_LOCAL`, `depth=10` 설정 시, 늦게 시작한 서브스크라이버가 최근 10개 메시지를 수신

```bash
$ ros2 run my_first_ros_rclpy_pkg helloworld_publisher
[INFO]: Published message: Hello World: 0
...
[INFO]: Published message: Hello World: 20
(이하 생략)

$ ros2 run my_first_ros_rclpy_pkg helloworld_subscriber
[INFO]: Received message: Hello World: 13   ← 최근 10개(11~20)가 아닌 시점 기준 10개 수신
[INFO]: Received message: Hello World: 14
...
[INFO]: Received message: Hello World: 20
[INFO]: Received message: Hello World: 21
(이하 생략)
```

---

### 4.3.4 Deadline

Deadline은 정해진 주기 안에 데이터가 발신·수신되지 않으면 `EventCallback`을 실행하는 옵션이다.

```python
# quality_of_service_demo_py/deadline.py (핵심 부분)

deadline = Duration(seconds=parsed_args.deadline / 1000.0)
qos_profile = QoSProfile(depth=10, deadline=deadline)

subscription_callbacks = SubscriptionEventCallbacks(
    deadline=lambda event: get_logger('Listener').info(str(event)))  # 수신 측 콜백

publisher_callbacks = PublisherEventCallbacks(
    deadline=lambda event: get_logger('Talker').info(str(event)))    # 발신 측 콜백
```

**정상 동작 (Deadline 주기 내 통신)**

```bash
# Deadline 0.7초, 3초 동안 발신, 쉬지 않음 (pause=0)
$ ros2 run quality_of_service_demo_py deadline 700 --publish-for 3000 --pause-for 0
[INFO]: Publishing: Talker says 0
[INFO]: I heard: Talker says 0
[INFO]: Publishing: Talker says 1
[INFO]: I heard: Talker says 1
...
```

**Deadline 초과 발생 (pause > deadline)**

```bash
# Deadline 0.7초, 3초 발신 후 1초 휴지
$ ros2 run quality_of_service_demo_py deadline 700 --publish-for 3000 --pause-for 1000
...
[INFO]: Publishing: Talker says 5
[INFO]: I heard: Talker says 5
[INFO]: QoSRequestedDeadlineMissedInfo(total_count=1, total_count_change=1)   ← 수신 측
[INFO]: QoSOfferedDeadlineMissedInfo(total_count=1, total_count_change=1)    ← 발신 측
[INFO]: Publishing: Talker says 6
...
```

1초 휴지 중에 0.7초 Deadline이 초과되어 퍼블리셔(`QoSOffered`)와 서브스크라이버(`QoSRequested`) 양쪽에서 EventCallback이 발생한다.

---

### 4.3.5 Lifespan

Lifespan은 퍼블리셔의 메시지 큐에서 정해진 시간이 지난 데이터를 삭제하는 옵션이다. `TRANSIENT_LOCAL`과 함께 사용하면 뒤늦게 참여한 서브스크라이버가 받을 수 있는 데이터 범위를 제어할 수 있다.

```python
# quality_of_service_demo_py/lifespan.py (핵심 부분)

lifespan = Duration(seconds=parsed_args.lifespan / 1000.0)
qos_profile = QoSProfile(
    depth=parsed_args.history,
    reliability=QoSReliabilityPolicy.RELIABLE,
    durability=QoSDurabilityPolicy.TRANSIENT_LOCAL,
    lifespan=lifespan)
```

**Lifespan = 1초 (짧음) — 오래된 데이터는 삭제**

```bash
# Lifespan 1초, 10개 발행, 3초 후 구독 시작
$ ros2 run quality_of_service_demo_py lifespan 1000 --publish-count 10 --subscribe-after 3000
[INFO]: Publishing: Talker says 0
[INFO]: Publishing: Talker says 1
...
[INFO]: Publishing: Talker says 4
[INFO]: Subscription created          ← 3초 후 구독 시작
[INFO]: Publishing: Talker says 5
[INFO]: I heard: Talker says 4        ← 1초 이내(최근) 데이터만 수신
[INFO]: I heard: Talker says 5
...
```

**Lifespan = 4초 (김) — 이전 데이터도 수신 가능**

```bash
# Lifespan 4초, 10개 발행, 3초 후 구독 시작
$ ros2 run quality_of_service_demo_py lifespan 4000 --publish-count 10 --subscribe-after 3000
[INFO]: Publishing: Talker says 0
...
[INFO]: Publishing: Talker says 5
[INFO]: Subscription created          ← 3초 후 구독 시작
[INFO]: I heard: Talker says 0        ← 4초 이내 데이터 전부 수신
[INFO]: I heard: Talker says 1
...
[INFO]: I heard: Talker says 5
[INFO]: Publishing: Talker says 6
[INFO]: I heard: Talker says 6
...
```

**Lifespan 비교**

| Lifespan | 구독 시작 시점 | 수신 가능 데이터 |
|---|---|---|
| 1초 | 3초 후 | 최근 1초 이내 데이터만 |
| 4초 | 3초 후 | 4초 이내(발행된 모든) 데이터 |

---

### 4.3.6 Liveliness

Liveliness는 정해진 주기 안에 노드 또는 토픽의 생존을 확인하는 옵션이다. 생존 확인 방식으로 `AUTOMATIC` 또는 `MANUAL_BY_TOPIC`을 선택할 수 있다.

```python
# quality_of_service_demo_py/liveliness.py (핵심 부분)

liveliness_lease_duration = Duration(seconds=parsed_args.liveliness_lease_duration / 1000.0)
liveliness_policy = POLICY_MAP[parsed_args.policy]

qos_profile = QoSProfile(
    depth=10,
    liveliness=liveliness_policy,
    liveliness_lease_duration=liveliness_lease_duration)

subscription_callbacks = SubscriptionEventCallbacks(
    liveliness=lambda event: get_logger('Listener').info(str(event)))  # 생존 변화 콜백
```

**AUTOMATIC — 노드 종료 감지**

```bash
# Liveliness 주기 1초, 2초 후 퍼블리셔 노드 종료
$ ros2 run quality_of_service_demo_py liveliness 1000 --kill-publisher-after 2000 --policy AUTOMATIC
[INFO]: Publishing: Talker says 0
[INFO]: QoSLivelinessChangedInfo(alive_count=1, ..., alive_count_change=1, ...)  ← 노드 등장
[INFO]: I heard: Talker says 0
...
[INFO]: Publishing: Talker says 3
[INFO]: QoSLivelinessChangedInfo(alive_count=0, ..., alive_count_change=-1, ...) ← 노드 종료 감지
```

노드가 종료되면 서브스크라이버 측에서 `alive_count=0`으로 변화를 감지한다.

**MANUAL_BY_TOPIC — 발행 중단 감지 (노드는 살아있음)**

```bash
# Liveliness 주기 1초, 2초 후 퍼블리시만 중단
$ ros2 run quality_of_service_demo_py liveliness 1000 --kill-publisher-after 2000 --policy MANUAL_BY_TOPIC
[INFO]: Publishing: Talker says 0
[INFO]: QoSLivelinessChangedInfo(alive_count=1, ..., alive_count_change=1, ...)
...
[INFO]: Publishing: Talker says 2
[INFO]: I heard: Talker says 2
[INFO]: QoSLivelinessChangedInfo(alive_count=0, not_alive_count=1, ...)  ← 발행 중단 감지
[INFO]: QoSLivelinessLostInfo(total_count=1, total_count_change=1)       ← 발신 측
```

`MANUAL_BY_TOPIC`은 노드가 살아있어도 발행이 중단되면 `not_alive_count`가 증가한다.

**Liveliness 정책 비교**

| 정책 | 감지 기준 | `not_alive_count` 증가 |
|---|---|---|
| `AUTOMATIC` | 노드 프로세스 종료 | 아니오 (단순 `alive_count` 감소) |
| `MANUAL_BY_TOPIC` | 토픽 발행 중단 | 예 |

---

## 4.4 유용한 QoS 예제

### incompatible_qos — QoS 불일치 감지 (RxO 체크)

퍼블리셔와 서브스크라이버의 QoS가 호환되지 않을 때 (`Requested by Offered`) 이벤트가 발생하는 것을 확인하는 노드이다.

> **RxO (Requested by Offered)**: 서브스크라이버(Requested)가 요청한 QoS가 퍼블리셔(Offered)가 제공하는 QoS보다 엄격할 때 비호환으로 판정하는 규칙. 예) Publisher가 `VOLATILE`인데 Subscriber가 `TRANSIENT_LOCAL`을 요구하면 비호환.

```bash
# durability 불일치 테스트
# Publisher: VOLATILE, Subscription: TRANSIENT_LOCAL → 비호환
$ ros2 run quality_of_service_demo_py incompatible_qos durability
Durability incompatibility selected.
Setting publisher durability to: VOLATILE
Setting subscription durability to: TRANSIENT_LOCAL

[INFO]: QoSRequestedIncompatibleQoSInfo(total_count=1, total_count_change=1, last_policy_kind=2)
[INFO]: Publishing: Talker says 0
[INFO]: Publishing: Talker says 1
...
```

다른 옵션도 테스트 가능하다.

```bash
$ ros2 run quality_of_service_demo_py incompatible_qos -h
usage: incompatible_qos [-h]
  {durability,deadline,liveliness_policy,liveliness_lease_duration,reliability}
```

---

### interactive_publisher / interactive_subscriber — QoS 인터랙티브 테스트

다양한 QoS 옵션을 직접 인자로 조합하여 퍼블리셔와 서브스크라이버를 실행해볼 수 있는 예제이다.

```bash
# 퍼블리셔: 0.5초 딜레이, Deadline 1초, MANUAL_BY_TOPIC Liveliness 1초
$ ros2 run quality_of_service_demo_cpp interactive_publisher \
  --delay 0.5 --deadline 1 --liveliness MANUAL_BY_TOPIC --lease 1
[INFO]: Talker starting up
HISTORY POLICY: keep last (depth: 10)
RELIABILITY POLICY: reliable
DURABILITY POLICY: volatile
DEADLINE: 1
LIVELINESS POLICY: manual by topic (lease duration: 1)
[INFO]: Publishing: 'Talker says 0'
...

# 서브스크라이버: 동일한 Deadline/Liveliness 설정으로 연결
$ ros2 run quality_of_service_demo_cpp interactive_subscriber \
  --deadline 1 --liveliness MANUAL_BY_TOPIC --lease 1
[INFO]: Listener starting up
DEADLINE: 1
LIVELINESS POLICY: manual by topic (lease duration: 1)
[INFO]: Liveliness changed - alive 1 (delta 1), not alive 0 (delta 0)
[INFO]: Listener heard: [Talker says 15]
...
```

---

## 정리

| QoS 옵션 | 핵심 역할 | 주요 설정값 |
|---|---|---|
| History | 메시지 보관 개수 | `KEEP_LAST` (depth 지정) / `KEEP_ALL` |
| Reliability | 전송 방식 | `RELIABLE` (재전송 보장) / `BEST_EFFORT` (속도 우선) |
| Durability | 구독 전 데이터 처리 | `TRANSIENT_LOCAL` (보관) / `VOLATILE` (기본, 무효) |
| Deadline | 주기 내 미수신 감지 | `deadline_duration` 설정 → EventCallback 실행 |
| Lifespan | 데이터 유효 기간 | `lifespan_duration` 설정 → 기한 초과 데이터 삭제 |
| Liveliness | 노드/토픽 생존 확인 | `AUTOMATIC` / `MANUAL_BY_TOPIC` + `lease_duration` |

기본적으로는 **History + Reliability + Durability** 3가지를 용도에 맞게 설정하고, 타이밍 보장이 필요한 경우 **Deadline**, 오래된 데이터를 걸러야 한다면 **Lifespan**, 노드 장애 감지가 필요한 경우 **Liveliness**를 추가로 활용하자.
