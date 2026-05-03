# Design: brief-skill

## Kiến trúc tổng thể

Skill là một file SKILL.md đặt tại `.codex/skills/brief/SKILL.md`, được Claude Code load khi user gõ `/brief`.

```
User: /brief
        │
        ▼
  Claude đọc SKILL.md instructions
        │
        ▼
  [Round 1] Hỏi nhóm thông tin cốt lõi
  → Problem & Goal (gộp để tự nhiên)
        │
        ▼
  [Round 2] Hỏi Scope (in / out)
        │
        ▼
  [Round 3] Hỏi Constraints + Acceptance Criteria
        │
        ▼
  Adaptive clarify nếu câu trả lời mơ hồ
        │
        ▼
  Tạo brief.md draft → hiển thị cho human
        │
        ▼
  Hỏi: "Bạn muốn chỉnh gì không?" (AskUserQuestion)
        │
   ┌────┴────┐
   │ Có     │ Không
   ▼         ▼
  Chỉnh    Lưu brief.md
  lại      + gợi ý /opsx:propose
```

## Brief Format

Output `brief.md` theo structure cố định để downstream tools đọc được:

```markdown
# Brief: <tên change>

## Problem
<vấn đề đang cần giải quyết, tại sao cần làm>

## Goal
<thành công trông như thế nào, outcome mong muốn>

## Scope

### In scope
- <cái sẽ làm>

### Out of scope
- <cái không làm>

## Constraints
- <ràng buộc kỹ thuật, thời gian, design, team>

## Acceptance Criteria
- [ ] <tiêu chí 1>
- [ ] <tiêu chí 2>
```

## Adaptive Clarification Logic

Skill phải clarify khi:
- Problem quá chung chung (ví dụ: "cải thiện hiệu năng" → hỏi: "hiệu năng của phần nào? đo bằng gì?")
- Goal không đo được (ví dụ: "tốt hơn" → hỏi: "tốt hơn so với gì? bằng cách nào biết là đạt?")
- Scope không rõ boundary (ví dụ: "làm trang user" → hỏi: "bao gồm authentication không? profile settings không?")

Giới hạn: tối đa 1 round clarify per nhóm thông tin. Không hỏi vòng lặp.

## Lưu File

- Tên file mặc định: `brief.md` tại working directory
- Nếu file đã tồn tại: hỏi overwrite hay đặt tên khác
- Sau khi lưu: thông báo path + gợi ý `/opsx:propose`

## Tích hợp với Team Workflow

```
[Intake + Clarification] ──► /brief ──► brief.md (human approved)
                                              │
                                              ▼
                                        /opsx:propose
                                              │
                                              ▼
                                        spec.md + plan.md + tasks.md
                                              │
                                              ▼
                                        /opsx:apply
```
