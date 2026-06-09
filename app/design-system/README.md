# /design-system 운영 페이지 — 실사용 인벤토리

> main 위에 추가된 디자인 시스템 운영(operations) 페이지의 **실제 사용 자원 목록**.
> MP-756 머지 이후 `/admin/design` 라우트로 이동 예정.

---

## 1. 라이브러리 의존성 (실사용)

main 의 `package.json` 에 이미 있는 라이브러리만 사용. **새 의존성 0개**.

| 라이브러리 | 버전 (main 기준) | page.tsx 가 쓰는 것 |
|---|---|---|
| `react` | 19.2 | `useEffect`, `useMemo`, `useState`, `MouseEvent` (type), `ReactNode` (type) |
| `next/link` | 16.0 | `Link` |
| `next/image` | 16.0 | `Image` |
| `lucide-react` | ^0.553 | 17개 아이콘: `Check`, `ChevronDown`, `Code2`, `Copy`, `ExternalLink`, `FileText`, `Home`, `Layers`, `MessageSquare`, `Minus`, `PanelLeftClose`, `PanelLeftOpen`, `PanelRight`, `Play`, `Plus`, `Search`, `Share2` |

---

## 2. 공통 컴포넌트 (실사용)

main 의 `@/components/_common/` 에서 import — main 코드 **수정 없음**.

| 컴포넌트 | 위치 |
|---|---|
| `Badge` | `components/_common/badge.tsx` |
| `Button` | `components/_common/button.tsx` |
| `Checkbox` | `components/_common/checkbox.tsx` |
| `Dropdown` | `components/_common/dropdown.tsx` |
| `EmptyState` | `components/_common/emptyState.tsx` |
| `Input` | `components/_common/input.tsx` |
| `PrimaryCategoryFilter` | `components/_common/primaryCategoryFilter.tsx` |
| `SecondaryCategoryFilter` | `components/_common/secondaryCategoryFilter.tsx` |

### 참고: 페이지 본문에 단어로 등장하지만 import 0건
- `Textarea`, `SearchBar` — main 에 미존재. 문자열·`data-*` 속성에만 단어 노출 (메타데이터로만 사용).

---

## 3. 디자인 토큰

### main 의 기본 토큰 (재사용, 변경 없음)

| 카테고리 | 토큰 |
|---|---|
| Primary | `--primary-{10,15,20,30,40,50,60,70}` |
| Neutral | `--neutral-{0,5,10,20,40,50,60,70,80,90,100}` |
| Surface | `--surface-{98,99,stroke,stroke-2}` |
| System | `--system-{alert,info}` |
| Font | `--font-{emphasis,body}` |

### 이번 작업에서 추가한 semantic 토큰

> `:root` 에 raw 정의 + `@theme inline` 에 Tailwind 매핑 → `text-default`, `bg-surface-card`, `border-subtle` 등의 **Tailwind 유틸리티 자동 생성**됨.

| 카테고리 | 정의된 토큰 수 | 토큰 키 | page.tsx 실사용 |
|---|---|---|---|
| **text** | 8 | `default`, `strong`, `body`, `secondary`, `subtle`, `muted`, `placeholder`, `inverse` | ✅ **4개** (`default` 3회, `strong` 58회, `secondary` 78회, `muted` 20회) |
| **surface** | 7 | `page`, `card`, `muted`, `divider`, `overlay`, `overlay-medium`, `overlay-strong` | ❌ 정의만 (0회 사용) |
| **border** | 4 | `default`, `subtle`, `muted`, `inverse-muted` | ✅ **3개** (`default` 1회, `subtle` 70회, `muted` 6회) |
| **action** | 11 | `primary-{bg,fg,hover,strong-hover}`, `secondary-{bg,hover}`, `tertiary-{border,fg,hover}`, `disabled-{bg,fg}` | ❌ 정의만 |
| **feedback** | 4 | `error`, `error-hover`, `success`, `info-strong` | ❌ 정의만 |
| **icon** | 4 | `default`, `subtle`, `muted`, `inverse-muted` | ❌ 정의만 |
| **focus** | 1 | `ring` | ❌ 정의만 |
| **shadow** | 2 | `pop-up`, `search-entry` | ❌ 정의만 |
| **합계** | **41** | — | **7개만 실사용 (≈ 17%)** |

### 🟡 검토 필요: 미사용 토큰 34개 처리 방향
- **(A) 보존** — 디자인 시스템 인프라 차원에서 미리 마련. 다른 페이지/컴포넌트가 쓸 때까지 보관.
- **(B) 사용처 발생 시 추가** — 일단 실사용 7개만 남기고 나머지 제거. 가벼움 우선.
- **(C) 사용처 늘 때까지 보관 후 재점검** — 다음 분기·릴리즈 때 재검토.

---

## 4. Figma 에셋

`public/design-system/figma/` 23개. page.tsx 가 직접 `src=` 로 참조.

| 카테고리 | 파일 |
|---|---|
| Color | `01.Color.{svg, jpg, pdf}` |
| Typography | `02.Typography.{svg, pdf}`, `02.Typography@3x.png` |
| Layout | `03.Layout.svg`, `03.Layout@{2x, 3x}.png` |
| Image | `04.Image.{svg, pdf}`, `04.Image@3x.png`, `image_01..04.svg` |
| Logo | `logo/{logo-symbol, type-a-{logo-favicon, symbol}, type-b-{logo-favicon, symbol}, typography}.svg`, `logo/logo_svg.zip` |

### 🟡 검토 필요: 중간 크기 바이너리 8개 (≈ 9.4MB)
- `.pdf` 3개 (1.9MB, 0.3MB, 0.2MB)
- `.png` 4개 (2.9MB ~ 0.2MB)
- `.zip` 1개 (2.7MB)

운영페이지가 표시는 하지만 SVG/JPG로도 충분한 경우가 많음 → **제거·다운사이즈 검토 권장**.

---

## 5. 페이지 구조

- 단일 파일: `app/design-system/page.tsx` (4,944줄)
- 섹션: Colors / Typography / Layout / Image / Logo / Component preview 등
- 향후 모듈화 검토 (예: `_sections/Colors.tsx`, `_sections/Components.tsx`) — 리뷰 부담 ↓

---

## 6. 향후 계획

### MP-756 머지 후 (Option A)
1. `git checkout -b feat/MP-XXX-admin-design-page origin/develop` (MP-756 포함된 develop 위)
2. `git mv app/design-system/page.tsx app/admin/design/page.tsx`
3. `app/admin/layout.tsx` 의 JWT roles 가드 자동 적용 → admin 인증 보호
4. PR → `develop`

### 정식 티켓 확보
- 현재 브랜치 `feat/design-system-page` 는 main 기준 임시 브랜치 (티켓 미발급 상태)
- 정식 MP-XXX 티켓 받은 뒤 PR 생성 권장

---

## 7. 의도적으로 미포함된 자원

| 항목 | 사유 | 보관 위치 |
|---|---|---|
| **파일 수정 API** (`app/api/design-system/file-edits/`, `app/design-system/actions.ts`, `lib/design-system/file-edit-*`) | route/server action 에 admin 인증 미구현. 운영 모델(라이브 편집 vs PR 워크플로) 협의 필요 | `chore/design-system-prep` 브랜치, `stash@{0}` (이중 백업) |
| **Storybook 인프라** (`.storybook/main.ts`, `preview.tsx`, `vitest.setup.ts`) + **stories 19개** | 운영페이지가 stories 경로를 메타데이터로 참조하지만 실제 통합 미정 | `feat/MP-000-my-work` 브랜치 (PR #74) |
| `figma_export/` 의 중복 에셋 | `public/design-system/figma/` 와 동일 콘텐츠 중복 | `feat/MP-000-my-work` 브랜치 (PR #74) |
