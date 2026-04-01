# 🚀 GitHub Copilot Hands-on Lab
## 開發工具中的 GitHub Copilot Workshop - 維運階段
- 模型選擇建議: **Claude Sonnet 4.5**

### 🧹 Lab 6 : 程式碼除錯
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

### 🔐 Lab 7 : 應用程式安全優化
#### 透過 ask mode 了解安全弱點並修補
- **示範重點：** Copilot 理解並修補安全弱點的能力
- **目的：** 展現 Copilot 如何讓開發人員直接取得安全專業知識，協助擴大 AppSec 覆蓋範圍
- **操作方式：**  
  1. 切換至 `cart-page-plan` 分支
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
