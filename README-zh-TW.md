# CaaS — CLI as a Service

> Agent 時代繼承 SaaS 的下一個架構範式。

**狀態**:Living specification, v0.1 draft (May 2026)
**作者**:[@irrenwill](https://github.com/irrenwill)
**授權**:[CC BY 4.0](./LICENSE)
**Languages**: [English](./README.md) | **繁體中文**

---

## CaaS 是什麼

**CaaS(CLI as a Service)** 是一種架構範式——
**CLI 本身就是服務**,是被人類、agent、和圖形介面**共同消費**的單一主權 API。

在 CaaS 應用裡:

- **CLI 是真實來源**(source of truth),所有操作都是 CLI 指令。
- **GUI 是 CLI 的 client**。
- **Agent**(Claude Code、Cursor、Windsurf 等)也是 CLI 的 client。
- **MCP server**(如果有)只是 CLI 的薄包裝層。
- **雲端**(如果有)是可選的加值,不是依賴。

這個架構**翻轉了過去 15 年 SaaS 的預設**——
SaaS 認為 web app 是產品,其他都是整合。

CaaS 認為:**CLI 才是產品,其他都是介面**。

---

## 為什麼是現在?

CaaS 在 2024–2026 才真正可行,因為五個關鍵基礎建設**剛好同時到位**:

1. **Tauri v2**(2024 底)——原生跨平台 GUI,內建 sidecar CLI 支援,
   bundle 小於 20MB。
2. **MCP**(2024 底)——把工具暴露給 agent 的標準協定。
3. **Skills**(2025)——可攜的 agent 能力封包,目前在 40+ agent runtime 上跑。
4. **Claude Code、Cursor、Windsurf**(2024–2025)——agent 成為主要開發介面,
   「**對著工具講話**」變成新常態。
5. **BYOK 經濟性**(2025–2026)——模型 API(Anthropic、OpenAI、fal.ai、
   ByteDance、Replicate)夠便宜也夠整合,
   讓「使用者自帶 key」從怪招變成真實的 distribution 路徑。

這五個塊只要少一個,CaaS 都還只是理論。**2026 是它們第一年同時就位**。
對想做出耐用、有主權的軟體的 indie 來說,
這是**目前阻力最小的路徑**。

---

## 五個核心原則

### 1. CLI 就是 API

沒有「內部 API + 外部 CLI」這種分層。**CLI binary 就是服務本身的合約**。
每個功能都暴露為一個 CLI 指令,同時提供**人類可讀**輸出和 `--json` 機器可讀輸出。
**做不到 CLI 的事情,在這個產品裡就不存在**。

### 2. Local-first 預設

使用者的資料**存在使用者自己的硬碟上**,用開放格式(SQLite、JSON、純文字)。
所有核心工作流**離線可用**。雲端功能只是加分,絕不是必要依賴。

### 3. BYOK 優先,不要 multi-tenant

使用者**自己帶 API key**到模型廠、金流、儲存。
App 把 key 存進 OS keychain,**絕不傳到任何遠端伺服器**。
這個設計**消滅了 SaaS 最致命的單位經濟陷阱**——
每個使用者的推論成本超過訂閱費。

### 4. GUI 和 agent 是 client,不是老大

Tauri / Electron / native GUI 透過 subprocess 或 sidecar 呼叫 CLI。
Agent 透過同樣的介面呼叫 CLI。
新的 client(VS Code 擴充、Discord bot、行動 app)可以加進來
**完全不用碰業務邏輯**——因為業務邏輯都在 CLI 裡。

### 5. 開源核心 + 可選雲端

CLI、daemon、GUI 用寬鬆開源授權發布(MIT / Apache)。
**營收來自可選的雲端加值**:加密同步、托管 MCP endpoint、團隊協作、託管備份。
照 Obsidian 的劇本:**永遠不要把使用者本地已經有的功能 paywall**。

---

## CaaS vs SaaS

| 維度 | SaaS | CaaS |
|---|---|---|
| 資料真實來源 | 廠商資料庫 | 使用者硬碟 |
| 主要介面 | 網頁瀏覽器 | CLI + GUI + agent（對等） |
| API 存取 | 通常付費階層，有 rate limit | 免費、原生、不用 auth |
| Agent 整合 | 事後補的，走 REST | 一等公民，走 subprocess / MCP |
| 廠商倒閉風險 | 全毀（使用者失去一切） | 極低（CLI 離線繼續跑） |
| 單位經濟 | 每使用者基建成本線性成長 | 邊際成本接近零（運算在使用者端） |
| Distribution | 付費獲客 + 業務 | 開源社群 + 口碑 |
| Solo dev 可維護性 | 難（multi-tenant、ops、scaling） | 易（每台機器一個 single-user runtime） |
| 隱私故事 | 「**請相信我們**」 | 「**你的硬碟、你的 key、你的檔案**」 |

---

## 體現 CaaS 原則的既有工具

這些工具早於 CaaS 這個名詞，但**架構上都符合**：

- **1Password** — CLI（`op`）+ 桌面 app + 瀏覽器擴充，
  全部都是共享本地 daemon 的 client。OS 生物辨識認證。雲端同步可選且端到端加密。
- **Obsidian** — 檔案在本地、GUI 免費、Sync（$5/月）和 Publish（$10/月）加值。
  **約 $25M ARR，9 個員工**。證明這個模式可以養活 indie 團隊的標竿案例。
- **GitHub CLI（`gh`）** — 每個 web UI 動作都有對應 CLI 指令，
  讓 agent 整合不用另外設計 API。
- **Aider** — 從第一天就是 agent-native，CLI-first，BYOK 到模型廠。
- **Stripe CLI、Supabase CLI、Vercel CLI** — 這些服務都把 CLI 設計成跟 web dashboard 平起平坐的介面。

CaaS 只是**把這些工具各自摸索出的架構正式命名**。

---

## 誰應該採用 CaaS

CaaS 適合你，如果：

- 你是 **indie 或小團隊**，沒有 infra / ops 預算。
- 你的使用者在乎**隱私、所有權、離線使用**。
- 你的應用**會因為 agent 自動化變強**。
- 你的單位經濟**做不成統一價 SaaS**（例如 AI 推論、大量運算、大儲存量）。
- 你想要**開源社群帶來的 distribution 槓桿**。

CaaS 不適合你，如果：

- 你的應用需要**即時多人協作**作為核心功能（Figma、Google Docs）。
- 你的使用者**不能裝軟體**（企業 IT、ChromeOS）。
- 你需要 **VC 等級的成長**（目標 $100M+ ARR）——
  CaaS 為「**可持續**」優化，不是為「**爆量**」優化。

---

## CaaS 不是什麼

- **不是「把 CLI 容器化」**。那是 Container-as-a-Service，2010 年代雲端運維的概念，
  跟 CaaS 無關。
- **不是「把 CLI 部署到雲端」**。CaaS 是 local-first，**CLI 跑在使用者機器上**。
- **不是「只有 CLI」**。CaaS app 通常有完整 GUI，重點是**GUI 不擁有邏輯**。
- **不是「反雲端」**。可選的雲端加值是被鼓勵的，只是不能變成必要依賴。

---

## 參考實作

- **Tethea** — AI 影片製作 pipeline（2026 開發中）
- *更多即將推出，歡迎 PR 補充*

---

## 貢獻

這是 living specification，issue 跟 PR 歡迎：

- 原則的精煉與修正
- 新的參考實作
- 對比 case study
- 翻譯（简体中文 / 日本語 / 其他語言）

---

## 詞彙表

- **Sovereign client（主權 client）** — 一個 client（GUI、agent、MCP server）
  把 CLI 當 API 使用，**自己不加任何業務邏輯**。
- **Sidecar** — 跟 GUI app 一起打包的 CLI binary，
  透過 subprocess 呼叫。Tauri v2 原生支援。
- **BYOK** — Bring Your Own Key。使用者自己提供 API 憑證，
  存在 OS keychain，**絕不傳輸到 app 廠商**。
- **Daemon** — 長期運行的本地行程，
  為多個 client（GUI + CLI 各次呼叫）持有共享狀態。可選但常見。

---

## 引用方式

如果你在寫作中引用 CaaS，請用：

> CaaS: CLI as a Service (v0.1). irrenwill, 2026.
> https://github.com/irrenwill/caas-spec
