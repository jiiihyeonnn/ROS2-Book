# ROS 2 QoS — 통신 품질을 설계하다

## 📊 소스 분석 요약
- 핵심 주제: ROS 2 QoS 옵션 6가지, RMW QoS Profile, 실습을 통한 동작 확인
- 핵심 메시지: History·Reliability·Durability 3가지를 기본으로 설정하고, 필요에 따라 Deadline·Lifespan·Liveliness를 추가 활용
- 대상 청중: ROS 2 통신 설계 학습자 및 DDS 기반 개발자
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
### 슬라이드 1: ROS 2 QoS

**슬라이드 내용**
- **ROS 2 QoS**
- 통신 품질을 설계하다
- 24장 · ROS 2 입문 시리즈

**시각적 제안**
- 레이아웃: 중앙 정렬, 타이틀 중심
- 시각 요소: 네트워크 신호 강도 아이콘 + DDS 연결 일러스트
- 강조: 부제를 Accent 컬러로 표현

---
### 슬라이드 2: 목차

**슬라이드 내용**
- QoS란 무엇인가
- 6가지 QoS 옵션
- RMW QoS Profile
- Topic · Service · Action QoS 설정
- 실습: Reliability / Durability / Deadline / Lifespan / Liveliness

**시각적 제안**
- 레이아웃: 번호 리스트 + 아이콘
- 시각 요소: 각 항목에 매칭 아이콘 배치
- 강조: 현재 장(24장) 하이라이트

---
### 슬라이드 3: QoS란 무엇인가

**슬라이드 내용**
- DDS 기반 ROS 2가 제공하는 통신 품질 제어 옵션
- RMW(ROS MiddleWare): DDS와 ROS 2 사이 추상화 레이어
- DDS 구현체(FastDDS, CycloneDDS)를 교체해도 코드 동일하게 동작
- 데이터 신뢰성·보관 방식·주기 검사 등을 세밀하게 설정 가능

**시각적 제안**
- 레이아웃: 좌측 설명 + 우측 ROS 2 / RMW / DDS 레이어 구조도
- 시각 요소: 3단 레이어 다이어그램
- 강조: "RMW" 키워드를 Sub Accent 컬러로 표현

---
### 슬라이드 4: 6가지 QoS 옵션

**슬라이드 내용**
- **History**: 메시지 보관 개수 (`KEEP_LAST` / `KEEP_ALL`)
- **Reliability**: 전송 방식 (`RELIABLE` / `BEST_EFFORT`)
- **Durability**: 구독 전 데이터 처리 (`TRANSIENT_LOCAL` / `VOLATILE`)
- **Deadline**: 정해진 주기 내 미수신 시 EventCallback
- **Lifespan**: 기한 초과 데이터 자동 삭제
- **Liveliness**: 노드·토픽 생존 확인 주기

**시각적 제안**
- 레이아웃: 6개 카드 그리드 (2×3)
- 시각 요소: 각 옵션에 아이콘 + 핵심 옵션값
- 강조: 기본 3가지(History·Reliability·Durability)를 Accent 컬러로 강조

---
### 슬라이드 5: RMW QoS Profile

**슬라이드 내용**
- 자주 쓰는 QoS 조합을 미리 정의한 프리셋
- **Default**: RELIABLE / KEEP_LAST / depth 10 / VOLATILE
- **Sensor Data**: BEST_EFFORT / KEEP_LAST / depth 5 / VOLATILE
- **Service**: RELIABLE / KEEP_LAST / depth 10 / VOLATILE
- **Parameter Events**: RELIABLE / KEEP_LAST / depth 1,000 / VOLATILE

**시각적 제안**
- 레이아웃: 비교 테이블 (Profile 종류 × QoS 옵션)
- 시각 요소: 셀 배경 색상으로 차이점 강조
- 강조: Default와 Sensor Data의 Reliability 차이를 Accent 컬러로 표현

---
### 슬라이드 6: Topic QoS 설정

**슬라이드 내용**
- 기본값: RMW Default Profile (RELIABLE / KEEP_LAST / depth 10 / VOLATILE)
- RCLPY: `QoSProfile(reliability=..., history=..., depth=..., durability=...)`
- RCLCPP: `rclcpp::QoS(KeepLast(10)).reliable().durability_volatile()`
- 명시적으로 4가지 옵션 작성을 권장 (가독성·협업)

**시각적 제안**
- 레이아웃: 2열 코드 비교 (RCLPY / RCLCPP)
- 시각 요소: 코드 스니펫 박스
- 강조: QoS 설정 라인을 Sub Accent 컬러로 하이라이트

---
### 슬라이드 7: 실습 — Reliability 비교

**슬라이드 내용**
- `tc` 유틸리티로 10% 패킷 손실 인위적으로 설정
- `RELIABLE`: 재전송으로 손실 없이 전부 수신 (단, 잠시 멈춤 발생)
- `BEST_EFFORT`: 손실 허용, 끊김 없이 진행 (일부 메시지 유실)
- 테스트 완료 후 반드시 `tc qdisc delete`로 손실 설정 해제

**시각적 제안**
- 레이아웃: 2열 비교 (RELIABLE / BEST_EFFORT)
- 시각 요소: 수신 메시지 흐름 다이어그램 (일부 칸이 비어있는 BEST_EFFORT)
- 강조: 유실된 메시지를 회색으로 표현

---
### 슬라이드 8: 실습 — Durability / Deadline / Lifespan

**슬라이드 내용**
- **Durability `TRANSIENT_LOCAL`**: 늦게 시작한 서브스크라이버가 depth만큼 이전 데이터 수신
- **Deadline**: 설정 주기 초과 시 Publisher·Subscriber 양쪽에 EventCallback 발생
- **Lifespan**: Lifespan 시간 초과 데이터는 큐에서 삭제 → 늦게 구독해도 오래된 데이터 미수신

**시각적 제안**
- 레이아웃: 3개 타임라인 다이어그램
- 시각 요소: 시간 축 위에 데이터 수신 구간 표시
- 강조: 각 옵션의 "임계점"을 Accent 컬러로 표현

---
### 슬라이드 9: 실습 — Liveliness / QoS 불일치

**슬라이드 내용**
- **`AUTOMATIC`**: 노드 프로세스 종료 시 `alive_count` 감소 감지
- **`MANUAL_BY_TOPIC`**: 노드가 살아있어도 발행 중단 시 `not_alive_count` 증가 감지
- **RxO (Requested by Offered)**: 서브스크라이버가 더 엄격한 QoS 요구 시 비호환 이벤트
- QoS 불일치 발생 시 통신이 연결되지 않음 → 설정 전 호환성 확인 필수

**시각적 제안**
- 레이아웃: 상단 Liveliness 비교 + 하단 QoS 불일치 예시
- 시각 요소: Publisher ↔ Subscriber QoS 호환 여부 화살표 다이어그램
- 강조: 비호환 케이스를 붉은 계열로 표현

---
### 슬라이드 10: 핵심 정리

**슬라이드 내용**
- History + Reliability + Durability — 기본 3가지를 용도에 맞게 설정
- Deadline — 타이밍 보장이 필요한 경우 추가
- Lifespan — 오래된 데이터를 걸러야 하는 경우 추가
- Liveliness — 노드 장애 감지가 필요한 경우 추가
- **QoS 설정은 퍼블리셔와 서브스크라이버 양쪽이 호환되어야 통신이 연결된다**

**시각적 제안**
- 레이아웃: 중앙 정렬, 4개 핵심 카드
- 시각 요소: 체크 아이콘 + 핵심 문장
- 강조: 마지막 문장을 대형 인용 스타일로 표현
