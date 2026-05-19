# ROS 2 CLI — 터미널로 제어하는 ROS 2

## 📊 소스 분석 요약
- 핵심 주제: ROS 2 CLI 명령어 체계와 실습, alias 활용, 신규 CLI 작성
- 핵심 메시지: CLI는 외우는 것이 아니라 탭 자동완성과 -h 옵션으로 체득하는 것
- 대상 청중: ROS 2 입문자 및 CLI 실습 학습자
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
### 슬라이드 1: ROS 2 CLI

**슬라이드 내용**
- **ROS 2 CLI**
- 터미널로 제어하는 ROS 2
- 22장 · ROS 2 입문 시리즈

**시각적 제안**
- 레이아웃: 중앙 정렬, 타이틀 중심
- 시각 요소: 터미널 창 아이콘 + 명령어 입력 일러스트
- 강조: 부제를 Accent 컬러로 표현

---
### 슬라이드 2: 목차

**슬라이드 내용**
- ROS 2 CLI 명령어 체계
- 실행·정보·보조 명령어 분류
- turtlesim 기반 CLI 실습
- alias로 빠른 실행
- ROS arguments 활용
- 신규 CLI 작성 방법

**시각적 제안**
- 레이아웃: 번호 리스트 + 아이콘
- 시각 요소: 각 항목에 매칭 아이콘 배치
- 강조: 현재 장(22장) 하이라이트

---
### 슬라이드 3: CLI 명령어 구조

**슬라이드 내용**
- 기본 구조: `ros2 [verbs] [sub-verbs] [options] [arguments]`
- **실행 명령어**: `run` (단일 노드), `launch` (런치 파일)
- **정보 명령어**: `node`, `topic`, `service`, `action`, `interface`, `param`, `bag`
- **보조 명령어**: `daemon`, `doctor`, `lifecycle`, `component`, `security`

**시각적 제안**
- 레이아웃: 중앙 구조도 + 카테고리별 색상 구분
- 시각 요소: 트리 다이어그램으로 verbs 계층 표현
- 강조: 3개 카테고리를 각기 다른 색상으로 구분

---
### 슬라이드 4: 핵심 CLI 실습 — 노드·토픽

**슬라이드 내용**
- `ros2 node list` / `ros2 node info <node>` — 노드 목록·정보
- `ros2 topic list -t` — 토픽 목록 + 타입
- `ros2 topic echo <topic>` — 토픽 데이터 출력
- `ros2 topic hz` / `bw` — 주기·대역폭 측정
- `ros2 topic pub --once <topic> <type> <data>` — 직접 발행

**시각적 제안**
- 레이아웃: 좌측 명령어 목록 + 우측 터미널 출력 예시
- 시각 요소: turtlesim 실행 중인 출력 결과 스크린샷 스타일
- 강조: `echo`, `pub` 명령어를 Accent 컬러로 표현

---
### 슬라이드 5: 핵심 CLI 실습 — 서비스·액션·인터페이스

**슬라이드 내용**
- `ros2 service call <srv> <type> <data>` — 서비스 직접 요청
- `ros2 action send_goal <action> <type> <goal>` — 액션 목표 전달
- `ros2 interface show <type>` — 인터페이스 구조 확인
- `ros2 interface proto <type>` — 프로토타입 출력 (pub 데이터 작성에 활용)

**시각적 제안**
- 레이아웃: 3열 카드 (서비스 / 액션 / 인터페이스)
- 시각 요소: 각 카드에 실행 예시 + 출력 결과
- 강조: `call`, `send_goal` 을 Sub Accent 컬러로 표현

---
### 슬라이드 6: 핵심 CLI 실습 — 파라미터·bag

**슬라이드 내용**
- `ros2 param get / set / list / dump` — 파라미터 읽기·쓰기·저장
- `ros2 bag record -a` — 모든 토픽 저장
- `ros2 bag info <bag>` — 저장된 bag 정보 확인
- `ros2 bag play <bag>` — bag 재생

**시각적 제안**
- 레이아웃: 2열 비교 (param / bag)
- 시각 요소: yaml 파일 내용 + bag 정보 출력 예시
- 강조: `dump`, `record` 명령어를 Accent 컬러로 표현

---
### 슬라이드 7: 유용한 보조 명령어

**슬라이드 내용**
- `ros2 daemon` — CLI 응답 속도 향상용 캐싱 데몬
- `ros2 doctor -rf` — 환경 문제 진단 (실패 항목만)
- `ros2 lifecycle set <node> configure` — 노드 상태 전환
- `ros2 component load <container> <pkg> <type>` — 컴포넌트 동적 로드

**시각적 제안**
- 레이아웃: 4분할 카드 레이아웃
- 시각 요소: 각 명령어 역할을 아이콘 + 한 줄 설명으로 표현
- 강조: `doctor`를 "환경 진단 도구"로 강조

---
### 슬라이드 8: alias로 CLI 빠르게 사용하기

**슬라이드 내용**
- `~/.bashrc`에 alias 등록 → 짧은 단어로 실행
- `alias rt='ros2 topic list'` / `re='ros2 topic echo'`
- `alias tn='ros2 run turtlesim turtlesim_node'`
- 자주 쓰는 조합을 미리 등록하면 개발 속도 향상

**시각적 제안**
- 레이아웃: 좌측 alias 목록 + 우측 실행 결과 비교
- 시각 요소: Before(긴 명령어) → After(alias) 변환 흐름
- 강조: alias 단축어를 Accent 컬러로 표현

---
### 슬라이드 9: --ros-args 로 실행 시 동적 설정

**슬라이드 내용**
- `-r __ns:=<namespace>` — 네임스페이스 설정
- `-r __node:=<name>` — 노드 이름 변경
- `-r <원래이름>:=<바꿀이름>` — 토픽/서비스/액션 재매핑
- `-p <파라미터>:=<값>` — 파라미터 값 설정
- `--params-file <yaml>` — yaml 파라미터 파일 적용

**시각적 제안**
- 레이아웃: 표 형식 + 하단 통합 예시 코드 블록
- 시각 요소: 하나의 실행 명령에 여러 args를 조합한 예시
- 강조: 재매핑 옵션을 Sub Accent 컬러로 표현

---
### 슬라이드 10: 핵심 정리

**슬라이드 내용**
- `run` / `launch` — 노드 실행
- `topic`, `service`, `action` — 통신 조회·테스트
- `param`, `bag` — 데이터 관리
- `doctor`, `lifecycle`, `component` — 환경 진단·고급 제어
- **탭 자동완성 + `-h` 옵션으로 체득하는 것이 핵심**

**시각적 제안**
- 레이아웃: 중앙 정렬, 4개 핵심 카드
- 시각 요소: 체크 아이콘 + 핵심 문장
- 강조: 마지막 문장을 대형 인용 스타일로 표현
