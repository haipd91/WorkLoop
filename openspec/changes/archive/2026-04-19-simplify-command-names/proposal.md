# Proposal: simplify-command-names

## Problem

Các slash commands hiện tại dùng namespace `opsx:` khiến tên dài và không tự nhiên khi gõ:
- `/opsx:propose` — gõ nhiều, ít ai nhớ ngay
- `/opsx:apply` — tên "apply" không diễn đạt rõ hành động (thực ra là "code")
- `/opsx:explore` và `/opsx:archive` — tương tự

Với workflow team đang xây, commands sẽ được dùng nhiều lần mỗi ngày. Tên ngắn, rõ nghĩa giúp team adopt nhanh hơn và ít nhầm lẫn hơn.

## Solution

Tạo aliases ngắn gọn tại `.claude/commands/` (không có namespace), song song với commands `opsx:` hiện tại:

| Command cũ       | Command mới | Lý do đổi tên           |
|------------------|-------------|--------------------------|
| `/opsx:propose`  | `/propose`  | Ngắn, rõ nghĩa           |
| `/opsx:apply`    | `/code`     | "code" diễn đạt đúng hành động |
| `/opsx:explore`  | `/explore`  | Ngắn, tự nhiên           |
| `/opsx:archive`  | `/archive`  | Ngắn, tự nhiên           |

Giữ nguyên `opsx:` commands để tránh breaking change — chúng là aliases, không phải replacement.

Các skill mới `/brief` và `/tdd` (đang được xây) đã có tên ngắn từ đầu, không cần thay đổi.

## Goals

- Team gõ commands nhanh hơn, ít typo hơn
- Tên commands phản ánh đúng hành động (`/code` thay vì `/apply`)
- Không breaking change — `opsx:` vẫn hoạt động

## Non-goals

- Không xóa `opsx:` commands
- Không thay đổi nội dung/behavior của commands
- Không đổi tên `/brief` và `/tdd` (đã ngắn)

## Success Criteria

- `/propose`, `/code`, `/explore`, `/archive` hoạt động giống hệt bản `opsx:`
- Team workflow doc cập nhật dùng tên mới
