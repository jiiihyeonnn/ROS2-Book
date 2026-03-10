# ROS 2로 시작하는 로봇 프로그래밍: 11주 집중 스터디 플랜 (Ubuntu 24.04)

이 저장소는 **[ROS 2로 시작하는 로봇 프로그래밍](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=276616576)** (표윤석, 임태훈 저)을 기반으로 한 11주 과정의 스터디 계획을 담고 있습니다.

## 📚 도구 및 환경
*   **OS:** **Ubuntu 24.04 LTS (Noble Numbat)**
*   **ROS 2:** **Jazzy Jalisco (LTS)**
*   **Language:** Python 3.12+

---

## 📅 주차별 스터디 플랜

### 1단계: ROS 2 기초 및 환경 구축
*   **1주차: ROS 2 소개 및 설치**
    *   학습 내용: ROS 2의 탄생 배경, ROS 1과의 차이점, **Ubuntu 24.04에서 Jazzy 설치**.
    *   실습: 빌드 도구(`colcon`) 및 환경 변수 설정.
*   **2주차: ROS 2 주요 개념 (1)**
    *   학습 내용: 노드(Node), 패키지(Package)의 이해.
    *   실습: `turtlesim`을 이용한 기본 노드 실행 및 CLI 도구 사용법.
*   **3주차: ROS 2 주요 개념 (2)**
    *   학습 내용: 토픽(Topic) 통신 - 발행(Publish)과 구독(Subscribe).
    *   실습: 간단한 Talker/Listener 노드 작성 (Python).

### 2단계: 핵심 통신 인터페이스
*   **4주차: 서비스(Service)와 파라미터(Parameter)**
    *   학습 내용: 요청-응답(Request-Response) 방식의 서비스, 노드 설정 관리를 위한 파라미터.
    *   실습: 서비스 서버와 클라이언트 구현.
*   **5주차: 액션(Action)**
    *   학습 내용: 비동기 양방향 통신, 피드백이 있는 장기 실행 작업.
    *   실습: Action 서버 및 피드백 처리 구현.
*   **6주차: ROS 2 인터페이스(Interface) 및 런치(Launch)**
    *   학습 내용: 커스텀 메시지(`.msg`), 서비스(`.srv`), 액션(`.action`) 정의 및 여러 노드 실행을 위한 Launch 파일.
    *   실습: 커스텀 인터페이스 정의 및 적용.

### 3단계: 미들웨어 및 고급 프로그래밍
*   **7주차: DDS(Data Distribution Service)와 QoS**
    *   학습 내용: ROS 2의 핵심 미들웨어 DDS 구조와 통신 품질 설정을 위한 QoS (Quality of Service).
    *   실습: 네트워크 상황에 따른 QoS 설정 변경 및 테스트.
*   **8주차: ROS 2 심화 프로그래밍 (Lifecycle & Component)**
    *   학습 내용: 노드의 상태를 관리하는 Lifecycle 노드, 실행 효율을 높이는 Component.
    *   실습: Lifecycle 노드 상태 전이 프로그래밍.

### 4단계: 실전 로봇 활용 및 마무리
*   **9주차: TF2 (Transform Library)와 로봇 모델링**
    *   학습 내용: 좌표계 변환(`tf2`)의 개념, URDF를 이용한 로봇 모델링 기초.
    *   실습: 간단한 로봇 팔 또는 이동 로봇의 좌표계 확인.
*   **10주차: 터틀봇3 (TurtleBot3) 시뮬레이션**
    *   학습 내용: 가상 환경(Gazebo)에서의 로봇 제어, 센서 데이터 확인.
    *   실습: Gazebo를 이용한 터틀봇3 주행 제어.
*   **11주차: 종합 프로젝트 및 정리**
    *   학습 내용: 그동안 배운 개념을 종합하여 간단한 시나리오 구현.
    *   실습: 자율 주행 맛보기 또는 센서 기반 회피 주행 프로젝트.

---

## 🎬 슬라이드 자동 생성 (`/notebooklm-slide`)

Gemini의 **NotebookLM Slide Creator** 워크플로우를 사용하면 학습 문서를 **10장 발표 슬라이드**로 자동 변환할 수 있습니다.

### 사용법

Gemini 채팅에서 아래와 같이 입력합니다:

```
@/notebooklm-slide 사용해서 study/robert/01_ros2_intro.md 파일을 01ppt.md로 작성해 주세요
```

### 출력 구성

| 섹션 | 내용 |
|------|------|
| 📊 소스 분석 요약 | 핵심 주제, 메시지, 대상 청중, 포맷, 시간 |
| 🎨 디자인 컨셉 | 색상 팔레트, 타이포그래피, 레이아웃 가이드 |
| 📝 슬라이드 스크립트 | 슬라이드별 내용 + 시각적 제안 (10장) |

### 생성된 슬라이드

| 소스 파일 | 슬라이드 파일 |
|-----------|---------------|
| `study/robert/01_ros2_intro.md` | [`01ppt.md`](study/robert/01ppt.md) |
| `study/robert/02_ros2_required_knowledge.md` | [`02ppt.md`](study/robert/02ppt.md) |
| `study/robert/03_ros2_dev_environment_setup.md` | [`03ppt.md`](study/robert/03ppt.md) |
| `study/robert/04_why_ros2.md` | [`04ppt.md`](study/robert/04ppt.md) |

---

## 🔗 관련 리소스
*   [ROS 2 공식 문서 (Jazzy)](https://docs.ros.org/en/jazzy/)
*   [ROBOTIS TurtleBot3 Manual](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)
*   [저자 운영 카페 (Oroca)](https://cafe.naver.com/openrt)

---
*본 스터디 플랜은 Ubuntu 24.04와 ROS 2 Jazzy 환경에 맞춰 최적화되었으며, 각 장의 핵심 주제를 주차별로 재구성하였습니다.*
