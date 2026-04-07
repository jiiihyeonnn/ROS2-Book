# 12장 ROS 2의 좌표 표현

## 12.1 좌표 표현 통일의 필요성

단위 불일치(11장)와 마찬가지로 **좌표계 불일치**도 치명적인 버그를 유발한다.

| 분야 | 기본 좌표계 |
|---|---|
| 로봇 (ROS) | x forward, y left, z up |
| 카메라 (컴퓨터 비전) | z forward, x right, y down |
| IMU - ENU 타입 | x east, y north, z up |
| IMU - NED 타입 | x north, y east, z down |

→ ROS 커뮤니티는 2010년부터 **REP-103**을 통해 좌표 표현 규칙 수립.

---

## 12.2 기본 규칙: 오른손 법칙

모든 좌표계는 **오른손 법칙(Right-hand rule)** 을 따른다.

- 검지(x), 중지(y), 엄지(z) 방향
- 오른손 손가락을 감는 방향 = **정회전(+)** 방향

예: 로봇이 12시 → 9시 방향으로 회전 = **+1.5708 rad**

---

## 12.3 축 방향 (Axis Orientation) 규칙

### 기본 3축

```
x → forward  (Red)
y → left     (Green)
z → up       (Blue)
```

RViz, Gazebo에서 **RGB = XYZ** 순서로 색상 표현.

---

### ENU 좌표

지리적 위치의 단거리 데카르트 표현에 사용.

```
x → East
y → North
z → Up
```

드론, 실외 자율주행 로봇에서 주로 사용.

---

### 접미사 프레임 (비표준 좌표계 구별)

| 접미사 | 좌표계 | 사용 상황 |
|---|---|---|
| `_optical` | z forward, x right, y down | 카메라(컴퓨터 비전) 좌표계 |
| `_ned` | x north, y east, z down | 실외 시스템, NED 기반 센서·지도 |

> 접미사 프레임 사용 시 로봇 좌표계와의 **TF(transform) 변환** 필요.

---

## 12.4 회전 표현 (Rotation Representation) 규칙

| 방식 | 표현 | 특이점 | 권장 여부 |
|---|---|---|---|
| 쿼터니언 (Quaternion) | (x, y, z, w) | 없음 | ✅ 권장 (가장 널리 사용) |
| 회전 매트릭스 (Rotation Matrix) | 3×3 행렬 | 없음 | ✅ 권장 |
| 고정축 roll/pitch/yaw | X, Y, Z축 순서 | 없음 | ✅ 권장 (각속도에 사용) |
| 오일러 각도 yaw/pitch/roll | Z, Y, X축 순서 | **짐벌락** 발생 | ❌ 비권장 |

> **짐벌락(Gimbal Lock)**: 한 축의 회전이 다른 축과 겹쳐 자유도를 잃는 문제.

---

# 정리

| 규칙 | 내용 |
|---|---|
| 기본 좌표 | x forward, y left, z up |
| 회전 방향 | 오른손 법칙 |
| 지리 좌표 | ENU (east-north-up) |
| 비표준 구별 | `_optical`, `_ned` 접미사 |
| 권장 회전 표현 | 쿼터니언, 회전 매트릭스, 고정축 RPY |
| 비권장 | 오일러 각도 (짐벌락 위험) |

좌표 표현 규칙을 숙지하고, 센서 사용 전 반드시 해당 센서의 좌표계를 확인하여 로봇 좌표계로 변환(TF)하자.
