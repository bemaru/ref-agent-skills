# Agent Skills 표준 - 개요 분석

## 1. Agent Skills란?

**Agent Skills**는 Vercel Labs(Shu Ding)가 만든 **오픈 표준 스펙**으로, AI 에이전트를 위한 재사용 가능한 명령어, 가이드라인, 실행 스크립트를 패키징하는 방법을 정의합니다.

### 핵심 개념
- **선언적(Declarative)**: 가이드라인/규칙으로 에이전트에게 "지식" 제공
- **명령적(Imperative)**: 스크립트로 에이전트에게 "실행 능력" 제공
- **표준화**: 다양한 AI 도구에서 동일한 포맷 사용

### 지원 플랫폼
- Claude Code (네이티브 지원: `~/.claude/skills/`)
- Cursor
- Copilot
- OpenCode
- 표준을 구현한 모든 AI 코딩 에이전트

---

## 2. 저장소 구조

```
agent-skills/
├── .github/                    # CI/CD 설정
│   └── workflows/
│       └── react-best-practices-ci.yml
├── .notes/                     # 개인 분석 노트
├── skills/                     # 스킬 모음
│   ├── react-best-practices/   # React 성능 최적화 (57개 규칙)
│   ├── composition-patterns/   # React 합성 패턴
│   ├── react-native-skills/    # React Native 가이드라인
│   ├── web-design-guidelines/  # 웹 접근성/성능/UX 가이드
│   └── claude.ai/
│       └── vercel-deploy-claimable/  # Vercel 배포 자동화
├── packages/                   # 빌드 인프라
│   └── react-best-practices-build/
│       └── src/
│           ├── build.ts        # AGENTS.md 컴파일러
│           ├── parser.ts       # 규칙 파서
│           ├── validate.ts     # 규칙 검증기
│           └── extract-tests.ts # 테스트 케이스 추출
├── AGENTS.md                   # 에이전트용 스킬 개발 가이드
├── CLAUDE.md                   # Claude Code 설정
└── README.md                   # 프로젝트 소개
```

---

## 3. SKILL.md 포맷 명세

### 기본 구조

```yaml
---
# 필수
name: {kebab-case-name}
description: 한 문장 설명. 트리거 문구 포함.

# 선택
license: MIT
metadata:
  author: {organization}
  version: {semver}
  argument-hint: {인수 설명}
---

# {스킬 제목}

{스킬 설명 1-2문단}

## When to Apply / When to Use

{에이전트가 이 스킬을 활성화해야 할 컨텍스트}

## How It Works

1. 단계 1
2. 단계 2
3. 결과 반환

## Usage

```bash
bash /mnt/skills/user/{skill-name}/scripts/{script}.sh [args]
```

## Output

{예상 출력 예시}

## Present Results to User

{사용자에게 보여줄 형식 템플릿}

## Troubleshooting

{일반적인 문제 및 해결 방법}
```

### Frontmatter 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `name` | ✅ | kebab-case 스킬 이름 |
| `description` | ✅ | 트리거 조건 포함 한 문장 설명 |
| `license` | ❌ | 라이선스 (기본: MIT) |
| `metadata.author` | ❌ | 작성 조직 |
| `metadata.version` | ❌ | 시맨틱 버전 |

---

## 4. 스킬 유형

### 유형 A: 가이드라인 스킬 (선언적)

에이전트에게 "규칙/지식"을 제공합니다.

**예시: react-best-practices**
```
skills/react-best-practices/
├── SKILL.md           # 스킬 정의 + 퀵 레퍼런스
├── AGENTS.md          # 컴파일된 전체 가이드 (57개 규칙)
├── rules/             # 개별 규칙 파일
│   ├── async-parallel.md
│   ├── bundle-barrel-imports.md
│   └── ...
└── metadata.json      # 버전, 조직, 참조
```

**규칙 파일 포맷:**
```yaml
---
title: 규칙 제목
impact: CRITICAL | HIGH | MEDIUM-HIGH | MEDIUM | LOW-MEDIUM | LOW
impactDescription: "2-10× 개선" (선택)
tags: tag1, tag2
---

## 규칙 제목

**Impact: {LEVEL} ({description})**

왜 중요한지 설명.

**잘못된 예 (이유):**
```typescript
// 나쁜 코드
```

**올바른 예 (이유):**
```typescript
// 좋은 코드
```
```

### 유형 B: 자동화 스킬 (명령적)

에이전트에게 "실행 능력"을 제공합니다.

**예시: vercel-deploy-claimable**
```
skills/claude.ai/vercel-deploy-claimable/
├── SKILL.md           # 스킬 정의
└── scripts/
    └── deploy.sh      # 배포 실행 스크립트
```

**스크립트 요구사항:**
- `#!/bin/bash` shebang
- `set -e` (fail-fast)
- 상태 메시지는 stderr로: `echo "Message" >&2`
- 결과는 stdout으로 JSON 출력
- 임시 파일 cleanup trap 포함

---

## 5. 포함된 스킬 분석

| 스킬 | 유형 | 규칙 수 | Impact | 설명 |
|------|------|---------|--------|------|
| react-best-practices | 가이드라인 | 57개 | CRITICAL~LOW | React/Next.js 성능 최적화 |
| composition-patterns | 가이드라인 | 8개 | CRITICAL~MEDIUM | React 합성 패턴 |
| react-native-skills | 가이드라인 | 16+개 | CRITICAL~LOW | React Native 모범 사례 |
| web-design-guidelines | 가이드라인 | 100+개 | - | 접근성/성능/UX |
| vercel-deploy-claimable | 자동화 | - | - | Vercel 원클릭 배포 |

---

## 6. 빌드 시스템

### packages/react-best-practices-build/

개별 규칙 파일을 컴파일된 `AGENTS.md`로 변환하는 빌드 도구체인:

**워크플로우:**
```
rules/*.md (개별 규칙)
    ↓ parser.ts (파싱)
    ↓ validate.ts (검증)
    ↓ build.ts (컴파일)
    ↓
AGENTS.md (완성된 가이드)
test-cases.json (테스트 케이스)
```

**주요 컴포넌트:**
- **parser.ts** - YAML frontmatter 추출, 코드 블록 파싱
- **validate.ts** - 필수 필드, 영향도, 예제 검증
- **build.ts** - 목차 생성, 섹션 조직, ID 자동 부여
- **extract-tests.ts** - 좋은/나쁜 코드 예제 추출

**빌드 스크립트:**
```bash
pnpm build        # AGENTS.md 컴파일 + 테스트 추출
pnpm validate     # 규칙 구조 검증
pnpm build-all    # 모든 스킬 빌드
```

---

## 7. 설치 방법

### Claude Code
```bash
# 방법 1: 직접 복사
cp -r skills/{skill-name} ~/.claude/skills/

# 방법 2: npx (이 저장소 전용)
npx skills add vercel-labs/agent-skills

# 방법 3: 프로젝트별
cp -r skills/{skill-name} .claude/skills/
```

### claude.ai
- SKILL.md 내용을 대화에 붙여넣기
- 또는 프로젝트 지식에 추가

---

## 8. 3계층 아키텍처

```
┌─────────────────────────────────────┐
│ Layer 1: SKILL.md (스킬 정의)       │
│ - 메타데이터 (name, description)    │
│ - 에이전트 지시사항                 │
│ - 사용법 및 예제                    │
│ - 트러블슈팅                        │
├─────────────────────────────────────┤
│ Layer 2: scripts/ (실행 스크립트)   │
│ - Bash 스크립트                     │
│ - JSON 출력                         │
│ - 실제 작업 수행                    │
├─────────────────────────────────────┤
│ Layer 3: references/ (참조 자료)    │
│ - 상세 가이드라인                   │
│ - 규칙 정의                         │
│ - 테스트 케이스                     │
└─────────────────────────────────────┘
```

---

## 9. 발견 & 활성화 흐름

```
에이전트 시작 → 스킬 인덱스 로드 (name + description만, 가벼움)
    ↓
사용자 요청 감지 → description 매칭
    ↓ (매치됨)
전체 SKILL.md 로드 → 컨텍스트에 주입
    ↓
실행:
  - 가이드라인: 코드 생성/리뷰 시 규칙 적용
  - 자동화: 스크립트 실행 → JSON 결과 캡처
```

---

## 10. Agent Skills vs Superpowers 관계

```
Agent Skills 표준 (이 저장소)
│
├── 정의: SKILL.md 포맷, 디렉토리 구조, 스크립트 규칙
├── 제공: 참조 구현 (react-best-practices, deploy 등)
│
└──→ Superpowers (obra/superpowers)
     │
     ├── 활용: Agent Skills 표준의 SKILL.md 포맷
     ├── 추가: 개발 워크플로우 스킬 (brainstorming, TDD, debugging)
     ├── 추가: 훅 시스템 (세션 시작 시 자동 주입)
     ├── 추가: 서브에이전트 조율
     └── 추가: 강제 메커니즘 (합리화 방지 등)
```

**핵심 차이점:**

| 구분 | Agent Skills | Superpowers |
|------|-------------|-------------|
| 성격 | 표준 스펙 + 참조 구현 | 스킬 모음집 (프레임워크) |
| 스킬 유형 | 가이드라인 + 자동화 | 워크플로우 + 프로세스 |
| 복잡도 | 단순 (규칙 기반) | 복잡 (에이전트 조율) |
| 목적 | 코드 품질 규칙 제공 | 개발 프로세스 전체 관리 |
| 강제성 | 권장 | 필수 (Iron Law) |

---

## 11. 커스텀 스킬 만들기 (요약)

### 1단계: 디렉토리 구조 생성
```
skills/my-skill/
├── SKILL.md
├── scripts/           # 자동화용 (선택)
│   └── run.sh
└── references/        # 상세 참조 (선택)
    └── guide.md
```

### 2단계: SKILL.md 작성
```yaml
---
name: my-skill
description: 이 스킬이 활성화되어야 할 상황 설명. 트리거 문구 포함.
---

# 스킬 제목

스킬 설명...

## Usage
...

## Output
...
```

### 3단계: 설치
```bash
# 개인 스킬 (모든 프로젝트)
cp -r skills/my-skill ~/.claude/skills/

# 프로젝트별 스킬
cp -r skills/my-skill .claude/skills/
```

### 네이밍 규칙
- 디렉토리: `kebab-case` (예: `security-linter`)
- SKILL.md: 항상 대문자 `SKILL.md`
- 스크립트: `kebab-case.sh` (예: `check-security.sh`)

### 컨텍스트 효율성
- SKILL.md는 500줄 이하
- 구체적인 description (에이전트가 활성화 시점 파악)
- 점진적 공개 (참조 파일 활용)
- 스크립트 선호 (실행은 컨텍스트 소비 안 함)

---

## 12. 요약

**Agent Skills = AI 에이전트 능력 확장을 위한 오픈 표준**

- ✅ 표준화된 SKILL.md 포맷
- ✅ 선언적(가이드라인) + 명령적(자동화) 지원
- ✅ 다양한 AI 도구 호환
- ✅ 컨텍스트 효율적 설계
- ✅ 빌드 시스템으로 규칙 관리
- ✅ 누구나 커스텀 스킬 생성 가능

**Superpowers와의 관계:**
- Agent Skills = **표준** (포맷, 구조)
- Superpowers = **구현** (개발 워크플로우 스킬)
- 나만의 스킬 = Agent Skills 표준으로 만들면 됨!
