# Test Scenario: 2 design analysis pipeline

- Source tasks:
  - 2.1 Implement bước sinh criteria groups từ change context để bao phủ kiến trúc, impact và rủi ro
  - 2.2 Implement chạy parallel agents, mỗi agent phụ trách một nhóm criteria độc lập
  - 2.3 Implement bước tổng hợp kết quả agent thành một draft `design.md` thống nhất
  - 2.4 Đảm bảo draft ghi rõ assumptions, trade-offs và open questions khi context chưa đủ
- Related requirements:
  - Analyze impact through grouped parallel criteria
  - Synthesize a standardized design document
- Assumptions:
  - Hệ thống có cơ chế chạy parallel agents
  - Criteria groups có thể tách thành các mảng độc lập đủ rõ để giao riêng

## Happy Path

- Khi `/design` chạy với context đầy đủ
- Thì skill sinh ra các criteria groups hợp lý như architecture boundaries, impacted systems, data flow, failure modes, review concerns
- Và mỗi group được giao cho đúng một agent chạy song song
- Sau đó kết quả từ các agent được tổng hợp vào một draft `design.md` thống nhất, không trùng lặp, có decisions và impact map rõ ràng

## Edge Cases

- Một criteria group có ít context hơn các group còn lại nhưng vẫn phải trả về assumptions rõ ràng
- Hai agent đưa ra findings chồng nhau và bước tổng hợp cần merge thay vì lặp lại
- Một change nhỏ không cần nhiều group nhưng skill vẫn phải giữ logic grouping tối thiểu

## Error Cases

- Một agent fail giữa chừng và không trả về findings
- Kết quả giữa hai agent mâu thuẫn nhau về boundary hoặc impact
- Output tổng hợp bỏ sót risk hoặc open questions khi context chưa đủ

## Expected Outcomes

- Draft `design.md` nêu được group nào đã phân tích, assumption nào đang giữ, và phần nào còn cần human review
- Không có hai agent cùng làm một criteria giống nhau mà không có lý do
- Khi có lỗi cục bộ, skill không giả vờ mọi phân tích đều hoàn tất

## Notes for Implementation

- Bước tổng hợp phải normalize xung đột giữa agent outputs
- Khi thiếu context, ưu tiên ghi rõ assumption thay vì tự bịa quyết định chắc chắn
