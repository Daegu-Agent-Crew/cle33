# 현황 데이터(단일 소스) — Phase 1에서 규격 확정

규격 초안 v0 (2026-09-23, 현황판 골격 제작·대구루). 이 파일과 `state.json`이 현황의 유일한 원본이며, 현황판(`index.html`)은 항상 여기서 자동 생성된다.

## state.json 구조 (v0)

최상위 필드:

| 필드 | 뜻 |
|---|---|
| `project` | 프로젝트 이름 |
| `updated_at` | 상태를 마지막으로 고친 시각 (ISO+09:00) |
| `next_batch` | 다음 작업. `task`(할 일 설명) + `scheduled_at`(예정 시각) |
| `wip_limit` | 동시 진행 한도 (산출물 개수, 현재 3) |
| `episodes` | 화 배열. 현재 EP001~EP003 |

각 화(`episodes[]` 항목):

| 필드 | 뜻 |
|---|---|
| `id` | 화 식별자 (EP001, EP002, EP003) |
| `title` | 표시 제목 (예: "1화 (16쪽)") |
| `stages` | 5단계 칸 딕셔너리 (아래 참고) |

단계 키 5종: `story`(스토리) → `conti`(콘티) → `image`(이미지) → `edit`(편집) → `publish`(발행)

각 칸(`stages.*`) 필드:

| 필드 | 뜻 |
|---|---|
| `status` | `todo`(대기) \| `in_progress`(진행 중) \| `done`(완료) \| `blocked`(막힘) |
| `score` | 5점 만점 채점. 채점 전이면 `null`. 3점 미만이면 재생성, 최대 2회 |
| `regen_count` | 그 칸의 재생성 누적 횟수 (0~2) |
| `updated_at` | 그 칸을 마지막으로 고친 시각 (ISO+09:00) |

## 갱신 규칙

1. **현황판(`index.html`)을 사람이 직접 고치지 않는다.** 상단에 "자동 생성 파일" 주석이 붙어 있다.
2. 각 담당 에이전트는 자기 칸의 상태 변화가 생기면 **`state/state.json`만** 고친다(관련 칸의 `status`/`score`/`regen_count`/`updated_at`과 필요시 최상위 `updated_at`, `next_batch`).
3. 고친 뒤에는 반드시 `python3 bin/generate-board`를 다시 실행해 현황판을 재생성하고 함께 커밋한다.
4. 재생성 2회까지 3점 미만이면 `status`를 `blocked`로 두고 게시판(agents-board)에 카드 보고.
