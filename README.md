# Team Arte



<div align="center">

<img src="image/Arte_logo2.png" alt="Arte Logo" width="1440"/>

###  프로젝트 명 : ABA (Arte Book Asistance)

**[ Presentation ](#)** | **[ Demo ](#)**

</div>

---

## 🚀 프로젝트 개요

> 프로젝트에 대한 한두 문장 요약을 작성해주세요.

### 🛠 기술 스택 (Tech Stack)

| 구분 | 기술 |
|------|------|
| **Language** | ![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white) ![C++](https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white) |
| **Framework** | ![ROS2](https://img.shields.io/badge/ROS2_Jazzy-22314E?style=for-the-badge&logo=ros&logoColor=white) |
| **Robot** | ![Robot Arm](https://img.shields.io/badge/로봇팔-FF6F00?style=for-the-badge&logo=robotframework&logoColor=white) ![AMR](https://img.shields.io/badge/주행로봇_AMR-009688?style=for-the-badge&logo=robotframework&logoColor=white) |
| **Simulation** | ![Gazebo](https://img.shields.io/badge/Gazebo-FF6600?style=for-the-badge&logo=gazebo&logoColor=white) ![RViz](https://img.shields.io/badge/RViz-22314E?style=for-the-badge&logo=ros&logoColor=white) |
| **Database** | ![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white) ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white) |
| **GUI** | ![PyQt5](https://img.shields.io/badge/PyQt5-41CD52?style=for-the-badge&logo=qt&logoColor=white) ![Web](https://img.shields.io/badge/Web-E34F26?style=for-the-badge&logo=html5&logoColor=white) |
| **Communication** | ![TCP/IP](https://img.shields.io/badge/TCP/IP-005571?style=for-the-badge&logo=internetcomputer&logoColor=white) ![ROS2 DDS](https://img.shields.io/badge/ROS2_DDS-22314E?style=for-the-badge&logo=ros&logoColor=white) |
| **Tools** | ![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white) ![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white) ![Notion](https://img.shields.io/badge/Notion-000000?style=for-the-badge&logo=notion&logoColor=white) |

### 📖 프로젝트 시나리오 (Scenario)

> 로봇이 수행하는 작업 흐름을 순서대로 설명해주세요. (아래 이미지는 예시 틀입니다)

#### 1️⃣ 도서 배달 요청 - 고객

<div align="center">

| 📱 도서 요청 | ➡️ | 🗺 경로 생성 | ➡️ | 🦾 상차 | ➡️ | 🤖 배달 | ➡️ | 🙆 배달 완료 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|

<!-- 시나리오 이미지로 교체 가능: <img src="docs/img/scenario_delivery.png" width="700"/> -->

</div>

#### 2️⃣ 도서 수거 요청 - 직원

<div align="center">

| 📱 수거 요청 | ➡️ | 🦾 상차 | ➡️ | 🤖 배달 | ➡️ | 🦾 하차 | ➡️ | 📚 서가 정리 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|

<!-- 시나리오 이미지로 교체 가능: <img src="docs/img/scenario_pickup.png" width="700"/> -->

</div>

### 🗺 Map 구성

> 작업 환경(Map) 이미지와 설명을 추가해주세요.

<img src="docs/img/map.png" alt="Map" width="600"/>

- 구역 설명: ...
- 주요 좌표/스테이션: ...

---

## 📂 Folder Structure

```
arte_readme/                         # pingdergarten 컨벤션
├── README.md
├── docs/                            # 문서, 이미지, 다이어그램
│   └── img/
├── controller/                      # 로봇 온보드 ROS2 (보드별 colcon ws)
│   ├── libi-drive-controller/       #   주행 (nav2)
│   └── libi-handy-controller/       #   팔 (MoveIt)
├── fleet/                           # ★ RMF 관제 (colcon ws) — fleet/src/libi_rmf_*
├── service/                         # 비-ROS 백엔드 (libi_service/aba_service/ai_service/labi_bot)
├── app/                             # PyQt 데스크톱 (libi_gui)
├── web/                             # 웹 (library_member/librarian)
└── db/  scripts/  tests/
```

---

## 🏗 System Architecture

> 전체 시스템 구성도를 추가해주세요. (노드 간 관계, 하드웨어/소프트웨어 구조)

<img src="docs/img/system_architecture.png" alt="System Architecture" width="700"/>

---

## 🚦 Fleet Management System

> 관제 시스템의 역할과 동작 방식을 설명해주세요.

- 로봇 상태 모니터링
- 작업 할당(Task Allocation) 로직
- 경로 관리 / 충돌 회피
- ...

<img src="docs/img/fleet_management.png" alt="Fleet Management" width="700"/>

---

## 🔄 Sequence Diagram

> 주요 시나리오에 대한 시퀀스 다이어그램을 추가해주세요.

<img src="docs/img/sequence_diagram.png" alt="Sequence Diagram" width="700"/>

---

## 🗄 ERD

> 데이터베이스 구조(ERD)를 추가해주세요.

<img src="docs/img/erd.png" alt="ERD" width="700"/>

---

## 🔌 Interface Specification

> 노드/모듈 간 통신 인터페이스(Topic, Service, Action, API)를 정리해주세요.

| Name | Type | Direction | Description |
|------|------|-----------|-------------|
| `/robot/goal` | Topic | GUI → Robot | 목적지 전달 |
| `/robot/status` | Topic | Robot → Fleet | 로봇 상태 보고 |
| ... | ... | ... | ... |

---

## 🖥 GUI

> GUI 화면 캡처와 주요 기능을 설명해주세요.

<img src="docs/img/gui.png" alt="GUI" width="700"/>

- 주요 기능 1: ...
- 주요 기능 2: ...

---

## ⚙️ 구현 (Implementation)

### 로봇팔 (Robot Arm)

> 구현 방안을 작성해주세요.

- 구현 방안: ...

### 주행로봇 (Mobile Robot)

> 구현 방안을 작성해주세요.

- 구현 방안: ...

---

## 🎬 Demo

### 로봇팔 (Robot Arm)

> 데모 영상 링크를 추가해주세요.

- 데모 영상: [링크](#)

### 주행로봇 (Mobile Robot)

> 데모 영상 링크를 추가해주세요.

- 데모 영상: [링크](#)

---

## 📅 Project Schedule

> 프로젝트 일정(WBS/간트차트)을 추가해주세요.

| 주차 | 기간 | 주요 내용 |
|------|------|-----------|
| 1주차 | MM/DD ~ MM/DD | 기획 / 요구사항 정의 |
| 2주차 | MM/DD ~ MM/DD | 설계 |
| 3주차 | MM/DD ~ MM/DD | 개발 |
| ... | ... | ... |

<img src="docs/img/schedule.png" alt="Schedule" width="700"/>

---

## 👥 팀원 소개

| 이름 | 역할 | 담당 업무 | GitHub |
|------|------|-----------|--------|
| 이강택 | 팀장 | ... | [@id](https://github.com/) |
| 인경일 | 팀원 | ... | [@id](https://github.com/) |
| 정호재 | 팀원 | ... | [@id](https://github.com/) |
| 이형주 | 팀원 | ... | [@id](https://github.com/) |
| 이주형 | 팀원 | ... | [@id](https://github.com/) |
| 이세형 | 팀원 | ... | [@id](https://github.com/) |
