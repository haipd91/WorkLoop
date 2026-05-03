## Context

Hiện tại team có `brief`, `proposal`, `tasks`, `tests` và `code`, nhưng chưa có
một bước chuyên để biến intent thành thiết kế kỹ thuật trước khi code chạy.
Brief yêu cầu `/design` phải:

- đọc artifacts từ change hiện tại
- tự nhóm tiêu chí phân tích impact
- chạy parallel agents để giảm thời gian
- tạo `design.md` đủ chi tiết cho human review
- hỗ trợ feedback loop trước khi lưu

Ràng buộc chính:

- output `design.md` phải theo format chuẩn, ổn định và dễ review
- parallel agents phải chạy đồng thời, không phải tuần tự giả lập
- không thay đổi logic cốt lõi của `/propose` hay `/code`

## Goals / Non-Goals

**Goals:**
- Tạo skill `/design` có thể tự chọn change và đọc đúng artifacts đầu vào
- Sinh `design.md` chứa kiến trúc chi tiết, impact map, quyết định kỹ thuật và rủi ro
- Tăng độ bao phủ phân tích bằng cách tách criteria thành các nhóm chuyên biệt và xử lý song song
- Có vòng human review để chỉnh sửa lặp trước khi chốt artifact
- Sau khi approve, `design.md` sẵn sàng làm context cho `/code`

**Non-Goals:**
- Không tự động chạy `/code` sau khi hoàn tất design
- Không thay đổi schema hoặc behavior nội bộ của `/propose`
- Không yêu cầu design phải đúng tuyệt đối ngay vòng đầu; chấp nhận refine qua feedback

## Decisions

**1. `/design` dùng artifacts trong change directory làm source of truth**

Skill sẽ đọc `proposal.md`, `tasks.md` và `brief.md` nếu có trong
`openspec/changes/<name>/`. Cách này giữ design gắn chặt với change đang làm, tránh
context trôi sang repo state chung.

Alternative considered:
- Quét cả repo để tự suy luận intent. Bị loại vì nhiễu và dễ sinh design lệch brief.

**2. Criteria được tạo động nhưng phải ổn định theo các nhóm chuẩn**

Skill sẽ map input thành một tập nhóm phân tích có cấu trúc, ví dụ:
- architecture boundaries
- impacted systems and files
- data and state flow
- failure modes and edge cases
- rollout and review concerns

Điều này cho phép mỗi agent nhận một scope rõ ràng nhưng vẫn giữ output dễ tổng hợp.

Alternative considered:
- Hardcode một checklist ngắn cố định. Bị loại vì không đủ linh hoạt cho các change khác nhau.

**3. Dùng parallel agents để phân tích theo nhóm criteria, không để agent chính làm hết**

Mỗi agent phụ trách một nhóm criteria riêng và trả về:
- findings
- assumptions
- proposed decisions
- unresolved questions

Agent chính tổng hợp thành draft `design.md`, loại bỏ trùng lặp và xử lý mâu thuẫn.

Alternative considered:
- Phân tích tuần tự trong một pass. Bị loại vì chậm và dễ bỏ sót impact chéo.

**4. `design.md` có format cố định để review nhanh**

Draft cuối cần có tối thiểu:
- context
- goals / non-goals
- proposed architecture
- impact map
- decisions with rationale
- risks / trade-offs
- review feedback / open questions

Format ổn định giúp human biết đọc ở đâu để approve hoặc phản biện.

Alternative considered:
- Xuất free-form markdown. Bị loại vì chất lượng review không ổn định.

**5. Review loop là bắt buộc trước khi chốt file**

Sau khi tạo draft, skill phải trình bày tóm tắt, nhận feedback của human, cập nhật
đúng section liên quan, rồi mới xác nhận lưu. Nếu chưa approve thì không kết thúc
như một handoff hoàn chỉnh.

Alternative considered:
- Lưu ngay rồi cho human sửa tay. Bị loại vì mất guardrail và giảm giá trị của skill.

## Risks / Trade-offs

- **Agent outputs mâu thuẫn nhau** -> Agent chính phải normalize và ưu tiên proposal/brief làm nguồn quyết định cuối
- **Proposal hoặc tasks quá mơ hồ** -> Skill cần nêu assumptions rõ trong design thay vì giả vờ chắc chắn
- **Phân tích quá rộng làm design dài và chậm review** -> Giới hạn output vào các quyết định ảnh hưởng trực tiếp implementation
- **Review loop kéo dài nhiều vòng** -> Cho phép iterative update, nhưng mỗi vòng phải sửa đúng phần bị phản hồi thay vì regenerate toàn bộ
- **Parallelization tăng chi phí context** -> Chỉ tách theo nhóm criteria thực sự độc lập để giữ hiệu quả
