# Brief: Formalize Design Agent Contracts

## Problem

`.codex` da co workflow AI-native cho `/design` voi orchestrator va specialist agents, nhung contract hien con o muc instruction markdown. Thieu schema that, registry, fixture kiem chung, va runtime contract ro. Dieu nay lam workflow kho verify, kho mo rong, va de lech hanh vi khi them agent hoac sua skill.

## Goal

Chuan hoa contract cho `/design` de nguoi doc va agent runner biet chinh xac:
- Specialist agent tra output theo schema nao
- Agent nao ton tai, vai tro gi, trigger khi nao
- Input/output cua tung agent la gi
- `/design` ky vong sinh section nao tu input artifacts
- Runtime chay agent song song/tuan tu, optional agent, va failure handling ra sao

## Scope

### In scope

- Them schema that cho `DesignAnalysisResult`
- Them agent registry gom `name`, `role`, `trigger`, `input`, `output`
- Them test/fixture cho `/design`: input artifacts -> expected design sections
- Ghi ro runtime contract: core agents, optional agents, parallel execution, fallback/failure behavior
- Chi chuan hoa architecture contract trong `.codex`

### Out of scope

- Implement runner that de tu dong load registry
- Enforce schema bang runtime validator
- Sua hoac mo rong OpenSpec CLI
- Spawn/manage agents that ngoai instruction-level workflow
- Them dependency moi neu khong bat buoc

## Constraints

- Giu workflow OpenSpec hien tai
- Uu tien markdown/json/yaml don gian, de doc, de review
- Khong dung app runtime neu khong can
- Khong them abstraction hoac dependency qua muc
- Thay doi nho, ro, tap trung trong `.codex`

## Acceptance Criteria

- [ ] Co schema file mo ta day du `DesignAnalysisResult`
- [ ] Co agent registry liet ke du design agents hien co
- [ ] Registry neu ro role, trigger, required input, expected output cua tung agent
- [ ] `/design` docs neu ro runtime contract: core agents, optional agents, parallel execution, failure handling
- [ ] Co fixture/test artifact the hien input artifacts va expected `design.md` sections
- [ ] Nguoi doc co the hieu cach them agent moi ma khong phai suy doan
- [ ] Khong implement runner/runtime validation trong change nay
