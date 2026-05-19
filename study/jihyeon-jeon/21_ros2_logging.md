# 1장 Logging

## 1.1 로그(Log)

새로운 프로그래밍 언어를 시작할 때 우리는 관습적으로 `Hello World!`를 프린트하여 터미널에서 확인한다. ROS 2에서도 노드를 실행하면 로깅 함수를 통해 아래와 같이 메시지를 출력할 수 있다.

```bash
$ ros2 run my_first_ros_rclcpp_pkg helloworld_publisher
[INFO]: Published message: 'Hello World: 0'
[INFO]: Published message: 'Hello World: 1'
[INFO]: Published message: 'Hello World: 2'
```

**ROS 2 로그(logger/logging 라이브러리)의 주요 특성**

| 특성 | 설명 |
|---|---|
| 단순한 인터페이스 | 초기화 없이 바로 사용 가능 |
| 다양한 로그 수준 | DEBUG, INFO, WARN, ERROR, FATAL |
| 다양한 필터링 | `_NAMED`, `_COND`, `_ONCE`, `_THROTTLE` 등 |
| 출력 스타일 | `printf`와 `stream` 스타일 모두 지원 |
| 성능 | 런타임 성능에 최소한의 영향 |
| 쓰레드 세이프 | 멀티쓰레드 환경에서 안전하게 사용 가능 |
| 상세 정보 | 파일명, 줄 수, 노드 이름, 네임스페이스 포함 가능 |
| 계층 구조 | `abc`, `abc.df` 형태의 계층적 로거 지원 |
| 외부 설정 | Launch 파일 및 런타임에서 로그 수준 변경 가능 |
| 문서 저장 | 로그를 파일로 저장하는 기능 제공 |

---

## 1.2 로그 설정

### 1.2.1 Log directory

로그가 저장되는 디렉토리는 아래 명령어로 확인할 수 있다.

```bash
$ ls ~/.ros/log
```

이전에 노드나 런치 파일을 실행한 적이 있다면 해당 디렉토리에 로그 파일들이 남아 있다. Foxy 버전에서는 저장 경로를 변경할 수 없지만, Galactic 이후 버전부터 경로 변경을 지원한다.

---

### 1.2.2 Log level

ROS 2에서 로그 수준은 총 5가지를 제공한다.

| 수준 | 설명 |
|---|---|
| DEBUG | 개발 단계에서 세부 정보 확인 |
| INFO | 일반적인 정보 출력 |
| WARN | 잠재적 문제 경고 |
| ERROR | 오류 발생 알림 |
| FATAL | 치명적 오류, 프로그램 종료 수준 |

> 로그 수준 기준은 개발자·회사 정책마다 다를 수 있으니 협업 시 사전에 협의하는 것이 좋다.

#### Programmatically (코드에서 설정)

가장 기본적인 방법은 코드에 로그 수준을 직접 작성하는 것이다. ROS 2 노드는 logger를 캡슐화(내부 구현을 숨기고 정해진 인터페이스로만 접근하게 하는 방식)하여 제공한다.

```cpp
// RCLCPP (C++)
RCLCPP_INFO(this->get_logger(), "Published message: '%s'", msg.data.c_str());
```

```python
# RCLPY (Python)
self.get_logger().info('Published message: {0}'.format(msg.data))
```

**RCLCPP 제공 매크로 함수** (`${SEVERITY}` 자리에 로그 수준 입력)

| 매크로 | 동작 |
|---|---|
| `RCLCPP_${SEVERITY}` | formatting을 지원하는 기본 출력 |
| `RCLCPP_${SEVERITY}_ONCE` | 딱 한 번만 출력 |
| `RCLCPP_${SEVERITY}_EXPRESSION` | expression이 참(true)일 때만 출력 |
| `RCLCPP_${SEVERITY}_FUNCTION` | function이 참(true)일 때만 출력 |
| `RCLCPP_${SEVERITY}_SKIPFIRST` | 처음 호출 시를 제외하고 출력 |
| `RCLCPP_${SEVERITY}_THROTTLE` | 특정 주기마다 출력 |
| `RCLCPP_${SEVERITY}_SKIPFIRST_THROTTLE` | 처음 호출 시를 제외하고 특정 주기마다 출력 |

#### Externally (외부 노드에서 설정)

ROS 1에서는 `rqt_logger_level` 노드를 통해 서비스 통신으로 특정 노드의 로그 수준을 변경할 수 있었다. ROS 2에서도 비슷한 개념을 지원하려 하지만 아직 완전히 구현되지 않았다.

#### Command line (실행 시 인자로 설정)

노드를 실행할 때 `--ros-args` 인자를 통해 로그 수준을 지정할 수 있다.

```bash
$ ros2 run logging_demo logging_demo_main --ros-args --log-level debug
```

---

### 1.2.3 Console output formatting

터미널에 출력되는 로그 형식은 환경변수로 변경할 수 있다.

```bash
export RCUTILS_CONSOLE_OUTPUT_FORMAT="[{severity} {time}] [{name}]: {message} ({function_name}() at {file_name}:{line_number})"
```

적용 결과 예시:

```
[INFO 1612664988.687346286] [helloworld_publisher]: Published message: 'Hello World: 0' (publish_helloworld_msg() at /home/user/.../helloworld_publisher.cpp:44)
```

---

### 1.2.4 Console output colorizing

로그 수준에 따라 글자 색이 다르게 표시된다.

| 수준 | 기본 색상 |
|---|---|
| INFO | 흰색 |
| WARN | 노란색 |
| ERROR | 빨간색 |

색상이 표시되지 않는다면 아래 환경변수를 확인한다.

```bash
export RCUTILS_COLORIZED_OUTPUT=1   # 1: 강제 활성화, 0: 비활성화
```

---

### 1.2.5 Default stream for console output

| 버전 | 동작 |
|---|---|
| Dashing, Eloquent | DEBUG/INFO → stdout, WARN/ERROR/FATAL → stderr |
| Foxy 이후 | 모든 로그 수준 → stderr (기본값) |

> `stdout` (standard output): 프로그램의 정상 출력 스트림. `stderr` (standard error): 오류 및 진단 메시지용 출력 스트림. 두 스트림은 독립적으로 버퍼링되며 리다이렉트 대상도 따로 지정할 수 있다.

Foxy 이전에는 stdout 버퍼가 가득 차지 않으면 DEBUG/INFO 로그가 타이밍에 맞게 출력되지 않는 문제가 있었다. Foxy부터 기본값이 stderr로 통일되었다.

stdout으로 변경하려면:

```bash
export RCUTILS_LOGGING_USE_STDOUT=1
```

---

### 1.2.6 Line buffered console output

ROS 2에서 INFO, DEBUG 수준 로그는 기본적으로 라인 버퍼링(줄 단위로 출력을 모았다가 개행 문자가 나올 때 한 번에 내보내는 방식)을 하지 않도록 설정되어 있다. 라인 버퍼링을 활성화하려면:

```bash
export RCUTILS_LOGGING_BUFFERED_STREAM=1
```

**추천 환경변수 설정 (run command 파일에 등록)**

```bash
# export RCUTILS_CONSOLE_OUTPUT_FORMAT='[{severity} {time}] [{name}]: {message} ({function_name}() at {file_name}:{line_number})'
export RCUTILS_CONSOLE_OUTPUT_FORMAT='[{severity}]: {message}'
export RCUTILS_COLORIZED_OUTPUT=1
export RCUTILS_LOGGING_USE_STDOUT=0
export RCUTILS_LOGGING_BUFFERED_STREAM=0
```

---

## 1.3 예제 코드

ROS 2 데모 패키지의 `logging_demo` 노드를 통해 다양한 로그 수준과 매크로 함수의 동작을 확인할 수 있다.

### 1.3.1 RCLCPP

**헤더 파일** (`logger_usage_component.hpp`)

```cpp
#ifndef LOGGING_DEMO__LOGGER_USAGE_COMPONENT_HPP_
#define LOGGING_DEMO__LOGGER_USAGE_COMPONENT_HPP_

#include <string>
#include "logging_demo/visibility_control.h"
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

namespace logging_demo
{

class LoggerUsage : public rclcpp::Node
{
public:
  LOGGING_DEMO_PUBLIC
  explicit LoggerUsage(rclcpp::NodeOptions options);

protected:
  void on_timer();

private:
  size_t count_;
  rclcpp::Publisher<std_msgs::msg::String>::SharedPtr pub_;
  rclcpp::TimerBase::SharedPtr one_shot_timer_, timer_;
  std::function<bool()> debug_function_to_evaluate_;
};

bool is_divisor_of_twelve(size_t val, rclcpp::Logger logger);
}  // namespace logging_demo

#endif  // LOGGING_DEMO__LOGGER_USAGE_COMPONENT_HPP_
```

**주요 멤버 변수**

| 변수 | 타입 | 역할 |
|---|---|---|
| `count_` | `size_t` | 주기마다 증가하는 카운터 |
| `pub_` | `Publisher<String>` | `logging_demo_count` 토픽 퍼블리셔 |
| `timer_` | `TimerBase` | 500ms 주기 콜백 타이머 |
| `one_shot_timer_` | `TimerBase` | 5500ms 후 한 번만 실행되는 타이머 |
| `debug_function_to_evaluate_` | `std::function<bool()>` | 함수 포인터 역할의 함수 객체 (`std::function`: C++ 함수, 람다, 멤버 함수 등을 담을 수 있는 범용 callable 래퍼) |

**구현 파일** (`logger_usage_component.cpp`)

```cpp
LoggerUsage::LoggerUsage(rclcpp::NodeOptions options)
: Node("logger_usage_demo", options), count_(0)
{
  pub_ = create_publisher<std_msgs::msg::String>("logging_demo_count", 10);
  timer_ = create_wall_timer(500ms, std::bind(&LoggerUsage::on_timer, this));
  debug_function_to_evaluate_ = std::bind(is_divisor_of_twelve, std::cref(count_), get_logger());

  auto on_one_shot_timer =
    [this]() -> void {
      one_shot_timer_->cancel();
      RCLCPP_INFO(get_logger(), "Setting severity threshold to DEBUG");
      auto ret = rcutils_logging_set_logger_level(
        get_logger().get_name(), RCUTILS_LOG_SEVERITY_DEBUG);
      if (ret != RCUTILS_RET_OK) {
        RCLCPP_ERROR(get_logger(), "Error setting severity: %s", rcutils_get_error_string().str);
        rcutils_reset_error();
      }
    };
  one_shot_timer_ = create_wall_timer(5500ms, on_one_shot_timer);
}
```

- `pub_`: `logging_demo_count` 토픽, history depth 10으로 초기화
- `timer_`: 500ms 주기로 `on_timer` 콜백
- `debug_function_to_evaluate_`: `std::bind`(함수와 인자를 미리 묶어 나중에 호출할 수 있게 하는 함수)로 `is_divisor_of_twelve` 함수와 연결, `std::cref`(상수 참조를 전달할 때 사용하는 래퍼 — 복사 없이 원본을 읽기 전용으로 공유)로 `count_`를 상수 참조로 전달
- `one_shot_timer_`: 5500ms 후(timer_ 콜백 11회 시점) 로그 수준을 DEBUG로 변경

```cpp
void LoggerUsage::on_timer()
{
  // 처음 호출될 때만 출력
  RCLCPP_INFO_ONCE(get_logger(), "Timer callback called (this will only log once)");

  auto msg = std::make_unique<std_msgs::msg::String>();
  msg->data = "Current count: " + std::to_string(count_);

  RCLCPP_INFO(get_logger(), "Publishing: '%s'", msg->data.c_str());
  pub_->publish(std::move(msg));

  // debug_function_to_evaluate_가 true일 때만 DEBUG 로그 출력
  RCLCPP_DEBUG_FUNCTION(
    get_logger(), &debug_function_to_evaluate_,
    "Count divides into 12 (function evaluated to true)");

  // count_가 짝수일 때만 DEBUG 로그 출력
  RCLCPP_DEBUG_EXPRESSION(
    get_logger(), (count_ % 2) == 0, "Count is even (expression evaluated to true)");

  if (count_++ >= 15) {
    RCLCPP_WARN(get_logger(), "Reseting count to 0");
    count_ = 0;
  }
}

bool is_divisor_of_twelve(size_t val, rclcpp::Logger logger)
{
  if (val == 0) {
    RCLCPP_ERROR(logger, "Modulo divisor cannot be 0");
    return false;
  }
  return (12 % val) == 0;
}
```

**매크로 함수 동작 요약**

| 매크로 | 조건 | 출력 |
|---|---|---|
| `RCLCPP_INFO_ONCE` | 첫 번째 호출 시 | "Timer callback called (this will only log once)" |
| `RCLCPP_DEBUG_FUNCTION` | DEBUG 수준 활성화 + 함수가 true | "Count divides into 12" |
| `RCLCPP_DEBUG_EXPRESSION` | DEBUG 수준 활성화 + count_ 짝수 | "Count is even" |
| `RCLCPP_WARN` | count_ > 15 | "Reseting count to 0" |
| `RCLCPP_ERROR` | val == 0 | "Modulo divisor cannot be 0" |

**실행 및 출력 확인**

```bash
$ ros2 run logging_demo logging_demo_main
[INFO]: Timer callback called (this will only log once)
[INFO]: Publishing: 'Current count: 0'
[INFO]: Publishing: 'Current count: 1'
...
[INFO]: Publishing: 'Current count: 10'
[INFO]: Setting severity threshold to DEBUG
[INFO]: Publishing: 'Current count: 11'
[INFO]: Publishing: 'Current count: 12'
[DEBUG]: Count divides into 12 (function evaluated to true)
[DEBUG]: Count is even (expression evaluated to true)
...
[INFO]: Publishing: 'Current count: 15'
[WARN]: Reseting count to 0
[INFO]: Publishing: 'Current count: 0'
[ERROR]: Modulo divisor cannot be 0
[DEBUG]: Count is even (expression evaluated to true)
```

- count_ 10까지: INFO 수준 로그만 출력
- count_ 11 이후: `one_shot_timer_` 동작으로 DEBUG 수준 활성화 → DEBUG 로그 함께 출력
- count_ 0일 때: 나눗셈 불가로 ERROR 출력
- count_ > 15: WARN 출력 후 0으로 초기화

**외부 노드로 로그 수준 변경 (`logger_config` 노드 활용)**

`logging_demo_main`은 `logger_usage_demo`와 `logger_config` 노드를 함께 실행한다. DEBUG 로그가 출력되는 중에 아래 서비스 콜을 입력하면 INFO 수준으로 되돌릴 수 있다.

```bash
$ ros2 service call /config_logger logging_demo/srv/ConfigLogger \
  "{logger_name: 'logger_usage_demo', level: INFO}"
```

---

### 1.3.2 RCLPY

RCLCPP 예제와 동일한 동작을 RCLPY로 구현한 버전이다. RCLPY에는 `_FUNCTION`과 `_EXPRESSION` 필터링이 별도로 구현되지 않아 직접 조건문으로 대체한다.

```python
import rclpy
from rclpy.logging import LoggingSeverity
from rclpy.node import Node
from std_msgs.msg import String


class LoggerUsage(Node):

    def __init__(self):
        super().__init__('logger_usage_demo')
        self.pub = self.create_publisher(String, 'logging_demo_count', 10)
        self.timer = self.create_timer(0.500, self.on_timer)
        self.count = 0

    def on_timer(self):
        self.get_logger().log(
            'Timer callback called (this will only log once)',
            LoggingSeverity.INFO,
            once=True)

        msg = String()
        msg.data = 'Current count: {0}'.format(self.count)

        self.get_logger().info('Publishing: {0}'.format(msg.data))
        self.pub.publish(msg)

        # DEBUG FUNCTION (직접 조건문으로 구현)
        if self.debug_function_to_evaluate():
            self.get_logger().debug('Count divides into 12 (function evaluated to true)')

        # DEBUG EXPRESSION (직접 조건문으로 구현)
        if self.count % 2 == 0:
            self.get_logger().debug('Count is even (expression evaluated to true)')

        self.count += 1
        if self.count > 15:
            self.get_logger().warn('Reseting count to 0')
            self.count = 0

    def debug_function_to_evaluate(self):
        return is_divisor_of_twelve(self.count, self.get_logger())


def is_divisor_of_twelve(val, logger):
    if val == 0:
        logger.error('Modulo divisor cannot be 0')
        return False
    return (12 % val) == 0


def main(args=None):
    rclpy.init(args=args)
    node = LoggerUsage()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        node.get_logger().info('Keyboard Interrupt (SIGINT)')
    finally:
        node.destroy_node()
        rclpy.shutdown()
```

**기본 실행 (INFO 수준)**

```bash
$ ros2 run logging_rclpy_example logging_example
[INFO]: Timer callback called (this will only log once)
[INFO]: Publishing: Current count: 0
[ERROR]: Modulo divisor cannot be 0
[INFO]: Publishing: Current count: 1
...
[INFO]: Publishing: Current count: 15
[WARN]: Reseting count to 0
```

RCLPY 예제는 RCLCPP와 달리 `one_shot_timer_`가 없어서 자동으로 DEBUG 수준이 활성화되지 않는다. count_ = 0일 때 나눗셈 오류는 처음부터 발생한다.

**DEBUG 수준 활성화 후 실행**

```bash
$ ros2 run logging_rclpy_example logging_example --ros-args --log-level debug
...
[INFO]: Timer callback called (this will only log once)
[INFO]: Publishing: Current count: 0
[ERROR]: Modulo divisor cannot be 0
[DEBUG]: Count is even (expression evaluated to true)
...
[INFO]: Publishing: Current count: 2
[DEBUG]: Count divides into 12 (function evaluated to true)
[DEBUG]: Count is even (expression evaluated to true)
```

---

## 정리

| 항목 | 내용 |
|---|---|
| 로그 수준 | DEBUG < INFO < WARN < ERROR < FATAL |
| 코드 설정 | `RCLCPP_${SEVERITY}` 매크로, `get_logger().info()` 등 |
| 외부 설정 | 서비스 콜, CLI `--log-level` 인자 |
| 형식 변경 | `RCUTILS_CONSOLE_OUTPUT_FORMAT` 환경변수 |
| 색상 | `RCUTILS_COLORIZED_OUTPUT` 환경변수 |
| 스트림 | Foxy 이후 기본 stderr, `RCUTILS_LOGGING_USE_STDOUT=1`로 변경 가능 |

로그는 가장 기본적인 디버깅 도구이면서, 사용자가 노드를 실행할 때 가장 먼저 만나는 정보이다. 개발자는 사용자에게 필요한 로그를 적절한 수준으로 코드에 채워 넣는 습관을 가져야 한다.
