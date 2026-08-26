---
name: external-system-boundary
description: 當建立, 修改或檢視 application 對 third-party HTTP API, operating-system facility, command-line tool, SDK 或其他 External System 的 direct semantic boundary 時使用；特別用於判斷 exposed surface, semantic fidelity, responsibility placement, mechanics containment 與 caller control 是否符合 External-System Boundary role
---

# External-System Boundary

依 `ARCHITECTURE-MODEL.md`, `External-System Boundary` 是 Software System 對單一 External System 直接依賴的最低 semantic boundary

它 expose current application 所需的 external capabilities，並 contain 使用這些 capabilities 所需的 technical mechanics。它可以由既有 SDK 或 client 直接構成，也可以是具有 material boundary responsibility 的 minimal wrapper

Application integration, feature coordination, domain classification, desired-state handling, workflow, lifecycle, recovery 與 access policy 不屬於此 role

本 Skill 規範 architecture properties 與 lint conditions，不擁有完整 external-system contract research, controlled probing, diagnostics strategy 或 observability design。依 Intended Work 目前可取得的 code, callers, tests, documentation 與其他 evidence 理解 material semantics；缺少 external documentation 不構成移除或壓縮既有 material behavior 的理由

## Invariants

### Current-needed external capability surface

Boundary 只 expose current callers 實際需要的 external operations, observations, mutations, inputs, values, outcomes 與 failure distinctions

Application needs 決定 surface 的範圍，但不把 application semantics 改寫成 external-system semantics

不得為尚未使用的 endpoints, hypothetical providers, future platforms, SDK 全量 coverage 或 speculative generic API 預先建模。除非 External System 本身提供 generic execution capability，不以 `Run`, `Call`, `Do` 等 generic operation 取代 current use 可辨識的 external capabilities

**Fact-preserving does not mean fact-exhaustive.** Public contract 只區分 current caller 或 operator 會賦予不同 material consequence 的 external facts, outcomes 與 failures；不因 External System 或 underlying implementation 存在更多 stages, metadata, status variants 或 partial outcomes 就全部提升為 public API

未公開的 implementation-stage 差異仍不得使 boundary 回傳 fabricated fact, false success 或比實際更強的 guarantee。只供 diagnostics 的 detail 可以留在 wrapped cause, internal representation 或 boundary-owned tests，而不形成 caller-visible classification

### External-semantic fidelity

Exposed operations, values, outcomes 與 failures描述 External System 能做什麼, 回報什麼或實際發生了什麼；不將 external facts 翻譯成 application goals, ownership, domain classifications, decisions 或 workflows

Exposed semantic 必須符合實際 observation 或 mutation 所建立的 scope, status 與 guarantee。不得在缺乏足夠 basis 時將 stored configuration 表達成 effective state, mutation acknowledgement 表達成 completed effect, local scope 表達成 whole-system state，或以其他方式增強 external semantics

一個 boundary operation 不要求只對應一次 HTTP request, command execution, SDK call 或 platform API call；判準是它是否仍忠於一項 coherent external-system capability，而不是 underlying interaction count

### Composition follows semantic ownership

Boundary 可以組合多次 external interactions，但該 composition 的存在理由必須來自 External System 的 operation semantics, protocol ceremony, representation mechanics 或其他 external-owned behavior

若 composition 的存在理由來自 application intent, invariant, desired state, ownership or domain classification, workflow, lifecycle, recovery, compensation, retry or fallback policy，該 coordination 留給 caller；boundary expose 組成它所需的 external observations, mutations, outcomes 與 failures

Boundary 不得將 application-owned coordination包裝成 External System 自身提供的 capability，也不得因 composition 自行產生 atomicity, transactionality, exclusivity, completion 或其他 External System 未建立的 guarantee

Application-owned coordination 留在 boundary 外不表示 external calls 必須散落於 domain 主流程；caller 可以使用 feature-private function, type 或 service 組織它

### Mechanics are contained

Transport, protocol, endpoint, serialization, command execution, stdout, stderr, exit status, platform API, SDK invocation, external representation parsing，以及遵從 External System contract 所需的 authentication mechanics 留在 boundary 內

Caller 不應重複或依賴這些 mechanics

Boundary 將 external representation 轉成 exposed fact 時，不得把無法可靠解讀的 representation silent 轉換成看似有效的 fact。這項約束是 semantic fidelity 的底線，不使本 Skill擴張為完整 contract-validation framework

Identity, account 或 tenant 選擇, reauthentication workflow, scope policy, credential consequence 與其他 application-owned authentication decisions 留在 boundary 外

### Application authority remains outside

Application ownership, domain classification, desired-state evaluation, lifecycle, orchestration, recovery, compensation, fatal or degraded interpretation 與 user-facing consequence 留給 caller

Retry, caching, fallback, circuit breaking, provider routing 與其他 external-access policies 原則上由 boundary 外可獨立組合的 decorator, middleware, coordinator 或 orchestrator 擁有；External System protocol 或 SDK 本身明確提供的 transparent mechanics 不因此被排除

Boundary 應回傳足以讓 caller 執行 current application-owned decisions 的 external facts與 failures，但不預先替 caller完成 classification 或決定下一步

### Purposeful implementation

優先直接採用已能提供 current-needed external semantics 的 SDK 或 client

只有當 current boundary 需要 material capability restriction, representation or error adaptation, external-owned composition, mechanics containment 或其他獨立 responsibility 時，才加入 custom wrapper；不建立只做 rename 與 delegation 的 pass-through layer

SDK 已提供適合的 external vocabulary 時，可以直接使用其 value, resource 與 error types；不為形式上的 decoupling 複製等價 local DTO。只有 current boundary 需要 SDK 無法表示的 material external fact, caller-needed outcome, capability restriction 或 app-owned stable contract 時才建立 local type

### Controllable seam

Boundary 應 expose 或允許適當且最小的 test seam，使 caller 能 deterministic control 它實際賦予不同 material consequence 的 boundary facts, outcomes 與 failures，而不必重建 irrelevant HTTP, CLI, SDK 或 platform mechanics

Test seam 可以是 transport, local test server, command runner, SDK subset, injected function 或 consumer-owned narrow interface；不只為 mocking 建立沒有 material production responsibility 的 broad wrapper

Caller-level test doubles 預設只產生 production boundary 合法可能輸出的 semantics，且不得賦予 production boundary 不具備的 atomicity 或其他 guarantee

## Typical lint findings

以下名稱只協助描述常見問題，不形成封閉 taxonomy:

- **Overexposed capability or outcome**: public surface 包含 current callers 不需要的 external capability, status, stage 或 failure distinction
- **Application coordination leakage**: application-owned intent, invariant, classification 或 workflow 被包入 boundary operation
- **Fabricated external semantic**: boundary 將 unknown, stored, partial 或 local fact 表達成更強的 external state
- **Fabricated guarantee**: composed operation 宣稱 External System 未提供的 atomicity, completion 或 transaction guarantee
- **Mechanics leakage**: caller 必須知道 transport, CLI, SDK, serialization 或 platform ceremony 才能使用 boundary
- **Redundant boundary wrapper**: existing SDK/client 已完整承擔 current boundary responsibility，而 wrapper 只做 rename/delegation
- **Unfaithful test seam**: fake/test double 提供 production boundary 不具備的 semantics或guarantees

## Postcondition

建立或修改 External-System Boundary 只有在以下 resulting state 成立時才算成功:

- boundary surface 只包含 current-needed external capabilities與material distinctions
- exposed semantics忠於 External System，而不是 application classification或goal
- composed operations由 external-owned semantics或mechanics支持；application-owned coordination留在 caller
- public API不宣稱 implementation或External System無法建立的guarantees
- transport, protocol, SDK, CLI, platform與authentication mechanics未漏入caller
- caller取得足以做current decisions的facts與failures，但沒有被迫依賴unused external detail
- existing SDK/client在足以承擔boundary時被直接採用；custom wrapper具有material responsibility
- caller能透過適當seam控制其真正materially區分的boundary outcomes
- 不存在unused API coverage, redundant SDK mirror, purely pass-through abstraction或其他speculative surface

檢視 External-System Boundary 時，finding 以以上 architecture properties 為判準，並指出 concrete code or behavior, affected semantic responsibility, caller consequence與最小的 semantic correction direction

不因一個 operation 包含多次 external calls, public API 使用 SDK types, code 位於某個特定 package, 或 external documentation 不完整就自動要求拆分或重寫。若沒有 material role violation，明確結論為不需改動
