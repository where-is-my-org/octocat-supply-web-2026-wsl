# 🚀 GitHub Copilot Hands-on Lab
## 🛠️ 開發工具中的 GitHub Copilot Workshop
- 模型選擇建議: **Claude Sonnet 4.5**

### 🤖 Lab 6 : 實作階段：使用 Agent 模式實作功能
#### 利用既有 prompt file 進行功能實作
- **示範重點：** 利用既有的 prompt file 在 Agent Mode 進行功能實作並驗證
- **目的：** 加速規劃功能的快速實作並獲得測試
- **操作方式：**
    1. 建立新分支 `cart-page-custom`，並切換到該分支開始實作
    2. 選擇 `Agent` chat mode
    3. 將購物車圖片 attach 到對話框中
    4. 開啟 [cart-page.prompt.md](../.github/prompts/cart-page.prompt.md) 檔案，點選右上角的 Run prompt in current chat 可開始實作購物車功能

#### 利用 plan mode 下生成之 prompt 進行功能實作
- **示範重點：** 利用 Plan Mode 生成的 prompt file 進行功能實作並驗證
- **目的：** 加速規劃功能的快速實作並獲得測試
- **操作方式：**
    1. 切換回 `main` 再建立新分支 `cart-page-plan`，並切換到該分支開始實作
    2. 選擇 `Agent` chat mode
    3. 將購物車圖片 attach 到對話框中
    4. 在 chat 中切換至 Agent Mode 並輸入 `/<prompt-file-name>`開始執行實作

---

### 🧑‍💻 Lab 7 : 實作單元測試
- **示範重點：** 利用 prompt file 實作單元測試並達成覆蓋率目標
- **目的：** 透過 prompt file 協助開發重複性工作
- **操作方式：**
    1. 切換到 `cart-page-plan` 分支，選擇 `Agent` chat mode，並輸入 `/pytest-` 開始生成測試案例
    2. 查看執行結果，並輸入 `為所有缺少的實體生成完整測試`

        ![alt text](unit-test-prompt-result.png)
    3. 查看生成的測試案例，並執行測試及查看測試覆蓋率

---

### 🧹 Lab 8 : 程式碼除錯
#### 盤點程式碼錯誤並產生修復建議
- **示範重點：** 利用 Copilot 的 custom agent 協助盤點程式碼問題
- **目的：** 展現 Copilot 如何協助開發人員盤點程式碼錯誤並產生修復建議
- **操作方式：**
  1. 切換至 `cart-page-plan` 分支
  2. 開啟 Copilot Chat，切換至 `Debug Mode Instructions` 模式
  3. 輸入 `檢查購物車功能是否有錯誤`，查看 Copilot 生成的錯誤盤點及修復建議

#### 修復程式碼功能面向錯誤
- **示範重點：** 利用 Copilot 的 custom agent 協助修復程式碼
- **目的：** 利用 Copilot 的 Debug Mode Instructions 模式協助修復程式碼問題
- **操作方式：**  
  1. 切換至 `login-page` 分支
  2. 開啟 Copilot Chat，切換至 `Debug Mode Instructions` 模式
  3. 輸入 `目前使用者創建帳號可選擇未來日期，進行修復`，查看 Copilot 生成的修復建議及改善

---
### 🔐 Lab 9 : 應用程式安全優化
#### 透過 ask mode 了解安全弱點並修補
- **示範重點：** Copilot 理解並修補安全弱點的能力
- **目的：** 展現 Copilot 如何讓開發人員直接取得安全專業知識，協助擴大 AppSec 覆蓋範圍
- **操作方式：**  
  1. 保留在 `cart-page-plan` 分支
  2. 開啟 Copilot Chat，切換至 `Ask` 模式
  3. 請 Copilot `#codebase 分析並檢查是否存在明顯的安全性弱點`
  4. 你應該會看到下列問題（視情況不同）：
      - 跨站腳本（XSS）
      - 指令注入
      - 不安全的 CORS 設定
      - 遺漏安全標頭
      - 不安全的驗證實作
  5. 切換至 `Agent` 模式並與 Copilot 互動，請它 `請修復 ...` 修補其中一項

#### 利用 custom agent 進行安全性審查並產生修復計畫
- **示範重點：** 示範如何使用自訂 Agent 進行安全性審查並產生修復計畫
- **目的：** 展現如何使用自訂 Agent 進行安全性審查，並根據審查結果產生具體的修復計畫
- **操作方式：**
  1. 保留在 `cart-page-plan` 分支
  2. 開啟 Copilot Chat，切換至 `Agent` 模式，選擇 `SE: Security` Agent
  3. 請 Copilot `#codebase 分析並檢查是否存在明顯的安全性弱點`
  4. 完成後會出現 `Begin drafting plan to Fix Security Issues` 的 handoff，點選後會看到 Copilot 根據審查結果產生的修復計畫，包含優先級分類和具體的程式碼修改建議

---

### 🔁 Lab 10 : 使用 Recursive Agent 處理大型任務
- **示範重點：** 示範如何使用 Recursive Agent 來處理需要分解的複雜任務
- **目的：** 讓使用者了解 Subagent 的運作方式
- **操作方式：**
1. 開啟 Copilot Chat，切換 `RecursiveProcessor` Agent
2. 輸入以下提示詞，讓 Agent 處理一個需要分解的任務
   ```
   針對目前的應用程式需要做使用者驗證及外部驗證串接的研究，根據目前的實作進行以下分析：
    1. 對 Node.js 應用程式 OAuth 2.0 實作模式進行獨立研究
    2. 將每一種模式與目前的實作方式進行比較
    3. 提出建議方案，同時列出各方案的優點與缺點
    4. 針對建議方案進行實作規劃，列出實作步驟與所需資源
    5. 針對建議方案進行安全性分析，列出可能的安全風險與對應的緩解措施
   ```
3. 觀察 Agent 如何將任務分解成子任務，並逐步完成整個任務的處理
