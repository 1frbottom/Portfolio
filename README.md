# 💼 포트폴리오

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

### 1. 멀티 뱀서라이크 <sub>(사이드 프로젝트)</sub>
> **요약 :** "언리얼 엔진<sub>(리슨서버)</sub>을 사용한 3D 뱀서라이크 게임"

| Preview & Info |
| :---: |
| <img src="https://github.com/user-attachments/assets/d0b5c84f-a9c8-467c-bb00-8f4069d7b734" width="500px"> |
| <br>**개발 기간 :** `2026.05 ~ 진행중`<br><br>**인원 :** `1명` <br><br>**유튜브 :** [링크](https://www.youtube.com/watch?v=KwKsSuFFGsM)<br><br>**Repository :** [링크(문서화 예정)](https://github.com/1frbottom/UE5_Protject_Nayuta)<br><br>**사용 기술 :** `UE5`, `C++`<br><br> |

#### 핵심 기여
1. Steam 세션 생성·검색·초대<br><br>
    - `GameInstance`에서 처리. 네트워크 실패 시 `OnNetworkFailure()`로 세션 파괴 후 메인메뉴 복귀(`ClientTravel`).<br><br>
2. 서버 권위 페이즈 / UI 동기화<br><br>
    - `GameState` 페이즈를 `RepNotify`로 복제해 보상·게임오버 UI를 맞추고, 선택·재도전은 `PlayerController` RPC로 서버에서만 검증.<br><br>

#### 트러블슈팅

<ul>
<details>
<summary> [ <sub>해결</sub> ] 대규모 몬스터 처리 부하 및 이동 동기화 ( Click )</summary>

<br>

>**문제 상황 :**
>
>`1,000`마리 스폰 테스트에서 프레임타임이 마릿수에 비례해 급증. 리슨 서버-클라이언트에선 이동 스터터링 발생.<br><br>
>
>**원인 분석 :**
>
>- 매 스폰 `SpawnActor()` → 할당/GC 부하.<br><br>
>- 이동 Sweep, 피격 오버랩, 체력바 위젯이 마릿수에 비례.<br><br>
>- `ReplicateMovement`로 트랜스폼을 전부 동기화하며 대역폭 포화.<br><br>
>
>**해결 과정 :**
>
>- 풀링으로 런타임 스폰 제거. Sweep OFF, 피격은 거리 판정, 체력바는 Material CPD로 교체. 1차(2026.05)에 `40ms` 구간까지 방어.<br><br>
>- 이동 복제를 끄고 활성화 시점에만 시드·타겟·스폰위치를 복제. 각 머신이 같은 경로를 계산. 대기는 `NetDormancy`, 원거리는 `NetCullDistance`.<br><br>
>- Sweep OFF로 잃은 지형 대응은 CMC 대신 바닥/스텝업만 가진 전용 `UPawnMovementComponent`로 복구. 유휴 틱은 `ShouldTick()`으로 차단.<br><br>
>
>**현재 상태 및 프로파일링 [영상](https://www.youtube.com/watch?v=UQ7nFVs-mc0)** (2026.08 / Steam OSS, 기기 2대, 클라 기준, `bSmoothFrameRate` OFF) :<br><br>
>
>- 처리 부하 : 
>   - 몬스터 `100`마리 `7.46ms` → `800`마리 `30.84ms` (`32 FPS`) > - GPU는 `5.24ms` → `6.13ms`로 유지<br>
>   - 게임 스레드가 `30.89ms`로 프레임타임과 일치<br>
>   - 병목 원인은 렌더(Draw)가 아닌 게임 스레드(Tick/로직 연산)<br><br>
>- 네트워크 : 복제 액터 `100` → `800`(8배), 송신은 `5.6KB/s` → `3.5~4.0KB/s`로 같거나 더 낮음. 스터터링 해소.
<br><br>
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
<summary> 고스트 AnimBP <--> 고스트 캐릭터 클래스 간 순환 참조 ( Click )</summary>

<br>

>**문제 상황 :**
>
>- 고스트 AnimBP 내부 노티파이에서 공격 판정 함수 `DoAttackHitCheck()` 호출을 위해 고스트 클래스로 캐스팅시, 에디터 컴파일 중 크래시 발생.
><br><br><br>
>
>**원인 분석 :**
>
>- 고스트 클래스가 생성자에서 ABP를 참조.<br><br>
>- 고스트 AnimBP 역시 노티파이 그래프에서 고스트 클래스를 캐스팅하며 참조.<br><br>
>- 컴파일타임에 양방향으로 의존성이 생기며 크래시 유발.
><br><br><br>
>
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
