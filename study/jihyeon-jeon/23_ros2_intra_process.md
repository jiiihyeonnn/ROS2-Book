# 3장 Intra-process Communication

## 3.1 ROS Node와 Nodelets

ROS에서 **Node**는 계산을 수행하는 하나의 프로세스이다. 노드는 단순한 동작만을 수행하고 다른 노드와 독립적으로 동작한다.

**노드의 장점**

| 장점 | 설명 |
|---|---|
| 장애 격리 | 단일 오류로 전체 시스템이 무너지지 않음 |
| 집중 개발 | 한 가지 기능에 집중하여 빠르게 개발 가능 |
| 모듈성 | 기능 단위로 분리되어 독립적으로 교체 가능 |
| 재활용성 | 다른 시스템에서도 재사용 가능 |

일반적으로 ROS를 사용하는 로봇은 복수의 노드로 구성된다. 예를 들어 모바일 로봇은 라이다 노드, 모터 제어 노드, 위치 추종 노드, 경로 생성 노드 등이 동시에 실행된다.

**단일 컴퓨팅 시스템에서 복수 노드 실행 시 문제점**

- 노드 간 데이터 통신 시 메모리 복사가 반복적으로 발생
- 퍼블리셔/서브스크라이버가 많을수록 메모리 사용량 증가
- 전체적인 시스템 성능 저하

ROS 1에서는 이 문제를 해결하기 위해 **Nodelets** 패키지를 제공했다. Nodelets는 단일 프로세스 내의 복수 노드 사이에 **zero-copy 포인터**를 제공하여 고정된 메모리 공간을 공유할 수 있도록 하였다.

---

## 3.2 Intra-process Communication

ROS 2에서는 단일 프로세스 내 복수 노드의 성능 저하 문제를 해결하기 위해 **IPC(Intra-Process Communication)** 를 제공한다.

**ROS 1 Nodelets와의 비교**

| 항목 | ROS 1 Nodelets | ROS 2 IPC |
|---|---|---|
| 역할 | 단일 프로세스 내 zero-copy 통신 | 단일 프로세스 내 zero-copy 통신 |
| API | 별도 Nodelet API 필요 | 일반 Node 설계와 유사한 API |
| 언어 지원 | C++ | rclcpp만 지원 |

**일반 통신 vs IPC 통신**

일반적으로 퍼블리셔 노드와 서브스크라이버 노드는 별개의 프로세스로 실행된다.

```bash
# 두 노드를 각각 실행하면 별도의 프로세스로 생성됨
$ ros2 run my_first_ros_rclcpp_pkg helloworld_publisher   # Terminal 1
$ ros2 run my_first_ros_rclcpp_pkg helloworld_subscriber  # Terminal 2

$ ps -e
990049 pts/0    00:00:00 helloworld_publ
990225 pts/3    00:00:00 helloworld_subs
```

서로 다른 프로세스이기 때문에 송수신 데이터가 여러 번 메모리에 복사된다. 퍼블리셔와 서브스크라이버가 많을수록 복사 횟수도 증가한다.

반면 IPC를 사용하면 복수의 노드를 **단일 프로세스** 안에서 실행하고, 고정된 메모리 공간에 접근하여 메모리 복사 없이 데이터를 주고받는다(**zero-copy**).

**IPC 사용의 핵심**

| 기법 | 역할 |
|---|---|
| `use_intra_process_comms(true)` | 노드 생성 시 IPC 활성화 |
| `unique_ptr` 메시지 | 하나의 객체에 대해 단 하나만 존재하는 소유권을 부여하는 스마트 포인터. 소유권 이전 시 복사 없이 포인터만 넘어감 |
| `shared_ptr` | 참조 카운팅으로 여러 곳에서 동일한 객체를 공유할 수 있는 스마트 포인터 |
| `weak_ptr` | `shared_ptr`이 가리키는 객체의 소유권 없이 유효성(살아있는지 여부)만 확인할 수 있는 포인터 |
| `std::move` | 객체의 소유권을 다른 변수로 이전하는 C++ 유틸리티. 이전 후 원본 변수는 비어있는 상태가 됨 |
| `SingleThreadedExecutor` | ROS 2의 실행기(Executor) — 등록된 노드들의 콜백을 단일 스레드에서 순차적으로 처리하며, 복수 노드를 하나의 프로세스 안에서 실행 가능하게 함 |

---

## 3.3 데모 코드

> IPC는 **rclcpp만 지원**한다.

ROS 2 데모 패키지(`intra_process_demo`)에 포함된 세 가지 데모를 통해 IPC의 동작과 효과를 확인한다.

---

### 3.3.1 The Two Node Pipeline Demo

**두 노드(Producer → Consumer)를 단일 프로세스로 실행하여 zero-copy를 확인하는 데모**

```cpp
// demos/intra_process_demo/src/two_node_pipeline/two_node_pipeline.cpp

#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/int32.hpp"
using namespace std::chrono_literals;

// 메시지를 생성하는 노드
struct Producer : public rclcpp::Node
{
  Producer(const std::string & name, const std::string & output)
  : Node(name, rclcpp::NodeOptions().use_intra_process_comms(true))  // IPC 활성화
  {
    pub_ = this->create_publisher<std_msgs::msg::Int32>(output, 10);
    std::weak_ptr<std::remove_pointer<decltype(pub_.get())>::type> captured_pub = pub_;

    auto callback = [captured_pub]() -> void {
        auto pub_ptr = captured_pub.lock();
        if (!pub_ptr) { return; }
        static int32_t count = 0;
        std_msgs::msg::Int32::UniquePtr msg(new std_msgs::msg::Int32());  // unique_ptr로 소유권 부여
        msg->data = count++;
        printf("Published message with value: %d, and address: 0x%" PRIXPTR "\n",
          msg->data, reinterpret_cast<std::uintptr_t>(msg.get()));
        pub_ptr->publish(std::move(msg));  // std::move로 소유권 이전
      };
    timer_ = this->create_wall_timer(1s, callback);
  }

  rclcpp::Publisher<std_msgs::msg::Int32>::SharedPtr pub_;
  rclcpp::TimerBase::SharedPtr timer_;
};

// 메시지를 소비하는 노드
struct Consumer : public rclcpp::Node
{
  Consumer(const std::string & name, const std::string & input)
  : Node(name, rclcpp::NodeOptions().use_intra_process_comms(true))  // IPC 활성화
  {
    sub_ = this->create_subscription<std_msgs::msg::Int32>(
      input, 10,
      [](std_msgs::msg::Int32::UniquePtr msg) {  // unique_ptr로 소유권 수신
        printf(" Received message with value: %d, and address: 0x%" PRIXPTR "\n",
          msg->data, reinterpret_cast<std::uintptr_t>(msg.get()));
      });
  }

  rclcpp::Subscription<std_msgs::msg::Int32>::SharedPtr sub_;
};

int main(int argc, char * argv[])
{
  setvbuf(stdout, NULL, _IONBF, BUFSIZ);
  rclcpp::init(argc, argv);
  rclcpp::executors::SingleThreadedExecutor executor;  // 단일 프로세스 executor

  auto producer = std::make_shared<Producer>("producer", "number");
  auto consumer = std::make_shared<Consumer>("consumer", "number");

  executor.add_node(producer);  // 같은 executor에 두 노드 추가
  executor.add_node(consumer);
  executor.spin();

  rclcpp::shutdown();
  return 0;
}
```

**코드 핵심 설명**

| 요소 | 설명 |
|---|---|
| `use_intra_process_comms(true)` | 노드 생성 시 IPC 활성화 선언 |
| `weak_ptr` | `shared_ptr`인 `pub_`의 유효성을 안전하게 확인 |
| `UniquePtr msg` | 메시지에 유일한 소유권 부여 |
| `pub_ptr->publish(std::move(msg))` | 소유권을 퍼블리셔로 이전 (복사 없음) |
| `SingleThreadedExecutor` | 두 노드를 단일 프로세스에서 동작 |

**실행 결과** — 발행·수신 메시지의 메모리 주소가 동일함을 확인

```bash
$ ros2 run intra_process_demo two_node_pipeline
Published message with value: 0, and address: 0x561F33830850
 Received message with value: 0, and address: 0x561F33830850
Published message with value: 1, and address: 0x561F33830850
 Received message with value: 1, and address: 0x561F33830850
Published message with value: 2, and address: 0x561F33830850
 Received message with value: 2, and address: 0x561F33830850
...
```

두 노드가 동일한 프로세스 ID를 가지는 것도 확인할 수 있다.

```bash
$ ps -e
990842 pts/0    00:00:00 two_node_pipeli
```

---

### 3.3.2 The Cyclic Pipeline Demo

**두 노드가 하나의 메시지를 서로 주고받으며 값을 증가시키는 순환 구조 데모**

첫 번째 데모와 달리 메시지를 매 주기마다 새로 생성하지 않고, **단 한 번 생성된 메시지를 두 노드가 반복적으로 주고받는다.**

```cpp
// demos/intra_process_demo/src/cyclic_pipeline/cyclic_pipeline.cpp

// 구독한 메시지를 1초 후 증가시켜 재발행하는 노드
struct IncrementerPipe : public rclcpp::Node
{
  IncrementerPipe(const std::string & name, const std::string & in, const std::string & out)
  : Node(name, rclcpp::NodeOptions().use_intra_process_comms(true))
  {
    pub = this->create_publisher<std_msgs::msg::Int32>(out, 10);
    std::weak_ptr<std::remove_pointer<decltype(pub.get())>::type> captured_pub = pub;

    sub = this->create_subscription<std_msgs::msg::Int32>(
      in, 10,
      [captured_pub](std_msgs::msg::Int32::UniquePtr msg) {
        auto pub_ptr = captured_pub.lock();
        if (!pub_ptr) { return; }
        printf("Received message with value:         %d, and address: 0x%" PRIXPTR "\n",
          msg->data, reinterpret_cast<std::uintptr_t>(msg.get()));
        printf("  sleeping for 1 second...\n");
        if (!rclcpp::sleep_for(1s)) { return; }
        printf("  done.\n");
        msg->data++;  // 값 증가
        printf("Incrementing and sending with value: %d, and address: 0x%" PRIXPTR "\n",
          msg->data, reinterpret_cast<std::uintptr_t>(msg.get()));
        pub_ptr->publish(std::move(msg));  // 동일한 메모리로 재발행
      });
  }

  rclcpp::Publisher<std_msgs::msg::Int32>::SharedPtr pub;
  rclcpp::Subscription<std_msgs::msg::Int32>::SharedPtr sub;
};

int main(int argc, char * argv[])
{
  setvbuf(stdout, NULL, _IONBF, BUFSIZ);
  rclcpp::init(argc, argv);
  rclcpp::executors::SingleThreadedExecutor executor;

  // pipe1: topic1 구독 → topic2 발행
  // pipe2: topic2 구독 → topic1 발행  (순환 구조)
  auto pipe1 = std::make_shared<IncrementerPipe>("pipe1", "topic1", "topic2");
  auto pipe2 = std::make_shared<IncrementerPipe>("pipe2", "topic2", "topic1");
  rclcpp::sleep_for(1s);  // 구독 연결 대기 (race condition 방지 — 두 작업이 거의 동시에 실행될 때 완료 순서에 따라 결과가 달라지는 상황)

  // 최초 메시지 생성 및 발행 (이후에는 이 메시지가 계속 순환됨)
  std::unique_ptr<std_msgs::msg::Int32> msg(new std_msgs::msg::Int32());
  msg->data = 42;
  printf("Published first message with value:  %d, and address: 0x%" PRIXPTR "\n",
    msg->data, reinterpret_cast<std::uintptr_t>(msg.get()));
  pipe1->pub->publish(std::move(msg));

  executor.add_node(pipe1);
  executor.add_node(pipe2);
  executor.spin();

  rclcpp::shutdown();
  return 0;
}
```

**노드 간 토픽 순환 구조**

```
pipe1 (topic1 구독 → topic2 발행)
         ↕
pipe2 (topic2 구독 → topic1 발행)
```

**실행 결과** — 동일한 메모리 주소에서 값만 계속 증가

```bash
$ ros2 run intra_process_demo cyclic_pipeline
Published first message with value:  42, and address: 0x5556A69CAC10
Received message with value:         42, and address: 0x5556A69CAC10
  sleeping for 1 second...
  done.
Incrementing and sending with value: 43, and address: 0x5556A69CAC10
Received message with value:         43, and address: 0x5556A69CAC10
  sleeping for 1 second...
  done.
Incrementing and sending with value: 44, and address: 0x5556A69CAC10
Received message with value:         44, and address: 0x5556A69CAC10
  sleeping for 1 second...
  done.
Incrementing and sending with value: 45, and address: 0x5556A69CAC10
...
```

메모리 주소(`0x5556A69CAC10`)가 변하지 않는 것을 통해 **하나의 메시지 객체가 메모리 복사 없이 순환**함을 확인할 수 있다.

---

### 3.3.3 Image Pipeline Demo

**실용적인 IPC 활용 예제 — 세 노드로 구성된 카메라 이미지 처리 파이프라인**

| 노드 | 역할 |
|---|---|
| `camera_node` | OpenCV로 카메라 입력을 받아 `sensor_msgs/msg/Image`로 발행 |
| `watermark_node` | 수신한 이미지에 텍스트를 추가하여 재발행 |
| `image_view_node` | 수신한 이미지를 `cv::imshow`로 화면에 표시 |

세 노드 모두 IPC를 지원하며, 동일한 프로세스 내에서 이미지를 메모리 복사 없이 전달한다.

**IPC 사용 시 실행**

```bash
# 세 노드를 단일 프로세스에서 실행
$ ros2 run intra_process_demo image_pipeline_all_in_one
```

이미지에 표시되는 텍스트에는 각 노드가 기록한 **PID(프로세스 ID)와 메모리 주소(ptr)** 가 나타난다. 세 노드 모두 동일한 PID와 동일한 메모리 주소를 공유하는 것을 확인할 수 있다.

**IPC 사용 vs 미사용 비교**

```bash
# Terminal 1: IPC 파이프라인 실행 (camera + watermark + image_view → 단일 프로세스)
$ ros2 run intra_process_demo image_pipeline_all_in_one

# Terminal 2: image_view_node만 별도 프로세스로 실행
$ ros2 run intra_process_demo image_view_node
```

| 창 | PID | 메모리 주소 | 설명 |
|---|---|---|---|
| Terminal 1의 창 | 동일 | 동일 | 세 노드 모두 zero-copy로 동작 |
| Terminal 2의 창 | 다름 | 다름 | 별도 프로세스이므로 메모리 복사 발생 |

Terminal 2의 `image_view_node`는 `camera_node`, `watermark_node`와 다른 프로세스에서 실행되므로 이미지를 수신할 때마다 메모리 복사가 발생한다.

**IPC 효과 요약**

| 구성 | 메모리 사용 |
|---|---|
| IPC 사용 (단일 프로세스) | 이미지 크기만큼 고정된 메모리 사용 |
| IPC 미사용 (복수 프로세스) | 노드 수 × 이미지 크기만큼 메모리 복사 발생 |

라이다, 카메라처럼 대용량 데이터를 처리하는 로봇 시스템에서는 처리 노드가 많을수록 IPC의 효과가 더욱 두드러진다.

---

## 정리

| 항목 | 내용 |
|---|---|
| 문제 | 복수 노드 간 통신 시 반복적인 메모리 복사로 성능 저하 |
| ROS 1 해결책 | Nodelets (별도 API, 단일 프로세스 zero-copy) |
| ROS 2 해결책 | IPC — 일반 Node API와 유사한 방식으로 zero-copy 구현 |
| 핵심 요소 | `use_intra_process_comms(true)` + `unique_ptr` + `std::move` + `Executor` |
| 언어 지원 | rclcpp만 지원 (rclpy 미지원) |
| 적용 효과 | 이미지 등 대용량 데이터 파이프라인에서 메모리를 고정 크기로 유지 가능 |

IPC는 코드 구조가 일반 노드 개발과 거의 동일하여 기존 코드를 쉽게 변환할 수 있다. 대용량 센서 데이터를 처리하는 노드가 여러 개 필요한 시스템이라면 IPC 적용을 적극적으로 고려하자.
