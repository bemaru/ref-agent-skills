# Agent Skills 분석 문서

Vercel Labs의 Agent Skills 오픈 표준을 분석한 개인 학습 자료입니다.

## 분석 문서 목록

1. **[overview.md](overview.md)** - Agent Skills 표준 개요, 아키텍처, SKILL.md 포맷

## 원본 저장소

- **GitHub**: https://github.com/vercel-labs/agent-skills
- **Author**: Vercel Labs (Shu Ding)
- **표준 사이트**: https://agentskills.io

## 핵심 요약

- Agent Skills = **AI 에이전트를 위한 오픈 표준 스펙**
- SKILL.md = 이 표준의 핵심 파일 포맷
- Claude Code, Cursor, Copilot 등 다양한 AI 에이전트에서 사용 가능
- Superpowers = 이 표준 위에 구축된 스킬 모음집

## 관계도

```
Agent Skills 표준 (SKILL.md 포맷 정의)
  ├── Vercel 공개 스킬 (react-best-practices 등)
  ├── Superpowers (obra/superpowers) - 개발 워크플로우 스킬
  └── 나만의 커스텀 스킬
```

> Agent Skills는 표준 스펙이고, Vercel 스킬과 Superpowers는 이 표준을 각각 독립적으로 활용하는 프로젝트.

## 분석 일자

- **2026-02-07** (초기 분석)
- **2026-02-14** (관계도 수정)
