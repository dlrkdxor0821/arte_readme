# ABA — 아키텍처 & 프로젝트 Scaffold 결정 노트

> 작성일: 2026-06-21 · 대상: Team Arte / **ABA (Arte Book Asistance)** — 도서관 책 배달·수거 모바일 매니퓰레이터
> 상태: 설계 결정 메모 (구현 전 합의용)

---

## 1. 시스템 개요

**모바일 매니퓰레이터** = 주행 AMR + 로봇팔. RMF(Open-RMF)를 관제(fleet management)로 얹어 작업을 오케스트레이션.

| 구성 | 역할 | 패키지(예) |
|---|---|---|
| 주행 (mobile) | AMR nav2 주행 | `libi_drive` |
| 팔 (arm) | 매니퓰레이션(상/하차) | `libi_handy` |
| 관제 (fleet) | RMF 교통·태스크 오케스트레이션 | `libi_rmf` |
| UI ×3 | PyQt(`libi_gui`) + 웹(`library_member`, `librarian`) | — |

---

## 2. SW 아키텍처 (3계층)

```
[Client]    Library Member / Librarian 브라우저  ──HTTP──┐
[Server]    ABA Service + ABA DB / Libi Service / AI Service
              │ ROS2(DDS)                         │ UDP(Image→AI)
[Equipment] Libi Drive Board (Controller+GUI)  ──DDS──  Libi Handy Board (Handy Controller)
```
- 통신: HTTP(클라↔서버), ROS2/DDS(서비스↔컨트롤러, drive↔handy), UDP(Image→AI), TCP(서버간)
- RMF 위치: **Libi Service 와 Libi Controller 사이의 "이동 지휘자"** (서버/코디네이터에서 실행, ROS2 로 drive 에 명령)

---

## 3. 폴더 구조 (Scaffold)

README 컨벤션(`fleet_management / robot_arm / mobile_robot / gui`)을 따르되 패키지 단위로 상세화.

```
ABA/                              # monorepo 루트
├── src/
│   ├── mobile_robot/             # [Equipment] 주행 온보드 ROS2
│   │   └── libi_drive/           #   nav2 스택
│   ├── robot_arm/                # [Equipment] 팔 온보드 ROS2
│   │   └── libi_handy/           #   MoveIt 등
│   │
│   ├── fleet_management/         # ★ RMF 관제 (robot 컨트롤러와 분리)
│   │   └── libi_rmf/
│   │       ├── libi_rmf_maps/            building.yaml → world/nav graph   (ament_cmake)
│   │       ├── libi_rmf_fleet_adapter/   RMF↔nav2 통역  ★핵심             (ament_cmake / C++)
│   │       ├── libi_rmf_bridge/          domain_bridge 설정                (ament_cmake)
│   │       ├── libi_rmf_tasks/           태스크 + 팔 perform_action 훅      (ament_python)
│   │       └── libi_rmf_bringup/         launch 조립                       (ament_cmake)
│   │
│   ├── service/                  # [Server] 백엔드
│   │   ├── libi_service/         #   ROS2 ↔ 로봇
│   │   ├── aba_service/          #   웹 백엔드(클라이언트)
│   │   └── ai_service/           #   AI(비전, UDP 수신)
│   │
│   └── gui/                      # [Client] UI
│       ├── libi_gui/             #   PyQt (Drive Board 탑재)
│       ├── library_member/       #   웹 (회원)
│       └── librarian/            #   웹 (사서)
│
├── docs/                         # 문서/이미지/다이어그램
├── scripts/                      # 빌드/운영/테스트
├── db/                           # ABA DB 스키마/마이그레이션
├── tests/
├── pyproject.toml                # 비-ROS Python 의존성 (extras 그룹)
└── docker-compose.yml
```

---

## 4. 핵심 설계 결정

### 4-1. RMF 는 `fleet_management` 에 (컨트롤러와 분리)
- `mobile_robot/`·`robot_arm/` = **로봇 보드 온보드 제어** (로봇 위에서 실행)
- `fleet_management/libi_rmf` = **로봇들을 지휘하는 오케스트레이션** (서버/코디네이터에서 실행)
- 성격이 다르므로 분리. RMF 는 `mobile_robot`/`robot_arm` 에 **코드 의존하지 않고 ROS2 토픽/액션으로만** 통신 → 컨트롤러들이 RMF 없이도 단독 동작.
- RMF 가 직접 지휘하는 건 **주행(libi_drive)**. 팔은 태스크 도착 후 **`perform_action`** 으로 트리거.

### 4-2. 언어 — fleet 는 C++, 나머지는 자유 (혼합 OK)
- RMF fleet adapter 는 **C++ EasyFullControl** 사용 (`rmf_fleet_adapter/agv/EasyFullControl.hpp` + `librmf_fleet_adapter.so`). Python 은 이 C++ 의 바인딩이라, C++ 가 "원본".
- 워크스페이스 레벨 혼합 OK: `fleet`=C++, `tasks`/`service`/`gui`=Python. ROS2 로 통신하므로 무관.
- **패키지 1개 = 빌드타입 1개** 유지 (ament_cmake=C++, ament_python=Python).

### 4-3. 의존성 관리 — pyproject.toml + ROS 분리 (2층)
```
ROS2 패키지 (C++ fleet, Python tasks)  →  package.xml + rosdep + colcon
비-ROS Python (service/gui/scripts)     →  루트 pyproject.toml (+ extras)
```
- `pyproject.toml` 권장 (requirements.txt 단독 ❌). 배포 대상별 extras 로 그룹화:
  ```toml
  [project.optional-dependencies]
  ai  = ["ultralytics", "torch"]   # AI 서버
  gui = ["PyQt5"]                  # drive board
  web = ["fastapi", "uvicorn"]     # 백엔드
  dev = ["pytest", "ruff"]
  ```
  설치: `pip install -e ".[ai]"` 등.
- ⚠️ **주의점**: ROS 노드는 system python3 에서 돔 → ROS 노드가 쓰는 Python 패키지는 **system python 에 설치**, service 용은 pyproject. 두 풀을 섞지 말 것.
- 재현성 필요 시(도커) `pip freeze`/`uv lock` 잠금 파일만 추가. 선언은 pyproject.

---

## 5. RMF ↔ 주행(nav2) 통합 메모

| 항목 | 결정 |
|---|---|
| 주행 스택 | nav2 표준 → RMF EasyFullControl 연동 (우호적) |
| URDF | **변경 불필요**, 기존 로봇 URDF 재사용. RMF 는 footprint 반경만 fleet config 에 |
| nav2 주행권 | RMF=이동/교통, 로컬 행동(추종/도킹)은 분리 → 충돌 방지 |
| 도메인 | 네임스페이스 없이 **domain_bridge** 로 로봇 도메인↔RMF 도메인 연결, CycloneDDS |

⚠️ **함정 2가지** (초반 정리):
1. **action 도메인 브릿지** — domain_bridge 는 토픽/서비스는 OK, NavigateToPose(action)는 까다로움 → **fleet adapter 를 로봇 도메인에서 실행**해 우회 (RMF 표준 토픽만 브릿지).
2. **RMF ↔ 로컬 행동 주행권 충돌** — 둘 다 nav2 를 건드리면 충돌 → 역할 분리.

---

## 6. 권장 진행 순서

1. **시뮬 1대 + 같은 도메인**에서 RMF fleet adapter PoC (domain_bridge 없이, 변수 최소화)
2. **domain_bridge 로 도메인 분리** 추가 (5장 우회법 적용)
3. **다중 로봇** 확장 (traffic negotiation 검증)
4. **팔 작업을 perform_action 으로 통합** (상/하차)
5. **실물 이관** — 시뮬 노드만 교체, RMF/nav graph/태스크 재사용

> 가장 큰 함정은 5장의 2가지. 이것만 초반에 설계로 정리하면 나머지는 rmf_demos 패턴을 따라가면 된다.
