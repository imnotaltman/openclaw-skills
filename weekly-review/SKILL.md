---
name: weekly-review
description: "매주 일요일 밤 9시 주간 리뷰. 이번 주 일기를 종합하고 가장 유망한 비즈니스 아이디어 1개를 선정한다."
---

# Weekly Review

매주 일요일 밤 9시에 실행되는 주간 리뷰 스킬.

## 프로토콜

### 1. 이번 주 일기 수집

최근 7일간 일기 파일 읽기:
```
!for i in 0 1 2 3 4 5 6; do
  date=$(date -v-${i}d "+%Y-%m-%d")
  file="$HOME/Library/Mobile Documents/com~apple~CloudDocs/가족/Obsidian/openclaw/diary/${date}.md"
  if [ -f "$file" ]; then echo "=== $date ==="; cat "$file"; echo ""; fi
done
```

### 2. 이번 주 비즈니스 아이디어 수집

최근 7일간 아이디어 파일 제목 읽기:
```
!find "$HOME/Library/Mobile Documents/com~apple~CloudDocs/가족/Obsidian/openclaw/business ideas/" -name "idea-*.md" -mtime -7 -exec grep -H "^# " {} \;
```

### 3. 주간 리뷰 작성

마크다운 파일 작성. 포함 내용:

- **이번 주 주요 성과** — 일기에서 추출한 완료 작업, 새 기능, 수정 사항
- **배운 점** — 시행착오, 새로 알게 된 것
- **다음 주 목표** — 미완료 작업, 개선할 점, 새 계획
- **이번 주 최고의 비즈니스 아이디어** — 수집한 아이디어 중 실행 가능성 + 임팩트 기준으로 1개 선정. 선정 이유 + 간단한 실행 계획 포함.

### 4. Obsidian에 저장

```
!year=$(date "+%Y"); week=$(date "+%V")
!mkdir -p "$HOME/Library/Mobile Documents/com~apple~CloudDocs/가족/Obsidian/openclaw/weekly-review"
```
저장 경로: `~/Library/Mobile Documents/com~apple~CloudDocs/가족/Obsidian/openclaw/weekly-review/{YYYY}-W{NN}.md`

### 5. iMessage 알림

저장 완료 후 exec tool로 전송:
```
!/opt/homebrew/bin/imsg send --to "+821086493137" --text "주간 리뷰 요약 내용"
```
포맷: 주요 성과 1줄, 배운 점 1줄, 다음 주 목표 1줄, 선정 아이디어 제목. 5줄 이내.

message tool 사용 금지. 반드시 exec로 imsg send를 실행해.
