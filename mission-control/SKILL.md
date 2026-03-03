---
name: mission-control
description: "Morning briefing dashboard. Shows weather, messages, mail, GitHub notifications, and system status."
metadata: { "openclaw": { "emoji": "🎛️", "always": true } }
---

# Mission Control

Morning briefing dashboard. Gather all info below and present as a clean, organized summary.

## Instructions

Run each section below and compile results into a single briefing. If any command fails, note it and continue with the rest.

### 1. Date & Time

```bash
date "+%Y-%m-%d %A %H:%M"
```

### 2. Weather (Seoul)

Open-Meteo API (free, no API key required):

```bash
curl -s "https://api.open-meteo.com/v1/forecast?latitude=37.5665&longitude=126.9780&current=temperature_2m,weather_code&timezone=Asia/Seoul" | jq -r '
def wmo_to_text:
  if . == 0 then "Clear"
  elif . == 1 or . == 2 or . == 3 then "Partly cloudy"
  elif . == 45 or . == 48 then "Foggy"
  elif . == 51 or . == 53 or . == 55 then "Drizzle"
  elif . == 61 or . == 63 or . == 65 then "Rain"
  elif . == 71 or . == 73 or . == 75 then "Snow"
  elif . == 80 or . == 81 or . == 82 then "Rain showers"
  elif . == 95 then "Thunderstorm"
  elif . == 96 or . == 99 then "Thunderstorm with hail"
  else "Unknown"
  end;
"Seoul: " + (.current.temperature_2m | tostring) + "°C, " + (.current.weather_code | wmo_to_text)
'
```

### 3. Recent Messages (iMessage)

List recent chats, then fetch history from the most recent ones:

```bash
/opt/homebrew/bin/imsg chats --limit 5 --json
```

For each chat, get recent messages:

```bash
/opt/homebrew/bin/imsg history --chat-id <ID> --limit 3
```

Show sender/chat and preview of each message.

### 4. Unread Mail (Apple Mail)

```bash
osascript -e '
tell application "Mail"
  set unreadMsgs to (messages of inbox whose read status is false and date received > (current date) - 1 * days)
  set output to ""
  repeat with msg in unreadMsgs
    set output to output & "From: " & (sender of msg) & " | Subject: " & (subject of msg) & linefeed
  end repeat
  if output is "" then
    return "No unread mail today."
  end if
  return output
end tell'
```

### 5. GitHub Notifications & PRs

```bash
gh api notifications --jq '.[].subject | "\(.type): \(.title)"' 2>/dev/null | head -10 || echo "No GitHub notifications (or not authenticated)"
```

```bash
gh pr list --search "is:open review-requested:@me" --json number,title,repository --jq '.[] | "#\(.number) \(.title) (\(.repository.name))"' 2>/dev/null | head -5
```

### 6. Hot Topics (overnight)

Search the web for the top 5 trending topics/news from overnight in Korea and globally (tech, AI, major events). Use a web search to find what's buzzing right now.

Present each topic as a one-liner with a brief summary:
```
1. [Topic] — brief summary
2. [Topic] — brief summary
...
```

### 7. System Status

```bash
uptime
```

```bash
df -h / | tail -1 | awk '{print "Disk: " $5 " used (" $3 " / " $2 ")"}'
```

```bash
memory_pressure | head -1
```

### 8. Auto Builder Dashboard

localhost:3002에서 대시보드 API를 호출해서 프로젝트 통계와 리뷰 제안 현황을 수집한다.

```bash
curl -s -H "x-api-key: ae42e21daa510a6711171db0b87e2992e69f6ca1615e3b88" http://localhost:3002/api/projects 2>/dev/null | jq '{total: length, public: [.[] | select(.status=="public")] | length, hold: [.[] | select(.status=="hold")] | length, built: [.[] | select(.status=="built")] | length}' || echo '{"error": "Dashboard API unavailable"}'
```

```bash
curl -s -H "x-api-key: ae42e21daa510a6711171db0b87e2992e69f6ca1615e3b88" "http://localhost:3002/api/proposals?status=pending" 2>/dev/null | jq '{pending: length, titles: [.[:3][] | .title]}' || echo '{"error": "Dashboard API unavailable"}'
```

```bash
curl -s -H "x-api-key: ae42e21daa510a6711171db0b87e2992e69f6ca1615e3b88" http://localhost:3002/api/proposals 2>/dev/null | jq '{total: length, pending: [.[] | select(.status=="pending")] | length, accepted: [.[] | select(.status=="accepted")] | length, committed: [.[] | select(.status=="committed")] | length}' || echo '{"error": "Dashboard API unavailable"}'
```

정리할 내용:
- 프로젝트 통계: total / public / hold / built 수
- 리뷰 제안: pending / accepted / committed / total 수
- 대기 중 제안 상위 3건 제목 (있으면)

API 응답이 실패하거나 서버가 꺼져있으면 "Dashboard offline" 한 줄로 표시하고 계속 진행.

## Output Format

Present as a clean dashboard:

```
=== Mission Control ===
Date: ...
Weather: ...

--- Messages ---
...

--- Mail ---
...

--- GitHub ---
...

--- Hot Topics ---
1. ...
2. ...
3. ...
4. ...
5. ...

--- Auto Builder ---
Projects: X total (Y public, Z hold)
Reviews: A pending / B accepted / C total
Pending:
  - 제안 제목 1
  - 제안 제목 2
  - 제안 제목 3

--- System ---
Uptime: ...
Disk: ...
Memory: ...
========================
```
