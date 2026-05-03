## Context

`/tdd` hiện giả định rằng output hợp lệ luôn là runnable test code theo một
framework cụ thể. Thiết kế này ổn với repo đã có stack kiểm thử rõ, nhưng fail
ở các change đang còn ở mức artifact, docs, workflow hoặc codebase chưa lộ rõ
framework. Trong các trường hợp đó, thứ team thực sự cần là test intent có cấu
trúc để human review và để `/code` dùng làm behavioral context.

Thiết kế hiện tại cũng có một friction không cần thiết:

- không detect được framework thì phải hỏi human ngay
- nếu human chưa muốn chốt framework, `/tdd` gần như không tạo ra giá trị

## Goals / Non-Goals

**Goals:**
- Cho `/tdd` hoạt động được cả khi repo chưa có framework test rõ ràng
- Giữ ưu tiên runnable tests khi detect được framework một cách đáng tin cậy
- Chuẩn hóa fallback output để scenario files vẫn hữu ích cho human review và `/code`
- Giảm số lần phải hỏi human trong các case không có framework

**Non-Goals:**
- Không bắt `/tdd` phải verify hay chạy tests
- Không đổi vai trò của `/code`; `/code` chỉ tiếp tục đọc `tests/` như context
- Không cố map scenario files thành runnable tests tự động trong change này

## Decisions

**1. `/tdd` có hai mode output**

Skill sẽ chọn một trong hai mode:

- `runnable mode`: generate test code theo framework detect được
- `scenario mode`: generate Markdown scenario files nếu không detect được framework

Điểm quan trọng là cả hai mode đều ghi vào `openspec/changes/<name>/tests/` để
không phá contract với `/code`.

Alternative considered:
- Vẫn bắt user chọn framework nếu detection fail. Bị loại vì tạo friction vô ích và làm `/tdd` không dùng được cho repo non-code hoặc early-stage change.

**2. Không detect được framework thì fallback mặc định, không hỏi ngay**

Fallback mặc định là behavior hợp lý hơn vì:

- không bịa framework
- không chặn workflow
- vẫn tạo ra test intent reviewable

Chỉ hỏi human khi:

- có nhiều framework khả dĩ và detection ambiguous
- hoặc user yêu cầu rõ runnable tests

Alternative considered:
- Luôn hỏi human trước khi fallback. Bị loại vì vẫn giữ nguyên friction gốc.

**3. Scenario mode phải có format đủ chặt để dùng làm implementation context**

Mỗi scenario file nên gắn với task hoặc capability và gồm tối thiểu:

- source task
- related requirements
- assumptions
- happy path
- edge cases
- error cases
- expected outcome
- notes for implementation

Format này giúp `/code` đọc như behavioral contract thay vì ghi chú rời rạc.

Alternative considered:
- Một file README tổng hợp cho cả change. Bị loại vì khó trace ngược về task và khó maintain khi review.

**4. `/code` không cần sửa contract trong change này**

`/code` hiện đã đọc mọi thứ trong `tests/` làm context bổ sung. Vì scenario mode
vẫn lưu trong cùng thư mục, không cần mở rộng thêm behavior ở `/code` trong change này.

Alternative considered:
- Sửa spec của `/code` để phân biệt markdown scenarios và runnable test files. Chưa cần, vì contract hiện tại đủ rộng để đọc cả hai loại file như context.

## Risks / Trade-offs

- **Tên `/tdd` lệch nghĩa cổ điển** -> Chấp nhận vì mục tiêu thực tế là define test intent trước khi code, không nhất thiết là runnable unit test ngay
- **Scenario files không chạy tự động được** -> Bù lại chúng cho done criteria và behavioral context rõ ràng
- **Fallback quá dễ khiến team trì hoãn framework thật** -> Mitigation: chỉ xem scenario mode là fallback, không thay thế nhu cầu runnable tests khi repo đã rõ stack
- **Một số user vẫn muốn runnable tests bằng mọi giá** -> Mitigation: nếu user nói rõ, skill vẫn hỏi framework hoặc dùng detected framework thay vì fallback
