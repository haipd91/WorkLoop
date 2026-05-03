# Brief: Integrate Review Into OpenSpec Graph

## Problem

`/review` hiện là skill-level gate, chưa nằm trong OpenSpec artifact graph/status/instructions. Vì vậy CLI vẫn có text generic như ready to archive, và OpenSpec chưa biết `review.md` là artifact/gate chính thức.

## Goal

Tích hợp review vào OpenSpec schema để `openspec status`, `openspec instructions review`, và archive flow hiểu review như một workflow artifact/gate chính thức giữa `/apply` và `/archive`.

## Scope

### In scope

- Thêm review artifact vào OpenSpec schema graph.
- `openspec status` hiển thị review artifact.
- `openspec instructions review` trả context, dependencies, và outputPath cho review.
- Apply all-done instruction route sang `/review`, không archive.
- Archive dùng graph/gate chính thức thay vì chỉ skill-level check nếu khả thi.
- Update docs/spec/tasks liên quan.

### Out of scope

- Không thay đổi nội dung review stance đã làm ở phase 1.
- Không build CI.
- Không yêu cầu human reviewer riêng.
- Không refactor toàn bộ OpenSpec CLI ngoài review artifact integration.
- Không thay đổi app runtime ngoài workflow tooling/schema.

## Constraints

- Giữ tương thích với `spec-driven-custom` schema hiện tại.
- Không làm yếu gate đã có ở phase 1.
- Không thêm dependency nếu schema/status logic hiện tại đủ.
- Review artifact phải nằm sau apply/tasks/tests, trước archive.
- Nếu OpenSpec CLI không hỗ trợ archiveRequires/review instruction sẵn, ghi rõ limitation và chọn minimal integration khả thi.

## Acceptance Criteria

- [ ] `openspec status --change <name>` hiển thị `review` artifact.
- [ ] `review` artifact có outputPath `review.md`.
- [ ] `review` artifact blocked cho tới khi required artifacts/tasks/tests đủ.
- [ ] `openspec instructions review --change <name> --json` trả context files cần review.
- [ ] Apply all-done instruction không còn nói ready to archive, mà hướng sang `/review`.
- [ ] Archive flow dùng review artifact/gate chính thức hoặc documented bridge nếu CLI còn limitation.
- [ ] Existing phase 1 behavior vẫn pass: `/review` tạo `review.md`, `/archive` block review fail/missing/stale.
