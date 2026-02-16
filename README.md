# OpenClaw Skills

이 레포는 OpenClaw 스킬과 자동화 자산을 모아 관리합니다.  
This repo centralizes OpenClaw skills and automation assets.  
로컬의 `~/.openclaw/workspace` 구성을 기준으로 정리됩니다.  
It is based on the active local setup in `~/.openclaw/workspace`.

## Included Skills / 포함된 스킬

### `mission-control`
아침 브리핑 대시보드. 다음을 수집합니다:  
Morning briefing dashboard. Collects:
- 날짜/시간 / Date/time
- 날씨(서울) / Weather (Seoul)
- 최근 iMessage 대화 / Recent iMessage chats
- 읽지 않은 Apple Mail / Unread Apple Mail
- GitHub 알림 및 리뷰 요청 PR / GitHub notifications and PR review requests
- 밤사이 핫 토픽 / Overnight hot topics
- 시스템 상태(업타임, 디스크, 메모리) / System status (uptime, disk, memory)

### `flowmind`
워크플로 체이닝 엔진. YAML 플로우를 순차/병렬 실행합니다.  
Workflow chaining engine. Executes YAML flows sequentially or in parallel.  
Flow 경로 / Path: `~/.openclaw/workspace/flows/`

### `night-worker`
야간 자율 작업 스킬. 매니페스트 기반으로 작업을 실행하고 상태를 업데이트합니다.  
Nightly autonomous worker. Executes manifest tasks, retries once on failure, updates status, and sends minimal iMessage progress updates.

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

## Next Steps / 다음 단계

- 현재 `skills/`, `flows/`, `nightwork/`을 레포로 내보내기  
  Export current `skills/`, `flows/`, and `nightwork/` into this repo
- YAML 유효성 검사 CI 추가  
  Add a CI check for YAML validity
- 매니페스트 스키마 문서화  
  Document the expected manifest schema
