# 🚀 SplashTag – Multiplayer Action Game (UE5)

**SplashTag**는 학창 시절 ‘경찰과 도둑’ 놀이에서 영감을 받아 제작한

**멀티플레이 캐주얼 액션 게임**입니다.

두 가지 모드를 제공하며, 플레이어는 역할에 따라 서로 다른 능력과 전략을 수행합니다.

---

# 1️⃣ 프로젝트 개요 (Project Overview)

- **프로젝트명:** SplashTag (멀티플레이 캐주얼 액션 게임)
- **엔진:** Unreal Engine 5.4
- **플랫폼:** PC (멀티플레이 / Listen Server 기반)
- **개발 방식:** C++ + Blueprint 혼합
- **개발 기간:** 약 1~2개월
- **장르:** 캐주얼 액션 / 술래잡기 / 숨바꼭질 / Online Multiplayer

---

# 2️⃣ 게임 모드 소개 (Game Modes)

## 🎒 **숫래잡기 모드 — Tag & Clean**

학생(러너) 팀과 술래(태거) 팀으로 나뉘어 플레이합니다.

- **학생(Runner):**
    
    학교 곳곳에 있는 낙서를 **물총으로 지우면 승리**
    
- **술래(Tagger):**
    
    모든 학생을 **잡으면 승리**
    

🥇 **러너 vs 태거의 실시간 추격전**

🥇 **물총, 회피, 달리기 등 다양한 능력 활용**

---

## 🎭 **숨바꼭질 모드 — Prop Hunt 스타일**

학생은 **랜덤한 사물로 변신**하여 숨고,

술래는 변신한 학생들을 찾아내 **모두 제거하면 승리**합니다.

- 장애물, 교실, 복도 등 다양한 지형 활용
- 시야각/노이즈 기반의 탐지 플레이

---

# 3️⃣프로젝트 영상

🎥 https://www.youtube.com/watch?v=NcN6n5M-Lq0

> Steam Listen Server + GAS 기반으로 제작된 캐주얼 액션 멀티플레이 게임.
> 

---

# 4️⃣담당 파트 (My Key Responsibilities)

이 프로젝트에서 **캐릭터 파이프라인 전체를 직접 설계·구현**했습니다.

### 🧩 캐릭터 시스템 설계

- BaseCharacter 기반 상속 구조 설계
- Runner / Tagger **역할별 캐릭터 분리**
- 물총(EQ), 공격(GA), 회피, 달리기 등 **행동 기반 어빌리티 제작**
- HitReact, Sprint, Aim, Equip 등 **애니메이션 이벤트 관리**

### ⚡ Gameplay Ability System(GAS)

- ASC / AttributeSet 초기화 및 수명주기(Avatar·Clear·Cancel·GC) 설계
- 입력(GA Input Tag) → Ability 발동 시스템 구축
- 스태미너·체력·피격 데미지 등 전투 스탯 구현

### 🖥 UI & HUD

- Tick 없이 **이벤트 기반(UI Delegate) 체력/스태미너 갱신**
- ASC 초기화 이전 요청을 큐에 쌓아두고 초기화 후 실행하는 구조 설계

### 🎮 입력(Enhanced Input)

- **Tag 기반 Press/Hold/Toggle 입력 시스템**
    
    → AbilityInputPressed/Released/Toggle 통합 처리
    

### 🕺 애니메이션 시스템

- Lyra 기반 **AnimLayer + AnimBP 분리 설계**
- Turn In Place, AimOffset, Cycle Movement 등 구현
- ThreadSafe Animation Update 도입

---

# 5️⃣Character System – Runner & Tagger Overview

## 📚 클래스 다이어그램

```
BaseCharacter
 ├── RunnerCharacter  (물총 발사/조준/회피 중심)
 └── TaggerCharacter  (추격/공격 중심)
```

각 캐릭터는 ASC를 통해 다음과 같은 시스템을 포함한다:

- **GA(Ability)**: Run, Crouch, Attack/Fire, Equip, Aim, HitReact
- **GE(Effect)**: 스태미너 감소, 대미지 처리, 버프/디버프
- **AttributeSet**: Health, Stamina, MoveSpeed…

---

# 6️⃣Ability System – 구조 상세

## 🔧 ASC 지연 초기화 시스템 (Lazy Initialization)

ASC가 아직 완전히 준비되지 않은 시점에서 UI 바인딩 등이 실행되는 문제를 해결하기 위해,

초기화 이전 요청은 큐(OnInitializedDelegate)에 저장하고

ASC 초기화 직후 자동 실행되도록 설계했다.

✔ UI/Ability 처리 레이스 컨디션 제거

✔ ASC 초기화 시점 통일

✔ BeginPlay/OnPossess 등 환경 변화에도 안정적

---

# 7️⃣  Input System – Tag 기반 입력 구조

입력을 키 코드 대신 **GameplayTag로 관리**하는 방식 도입.

```
Input.Fire
Input.Run
Input.Crouch
Input_Hold.Run
Input_Toggle.Crouch

```

### ⭐ 장점

- 키 바인딩이 바뀌어도 코드 수정 없어도 됨
- Press/Hold/Toggle 패턴을 ASC 한 곳에서 처리
- Ability 간 규칙 일관성 유지

---

# 8️⃣ Animation System – Lyra 기반 구조

- **AnimLayer:** 속성/상태 계산
- **AnimBP:** 실제 표현 레이어
- Turn in Place, AimOffset, Idle/Cycle State 처리
- **ThreadSafe 업데이트**로 프레임 안정성 확보
