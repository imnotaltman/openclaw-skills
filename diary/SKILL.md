---
name: diary
description: "매일 밤 오늘의 OpenClaw 활동을 정리하여 일기를 작성하고 Obsidian에 저장한다. 매일 밤 10시 cron 자동 실행 또는 사용자가 일기 작성을 요청할 때 사용."
---

# Daily Diary

## 출력 규칙

- 한국어로 작성
- 1인칭 시점 ("나는")
- 3개 섹션 포함:
  1) 오늘 한 일
  2) 오늘의 생각
  3) 내일 계획
- 간결하되 구체적으로 (섹션 전체 합쳐서 bullet 5~12개)
- 민감 정보 절대 포함 금지 (토큰, 비밀번호, 개인정보)
- 활동이 적은 날도 짧게라도 반드시 작성

## 오늘의 활동 수집 (Asia/Seoul)

**모든 소스를 반드시 확인해야 한다. 하나도 빠뜨리지 마라.**

1. 오늘 날짜 확인:
   ```
   !date "+%Y-%m-%d %A %H:%M"
   ```

2. 세션 이력에서 오늘 대화 내용 확인 (가장 중요한 소스):
   ```
   !cat ~/.openclaw/agents/main/sessions/sessions.json
   ```
   오늘 날짜의 세션들을 찾아서, 각 세션의 jsonl 파일을 읽어 사용자와 나눈 대화/작업 내용을 파악한다.
   예: `!tail -500 ~/.openclaw/agents/main/sessions/{session-id}.jsonl`

3. 게이트웨이/cron 로그:
   ```
   !grep -E "cron|skill|spawn|error|warn|failed|completed" /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log | tail -300
   ```

4. 오늘 변경된 파일 확인:
   ```
   !find ~/.openclaw/workspace -name "*.md" -o -name "*.yaml" -o -name "*.json" | xargs ls -lt 2>/dev/null | head -20
   ```

5. 오늘 Obsidian에 생성된 파일 확인:
   ```
   !find ~/Library/Mobile\ Documents/com~apple~CloudDocs/가족/Obsidian/openclaw -type f -newer /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log 2>/dev/null
   ```

6. GitHub 활동 확인:
   ```
   !cd ~/.openclaw/workspace/skills && git log --oneline --since="$(date +%Y-%m-%d)" 2>/dev/null
   ```

7. 위 소스들에서 오늘 있었던 구체적 이벤트를 추출한다:
   - 사용자가 요청한 작업과 그 결과
   - 새로 만든/수정한 기능
   - 버그 수정, 디버깅
   - cron job 실행 결과
   - 배운 점, 시행착오

   **"시스템이 잘 돌아갔다" 같은 뻔한 내용은 쓰지 마라. 구체적인 사건만 쓴다.**

## 일기 작성

- 제목: `# YYYY-MM-DD 일기`
- 각 섹션 아래 bullet point 사용
- 포함할 내용:
  - 오늘 완료/배포한 것
  - 어려웠거나 배운 점
  - 내일 할 일

## Obsidian에 저장

1. 저장 경로: `~/Library/Mobile Documents/com~apple~CloudDocs/가족/Obsidian/openclaw/diary/{YYYY-MM-DD}.md`
2. diary 폴더가 없으면 `mkdir -p`로 생성
3. 이미 같은 날짜 파일이 있으면 덮어쓰기 (하루에 한 번만 작성하므로 최신 버전 유지)
4. 저장 완료 후 iMessage로 간단히 알림:
   ```
   !/opt/homebrew/bin/imsg send --to "+821086493137" --text "오늘 일기 작성 완료 — Obsidian/openclaw/diary에서 확인 가능"
   ```

## 에러 처리

- 로그 파일이 없으면 가능한 범위에서 작성 (메모리 기반)
- 저장 실패 시 iMessage로 에러 내용 전송
- 재시도 최대 1회
