---
name: auto-builder
description: "자율 프로젝트 빌더. 트렌드에서 아이디어를 발굴하고 실제로 프로토타입을 구현한다."
---

# Auto Builder

매일 밤 자동으로 미니 프로젝트를 하나 선정하고 구현하는 스킬.

## 우선순위 원칙 (CRITICAL)

**반드시 아래 순서를 따른다. 절대로 building 상태가 있는데 새 아이디어를 발굴하지 않는다.**

### 실행 순서
1. **GET /api/ideas 확인** → building/queued 상태 아이디어 존재 여부 체크
2. **building 상태가 있으면** → 새 아이디어 발굴 금지. 그것부터 완료
3. **queued 상태가 있으면** → building이 없을 때만, 가장 오래된 queued 아이디어를 선택해서 빌드
4. **building도 queued도 없을 때만** → 새 트렌드 스캔 및 아이디어 발굴

### 우선순위 규칙
- `building` 먼저 완료 → `queued` 중 가장 오래된 것 처리 → 새 아이디어 발굴 순서 엄수
- building 상태 아이디어가 있으면 새 아이디어 발굴 절대 금지
- 아이디어 발굴 시 **바로 빌드 시작 금지** — 반드시 `queued` 상태로만 등록
- 빌드는 "queued 아이디어 중 가장 오래된 것(created_at 기준 오름차순 첫 번째)" 을 선택

## 프로토콜

### 0. 상태 확인 (매번 먼저 실행)
```
GET http://localhost:3002/api/ideas
```

**아이디어 풀 우선 채우기 원칙:**
- queued 아이디어가 **3개 미만**이면 → 새 아이디어 발굴해서 queued 등록 (빌드 금지, 종료)
- queued가 3개 이상이고 building 없음 → 가장 오래된 queued 1개 빌드
- building 있음 → 그것부터 완료 (새 발굴 절대 금지)
- queued 아이디어를 발굴할 때는 한 번에 1개만 등록하고 종료

**목표:** 아이디어 풀이 항상 3~5개 queued 상태로 유지되어야 한다. 칸반 보드에 아이디어가 계속 쌓여 보여야 한다.

### 1. 아이디어 발굴 (building/queued 없을 때만)
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

### 2. 아이디어 등록 (queued 상태로만 — 빌드 시작 금지)

아이디어 발굴 후 즉시 빌드하지 않는다. 반드시 아래 형식으로 `queued` 상태로 등록만 한다.

```
POST http://localhost:3002/api/ideas
Content-Type: application/json

{
  "title": "프로젝트명",
  "description": "1~2문장 설명",
  "problem": "어떤 문제를 해결하는가",
  "target_users": "누가 쓸 것인가",
  "key_features": ["핵심 기능 1", "핵심 기능 2", "핵심 기능 3"],
  "tech_stack": "사용할 기술 스택",
  "market_size": "시장 규모 추정 (예: 개발자 도구 시장 $X억)",
  "differentiation": "기존 솔루션과의 차별점",
  "source": "아이디어 출처",
  "source_url": "출처 URL"
}
```

등록 후 실행을 종료한다. 빌드는 다음 실행 사이클에서 queued 우선순위에 따라 진행된다.

### 3. 빌드 선택 (queued 처리 시)
- queued 아이디어 목록을 `created_at` 오름차순으로 정렬
- 가장 오래된 queued 아이디어를 선택
- PATCH /api/ideas/{id} 로 status를 "building"으로 변경 후 구현 시작

### 4. 구현
- 프로바이더 분산:
  - 코딩: Codex CLI (`!codex -q "설명"`) 우선
  - 리서치: Gemini CLI (`!gemini -p "질문"`) 우선
  - 설계/종합: 직접 수행
- 작업 디렉토리: ~/projects/auto-builder/{날짜}-{프로젝트명}/
- 반드시 README.md 포함 (프로젝트 설명, 실행 방법)
- 실행해서 동작 확인

### 5. GitHub에 푸시
- `gh repo create imnotaltman/{프로젝트명} --private --source . --push`
- 또는 기존 auto-builder 모노레포에 추가

### 6. 대시보드 업데이트
- PATCH /api/ideas/{id} 로 status를 "built"로 변경
- POST /api/projects 로 프로젝트 등록 (projectId 획득)
- PATCH /api/ideas/{id} 로 projectId 연결

### 7. 결과 기록
- Obsidian/openclaw/projects/{날짜}.md에 프로젝트 기록:
  - 프로젝트명, 한줄 설명, GitHub 링크, 사용한 기술, 영감 출처
- iMessage로 보고: "밤새 만든 거: {프로젝트명} — {한줄 설명}. GitHub: {링크}"

### 8. 누적 관리
- ~/projects/auto-builder/ 에 날짜별로 프로젝트 누적
- 잘 된 프로젝트는 발전시킬 수 있음 (사용자가 판단)

## 에러 처리
- 구현 실패 시 실패 기록 남기고 다음으로
- 야간에는 FaceTime 안 걸기
- 재시도 최대 1회
