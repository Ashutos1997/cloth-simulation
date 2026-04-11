# Fabric — Interactive Cloth Simulation
# Fabric — 인터랙티브 천 시뮬레이션

[**English**](#english) · [**한국어**](#한국어)

---

<a name="english"></a>

## English

A real-time cloth physics simulation running entirely in the browser — WebGL rendering, no dependencies, single HTML file.

The cloth is a grid of points connected by distance constraints and simulated with Verlet integration. Drag it, tear it, blow wind across it, shake it with your microphone, or overlay a texture.

### Live demo

Open `artifacts/cloth-sim/index.html` in any WebGL-capable browser (Chrome, Firefox, Safari 15+). No server, no install step.

---

### Controls — Desktop

| Key / Input | Action |
|---|---|
| **Click + Drag** | Grab the nearest cloth point and pull the fabric |
| **T** | Toggle tear mode — move the mouse to cut through the cloth |
| **R** | Reset the cloth to its original state |
| **W** | Toggle wind — a sinusoidal horizontal force |
| **S** | Save a screenshot of the current frame |
| **G** | Toggle film-grain overlay |
| **H** | Hide / reveal the HUD |

Every shortcut row in the HUD is also **clickable** — no keyboard required. Active modes (tear, wind, grain) light up their row.

### Controls — Mobile

| Button | Action |
|---|---|
| **Effects** | Opens a bottom sheet — toggle Tear, Wind, Grain; pick a texture preset |
| **Reset** | Resets the cloth |
| **Save** | Saves a screenshot |
| **Upload** | Uploads a local image as a cloth texture |
| **Mic** | Activates the microphone — amplitude shakes the cloth |

---

### Fabric Presets

Three preset fabrics are available from the HUD (desktop) or the Effects sheet (mobile):

| Preset | Colour | Physics |
|---|---|---|
| **Silk** | Warm ivory | Lower gravity, higher iteration count — smooth and fluid |
| **Denim** | Slate blue | Higher gravity, stiffer feel |
| **Linen** | Sandy beige | Mid-weight, natural drape |

A **↩ default** link resets back to the plain cloth. Presets pick a darker colour variant automatically in dark mode.

---

### How the Physics Work

**Points** — The cloth is a 30 × 30 grid (900 points). Each point stores its current and previous position; the top row is pinned and acts as the hanger.

**Verlet integration** — Velocity is encoded implicitly as `current − previous`. Each frame:
1. Implied velocity is scaled by a damping factor (0.99)
2. Gravity and wind acceleration are applied
3. The point moves to its new position

**Constraints** — Adjacent points are linked by distance constraints. The solver runs multiple relaxation passes per frame, pushing or pulling each pair towards its rest length. Pinned points are never adjusted.

**Tearing** — A constraint is removed when either: (a) the user drags in tear mode, or (b) a segment stretches beyond 60× its rest length. Once removed, the two endpoints drift independently.

**Wind** — A sinusoidal horizontal force applied per-point, optionally driven by microphone amplitude.

---

### Rendering

The cloth is drawn with WebGL. Two shaders handle the mesh:

- **Line shader** — draws each constraint segment, shading it from neutral to red as stretch increases
- **Texture shader** — when a texture is loaded, maps it across the cloth grid using UV coordinates that follow the point grid

Uniforms: `uResolution`, `uHasTexture`, `uIsLine`, `uTexture`, `uClothColor` (vec3), `uLineAlpha` (float).

---

### Tech Stack

| Layer | Technology |
|---|---|
| Language | Vanilla JavaScript (ES2020) |
| Rendering | WebGL 1.0 |
| Icons | Phosphor Icons 2.1.1 (CDN) |
| Fonts | Pretendard · Noto Sans KR (CDN) |
| Build tools | Vite (dev server only — the simulation is a single static file) |
| Dependencies | None at runtime |

---

### Project Structure

```
artifacts/cloth-sim/
├── index.html        ← entire simulation (physics + shaders + UI)
└── public/
    ├── favicon.svg
    └── opengraph.jpg
```

All simulation logic lives in `index.html`. Key classes:

- **`Point`** — mass with current/previous position, pinned flag, Verlet update
- **`Constraint`** — distance link between two Points, `satisfy()` relaxation, tear detection
- **`Cloth`** — owns the full grid; exposes `update()`, `reset()`, `nearestPoint()`, `tearNear()`

---

<a name="한국어"></a>

## 한국어

WebGL로 렌더링되는 실시간 천 물리 시뮬레이션입니다. 외부 라이브러리 없이 단일 HTML 파일로 동작합니다.

천은 거리 제약으로 연결된 격자 점들로 구성되며, Verlet 수치 적분법으로 시뮬레이션됩니다. 잡아당기거나, 찢거나, 바람을 불거나, 마이크로 흔들거나, 텍스처를 입힐 수 있습니다.

### 실행 방법

WebGL을 지원하는 브라우저(Chrome, Firefox, Safari 15+)에서 `artifacts/cloth-sim/index.html`을 열면 됩니다. 서버나 설치 과정이 필요 없습니다.

---

### 조작법 — 데스크톱

| 키 / 입력 | 동작 |
|---|---|
| **클릭 + 드래그** | 가장 가까운 점을 잡아 천을 끌어당깁니다 |
| **T** | 찢기 모드 전환 — 마우스를 움직여 천을 자릅니다 |
| **R** | 천을 원래 상태로 초기화합니다 |
| **W** | 바람 전환 — 사인파 형태의 수평 힘을 적용합니다 |
| **S** | 현재 화면을 스크린샷으로 저장합니다 |
| **G** | 필름 그레인 오버레이를 켜고 끕니다 |
| **H** | HUD를 숨기거나 표시합니다 |

HUD의 각 단축키 행은 **클릭 가능**합니다. 활성 모드(찢기, 바람, 그레인)는 해당 행이 강조 표시됩니다.

### 조작법 — 모바일

| 버튼 | 동작 |
|---|---|
| **효과** | 하단 시트를 열어 찢기·바람·그레인 전환 및 텍스처 프리셋 선택 |
| **초기화** | 천을 초기화합니다 |
| **저장** | 스크린샷을 저장합니다 |
| **업로드** | 로컬 이미지를 텍스처로 업로드합니다 |
| **마이크** | 마이크를 활성화합니다 — 음량에 따라 천이 흔들립니다 |

---

### 패브릭 프리셋

HUD(데스크톱) 또는 효과 시트(모바일)에서 세 가지 프리셋 천을 선택할 수 있습니다:

| 프리셋 | 색상 | 물리 |
|---|---|---|
| **실크 (Silk)** | 따뜻한 아이보리 | 낮은 중력, 높은 반복 횟수 — 부드럽고 유동적 |
| **데님 (Denim)** | 슬레이트 블루 | 높은 중력, 뻣뻣한 질감 |
| **리넨 (Linen)** | 샌디 베이지 | 중간 무게감, 자연스러운 드레이프 |

**↩ 기본 질감** 링크를 클릭하면 기본 천으로 돌아갑니다. 다크 모드에서는 더 어두운 색상 변형이 자동으로 적용됩니다.

---

### 물리 엔진 원리

**점 (Point)** — 천은 30 × 30 격자(900개 점)로 구성됩니다. 각 점은 현재 위치와 이전 위치를 저장하며, 상단 행은 고정되어 걸이 역할을 합니다.

**Verlet 적분** — 속도는 `현재 위치 − 이전 위치`로 암묵적으로 인코딩됩니다. 매 프레임:
1. 암묵적 속도에 감쇠 계수(0.99)를 적용합니다
2. 중력과 바람 가속도를 더합니다
3. 점이 새 위치로 이동합니다

**제약 (Constraint)** — 인접한 점들은 거리 제약으로 연결됩니다. 솔버는 프레임마다 여러 번의 완화(relaxation) 패스를 실행하여 각 쌍을 휴식 길이로 밀거나 당깁니다. 고정된 점은 조정되지 않습니다.

**찢기** — 제약은 다음 경우에 제거됩니다: (a) 사용자가 찢기 모드에서 드래그하거나, (b) 세그먼트가 휴식 길이의 60배를 초과하여 늘어날 때. 제거되면 두 끝점이 독립적으로 이동합니다.

**바람** — 점별로 적용되는 사인파 수평 힘으로, 마이크 음량으로 구동할 수도 있습니다.

---

### 렌더링

천은 WebGL로 그려집니다. 두 셰이더가 메시를 처리합니다:

- **라인 셰이더** — 각 제약 세그먼트를 그리며, 신장도에 따라 중립에서 빨간색으로 음영 처리
- **텍스처 셰이더** — 텍스처가 로드되면 점 격자를 따라가는 UV 좌표를 사용하여 천에 매핑

---

### 기술 스택

| 레이어 | 기술 |
|---|---|
| 언어 | 바닐라 JavaScript (ES2020) |
| 렌더링 | WebGL 1.0 |
| 아이콘 | Phosphor Icons 2.1.1 (CDN) |
| 폰트 | Pretendard · Noto Sans KR (CDN) |
| 빌드 도구 | Vite (개발 서버 전용 — 시뮬레이션은 단일 정적 파일) |
| 런타임 의존성 | 없음 |

---

### 프로젝트 구조

```
artifacts/cloth-sim/
├── index.html        ← 전체 시뮬레이션 (물리 + 셰이더 + UI)
└── public/
    ├── favicon.svg
    └── opengraph.jpg
```

모든 시뮬레이션 로직은 `index.html`에 있습니다. 주요 클래스:

- **`Point`** — 현재/이전 위치, 고정 플래그, Verlet 업데이트를 가진 질점
- **`Constraint`** — 두 Point 사이의 거리 링크, `satisfy()` 완화, 찢기 감지
- **`Cloth`** — 전체 격자를 소유; `update()`, `reset()`, `nearestPoint()`, `tearNear()` 제공
