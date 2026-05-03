# AI Native Codex Workflow

## 1. Mục tiêu

- Repo này định nghĩa workflow AI Native cho Codex.
- Mục tiêu không phải viết tài liệu tham khảo chung, mà tạo một hệ điều hành làm việc:
  - human ra quyết định
  - AI hỏi rõ vấn đề
  - AI tạo artifact
  - AI phân tích bằng specialist agents
  - AI implement theo task đã được chốt
  - human review, approve, accept risk

## 2. Nguyên tắc cốt lõi

- Artifact-driven, không chat-driven.
- Spec trước code.
- Design và test intent phải rõ trước implementation.
- Task phải nhỏ, có thể review, test, rollback.
- AI được quyền tăng tốc phân tích và thực thi, nhưng không được tự chốt scope/risk thay human.
- Project được mở rộng workflow, nhưng không được làm yếu core gates.
- Local OpenSpec CLI patch phải được ghi lại trong repo docs nếu workflow phụ thuộc vào behavior chưa upstream.

## 3. Mental Model

```text
Ý tưởng
  |
  v
brief.md
  |
  v
proposal.md + specs/
  |
  v
design.md
  |
  v
design review decision
  |-- required -> /design
  |-- skippable -> record reason
  |
  v
tests/
  |
  v
tasks.md -> code changes
  |
  v
review.md -> /archive
```

- Chat chỉ là interface.
- Artifact mới là source of truth.
- Mỗi bước phải để lại output có thể đọc lại, review lại, và dùng làm input cho bước sau.

## 4. Command Workflow

### 4.1 `/explore`

- Dùng khi:
  - ý tưởng còn mơ hồ
  - cần so sánh hướng làm
  - cần đọc codebase trước khi chốt change
- Output:
  - không bắt buộc tạo file
  - có thể chốt insight để chuyển sang `/brief` hoặc `/propose`
- Rủi ro:
  - nếu kéo dài quá lâu mà không tạo artifact, workflow quay lại chat-driven.

### 4.2 `/brief`

- Dùng khi:
  - bắt đầu một change mới
  - cần làm rõ problem, goal, scope, constraints, acceptance criteria
- Cách hoạt động:
  - AI hỏi theo 3 vòng:
    - problem + goal
    - scope in/out
    - constraints + acceptance criteria
  - AI tạo draft `brief.md`
  - human approve trước khi save
- Output:
  - `openspec/changes/<change-name>/brief.md`
- Gate:
  - không save nếu human chưa approve.

### 4.3 `/propose`

- Dùng khi:
  - đã có ý tưởng đủ rõ để tạo change artifacts
  - muốn tạo proposal/spec/design/tasks theo OpenSpec
- Cách hoạt động:
  - tạo change directory nếu chưa có
  - đọc `brief.md` nếu có
  - chạy theo artifact graph của OpenSpec
  - tạo artifact theo dependency order
- Output chính:
  - `proposal.md`
  - `specs/**/*.md`
  - `design.md`
  - `tasks.md`
- Gate:
  - proposal phải nói rõ why/scope/capability impact
  - specs phải có requirement testable và scenario
  - tasks phải là checkbox nhỏ, dependency-ordered, verifiable.

### 4.4 `/design`

- Dùng khi:
  - cần technical handoff trước implementation
  - change có architecture, dependency, security, migration, ambiguity, hoặc cross-module risk
- Có thể skip khi:
  - docs-only
  - test-only
  - text/label rename đơn giản
  - config nhỏ không đổi behavior
  - bug fix rất hẹp, scope file rõ
  - `proposal.md`, `specs/`, `tasks.md` đã đủ rõ
  - human explicitly accept skip
- Không được skip khi:
  - đổi architecture/module boundary
  - đổi data flow/state/API/schema/contract
  - đụng auth, permission, validation, sensitive data, trust boundary
  - thêm dependency mới
  - migration/data model/rollback phức tạp
  - có performance/concurrency/scale/cost risk
  - requirement còn mơ hồ
  - thay đổi nhiều module hoặc blast radius chưa rõ
- Cách hoạt động:
  - một Design Orchestrator điều phối
  - specialist agents phân tích theo vai trò riêng
  - chỉ orchestrator tổng hợp và ghi `design.md`
- Input:
  - `proposal.md`
  - `tasks.md`
  - `brief.md` nếu có
  - `specs/**/*.md` nếu có
  - `design.md` hiện có nếu refine
- Output:
  - `openspec/changes/<name>/design.md`
- Gate:
  - nếu `/design` chạy, design phải được human approve trước khi làm `/tdd` hoặc `/apply`
  - nếu `/design` skip, phải ghi rõ lý do skip và human accept.

### 4.5 `/tdd`

- Dùng khi:
  - cần định nghĩa expected behavior trước code
  - muốn AI tạo test intent từ artifacts
- Cách hoạt động:
  - đọc proposal/specs/design/tasks
  - detect test framework
  - nếu rõ framework: tạo runnable tests
  - nếu không rõ framework: tạo scenario-based test specs
- Output:
  - `openspec/changes/<name>/tests/`
- Gate:
  - human review và approve test files trước khi chuyển sang implementation.

### 4.6 `/apply`

- Dùng khi:
  - artifacts đã đủ để implement
  - cần làm từng task trong `tasks.md`
- Cách hoạt động:
  - chọn change
  - đọc OpenSpec status
  - đọc context files từ apply instructions
  - đọc `tests/` nếu có
  - implement từng pending task
  - mark checkbox complete ngay sau khi task xong
- Output:
  - code/docs/test changes
  - `tasks.md` được cập nhật checkbox
- Gate:
  - không đoán nếu task mơ hồ
  - không mở rộng scope ngoài artifact
  - verification phải chạy hoặc phải ghi rõ vì sao chưa chạy được.
  - khi implementation xong, phải handoff sang `/review`, không đi thẳng `/archive`.
  - nếu handoff này phụ thuộc patch local OpenSpec CLI, xem `docs/openspec-local-cli-patch.md`.

### 4.7 `/review`

- Dùng khi:
  - `/apply` đã implement xong
  - cần review implementation trước khi archive
  - cần record findings, tests run, residual risk, verdict
- Cách hoạt động:
  - đọc proposal/specs/design/tasks/tests nếu có
  - đọc diff/worktree state
  - review theo code-review stance
  - ghi `openspec/changes/<name>/review.md`
- Output:
  - `review.md` với metadata parseable:
    - `verdict: pass|fail`
    - `blocking_findings: none|present`
    - `reviewed_ref`
    - `reviewed_diff`
    - `reviewed_at`
  - sections:
    - Findings
    - Tests Run
    - Residual Risk
    - Verdict
- Gate:
  - trong schema `spec-driven-custom`, `review` là artifact chính thức sau `tests`
  - `openspec status` chỉ coi `review: done` là `review.md` tồn tại, không phải verdict pass
  - blocking findings => `verdict: fail`
  - skipped tests/missing artifacts phải ghi vào residual risk
  - review evidence phải durable, không chỉ nằm trong chat.

### 4.8 `/archive`

- Dùng khi:
  - change đã implement xong
  - tasks đã complete
  - artifacts đã đủ
- Cách hoạt động:
  - kiểm tra artifact completion
  - kiểm tra task checkbox
  - kiểm tra OpenSpec `review` artifact nếu schema expose
  - parse `review.md` metadata dù `review` artifact đã done
  - kiểm tra delta specs nếu có
  - move change vào `openspec/changes/archive/YYYY-MM-DD-<name>/`
- Gate:
  - nếu còn incomplete artifact/task, phải cảnh báo và human confirm.
  - nếu `review` artifact thiếu/chưa done, thiếu `review.md`, review malformed, stale, verdict không pass, hoặc còn blocking findings thì phải block.
  - trước khi archive, phải hiện review verdict và residual risk để human accept risk.

## 5. Specialist Agent Model Cho `/design`

### 5.1 Core agents

- `Architecture`
  - phân tích boundaries, layering, dependency direction, architectural fit
  - file: `.codex/agents/architecture.md`
- `Impact`
  - phân tích files/modules/workflows bị ảnh hưởng và blast radius
  - file: `.codex/agents/impact.md`
- `Failure Modes`
  - phân tích unhappy paths, edge cases, degraded behavior, recovery
  - file: `.codex/agents/failure-modes.md`

### 5.2 Optional agents

- `Data Flow`
  - bật khi change có state, contract, handoff, artifact dependency
- `Review and Docs`
  - bật khi change ảnh hưởng docs, gates, rollout, approval
- `Security`
  - bật khi change đụng auth, authz, sensitive data, validation, trust boundary
- `Performance`
  - bật khi change có latency, throughput, scale, concurrency, cost risk

### 5.3 Contract

- Registry:
  - `.codex/agents/registry.json`
- Output schema:
  - `.codex/schemas/design-analysis-result.schema.json`
- Mỗi specialist phải trả về:
  - `scope_analyzed`
  - `findings`
  - `impacted_areas`
  - `assumptions`
  - `risks`
  - `unresolved_questions`
  - `recommended_decisions`

### 5.4 Quy tắc vận hành

- Orchestrator chọn agent theo độ phức tạp.
- Core agent failure là blocker.
- Optional agent failure được ghi thành risk nếu không block.
- Agent không ghi `design.md` trực tiếp.
- Agent không làm trùng vai trò của nhau.
- Conflict giữa agents phải được ghi vào `Open Questions`, không được bỏ qua.

## 6. OpenSpec Contract

- Workflow schema chính:
  - `openspec/schemas/spec-driven-custom/schema.yaml`
- Project context:
  - `openspec/config.yaml`
- Artifact order:
  - `proposal`
  - `specs`
  - `design`
  - `tasks`
  - `tests`
- Apply requires:
  - `specs`
  - `tasks`
  - `tests`

Điểm cần chú ý:

- `proposal.md` trả lời vì sao cần change.
- `specs/**/*.md` trả lời hệ thống phải làm gì.
- `design.md` trả lời làm như thế nào và trade-off nào được chọn.
- `tasks.md` chia implementation thành việc nhỏ.
- `tests/` giữ expected behavior để `/apply` không code mù.

## 7. Daily Usage

### Change nhỏ, rõ

```text
/propose <mô tả ngắn>
/tdd <change-name>
/apply <change-name>
/review <change-name>
/archive <change-name>
```

- Chỉ dùng flow này khi `/design` review được skip có lý do rõ.
- Ví dụ: docs-only, test-only, rename text, config nhỏ, bug fix hẹp.
- Không nhảy thẳng từ `/propose` sang `/apply`.

### Change cần design review

```text
/propose <mô tả ngắn>
/design <change-name>
/tdd <change-name>
/apply <change-name>
/review <change-name>
/archive <change-name>
```

- Dùng khi có architecture/data/security/performance/migration/ambiguity/cross-module risk.
- Nếu nghi ngờ, chạy `/design`; chi phí review thấp hơn chi phí sửa sai sau implementation.

### Change còn mơ hồ

```text
/explore
/brief
/propose <change-name>
/design <change-name>
/tdd <change-name>
/apply <change-name>
/review <change-name>
/archive <change-name>
```

### Change có rủi ro kiến trúc

```text
/brief
/propose <change-name>
/design <change-name>
  - chạy Architecture
  - chạy Impact
  - chạy Failure Modes
  - bật thêm Security/Data Flow/Performance nếu cần
/tdd <change-name>
/apply <change-name>
/review <change-name>
```

## 8. Definition Of Done

Một change chỉ được xem là xong khi:

- Problem và goal rõ.
- Scope in/out rõ.
- Requirement có scenario testable.
- Design decision và trade-off được ghi lại nếu change có rủi ro.
- Task nhỏ, dependency-ordered, checkbox complete.
- Test intent hoặc test code tồn tại trong `tests/`.
- Verification đã chạy, hoặc lý do không chạy được đã được ghi rõ.
- `review.md` tồn tại và có `verdict: pass`, `blocking_findings: none`.
- Human đã review và accept risk.
- Change đã archive nếu không còn active work.

## 9. Anti-Patterns

- Code ngay từ chat mà không có artifact.
- Chạy `/apply` ngay sau `/propose` khi chưa qua design/test gates.
- Dùng `/apply` khi `tasks.md` còn mơ hồ.
- Tạo design quá dài nhưng không có quyết định cụ thể.
- Bật mọi specialist agent cho mọi change.
- Để nhiều agent phân tích cùng một câu hỏi.
- Mark task complete trước khi verify.
- Sửa ngoài scope rồi biện minh là cleanup.
- Đi thẳng từ `/apply` sang `/archive` mà không chạy `/review`.
- Archive khi `review.md` thiếu, fail, stale, malformed, hoặc còn blocking findings.
- Archive change khi còn open question chưa được human accept.

## 10. Ví dụ thực tế

### Ví dụ 1: Rename command `/code` thành `/apply`

- `/brief`:
  - làm rõ vấn đề: tên `/code` sai domain meaning
  - goal: thống nhất implementation command là `/apply`
- `/propose`:
  - tạo proposal/spec/tasks cho command surface
- `/design`:
  - phân tích impact lên docs, skill instructions, handoff wording
- `/tdd`:
  - tạo scenario test cho command reference và artifact loading
- `/apply`:
  - sửa command docs và skill text
  - mark tasks complete
- `/review`:
  - review diff và ghi `review.md`
- `/archive`:
  - move change vào archive theo ngày

### Ví dụ 2: Formalize `/design` agent contracts

- `/brief`:
  - xác định gap: agents có markdown instruction nhưng thiếu schema/registry/fixture
- `/propose`:
  - tạo capability `design-agent-contracts`
- `/design`:
  - chốt orchestrator pattern
  - chốt core/optional agents
  - chốt failure policy
- `/apply`:
  - thêm `.codex/agents/registry.json`
  - thêm `.codex/schemas/design-analysis-result.schema.json`
  - thêm fixtures
- `/review`:
  - xác nhận artifacts, docs, fixtures đúng contract
- `/archive`:
  - lưu change hoàn thành.

## 11. Checklist Trước Khi Code

- Có `openspec/changes/<name>/proposal.md`.
- Có `openspec/changes/<name>/specs/**/*.md`.
- Có `openspec/changes/<name>/design.md` nếu change có rủi ro.
- Có `openspec/changes/<name>/tasks.md`.
- Có `openspec/changes/<name>/tests/` hoặc lý do rõ vì sao chưa có.
- Task tiếp theo đủ nhỏ để làm trong một session.
- Phạm vi file dự kiến sửa đã rõ.
- Verification command đã biết trước khi sửa.

## 12. Checklist Sau Khi Code

- Chỉ sửa file thuộc scope.
- Task checkbox đã update đúng.
- Test/lint/typecheck liên quan đã chạy.
- Nếu không chạy được check, ghi rõ lý do.
- Docs cập nhật nếu behavior/workflow thay đổi.
- Open question mới được ghi lại, không giấu trong code.
- Chạy `/review <name>`.
- `review.md` ghi verdict, blocking findings, tests run, residual risk.
- Chỉ archive sau khi `/review` pass và human accept residual risk.
