---
name: auto-builder
description: "자율 프로젝트 빌더. 트렌드에서 아이디어를 발굴하고 실제로 프로토타입을 구현한다."
---

# Auto Builder

매일 밤 자동으로 미니 프로젝트를 하나 선정하고 구현하는 스킬.

## 프로토콜

### 1. 아이디어 발굴
- web_fetch로 news.hada.io, Hacker News RSS 확인
- 최근 Obsidian/openclaw/business ideas/ 에서 유망한 아이디어 확인
- 아래 관심사 태그에서 영감 받기

### 관심사 태그
- 퀀트 트레이딩 (시장 데이터, 백테스팅, 전략)
- AI 에이전트 / 자동화
- 데이터 분석 / 시각화
- CLI 도구 / 개발자 생산성
- 웹 스크래핑 / API 연동
- 미니 SaaS / 유틸리티

### 2. 프로젝트 선정
- 하룻밤에 완성 가능한 규모 (파이썬 스크립트, 간단한 앱, 데이터 분석)
- 구체적인 결과물이 나오는 것 (실행 가능한 코드, 분석 리포트, 시각화)
- 최근 7일간 만든 프로젝트와 중복 안 되게

### 3. 구현
- 프로바이더 분산:
  - 코딩: Codex CLI (`!codex -q "설명"`) 우선
  - 리서치: Gemini CLI (`!gemini -p "질문"`) 우선
  - 설계/종합: 직접 수행
- 작업 디렉토리: ~/projects/auto-builder/{날짜}-{프로젝트명}/
- 반드시 README.md 포함 (프로젝트 설명, 실행 방법)
- 실행해서 동작 확인

### 4. GitHub에 푸시
- `gh repo create imnotaltman/{프로젝트명} --private --source . --push`
- 또는 기존 auto-builder 모노레포에 추가

### 5. 결과 기록
- Obsidian/openclaw/projects/{날짜}.md에 프로젝트 기록:
  - 프로젝트명, 한줄 설명, GitHub 링크, 사용한 기술, 영감 출처
- iMessage로 보고: "밤새 만든 거: {프로젝트명} — {한줄 설명}. GitHub: {링크}"

### 6. 누적 관리
- ~/projects/auto-builder/ 에 날짜별로 프로젝트 누적
- 잘 된 프로젝트는 발전시킬 수 있음 (사용자가 판단)

## 에러 처리
- 구현 실패 시 실패 기록 남기고 다음으로
- 야간에는 FaceTime 안 걸기
- 재시도 최대 1회
