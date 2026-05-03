# Test Scenario: 3 design review loop

- Source tasks:
  - 3.1 Hiển thị summary ngắn của design draft để human review nhanh
  - 3.2 Implement feedback loop để human yêu cầu chỉnh sửa và skill cập nhật đúng section liên quan
  - 3.3 Chỉ xác nhận hoàn tất khi design đã được human approve
- Related requirements:
  - Support human review and iterative refinement
  - Preserve approved design as `/code` input
- Assumptions:
  - Human review là gate bắt buộc trước khi handoff sang `/code`
  - Feedback có thể tới theo nhiều vòng, nhưng mỗi vòng phải trace được phần bị sửa

## Happy Path

- Khi draft `design.md` đã được tạo
- Thì skill hiển thị summary đủ ngắn để human review nhanh
- Và khi human góp ý, skill cập nhật đúng section liên quan rồi trình bày lại bản mới
- Khi human approve, skill xác nhận `design.md` sẵn sàng làm input cho `/code`

## Edge Cases

- Feedback chỉ yêu cầu chỉnh một section nhỏ như risks hoặc decisions
- Human yêu cầu làm rõ assumption thay vì thay đổi cả kiến trúc
- Có nhiều vòng review liên tiếp nhưng skill vẫn giữ được structure ổn định

## Error Cases

- Skill regenerate toàn bộ tài liệu dù feedback chỉ đụng một phần nhỏ
- Skill tự xác nhận hoàn tất dù chưa có approve rõ ràng
- Skill mất dấu feedback cũ và vô tình xóa phần đã được chốt ở vòng trước

## Expected Outcomes

- Mỗi vòng feedback đều dẫn đến thay đổi có chủ đích, không phá các phần không liên quan
- Handoff chỉ được coi là complete sau khi human approve rõ ràng
- `design.md` cuối cùng giữ được traceable link giữa review feedback và nội dung đã sửa

## Notes for Implementation

- Cần phân biệt rõ review summary với nội dung file đầy đủ
- Không cho phép kết thúc flow ở trạng thái mập mờ kiểu "coi như ổn"
