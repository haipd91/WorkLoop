# Brief: Rename Code Command To Apply

## Problem

Hien tai command `/code` thuc chat dung de ap dung thay doi vao workflow, nhung ten `code` khien nguoi dung hieu sai la chuc nang generate code. Su lech nghia nay lam domain language khong chinh xac va gay nhieu trong cach su dung he thong.

## Goal

Doi command chuan tu `/code` sang `/apply` de phan anh dung nghiep vu "ap dung thay doi vao workflow", dong thoi cap nhat toan bo tai lieu va user-facing reference lien quan de domain term thong nhat.

## Scope

### In scope

- Doi command user-facing tu `/code` sang `/apply`
- Cap nhat moi tai lieu lien quan va moi user-facing reference tu `code` sang `apply`
- Dieu chinh workflow hien tai de command moi phan anh dung semantics va hoat dong chuan theo intended flow

### Out of scope

- Refactor sau logic noi bo neu khong thuc su can de ho tro viec doi command
- Cac thay doi ky thuat khong phuc vu truc tiep cho viec doi ten command va chuan hoa user-facing behavior

## Constraints

- Domain term chuan phai la `apply`, khong con `code` duoi bat ky dang command user-facing nao
- Moi cho user-facing lien quan phai thong nhat dung `apply`
- Can sua workflow hien tai de semantics va hanh vi khop voi command moi

## Acceptance Criteria

- [ ] `/apply` hoat dong voi hanh vi tuong duong hoac dung hon so voi `/code` hien tai cho workflow muc tieu
- [ ] Moi docs va user-facing reference lien quan da duoc doi tu `/code` sang `/apply`
- [ ] `/code` khong con duoc ho tro nhu command user-facing
- [ ] `apply` la ten chuan duy nhat trong trai nghiem nguoi dung
- [ ] Workflow hien tai da duoc chinh lai de phan anh dung semantics "apply change" thay vi "code"
