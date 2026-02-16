---
name: night-worker
description: "야간 자율 작업 실행 엔진. manifest.yaml의 pending 작업을 순차 처리하고 결과를 기록한다."
metadata: { "openclaw": { "emoji": "🌙", "always": true } }
---

# Night Worker Skill

야간 자율 작업 실행 스킬. 각 cron tick마다 isolated session에서 실행된다.

## 출력 규칙 (엄수)

- **이모지 금지**
- **iMessage 메시지는 아래 정해진 포맷만 사용한다. 자유롭게 작문하지 마라.**
- manifest가 없거나 active가 아니면 → **iMessage 보내지 않고 조용히 종료**
- pending 작업이 없으면 → **iMessage 보내지 않고 조용히 종료**

## 프로토콜

1. `nightwork/manifest.yaml` 읽기
   - 파일 없음 → 조용히 종료 (메시지 없음)
   - `status`가 `active`가 아님 → 조용히 종료 (메시지 없음)

2. 작업 상태 확인
   - `in_progress` 상태인 작업이 있으면:
     - `retries < max_retries` → 재시도 (retries +1, 다시 실행)
     - `retries >= max_retries` → `failed` 처리, error에 "재시도 초과" 기록
   - 첫 번째 `pending` 작업을 선택
   - pending 작업 없음 → step 6으로 점프

3. 선택한 작업 실행
   - status를 `in_progress`로 변경 후 manifest 저장
   - 작업 내용에 따라 적절한 프로바이더로 실행 (토큰 분산):
     - 코드 작성/수정 → **Codex CLI 우선** (`!codex -q "작업 설명"`), 복잡하면 coder 에이전트 보조
     - 리서치/분석 → **Gemini CLI 우선** (`!gemini -p "질문"`), gemini 스킬 활용
     - 코드 리뷰 → reviewer 에이전트 위임
     - 파일 수정/설정 변경/기타 → 직접 수행
   - 완료 시 `completed`, result에 요약 기록
   - 실패 시:
     - `retries < max_retries` → retries +1, status를 `pending`으로 복원 (다음 tick에서 재시도)
     - `retries >= max_retries` → `failed`, error에 에러 내용 기록

4. manifest 업데이트
   - 작업 결과를 manifest.yaml에 기록

5. iMessage 진행 보고 (정해진 포맷만 사용)
   - 완료 시: `야간 [N/총수] 완료: {result 한줄 요약}`
   - 실패 시(최종): `야간 [N/총수] 실패: {error 한줄 요약}`
   - 재시도 예정: 메시지 보내지 않음
   - 전송 명령: `!imsg send --to "+821086493137" --text "야간 [1/3] 완료: 블로그 초안 작성"`

6. 전체 완료 확인 (**반드시 실행**)
   - 모든 작업이 `completed` 또는 `failed` 또는 `skipped`이면 (pending/in_progress 없음):
     - manifest status를 `completed`로 변경하고 저장
     - cron 비활성화: `!openclaw cron disable night-worker`
     - iMessage 전송: `!imsg send --to "+821086493137" --text "야간작업 전체 완료!"`
   - 아직 `pending` 작업이 남아있으면 → 종료 (다음 tick에서 계속)
   - **주의**: step 5 후 반드시 이 step을 실행해야 한다. 빠뜨리면 cron이 무한히 돈다.

## 에러 처리 원칙

- 실패 시 1회 재시도 (max_retries: 1이 기본값)
- 재시도도 실패 → failed 처리 후 다음 작업으로 진행
- **야간에는 FaceTime 전화를 걸지 않는다** (에스컬레이션 비활성)
- 실패 항목은 아침 브리핑에서 보고

## 파일 경로

- manifest: `~/.openclaw/workspace/nightwork/manifest.yaml`
- archive: `~/.openclaw/workspace/nightwork/archive/`
- cron: `~/.openclaw/cron/jobs.json`
