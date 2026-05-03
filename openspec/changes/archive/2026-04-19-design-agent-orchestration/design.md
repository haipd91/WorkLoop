## Context

`design-skill` vừa được thêm vào workflow như bước technical design review giữa
`/propose` và `/code`. Tuy nhiên thiết kế hiện tại mới dừng ở mức:

- tự nhóm criteria
- chạy parallel agents
- tổng hợp thành `design.md`

Vấn đề là kiến trúc này chưa có contract thực thi rõ ràng. Nếu implement theo mô
tả hiện tại, hệ thống rất dễ gặp các lỗi sau:

- nhiều agent phân tích cùng một mảng vì không có ownership rõ
- orchestrator không biết merge conflict theo rule nào
- “parallel agents” chỉ là prompt wording, không thành workflow đáng tin
- output từng agent không có schema thống nhất nên `design.md` thiếu ổn định

Theo docs OpenAI, khi bài toán cần orchestration nhiều bước, tool use, handoffs,
guardrails và context, hướng khuyến nghị là dùng Agents SDK thay vì tự điều phối
toàn bộ bằng Responses API. Đồng thời, pattern `agent-as-tool` phù hợp hơn
`handoff` khi cần giữ một trung tâm điều phối và global view cho output cuối.

## Goals / Non-Goals

**Goals:**
- Đổi `/design` sang mô hình một orchestrator agent + nhiều specialist agents
- Mỗi specialist agent có ownership riêng để tránh overlap
- Chuẩn hóa input/output contract cho từng agent
- Giữ `design.md` là output do orchestrator tổng hợp và sở hữu cuối cùng
- Làm rõ rule chọn `agent-as-tool` mặc định và khi nào handoff mới hợp lý

**Non-Goals:**
- Không yêu cầu implement Agents SDK runtime thật trong change này nếu repo chưa có app layer tương ứng
- Không mở rộng `/code` hay `/tdd` ngoài phần cần làm rõ contract với `/design`
- Không biến specialist agents thành các workflow độc lập có quyền ghi file cuối

## Decisions

**1. Dùng orchestrator trung tâm thay vì handoff tự do**

`/design` cần một global view thống nhất để tạo `design.md`. Vì vậy orchestrator
phải giữ quyền:

- chọn agents cần dùng
- phát task cho từng agent
- tổng hợp findings
- resolve conflict
- ghi file cuối

Handoff chỉ nên dùng khi một specialist thực sự phải sở hữu next response. Với
`design-skill`, đó không phải pattern mặc định.

Alternative considered:
- Để các agents handoff cho nhau tự do. Bị loại vì mất thread điều phối trung tâm và tăng nguy cơ thiết kế rời rạc.

**2. Mỗi agent chỉ giữ một nhóm câu hỏi chính**

Bộ agent khởi đầu nên gồm:

- `Architecture`
- `Impact`
- `Data Flow`
- `Failure Modes`
- `Review and Docs`

Mỗi agent phải có boundary rõ ràng. Nếu cần thêm `Security` hoặc
`Performance`, orchestrator bật theo context chứ không mặc định bật hết.

Alternative considered:
- Một checklist động nhưng không gắn thành agent cố định. Bị loại vì vẫn mơ hồ về ownership.

**3. Lưu specialist agents trong `.codex/agents/`**

Specialist agents nên được materialize thành các file riêng dưới `.codex/agents/`
và nên phản ánh agent surface theo hướng của OpenAI docs: `name`,
`instructions`, `tools`, `handoffDescription`, `handoffs`, và `outputType`,
thay vì chỉ là checklist prose bị nhúng trong `.codex/skills/design/SKILL.md`.

Lợi ích:

- agent definitions trở thành first-class artifact
- dễ sửa từng agent độc lập
- dễ trace drift giữa orchestrator và specialist
- dễ tái sử dụng cho workflow khác sau này

Alternative considered:
- Đặt agents trong `.codex/skills/design/agents/`. Bị loại vì coupling quá chặt vào một skill duy nhất và làm agent definitions khó tái sử dụng.

**4. Dùng pattern agent-as-tool làm mặc định**

Specialist agents nên được gọi như sub-agents hoặc tools dưới quyền orchestrator.
Điều này giữ:

- một luồng điều khiển duy nhất
- khả năng chạy song song
- merge logic minh bạch
- tracing dễ đọc hơn

Alternative considered:
- Handoff-first architecture. Bị loại vì phù hợp hơn cho conversational routing chứ không phải synthesis artifact.

**5. Chuẩn hóa output schema của specialist agents**

Mọi specialist agent phải trả về cùng skeleton:

- scope analyzed
- findings
- impacted artifacts or modules
- assumptions
- risks
- unresolved questions
- recommended decisions

Orchestrator chỉ merge những output đúng schema này.

Alternative considered:
- Cho mỗi agent trả free-form text. Bị loại vì khó normalize và khó review.

**6. Conflict resolution là trách nhiệm explicit của orchestrator**

Khi outputs mâu thuẫn:

- ưu tiên proposal / brief / specs làm source of truth
- ghi nhận conflict trong `Open Questions` nếu chưa thể chốt
- không được âm thầm bỏ mâu thuẫn

Alternative considered:
- “best effort merge” không ghi conflict. Bị loại vì che giấu rủi ro thiết kế.

## Risks / Trade-offs

- **Nhiều agent hơn làm tăng overhead** -> Mitigation: chỉ bật core agents mặc định, các agent khác là optional
- **Agent contracts quá cứng có thể giảm linh hoạt** -> Mitigation: cho phép orchestrator thêm agent theo context nhưng không bỏ schema chuẩn
- **Docs nói theo Agents SDK nhưng repo chưa có runtime thật** -> Mitigation: tách rõ mức spec/design với mức implementation runtime
- **Handoff và agent-as-tool bị dùng lẫn lộn** -> Mitigation: quy định agent-as-tool là default, handoff là exception có điều kiện
- **Agent files bị drift khỏi orchestrator skill** -> Mitigation: orchestrator chỉ tham chiếu `.codex/agents/` và giữ output schema chung bắt buộc
