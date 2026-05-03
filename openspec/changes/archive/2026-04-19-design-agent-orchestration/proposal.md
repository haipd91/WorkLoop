## Why

`design-skill` hiện mô tả “parallel agents” nhưng chưa định nghĩa rõ agent nào,
ownership của từng agent là gì, input/output contract ra sao, và orchestrator sẽ
merge kết quả thế nào. Thiết kế này chưa đủ chặt để implement đáng tin, dễ dẫn
đến fake parallelism, overlap phân tích và output khó tổng hợp.

## What Changes

- **MODIFIED**: `design-skill` chuyển từ mô tả “parallel analysis” chung chung sang
  mô hình `design orchestrator + specialist agents`
- Định nghĩa một orchestrator giữ quyền điều phối, tổng hợp và ghi `design.md`
- Định nghĩa bộ specialist agents với trách nhiệm tách biệt theo từng tiêu chí
- Materialize specialist agents thành các agent definition files riêng trong `.codex/agents/`
- Chuẩn hóa input/output contract của từng agent để tránh trùng lặp và dễ merge
- Làm rõ khi nào dùng handoff và khi nào dùng manager-controlled sub-agents
- Bổ sung rule conflict resolution, traceability và reviewability cho workflow `/design`

## Capabilities

### New Capabilities

### Modified Capabilities
- `design-skill`: Chuyển sang agentized orchestration với orchestrator trung tâm và specialist agents có ownership rõ ràng

## Impact

- `.codex/skills/design/SKILL.md` - sửa workflow `/design` theo mô hình orchestrator + specialist agents
- `.codex/agents/` - nơi lưu các specialist agent definitions dùng lại được cho `/design`
- `openspec/specs/design-skill/spec.md` hoặc delta spec tương ứng - cập nhật requirements về agent roles, contracts, orchestration
- `docs/team-ai-workflow.md` - làm rõ `/design` là bước orchestration design review với multi-agent ownership tách biệt
