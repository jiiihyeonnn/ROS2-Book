# 11장 ROS 2의 물리량 표준 단위

## 11.1 표준 단위의 필요성

노드 간 데이터 통신 시 **단위 불일치**는 치명적인 버그로 이어질 수 있다.

예: `geometry_msgs/msg/Twist`의 병진 속도를 개발자는 `m/s`로, 사용자가 `cm/s`로 해석하면 의도치 않은 동작 또는 사고 발생.

→ ROS 커뮤니티는 2010년부터 **REP-103**을 통해 표준 단위 규칙을 수립.

---

## 11.2 ROS 2 표준 단위

국제단위계 **SI 단위**와 **SI 유도 단위**를 표준으로 채택.

| 물리량 | 단위 (SI unit) | 물리량 | 단위 (SI derived unit) |
|---|---|---|---|
| length (길이) | meter (m) | angle (평면각) | radian (rad) |
| mass (질량) | kilogram (kg) | frequency (주파수) | hertz (Hz) |
| time (시간) | second (s) | force (힘) | newton (N) |
| current (전류) | ampere (A) | power (일률) | watt (W) |
| | | voltage (전압) | volt (V) |
| | | temperature (온도) | celsius (°C) |
| | | magnetism (자기장) | tesla (T) |

### 주요 조합 단위

| 물리량 | 단위 |
|---|---|
| 병진 속도 | m/s |
| 회전 속도 | rad/s |
| 각도 | rad (degree ❌) |

---

## 11.3 표준 단위 적용 원칙

- **인터페이스 정의**: `ROS 2 interface`에서 데이터 이름·자료형 규정
- **단위 규정**: `REP-103`에서 물리량별 표준 단위 지정
- 표준 단위 사용으로 얻는 이점:
  - 패키지 간 호환성 확보
  - 단위 변환 연산 제거
  - 버그 및 오용 방지

---

# 정리

| 규칙 | 내용 |
|---|---|
| 표준 | SI 단위 + SI 유도 단위 |
| 근거 문서 | REP-103 |
| 각도 | degree ❌ → radian ✅ |
| 속도 | cm/s ❌ → m/s ✅ |
| 온도 | kelvin ❌ → celsius ✅ |

ROS 프로그래밍 시 항상 표준 단위를 사용하여 단위 불일치로 인한 문제를 예방하자.
