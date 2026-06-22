# 문서요약 팀 플러그인

AI 뉴스 수집·요약·검증 파이프라인을 제공하는 Claude Code 플러그인.  
지난 24시간 AI 관련 뉴스(해외 2개 + 국내 3개)를 자동 수집하여 요약 리포트를 생성함.

## 기능

### 슬래시 명령

| 명령 | 설명 |
|------|------|
| `/summary:summary` | AI 뉴스 요약 리포트 자동 생성. `reports/news_{YYYYMMDD}_{HHMM}.md`로 저장 |

### 에이전트

| 에이전트 | 닉네임 | 역할 |
|---------|--------|------|
| `summary:news-collector` | 파키 | 웹 검색으로 AI 뉴스 원문 수집 (해외 2개 또는 국내 3개) |
| `summary:news-summarizer` | 수미 | 수집된 원문을 500자 이내 요약 초안으로 작성 |
| `summary:news-verifier` | 베리 | 요약 초안과 원문을 대조하여 사실 일치·형식 검증 |

### 파이프라인

```
[병렬] 파키(해외 수집) + 파키(국내 수집)
         ↓
[병렬] 수미×5 (각 뉴스 요약 초안)
         ↓
[병렬] 베리×5 (검증, 최대 2회 루프)
         ↓
[클로니] 최종 레포트 통합 → reports/news_{날짜시각}.md 저장
```

## 플러그인 설치

### 마켓플레이스 등록

> 플러그인을 GitHub에 푸시한 후 아래 명령으로 마켓플레이스를 등록함.  
> `{GitHub_저장소_URL}` 자리에 실제 저장소 URL 입력.

```shell
/plugin marketplace add {GitHub_저장소_URL}
```

### 플러그인 설치

```shell
/plugin install summary@summary-team
```

설치 범위 지정 (기본값: user):

```shell
/plugin install summary@summary-team --scope project   # 팀 공유 (버전 관리 포함)
/plugin install summary@summary-team --scope user      # 개인 전체 프로젝트
```

### 로컬 개발/테스트 (마켓플레이스 없이 즉시 로드)

```shell
claude --plugin-dir ./
```

## 플러그인 조회

```shell
/plugin list
```

설치된 플러그인 목록 및 활성화 상태를 확인함.

## 플러그인 업그레이드

```shell
/plugin update summary
```

마켓플레이스에서 최신 버전으로 업데이트함.  
버전이 `plugin.json`의 `version` 필드에 고정된 경우, 해당 필드를 업데이트 후 재배포해야 적용됨.

## 플러그인 삭제

```shell
/plugin uninstall summary
```

## 플러그인 재로드 (개발 중 변경사항 반영)

```shell
/reload-plugins
```

## 디렉터리 구조

```
summary/                        ← 플러그인 루트
├── .claude-plugin/
│   ├── plugin.json             ← 플러그인 매니페스트
│   └── marketplace.json        ← 마켓플레이스 카탈로그
├── skills/
│   └── summary/
│       └── SKILL.md            ← 오케스트레이터 지시서 (모델 자동 호출 비활성화)
├── agents/
│   ├── news-collector.md       ← 파키: 뉴스 수집 에이전트
│   ├── news-summarizer.md      ← 수미: 요약 에이전트
│   └── news-verifier.md        ← 베리: 검증 에이전트
├── commands/
│   └── summary.md              ← /summary:summary 슬래시 명령 진입점
└── reports/                    ← 생성된 뉴스 리포트 저장 위치 (자동 생성)
```

## 출력 예시

```
reports/news_20260622_1430.md
```

```markdown
# 🤖 AI 뉴스 브리핑 — 2026년 06월 22일

> 오늘도 AI 세상은 바쁘게 돌아가고 있습니다. 5분이면 충분해요!

## 🌍 해외 뉴스

### 1. [뉴스 제목]
**핵심 요약**: ...
**시사점**: ...
**출처**: [언론사명](URL)

## 🇰🇷 국내 뉴스

### 3. [뉴스 제목]
...
```
