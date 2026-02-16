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

--- System ---
Uptime: ...
Disk: ...
Memory: ...
========================
```
