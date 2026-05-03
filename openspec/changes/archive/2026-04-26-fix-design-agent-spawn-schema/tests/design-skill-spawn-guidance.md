# Test Scenario: design-skill-spawn-guidance

- Source tasks:
  - 1.1 Update `.codex/skills/design/SKILL.md` under specialist analysis execution with Codex-safe spawn guidance.
  - 1.2 Document that `fork_context: true` must not be combined with an explicit `agent_type`.
  - 1.3 Document the required prompt context for each specialist.
  - 1.4 Document schema-rejection retry behavior.
- Related requirements:
  - `design-skill`: Use manager-controlled collaboration by default
- Assumptions:
  - `/design` continues to use managed specialist agents under an orchestrator.
  - The change is documentation/spec-only and does not alter the Codex runtime schema.

## Happy Path

- Khi reviewer đọc phần `Run specialist analysis` trong `.codex/skills/design/SKILL.md`
- Thì docs nêu rõ specialist dispatch trong Codex phải dùng schema-safe pattern.
- Thì docs nêu rõ không combine `fork_context: true` với explicit `agent_type`.
- Thì docs nêu rõ orchestrator truyền workspace path, change name, artifact paths, specialist definition path, và output schema path trong prompt.
- Thì docs nêu rõ specialist tự đọc context từ disk.

## Edge Cases

- Nếu `/design` chạy serialized thay vì parallel, hướng dẫn schema-safe vẫn áp dụng cho từng specialist pass.
- Nếu orchestrator không dùng explicit `agent_type`, docs không bắt buộc bỏ `fork_context`; rule chỉ chặn combination đã gây reject.
- Nếu specialist cần thêm context, docs vẫn giữ nguyên nguyên tắc truyền path/context qua prompt thay vì phụ thuộc forked conversation context.

## Error Cases

- Nếu spawn đầu bị reject do request shape không được Codex chấp nhận
- Thì docs hướng dẫn retry không dùng forked context.
- Thì output specialist vẫn phải được giữ isolated theo agent name để orchestrator tổng hợp an toàn.

## Expected Outcomes

- `/design` skill docs đủ rõ để tránh lỗi spawn schema đã quan sát.
- Orchestrator vẫn giữ ownership final `design.md`.
- Không có hướng dẫn nào yêu cầu sửa runtime `spawn_agent`.

## Notes for Implementation

- Đặt guidance gần bước dispatch specialist agents, không chỉ ở guardrails cuối file.
- Không overgeneralize sang mọi schema constraint chưa được quan sát.
