# ROS 2 Logging — 디버깅의 시작

## 📊 소스 분석 요약
- 핵심 주제: ROS 2 로깅 시스템의 개념, 설정, 예제 코드
- 핵심 메시지: 로그는 가장 기본적인 디버깅 도구이며 사용자가 노드 실행 시 가장 먼저 만나는 정보
- 대상 청중: ROS 2 입문자 및 노드 개발 학습자
- 포맷: Presenter Slides
- 총 슬라이드 수: 10장
- 예상 발표 시간: 15분

## 🎨 디자인 컨셉: Friendly Learning

**컨셉 요약**: 교육용 콘텐츠에 최적화된 친근하고 깔끔한 디자인

### 색상 팔레트
| 용도 | 색상 | HEX |
|:---:|---|---|
| 배경 (Primary) | ██ | `#E3F2FD` |
| 텍스트 (Primary) | ██ | `#212121` |
| 강조 (Accent) | ██ | `#FF7043` |
| 서브 강조 | ██ | `#29B6F6` |
| 배경 (Secondary) | ██ | `#FFFFFF` |

### 타이포그래피
| 용도 | 폰트 | 크기 | 두께 |
|:---:|---|---|---|
| 제목 (H1) | Nunito | 44pt | Bold |
| 부제 (H2) | Nunito | 28pt | SemiBold |
| 본문 | Quicksand | 18pt | Regular |
| 캡션/라벨 | Quicksand | 14pt | Medium |

### 레이아웃 가이드
- **여백**: 상하좌우 최소 48px
- **정렬**: 좌측 정렬
- **이미지 스타일**: 둥근 모서리 12px + 소프트 그림자
- **아이콘**: 듀오톤 (Accent + Sub Accent)

### 비주얼 무드
- 친근한, 깔끔한, 교육적, 구조적, 모던
- 참고 스타일: Google I/O 프레젠테이션 / Pitch.com

---

## 📝 슬라이드 스크립트

---
### 슬라이드 1: ROS 2 Logging

**슬라이드 내용**
- **ROS 2 Logging**
- 디버깅의 시작
- 21장 · ROS 2 입문 시리즈

**시각적 제안**
- 레이아웃: 중앙 정렬, 타이틀 중심
- 시각 요소: 터미널 출력 아이콘 + 로그 레벨 일러스트
- 강조: 부제를 Accent 컬러로 표현

---
### 슬라이드 2: 목차

**슬라이드 내용**
- 로그(Log)란 무엇인가
- 로그 설정 (디렉토리 · 수준 · 형식 · 색상 · 스트림)
- 로그 매크로 함수
- RCLCPP 예제 코드
- RCLPY 예제 코드

**시각적 제안**
- 레이아웃: 번호 리스트 + 아이콘
- 시각 요소: 각 항목에 매칭 아이콘 배치
- 강조: 현재 장(21장) 하이라이트

---
### 슬라이드 3: ROS 2 로그란

**슬라이드 내용**
- 초기화 없이 바로 사용 가능한 단순 인터페이스
- 5가지 로그 수준: DEBUG / INFO / WARN / ERROR / FATAL
- `printf`와 `stream` 스타일 모두 지원
- 파일명·줄 수·노드 이름 등 상세 정보 포함 가능
- 멀티쓰레드 환경에서도 안전 (쓰레드 세이프)

**시각적 제안**
- 레이아웃: 좌측 텍스트 + 우측 터미널 출력 예시
- 시각 요소: 로그 출력 스크린샷 스타일 박스
- 강조: "5가지 로그 수준" 키워드 하이라이트

---
### 슬라이드 4: 로그 수준 (Log Level)

**슬라이드 내용**
- DEBUG — 개발 단계 세부 정보
- INFO — 일반적인 정보 출력
- WARN — 잠재적 문제 경고
- ERROR — 오류 발생 알림
- FATAL — 치명적 오류, 프로그램 종료 수준

**시각적 제안**
- 레이아웃: 5단계 수직 흐름 카드
- 시각 요소: 각 수준에 색상 배지 (DEBUG=회색, INFO=파랑, WARN=노랑, ERROR=주황, FATAL=빨강)
- 강조: 수준 간 계층 구조를 화살표로 표현

---
### 슬라이드 5: 로그 설정 — 수준 변경

**슬라이드 내용**
- **코드에서 설정**: `RCLCPP_INFO(get_logger(), ...)` 매크로 직접 작성
- **CLI로 설정**: `--ros-args --log-level debug` 실행 시 인자
- **외부 노드로 설정**: 서비스 콜로 런타임 중 변경

```bash
$ ros2 run logging_demo logging_demo_main --ros-args --log-level debug
```

**시각적 제안**
- 레이아웃: 3열 비교 카드 (코드 / CLI / 외부)
- 시각 요소: 각 설정 방법에 코드 스니펫 포함
- 강조: CLI 방식을 Accent 컬러로 표현

---
### 슬라이드 6: 로그 출력 형식 커스터마이징

**슬라이드 내용**
- `RCUTILS_CONSOLE_OUTPUT_FORMAT` 환경변수로 형식 변경
- `RCUTILS_COLORIZED_OUTPUT=1` 으로 색상 강제 활성화
- Foxy 이후: 모든 로그 수준이 기본적으로 `stderr`로 출력
- `RCUTILS_LOGGING_USE_STDOUT=1` 로 stdout으로 변경 가능

**시각적 제안**
- 레이아웃: 좌측 환경변수 목록 + 우측 출력 예시 박스
- 시각 요소: 터미널 스타일 코드 블록
- 강조: 권장 환경변수 설정을 박스로 묶어 표현

---
### 슬라이드 7: RCLCPP 로그 매크로 함수

**슬라이드 내용**
- `RCLCPP_${SEVERITY}` — 기본 출력
- `RCLCPP_${SEVERITY}_ONCE` — 딱 한 번만 출력
- `RCLCPP_${SEVERITY}_THROTTLE` — 특정 주기마다 출력
- `RCLCPP_${SEVERITY}_EXPRESSION` — 조건이 참일 때만 출력
- `RCLCPP_${SEVERITY}_FUNCTION` — 함수가 참일 때만 출력

**시각적 제안**
- 레이아웃: 표 형식 카드
- 시각 요소: 각 매크로 옆에 간단한 사용 예시 코드
- 강조: `_ONCE`, `_THROTTLE` 을 Sub Accent 컬러로 표현

---
### 슬라이드 8: RCLCPP 예제 — 핵심 동작

**슬라이드 내용**
- 500ms 타이머로 `on_timer` 콜백 반복 실행
- `RCLCPP_INFO_ONCE` — 첫 호출 시만 출력
- `RCLCPP_DEBUG_FUNCTION` — 함수가 true일 때만 DEBUG 출력
- 5500ms 후 `one_shot_timer_`가 로그 수준을 DEBUG로 변경
- count > 15 → WARN, count == 0 나눗셈 → ERROR

**시각적 제안**
- 레이아웃: 타임라인 다이어그램 + 우측 출력 결과
- 시각 요소: 시간 흐름에 따른 로그 수준 변화 그래프
- 강조: DEBUG 활성화 시점을 Accent 컬러로 표시

---
### 슬라이드 9: RCLPY 예제와 RCLCPP 비교

**슬라이드 내용**
- RCLPY는 `get_logger().info()` / `.warn()` / `.error()` 사용
- `_FUNCTION`, `_EXPRESSION` 매크로 없음 → 직접 조건문으로 대체
- `once=True` 파라미터로 RCLCPP의 `_ONCE`와 동일한 동작
- RCLCPP와 달리 `one_shot_timer_` 없어 DEBUG 자동 전환 없음

**시각적 제안**
- 레이아웃: 2열 비교 (RCLCPP / RCLPY)
- 시각 요소: 코드 스니펫 + 차이점 강조 표시
- 강조: 차이점을 붉은 계열 서브 컬러로 표현

---
### 슬라이드 10: 핵심 정리

**슬라이드 내용**
- 로그 수준: DEBUG < INFO < WARN < ERROR < FATAL
- 코드 설정: `RCLCPP_${SEVERITY}` 매크로 / `get_logger().info()`
- 출력 형식: `RCUTILS_CONSOLE_OUTPUT_FORMAT` 환경변수
- **로그는 사용자가 가장 먼저 만나는 정보 — 적절한 수준으로 채워야 한다**

**시각적 제안**
- 레이아웃: 중앙 정렬, 4개 핵심 카드
- 시각 요소: 체크 아이콘 + 핵심 문장
- 강조: 마지막 문장을 대형 인용 스타일로 표현
