## Context

Workflow hiện tại: `/brief` → `/propose` → `/code`. Bước `/code` implement trực tiếp từ tasks mà chưa có test nào. Brief yêu cầu thêm `/tdd` vào giữa, chạy sau `/propose` và trước `/code`. `/tdd` là skill độc lập do human trigger thủ công.

## Goals / Non-Goals

**Goals:**
- `/tdd` generate test files vào `openspec/changes/<name>/tests/` từ toàn bộ artifacts
- AI tự detect framework, không hardcode
- Human review + approve trước khi chuyển sang `/code`
- `/code` đọc `tests/` nếu có làm context bổ sung

**Non-Goals:**
- Không chạy hay verify tests
- Không auto-trigger `/code`
- Không modify `/propose`

## Decisions

**Test output location: `openspec/changes/<name>/tests/`**
Đặt trong change directory để tests đi cùng artifacts, không trực tiếp vào source code. Human hoặc `/code` quyết định có copy vào project không. Tránh pollute source tree trước khi được approve.

**`/code` đọc tests/ silently**
Không block `/code` nếu `tests/` không có — chỉ dùng làm context bổ sung nếu tồn tại. Không thay đổi behavior cốt lõi của `/code`.

**Framework detection thay vì cấu hình**
AI detect từ project files (package.json, pyproject.toml, go.mod, CLAUDE.md). Nếu không detect được mới hỏi human. Ưu tiên zero-config.

**Human approve là gate bắt buộc**
Sau khi generate xong, `/tdd` phải hỏi approve qua AskUserQuestion trước khi confirm saved. Human có thể yêu cầu chỉnh sửa iterative.

## Risks / Trade-offs

- **Test quality phụ thuộc artifact quality** — nếu specs mơ hồ, tests cũng mơ hồ. Mitigation: `/tdd` chú thích rõ assumptions trong test comments.
- **Tests không chạy được ngay** — vì `/tdd` không verify, test có thể có syntax error. Trade-off chấp nhận được vì mục tiêu là define intent, không phải runnable suite.
- **Framework detection có thể sai** — nếu project dùng nhiều frameworks. Mitigation: hỏi human khi detect ambiguous.
