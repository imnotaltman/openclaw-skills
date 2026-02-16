# OpenClaw Skills

이 레포는 OpenClaw 스킬과 자동화 자산을 모아 관리합니다.  
This repo centralizes OpenClaw skills and automation assets.  
로컬의 `~/.openclaw/workspace` 구성을 기준으로 정리됩니다.  
It is based on the active local setup in `~/.openclaw/workspace`.

## Included Skills / 포함된 스킬

| 이름 | 설명 (한국어) | Description (English) | 상태 |
|------|--------------|----------------------|------|
| 🎛️ **mission-control** | 아침 브리핑 대시보드. 날씨, 메시지, 메일, GitHub 알림, 시스템 상태를 수집. | Morning briefing dashboard. Shows weather, messages, mail, GitHub notifications, and system status. | ✅ Active |
| 🔄 **flowmind** | 워크플로 체이닝 엔진. YAML 플로우를 순차/병렬 실행. | Workflow chaining engine. Define multi-step flows in YAML and execute them sequentially or in parallel. | ✅ Active |
| 🌙 **night-worker** | 야간 자율 작업 실행 엔진. manifest.yaml의 pending 작업을 순차 처리. | Autonomous night worker. Processes pending tasks from manifest.yaml and logs results. | ✅ Active |
| 📔 **diary** | 매일 밤 10시 자동 실행. OpenClaw 활동을 정리하여 Obsidian 일기 작성. | Daily diary generator. Runs at 10 PM, summarizes OpenClaw activities into Obsidian. | ✅ Active |
| 📅 **weekly-review** | 매주 일요일 밤 9시 실행. 주간 일기를 종합하여 주간 리뷰 작성. | Weekly review generator. Runs every Sunday at 9 PM, summarizes the week's diary entries. | 🚧 New |

### Skill Details / 상세 설명

#### `mission-control` 🎛️
아침 브리핑 대시보드. 다음을 수집합니다:
Morning briefing dashboard. Collects:
- 날짜/시간 / Date/time
- 날씨(서울, Open-Meteo API) / Weather (Seoul, Open-Meteo API)
- 최근 iMessage 대화 / Recent iMessage chats
- 읽지 않은 Apple Mail / Unread Apple Mail
- GitHub 알림 및 리뷰 요청 PR / GitHub notifications and PR review requests
- 밤사이 핫 토픽 / Overnight hot topics
- 시스템 상태(업타임, 디스크, 메모리) / System status (uptime, disk, memory)

#### `flowmind` 🔄
워크플로 체이닝 엔진. YAML 플로우를 순차/병렬 실행합니다.
Workflow chaining engine. Executes YAML flows sequentially or in parallel.
Flow 경로 / Path: `~/.openclaw/workspace/flows/`

예시 플로우 / Example flow: `morning.yaml` (아침 브리핑 + 비즈니스 아이디어 생성)

#### `night-worker` 🌙
야간 자율 작업 스킬. 매니페스트 기반으로 작업을 실행하고 상태를 업데이트합니다.
Nightly autonomous worker. Executes manifest tasks, retries once on failure, updates status, and sends minimal iMessage progress updates.

Manifest 경로 / Path: `nightwork/manifest.yaml`

#### `diary` 📔
매일 밤 10시 cron 자동 실행. 오늘의 OpenClaw 활동을 정리하여 Obsidian 일기를 작성합니다.
Runs daily at 10 PM. Summarizes OpenClaw activities into a diary entry in Obsidian.

저장 경로 / Save path: `Obsidian/openclaw/diary/YYYY-MM-DD.md`

#### `weekly-review` 📅 (NEW)
매주 일요일 밤 9시 실행. 최근 7일간의 일기를 읽어 주간 리뷰를 작성합니다.
Runs every Sunday at 9 PM. Reads the past 7 days of diary entries and generates a weekly review.

저장 경로 / Save path: `Obsidian/openclaw/weekly-review/YYYY-WNN.md`

## Related Directories / 관련 디렉토리

- `skills/` — 스킬 정의 및 스크립트 / Skill definitions and scripts
- `flows/` — 플로우 정의 (YAML) / Flow definitions (YAML)
- `nightwork/` — 야간 작업 매니페스트 및 아카이브 / Night-worker manifest and archive
- `scripts/` — 헬퍼 스크립트 / Helper scripts

## How This Repo Is Structured / 레포 구조

이 레포는 아래 경로의 내용을 미러링하거나 내보내는 용도입니다.  
This repo mirrors or exports the automation logic from:

```
~/.openclaw/workspace
```

## Installation / 설치 방법

1. 레포 클론 / Clone this repo:
   ```bash
   git clone https://github.com/imnotaltman/openclaw-skills.git
   cd openclaw-skills
   ```

2. 스킬을 OpenClaw workspace에 복사 / Copy skills to OpenClaw workspace:
   ```bash
   cp -r ./* ~/.openclaw/workspace/skills/
   ```

3. Cron job 설정 (선택) / Configure cron jobs (optional):
   ```bash
   openclaw cron enable diary        # 매일 밤 10시 일기 작성
   openclaw cron enable night-worker # 야간 작업 실행 (필요 시)
   openclaw cron enable weekly-review # 매주 일요일 밤 9시 주간 리뷰
   ```

## Usage Examples / 사용 예시

### 아침 브리핑 / Morning Briefing
```bash
openclaw skill mission-control
```

또는 플로우로 실행 / Or run with flow:
```bash
openclaw skill flowmind morning
```

### 일기 작성 / Write Diary
```bash
openclaw skill diary
```

### 야간 작업 등록 / Register Night Tasks
사용자가 잠들기 전 iMessage로:
```
자는 동안 해놔:
1. HEARTBEAT.md 설정
2. mission-control 날씨 API 수정
3. weekly-review 스킬 생성
```

James(AI)가 자동으로 `nightwork/manifest.yaml`을 생성하고 cron을 활성화합니다.

### 주간 리뷰 / Weekly Review
```bash
openclaw skill weekly-review
```

## Next Steps / 다음 단계

- ✅ 스킬 테이블 및 설치/사용 가이드 추가 / Added skill table and installation/usage guide
- YAML 유효성 검사 CI 추가 / Add a CI check for YAML validity
- 매니페스트 스키마 문서화 / Document the expected manifest schema
