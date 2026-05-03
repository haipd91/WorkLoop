# Brief: Fix Design Agent Spawn Schema

## Problem

Khi chạy skill `/design`, lần spawn specialist agent đầu có thể bị reject vì orchestration gọi `spawn_agent` sai schema: kết hợp `fork_context: true` với explicit `agent_type`. Điều này làm workflow `/design` bị lỗi dù hướng retry đúng là không fork context và để mỗi agent tự đọc workspace/artifacts.

## Goal

Skill `/design` phải mô tả rõ cách spawn specialist agents hợp lệ trong Codex để tránh lỗi schema tương tự. Khi chạy lại `/design`, orchestrator phải biết dùng schema-safe pattern: không combine `fork_context: true` với explicit `agent_type`, truyền path/context cần thiết qua prompt, và để agent tự đọc workspace.

## Scope

### In scope

- Cập nhật `.codex/skills/design/SKILL.md` với guardrail runtime cho `spawn_agent`.
- Ghi rõ không dùng `fork_context: true` cùng explicit `agent_type`.
- Ghi rõ specialist agents phải tự đọc workspace/artifacts từ path được truyền trong prompt.
- Cập nhật OpenSpec artifact/test tối thiểu nếu cần để capture behavior này.

### Out of scope

- Không sửa runtime/tool schema `spawn_agent`.
- Không sửa application code.
- Không redesign toàn bộ `/design` workflow.
- Không thay đổi `DesignAnalysisResult` output contract.
- Không thay đổi agent registry/schema nếu không cần.

## Constraints

- Diff nhỏ, tập trung vào skill docs/OpenSpec artifacts.
- Giữ tương thích workflow `/design` hiện tại.
- Không thêm dependency.
- Không mở rộng scope sang implementation runtime.

## Acceptance Criteria

- [ ] `/design` docs có rule schema-safe cho specialist agent spawning.
- [ ] Docs nêu rõ không combine `fork_context: true` với explicit `agent_type`.
- [ ] Docs nêu rõ mỗi specialist agent tự đọc workspace/artifacts từ path được truyền vào prompt.
- [ ] Docs mô tả fallback/retry hợp lệ: không fork context, truyền cwd/change/artifact paths rõ ràng.
- [ ] Nếu có OpenSpec artifact/test liên quan, scenario lỗi spawn schema được capture tối thiểu.
