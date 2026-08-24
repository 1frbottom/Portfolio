3 # 💼 포트폴리오

> **"신입 게임 클라이언트 프로그래머 [심조운]입니다."**<br><br>
> **"더 나은 가독성을 위해 [ReadMe](https://github.com/1frbottom/Portfolio/blob/main/README.md) 클릭 부탁드립니다."**

<br>

##  Tech Stack
| **Languages** | ![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square&logo=c&logoColor=white) ![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=c%2B%2B&logoColor=white) |
| :--- | :--- |
| **Engines** | ![Unreal](https://img.shields.io/badge/Unreal_Engine-313131?style=flat-square&logo=unrealengine&logoColor=white) |
| **Tools** | ![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white) |

<br>

## 📄 Projects

### 1. 멀티 뱀서라이크 <sub>(사이드 프로젝트)<sub>
> **요약 :** "언리얼 엔진을 사용한 3D 뱀서라이크 게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/eeb11186-2e18-403a-9015-3614187cb8e8" width="500px"> |
| <br>**개발 기간 :** `2026.03 ~ 진행중`<br><br>**인원 :** `1명` <br><br>**Repository :** [링크(미완)](https://github.com/1frbottom/UE5_Protject_Nayuta)<br><br>**사용 기술 :** `UE5`, `C++`<br><br> |

#### 핵심 기여
1. `OnlineSubsystem` 기반 멀티플레이어 세션<br><br>
    - `GameInstance` 에서 세션 생성-검색 및 스팀 친구 초대 콜백 처리. 예외 발생 시 `OnNetworkFailure()` 연동으로 안전하게 세션파괴 이후 메인 메뉴 복귀( `ClientTravel` ).<br><br>
2. 서버 권위 게임 흐름 / UI 동기화<br><br>
    - `GameState` 가 게임 페이즈를 통제, `RepNotify` 로 각 클라이언트가 적절하게 보상-게임오버 UI를 띄우도록 동기화.<br><br>
    - 클라이언트 입력(`보상 선택 / 재도전`)은 `PlayerController` RPC로 서버 `GameMode` 에 전달-검증. UI 렌더링은 `BIE` 로 분리해 소스-블루프린트 결합도 절감.<br><br>
3. 데이터 드리븐 스테이지 구성<br><br>
    - `웨이브 진행( 마리수, 스폰간격, 몬스터종류 )` / `레벨업요구치` / `처치보상` / `무기 성장치` 등을 각각 DataTable로 관리함으로서 밸런스 조정 편의성 도모.<br><br>
4. 대량 몬스터 전용 이동 컴포넌트 자작<br><br>
    - `UCharacterMovementComponent` 는 수백 마리 동시 구동에 과중하며, 몬스터 이동은 애초에 복제하지 않으므로 CMC의 네트워크 예측 기능이 통째로 낭비. `UPawnMovementComponent` 를 상속해 실제로 필요한 `바닥 탐지 / 중력 / 계단 스텝업 / 경사 슬라이드 / 넉백` 만 직접 구현.<br><br>
    - 컴포넌트 자체 틱 등록을 끄고( `bAutoUpdateTickRegistration = false` ) 소유 액터의 `Tick` 에서만 구동. 풀링된 몬스터마다 게이팅되지 않은 두 번째 틱이 도는 것을 방지.<br><br>
    - 매 프레임 `실제 스윕된 거리` 를 `Velocity` 로 발행해 `APawn::GetVelocity()` 가 이동 의도가 아닌 실이동을 반환하도록 구성. `AnimInstance` 가 별도 위치 추적 없이 엔진 표준 경로만 읽도록 단순화.<br><br>

#### 트러블슈팅

<ul>
<details>
<summary> [ <sub>진행중</sub> ] 대규모 몬스터 처리 성능 최적화 및 네트워크 동기화 이슈 ( Click )</summary>

<br>

>**문제 상황 :**
>
>`1,000`마리 스폰 테스트에서 프레임타임이 선형적으로 급증. 리슨 서버-클라이언트 환경에선 네트워크 대역폭 초과로 몬스터 이동에 스터터링 발생.<br><br>
>
>**원인 분석 :**
>
>- 매 스폰마다 `SpawnActor()` 호출 → 메모리 할당 및 가비지컬렉터 부하.<br><br>
>- 몬스터의 ( 이동 물리 Sweep / 플레이어공격 오버랩쿼리 / 체력바 위젯 렌더링 ) 세가지가 마릿수에 비례해 누적.<br><br>
>- 대량의 몬스터 트랜스폼을 서버가 매 프레임 `ReplicateMovement` 로 동기화하며 발생한 네트워크 병목.<br><br>
>
>**해결 과정 :**
>
>- 로컬 성능 ( `해결` ): Object Pool 도입으로 액터 생성 비용 제거. 이동 Sweep 비활성화, 피격 판정을 타겟과의 거리 벡터로 대체. 체력바 위젯 대신 Material CPD( `Custom Primitive Data` )로 교체. `40ms` 구간까지 프레임 타임 방어 성공.<br><br>
>- 네트워크 최적화 ( `해결중` ): `ReplicateMovement` 를 끄고, 활성화 시점에만 타겟·스폰 위치( `FMonsterActivationData` )만 복제 후 클라이언트가 자체적으로 이동을 연산하는 Dead Reckoning 방식 적용. 비활성 풀 몬스터는 `NetDormancy` 로 복제 부하 추가 절감. 스터터링은 크게 완화됐으나 대규모 동시 스폰 기준 재검증은 미완.<br><br>
>
>**현재 상태 :**
>
>위 `1,000`마리 스트레스 테스트 이후로는 대규모 재측정 미진행. 현재는 일반 웨이브 설정( 스폰 간격 `2초`, `SpawnCountPerTick` `30` ) 기준 정상 동작만 확인한 상태.<br><br>

</details>
</ul>

<ul></ul>
<details>
<summary> [ <sub>해결</sub> ] 최적화 이후 몬스터가 지형을 무시하는 문제 ( Click )</summary>

<br>

>**문제 상황 :**
>
>성능 최적화로 이동 Sweep을 비활성화한 이후, 몬스터가 공중에 뜨거나 지형 아래에 스폰되고 언덕에서 튕겨 오르는 현상 발생.<br><br>
>
>**원인 분석 :**
>
>- Sweep을 끄면서 바닥 접지 판정 자체가 사라짐. Z를 고정값으로 다루던 탓에 경사·계단 대응이 전무.<br><br>
>- 웨이브 스폰이 플레이어 주변 원형으로 좌표만 굴릴 뿐, 해당 지점이 유효한 지형인지 검증하지 않음.<br><br>
>
>**해결 과정 :**
>
>- 스폰 XY를 `ProjectPointToNavigation()` 으로 NavMesh에 투영하고, 대응 poly가 없으면 해당 슬롯을 건너뛰도록 변경.<br><br>
>- 라인트레이스 기반 바닥 탐지와 중력을 이동 컴포넌트로 이관. Seek은 2D만 담당하고 Z는 바닥 추종이 전담하도록 책임 분리.<br><br>
>- 블로킹 히트의 `Normal.Z` 로 걸을 수 있는 경사와 벽을 구분. 경사면은 잔여 이동량을 표면 방향으로 슬라이드시키고, 수직 히트는 고정 hop 대신 실측한 계단 높이만큼만 상승.<br><br>
>- 캡슐을 바닥에서 `FloorClearance` 만큼 띄워 유지. 정확히 접촉시키면 엔진이 이를 즉시 블로킹 히트로 보고해 평지에서도 이동이 멈추는 문제가 있었음.<br><br>
>
>**결과 :**
>
>경사·계단 통과와 낙하가 정상 동작. CMC 없이도 지형 대응을 확보하면서 최적화로 얻은 프레임 이득은 유지.<br><br>
</details>
</ul>

<br>
<hr>

### 2. 언리얼 공포 게임 <sub>(사이드 프로젝트)<sub>
> **요약 :** "언리얼 엔진을 사용한 3D 인도어 호러게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/464dadda-7430-4ac0-95b4-f70ecb471c83" width="500px"> |
| <br>**개발 기간 :** `2024.03 ~ 2024.09`<br><br>**인원 :** `2명` ( 1 Designer, **`1 Programmer`** )<br><br>**Repository :** [링크](https://github.com/1frbottom/UE5_Horror)<br><br>**사용 기술 :** `UE5`, `C++`, `AIPerception`<br><br> |

#### 핵심 기여
1. `AIPerception`, `Behavior Tree` 기반 퍼셉션 AI<br><br>
    - Sight-Hearing 자극을 감지하고 `BlackBoard`에 갱신, `Trace` → `Hearing` → `Patrol`로 이어지는 행동 패턴을 구현.<br><br>
    - 컨트롤러와 캐릭터를 분리하고, 인터페이스와 델리게이트로 애니메이션 종료 시점을 AI에 동기화.<br><br>
2. `Enhanced Input` 기반 동적 시점 전환 시스템<br><br>
    - 1인칭-숄더뷰-쿼터뷰를 시점별 `IMC` 교체로 실시간 전환.<br><br>
    - 시점별 데이터를 `TMap`으로 관리해 하드코딩 방지 및 확장성을 확보.<br><br>
3. 인터페이스 기반 상호작용 시스템<br><br>
    - 상호작용 대상을 `Cast` 대신 인터페이스로 처리해 결합도 저하.<br><br>
    - 플레이어와 개별 사물 클래스의 분리로 유지보수성 향상.<br><br>

#### 트러블슈팅

<ul>
<details>
<summary> 고스트 ABP <--> Cpp 클래스 간 순환 참조 ( Click )</summary>

<br>

>**문제 상황 :**
>
>고스트 ABP 노티파이에서 공격 판정 함수 `AttackHitCheck()`호출 위해 Cpp 클래스로 캐스팅시, 에디터 컴파일 중 크래시 발생.
><br><br>
>**원인 분석 :**
>
>고스트 C++ 클래스가 생성자에서 ABP를 참조.
><br><br>
>**해결 과정 :**
>- C++ 인터페이스( `IAttackAnimEventsInterface` )에 `DoAttackHitCheck()`를 선언하고 고스트 C++ 클래스가 이를 상속.<br><br>
>- 고스트 C++ 클래스에서 함수를 정의해, ABP에서는 캐스팅 노드 대신 `Interface Message` 노드로 호출.<br><br>
>- 컴파일타임 정적 검사 대신 런타임 리플렉션을 활용해 순환 참조를 해소.

</details>
</ul>

<br>
<hr>

### 3. 자바 모작 게임 <sub>(교내 프로젝트)<sub>
> **요약 :** "종스크롤 슈팅 모작 게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/483934f5-500e-42bd-9590-93aa69fa0205" width="500px"> |
| <br>**개발 기간 :** `2024.03 ~ 2024.06`<br><br>**인원 :** `3명` ( **`3 Developer`** )<br><br>**Repository :** [링크](https://github.com/1frbottom/Java_Clone_Game)<br><br>**사용 기술 :** `Java`, `awt`, `swing`<br><br> |

#### 핵심 기여
1. Playing state 세부로직 구현<br><br>
    - 일정시간마다 날아오는 유도 장애물에 플레이어 위치와의 거리 차에 비례한 가속도 보간을 적용하여, 관성을 느낄수 있게 구현.<br><br>
    - ArrayList 사용하여 몬스터 스폰 로직 & 객체의 상태변화에 따른 메모리 해제 및 렌더링 설계.<br><br>

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
>- dx값이 특정 임계값(20.0f) 이내로 좁혀졌을 때 작동하는 마찰(Damping) 로직 도입.<br><br>
>- 정렬이 거의 완료되면 매 프레임 x축 속도에 감속 계수(0.95f)를 곱해 관성을 상쇄, 부드럽게 플레이어를 따라 하강.
>

</details>
</ul>

<br>
<hr>

### 4. 디지털트윈 데이터 파이프라인 <sub>(교내 프로젝트)<sub>
> **요약 :** "디지털트윈을 위한 실시간 공공 교통데이터 동기화 파이프라인 및 가시화"<br><br>디지털트윈은 `데이터 수집 -> 데이터 동기화 -> 데이터 시각화` 의 세 단계로 나뉘며, 이중 데이터 동기화 부분은 다른 단계에 비해 미비한 실정으로, 이를 개선하고자 진행된 프로젝트입니다.<br><br>파이프라인은 `데이터 수집 -> Kafka -> Spark -> DBMS -> Api Server -> 웹` 과 같습니다.<br><br>아래 프리뷰의 우측은 에픽게임즈 사가 디지털트윈의 표준으로 제시한 언리얼 엔진 오픈소스 프로젝트인 `City Sample` 위에 해당 프로젝트 파이프라인을 얹은 모습입니다. 

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/9eb71472-ac09-4592-840d-354bec96641d" width="500px"> |
| <br>**개발 기간 :** `2025.09 ~ 2025.12`<br><br>**인원 :** `4명` ( **`2 Backend`**, 2 Frontend )<br><br>**Repository :** [링크](https://github.com/1frbottom/DigitalTwin_PipeLine)<br><br>**사용 기술 :** `Python`, `JavaScript`, `Html`, `Css`<br>`Kafka`, `Spark`, `FastAPI`, `PostgreSQL`, `Docker` <br><br> |

#### 핵심 기여
1. 프로젝트 인프라 구축<br><br>
    - `Docker Compose` 활용하여 Kafka, Spark, PostgreSQL, FastAPI 등 9종의 컨테이너 Loose Coupling.<br><br>
    - `kafka-setup` 컨테이너와 `healthcheck` 도입하여 컨테이너 실행에서의 레이스 컨디션 방지.<br><br>
2. 데이터 파이프라인 구현<br><br>
    - `Spark Structured Streaming` 통해 Kafka의 토픽들을 구독하여 초 단위 마이크로 배치 처리.<br><br>
    - 공공데이터의 비정형적인 JSON구조를 `from_json()`, `explode()` 사용하여 평탄화 및 정규화.<br><br>
    - `dropDuplicates()` 및 `withWatermark()` 사용하여 무결성 유지.<br><br>
3. 백엔드 구현<br><br>
    - `Router - CRUD - Schema - Model` 패턴으로 유지보수성 및 확장성 확보.<br><br>
    - 인구현황 등의 정형 데이터는 관계형 테이블로, 승하차 정보 등의 가변구조 데이터는 Raw JSON으로 저장하여 Read-Time에 파싱함으로서 유연성 확보.<br><br>

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
>- Spark SQL의 `coalesce()` 사용하여 JSON 스키마 경로가 변경되더라도 유연하게 대처.<br><br>
>- 백엔드에서는 `json.loads()` 수행시 예외처리와 헬퍼 함수 `get_Val()`를 구현하여 필드누락이나 비정상데이터에 대해서 안전하게 방어.
</details>
</ul>

<br>
<hr>

## 📄 Other Projects
* **[Java_LibGDX_JumpKingLike](https://github.com/1frbottom/Java_LibGDX_JumpKingLike/blob/main/README.md) :** 자바의 LibGDX 라이브러리 사용하여 만든 간단한 2d 점프킹류 게임

* **[UN6_VR_ZombieDefense](https://github.com/1frbottom/UN6_VR_ZombieDefense/blob/main/README.md) :** 유니티와 메타 SDK를 사용한 간단한 VR 디펜스 fps 게임
