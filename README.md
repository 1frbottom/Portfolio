# 💼 포트폴리오

> **"신입 게임 클라이언트 프로그래머 [심조운]입니다."**

<br>

##  Tech Stack
| **Languages** | ![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=white) ![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=c%2B%2B&logoColor=white) |
| :--- | :--- |
| **Engines** | ![Unreal](https://img.shields.io/badge/Unreal_Engine-313131?style=flat-square&logo=unrealengine&logoColor=white) |
| **Tools** | ![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white) |

<br>

## 📄 Projects

### 1. 언리얼 공포 게임 <sub>(사이드 프로젝트)<sub>
> **요약 :** "언리얼 엔진을 사용한 3D 인도어 호러게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/464dadda-7430-4ac0-95b4-f70ecb471c83" width="500px"> |
| <br>**개발 기간 :** `2024.03 ~ 2024.09`<br><br>**인원 :** `2명` ( 1 Designer, **`1 Programmer`** )<br><br>**Repository :** [링크](https://github.com/1frbottom/UE5_Horror)<br><br>**사용 기술 :** `UE5`, `C++`, `AIPerception`<br><br> |

#### 핵심 기여
1. `AIPerception` / `Behavior Tree`를 활용한 퍼셉션 기반 AI
    - `AIPerception` 컴포넌트를 통해 Sight과 Hearing 자극을 구분하여 감지. 감지된 정보( `플레이어 위치`, `소리 발생 위치` )를 `BlackBoard`에 업데이트, 이를 기반으로 `Trace` -> `Hearing` -> `Patrol`로 이어지는 유기적인 행동 패턴을 `Behavior Tree`로 구현.

    - AI의 판단 로직( `Controller` )과 수행 로직( `Character` )을 분리. 공격 수행 시 `IHRCharacterAIInterface`와 델리게이트( `OnAttackFinished` )를 통해 애니메이션 종료 시점을 AI 컨트롤러에 정확히 동기화.<br><br>

2. `Enhanced Input` 기반 동적 시점 변환 시스템
    - 1인칭, 숄더뷰, 쿼터뷰의 3가지 시점을 실시간으로 전환하는 기능을 구현. 단순 카메라 위치만 바꾸는 것이 아닌, 각 시점에 맞는 `Input Mapping Context`를 동적으로 교체( `RemoveMappingContext()` -> `AddMappingContext()` )하여 조작감을 최적화.

    - TMap 형의 `CharacterControlManager`를 사용해 시점별 데이터( `회전 보간 여부`, `Control Rotation 사용 여부`, ... )를 데이터 테이블처럼 관리하여 하드코딩을 방지하고 확장성을 확보.<br><br>

3. 인터페이스를 활용한 의존성 최소화 및 상호작용 시스템
    - 아이템 습득, 문 열기 등 다양한 상호작용 대상을 Cast To로 일일이 확인하는 대신, `IHRInteractableActorInterface`를 상속받게 하여 유연한 상호작용 시스템 설계.

    - 플레이어 클래스가 특정 사물 클래스( 예: Door, Item )를 알 필요가 없게 만들어 코드의 재사용성과 유지보수성 향상.<br><br>

#### 트러블슈팅

<ul>
<details>
<summary> 고스트 애니메이션 블루프린트와 고스트(npc) C++ 클래스 간의 순환 참조 ( Click )</summary>

<br>

>**문제 상황 :**
>
>고스트 ABP의 노티파이에서 공격 판정 함수 `AttackHitCheck()`를 호출하기 위해 해당 ABP에서 고스트 C++ 클래스로 캐스팅 후 에디터에서 컴파일중 크래쉬( `Assertion failed` ) 발생.
><br><br>
>**원인 분석 :**
>
>강한 결합( `Tight Coupling` ) -> 기존에 고스트 C++ 클래스가 생성자에서 ABP를 참조하고 있었기 때문에 컴파일타임에 헤더 파일끼리 서로를 참조하는 `Circular Dependency` 오류 발생.
><br><br>
>**해결 과정 :**
>- C++ 인터페이스( `IAttackAnimEventsInterface` ) 안에 `DoAttackHitCheck()` 를 선언하여 고스트 C++ 클래스가 상속.
>- 고스트 C++ 클래스에서 이 함수를 정의함으로서 ABP에서는 캐스팅 노드 대신 `Interface Message` 노드를 사용하여 함수를 호출.
>- 컴파일타임의 정적 검사 대신, 런타임에 언리얼의 리플렉션 시스템을 이용하여 순환 참조를 해결. 

</details>
</ul>

<br>
<hr>

### 2. 자바 모작 게임 <sub>(교내 프로젝트)<sub>
> **요약 :** "종스크롤 슈팅 모작 게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/483934f5-500e-42bd-9590-93aa69fa0205" width="500px"> |
| <br>**개발 기간 :** `2024.03 ~ 2024.06`<br><br>**인원 :** `3명` ( **`3 Developer`** )<br><br>**Repository :** [링크](https://github.com/1frbottom/Java_Clone_Game)<br><br>**사용 기술 :** `Java`, `awt`, `swing`<br><br> |

#### 핵심 기여
1. Playing state 세부로직 구현

    - 일정시간마다 날아오는 유도 장애물에 플레이어 위치와의 거리 차에 비례한 가속도 보간을 적용하여, 관성을 느낄수 있게 구현.
    - ArrayList 사용하여 몬스터 스폰 로직 & 객체의 상태변화에 따른 메모리 해제 및 렌더링 설계.

#### 트러블슈팅

<ul>
<details>
<summary> 유도 장애물의 보간에서 발생하는 진동현상 ( Click )</summary>

<br>

>**문제 상황:**
>
>유도 장애물이 목표 위치에 도달했을 때, 부드럽게 멈추지 않고 좌우로 심하게 떨리는 현상이 발생.
><br><br>
>**원인 분석:**
>
>x축 거리에 비례해 속도를 더하는 가속도 로직( `velocity += dx * coefficient` ) 특성상, 둘 사이의 거리가 0이 되더라도 이미 누적된 횡이동 속도(관성)가 남아있음.
><br><br>
>**해결 과정:**
>- dx값이 특정 임계값(20.0f) 이내로 좁혀졌을 때 작동하는 마찰(Damping) 로직 도입.
>- 정렬이 거의 완료되면 매 프레임 x축 속도에 감속 계수(0.95f)를 곱해 관성을 상쇄, 부드럽게 플레이어를 따라 하강.
>

</details>
</ul>

<br>
<hr>

### 3. 디지털트윈 데이터 파이프라인 <sub>(교내 프로젝트)<sub>
> **요약 :** "디지털트윈을 위한 실시간 공공 교통데이터 동기화 파이프라인 및 가시화"<br><br>디지털트윈은 `데이터 수집 -> 데이터 동기화 -> 데이터 시각화` 의 세 단계로 나뉘며, 이중 데이터 동기화 부분은 다른 단계에 비해 미비한 실정으로, 이를 개선하고자 진행된 프로젝트입니다.<br><br>파이프라인은 `데이터 수집 -> Kafka -> Spark -> DBMS -> Api Server -> 웹` 과 같습니다.<br><br>프리뷰의 우측 창은 에픽게임즈가 디지털트윈의 표준으로 제시한 오픈소스 프로젝트인 `City Sample` 위에 해당 프로젝트 파이프라인을 얹은 모습입니다. 

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/9eb71472-ac09-4592-840d-354bec96641d" width="500px"> |
| <br>**개발 기간 :** `2025.09 ~ 2025.12`<br><br>**인원 :** `4명` ( **`2 Backend`**, 2 Frontend )<br><br>**Repository :** [링크](https://github.com/1frbottom/DigitalTwin_PipeLine)<br><br>**사용 기술 :** `Python`, `JavaScript`, `Html`, `Css`<br>`Kafka`, `Spark`, `FastAPI`, `PostgreSQL`, `Docker` <br><br> |

#### 핵심 기여
1. 프로젝트 인프라 구축
    - `Docker Compose` 활용하여 Kafka, Spark, PostgreSQL, FastAPI 등 9종의 컨테이너 Loose Coupling.
    - `kafka-setup` 컨테이너와 `healthcheck` 도입하여 컨테이너 실행에서의 레이스 컨디션 방지.

2. 데이터 파이프라인 구현
    - `Spark Structured Streaming` 통해 Kafka의 토픽들을 구독하여 초 단위 마이크로 배치 처리.
    - 공공데이터의 비정형적인 JSON구조를 `from_json()`, `explode()` 사용하여 평탄화 및 정규화.
    - `dropDuplicates()` 및 `withWatermark()` 사용하여 무결성 유지.

3. 백엔드 구현
    - `Router - CRUD - Schema - Model` 패턴으로 유지보수성 및 확장성 확보.
    - 인구현황 등의 정형 데이터는 관계형 테이블로, 승하차 정보 등의 가변구조 데이터는 Raw JSON으로 저장하여 Read-Time에 파싱함으로서 유연성 확보.

#### 트러블슈팅

<ul>
<details>
<summary> JSON 스키마 불일치 및 파싱 오류 ( Click )</summary>

<br>

>**문제 상황 :**
>
> JSON/XML의 스키마 불일치 및 파싱 오류
><br><br>
>**원인 분석 :**
>
>공공 데이터 API가 특정 케이스마다 JSON 구조를 다르게 반환(Object vs Array)하거나, XML 필드가 누락되어 Spark Job 중단.
><br><br>
>**해결 과정 :**
>
>- Spark SQL의 `coalesce()` 사용하여 JSON 스키마 경로가 변경되더라도 유연하게 대처.
>- 백엔드에서는 `json.loads()` 수행시 예외처리와 헬퍼 함수 `get_Val()`를 구현하여 필드누락이나 비정상데이터에 대해서 안전하게 방어.
</details>
</ul>

<br>
<hr>

## 📄 Other Projects
* **[Java_LibGDX_JumpKingLike](https://github.com/1frbottom/Java_LibGDX_JumpKingLike/blob/main/README.md) :** 자바의 LibGDX 라이브러리 사용하여 만든 간단한 2d 점프킹류 게임

* **[UN6_VR_ZombieDefense](https://github.com/1frbottom/UN6_VR_ZombieDefense/blob/main/README.md) :** 유니티와 메타 SDK를 사용한 간단한 VR 디펜스 fps 게임
