---
name: weekly-review
description: "Weekly review skill. Summarizes the week's diary entries, highlights achievements, learnings, and selects the most promising business idea."
metadata: { "openclaw": { "emoji": "📅", "always": true } }
---

# Weekly Review

매주 일요일 밤 9시에 실행되는 주간 리뷰 스킬입니다.

## Instructions

### 1. 이번 주 일기 수집

최근 7일간의 일기 파일 읽기:

```bash
for i in {0..6}; do
  date=$(date -v-${i}d "+%Y-%m-%d")
  file="/Users/imnotaltman/.openclaw/workspace/diary/${date}.md"
  if [ -f "$file" ]; then
    echo "=== $date ==="
    cat "$file"
    echo ""
  fi
done
```

### 2. 주간 리뷰 작성

다음 항목들을 포함하여 리뷰 작성:

- **이번 주 주요 성과** (Major Achievements)
- **배운 점** (Key Learnings)
- **다음 주 목표** (Next Week Goals)
- **가장 유망한 비즈니스 아이디어** (Most Promising Business Idea)
  - 이번 주 생성된 비즈니스 아이디어 중 가장 실행 가능성이 높고 임팩트가 큰 것 1개 선정
  - 선정 이유와 간단한 실행 계획 포함

### 3. 파일 저장

주차 계산 및 파일 저장:

```bash
# ISO 주차 계산 (월요일 기준)
year=$(date "+%Y")
week=$(date "+%V")
output_file="/Users/imnotaltman/Library/Mobile Documents/iCloud~md~obsidian/Documents/openclaw/weekly-review/${year}-W${week}.md"

# 디렉토리 생성
mkdir -p "$(dirname "$output_file")"

# 리뷰 내용 저장
cat > "$output_file" <<'EOF'
# Weekly Review ${year}-W${week}

## 이번 주 주요 성과

...

## 배운 점

...

## 다음 주 목표

...

## 가장 유망한 비즈니스 아이디어

**아이디어:** ...

**선정 이유:** ...

**실행 계획:** ...

---
Generated on $(date "+%Y-%m-%d %H:%M")
EOF
```

### 4. iMessage 알림

주간 리뷰 요약을 iMessage로 전송:

```
📅 주간 리뷰 완료

이번 주 핵심:
- 주요 성과: [한 줄 요약]
- 배운 점: [한 줄 요약]
- 다음 주: [한 줄 요약]
- 선정 아이디어: [제목]

자세한 내용은 Obsidian에서 확인하세요.
```

## Cron Job 설정

매주 일요일 밤 9시 실행:

```json
{
  "id": "weekly-review",
  "name": "Weekly Review",
  "description": "Generate weekly review from diary entries",
  "schedule": "0 21 * * 0",
  "enabled": true,
  "command": "openclaw skill weekly-review"
}
```

이 설정을 `~/.openclaw/config/jobs.json`에 추가하세요.
