---
name: codebase-architecture
description: 當需要從整體 Software System 角度辨識 codebase 中的 architectural entities, roles 與 relationships, 建立 architecture panorama, 或檢視 responsibility placement 與 entity relationships 是否合理時使用；不以 folder, package, monorepo 或 file layout 作為主要判準
---

# Codebase Architecture

依 `ARCHITECTURE-MODEL.md` 對 Software System 進行 logical architecture linting

這個 Skill 不要求 codebase 符合一套預先選定的 architecture pattern。從目前 code 與 behavior 可支持的 semantic responsibilities 出發，辨識 Architectural Entities, 已知 Architectural Roles 與 material Relationships；尚未能可靠分類的部分保持 `Unclassified System Interior`

## Invariants

### Architecture is semantic before physical

Architectural Entity 依 coherent responsibility 辨識，不依 file, package, directory, repository, process 或 class boundary 自動決定

Physical organization 可以作為 evidence，但不得只因兩段 code 位於同一 package 就判定它們屬於同一 entity，也不得只因兩個 responsibilities 不同就自動要求 physical separation

### Classification is conservative

只在 entity materially 符合已知 role 時才 assign 該 role。不得為了得到完整 diagram 將未知 responsibility 塞進最接近的 role

目前無適當 role 的 material code 保持 `Unclassified System Interior`; unclassified 不構成 finding

### Known roles carry their own lint semantics

對每個已辨識為 known Architectural Role 的 entity，使用該 role 的 canonical lint rules 檢視其 responsibility boundary

目前已知 role:

- `External-System Boundary`: `skills/external-system-boundary/SKILL.md`

Role-specific rule 不複製到本 Skill；本 Skill負責辨識 role, 建立 panorama 與檢視 entity relationships

### Relationships preserve semantic authority

檢視 entity 之間的 dependency, interaction, information flow 與 coordination 是否符合 responsibility ownership

一個 entity 不應只因較接近 technical mechanism 就吸收另一個 entity 擁有的 intent, classification, policy, lifecycle 或其他 decision authority；反之，higher-level entity 也不應重複 lower-level entity 已負責 contain 的 external or technical mechanics

### Findings are architecture-wise

Finding 描述哪個 entity or relationship 承擔錯誤 responsibility, 遺失必要 semantic boundary, 或產生不合理 dependency

除非 User 另有要求，不以建立資料夾, 移動檔案, 拆 package, monorepo 調整, class hierarchy 或其他 physical refactor 作為 finding 本身。可以指出需要 separation，但 implementation shape 留給後續設計

### The panorama reflects only established distinctions

Architecture diagram 只呈現目前可支持的 known roles, entities 與 relationships

不要補上未被實際辨識的 `domain`, `application`, `service`, `repository`, `infrastructure` 或其他 textbook layers。當新的 reusable role 尚未被 Atlas 定義時，它在 panorama 中維持 unclassified semantic space

## Linting result

檢視 Software System 時，應能產生:

- current architectural panorama
- 已辨識的 Architectural Entities 與 assigned roles
- material relationships
- role-local violations
- relationship violations
- materially relevant unclassified responsibilities, 僅作為 model frontier 而非 defect

每個 material finding 應指出:

- affected entities or relationship
- relevant architectural responsibility or role rule
- concrete code or behavioral evidence
- architecture consequence
- 最小的 semantic correction direction

若 responsibility placement 與 known role constraints 沒有 material violation，明確表示目前不需 architecture correction

## Postcondition

Architecture review 只有在 resulting model 不依賴 speculative roles, known entities 已依 applicable role rules 被檢視, material relationship problems 已揭露, 且 findings 沒有將 physical organization choice 冒充成 semantic architecture requirement 時才算完成
