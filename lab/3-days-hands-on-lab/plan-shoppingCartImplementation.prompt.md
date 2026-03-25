# 購物車功能實現計劃

## TL;DR
為 OctoCAT Supply 應用實現購物車功能，包括購物車頁面、導覽列購物車圖示、商品數量即時顯示、運費與折扣計算邏輯，並支援深色/淺色模式。採用前端 localStorage 存儲，無需後端 API。

## Implementation Steps

### Phase 1: 購物車狀態管理 (獨立完成)
1. **建立 CartContext** (`frontend/src/context/CartContext.tsx`)
   - 定義 `CartItem` 類型：`{ productId, name, price, quantity, imageUrl }`
   - 實現 Context Provider：管理購物車項目陣列
   - 提供操作方法：`addToCart()`, `removeFromCart()`, `updateQuantity()`, `clearCart()`
   - 實現 localStorage 持久化：讀取/儲存購物車狀態
   - 計算方法：`getTotalItems()`, `getSubtotal()`, `getDiscount()`, `getShipping()`, `getGrandTotal()`
   - 折扣邏輯：小計的 5%
   - 運費邏輯：基本運費 $25，訂單小計超過 $150 免運費

2. **建立 useCart Hook** (`frontend/src/context/CartContext.tsx`)
   - 封裝 Context 消費邏輯，方便組件使用

### Phase 2: 購物車頁面開發 (*depends on Phase 1*)
3. **建立 Cart 組件** (`frontend/src/components/Cart.tsx`)
   - 引入附圖設計元素：深色主題的表格式布局
   - 商品列表表格：
     - 欄位：S.No, Product Image, Product Name, Unit Price, Quantity, Total, Remove
     - 顯示產品圖片（使用 `assets/products/` 路徑）
     - 數量調整器：+/- 按鈕（類似 Products 組件）
     - 移除按鈕：垃圾桶圖示
   - 優惠券輸入區：輸入框 + "Apply Coupon" 按鈕（UI only，不實際處理）
   - "Update Cart" 按鈕（儲存購物車更新）
   - Order Summary 卡片：
     - Subtotal（小計）
     - Discount (5%)（負值顯示）
     - Shipping（運費顯示，超過 $150 顯示 "Free"）
     - Grand Total（總計）
     - "Proceed To Checkout" 按鈕（點擊顯示開發中提示）
   - 空購物車狀態：顯示訊息和返回商品頁面連結
   - 深色/淺色模式樣式：參考 Navigation 和 Products 的模式

### Phase 3: 導覽列整合 (*depends on Phase 1*)
4. **修改 Navigation 組件** ([frontend/src/components/Navigation.tsx](frontend/src/components/Navigation.tsx))
   - 在主題切換按鈕前插入購物車圖示
   - 購物車圖示設計：
     - 使用 SVG 購物車圖示（參考 Heroicons）
     - 顯示徽章（badge）：顯示購物車總商品數量
     - 徽章樣式：綠色圓形，白色文字，絕對定位於圖示右上角
   - 點擊圖示導向 `/cart` 路由
   - 即時更新：使用 `useCart()` hook 訂閱購物車變化

### Phase 4: 商品頁面整合 (*depends on Phase 1*)
5. **修改 Products 組件** ([frontend/src/components/entity/product/Products.tsx](frontend/src/components/entity/product/Products.tsx))
   - 替換現有的 `handleAddToCart()` 實現（目前只有 alert）
   - 調用 `CartContext` 的 `addToCart()` 方法
   - 添加成功提示：toast 通知或臨時訊息
   - 添加後重置數量選擇器為 0

### Phase 5: 路由和整合 (*depends on Phase 2*)
6. **更新 App 路由配置** ([frontend/src/App.tsx](frontend/src/App.tsx))
   - 添加 `/cart` 路由，對應 Cart 組件
   - 在 ThemeProvider 同層級包裹 CartProvider

7. **更新 API 配置** ([frontend/src/api/config.ts](frontend/src/api/config.ts))
   - 不需修改（純前端功能，無需後端端點）

## Relevant Files

### 需要建立的新檔案
- `frontend/src/context/CartContext.tsx` — 購物車狀態管理 Context，參考 [ThemeContext.tsx](frontend/src/context/ThemeContext.tsx) 和 [AuthContext.tsx](frontend/src/context/AuthContext.tsx) 的 Provider 模式
- `frontend/src/components/Cart.tsx` — 購物車頁面主組件，參考 [Products.tsx](frontend/src/components/entity/product/Products.tsx) 的數量選擇器和表格樣式

### 需要修改的檔案
- [frontend/src/components/Navigation.tsx](frontend/src/components/Navigation.tsx) — 添加購物車圖示和徽章（第 20-40 行的按鈕區域）
- [frontend/src/components/entity/product/Products.tsx](frontend/src/components/entity/product/Products.tsx) — 實現 `handleAddToCart()` 函數（第 44-49 行）
- [frontend/src/App.tsx](frontend/src/App.tsx) — 添加 `/cart` 路由和 CartProvider（第 10-20 行的 Provider 區塊，第 30-45 行的 Routes）

## Verification Steps

### 1. 功能測試
- 在商品頁面選擇數量並添加商品到購物車，確認導覽列徽章更新
- 點擊導覽列購物車圖示，確認導向購物車頁面
- 在購物車頁面調整商品數量，確認小計和總計即時更新
- 移除商品，確認列表和總計更新
- 測試運費邏輯：
  - 小計 < $150 時顯示 $25 運費
  - 小計 ≥ $150 時顯示免運費
- 測試折扣計算：確認為小計的 5%
- 點擊結帳按鈕，確認顯示「功能開發中」提示

### 2. 持久化測試
- 添加商品到購物車後重新整理頁面，確認購物車內容保留
- 關閉瀏覽器標籤頁再重新開啟，確認購物車內容保留
- 清除購物車後重新整理，確認購物車已清空

### 3. 主題模式測試
- 切換深色/淺色模式，確認購物車頁面所有元素正確顯示
- 確認按鈕、輸入框、表格在兩種模式下的對比度和可讀性
- 確認導覽列購物車圖示和徽章在兩種模式下清晰可見

### 4. 響應式測試
- 在不同螢幕尺寸測試購物車頁面布局
- 確認移動裝置上表格可水平滾動或自適應
- 確認 Order Summary 在小螢幕上正確顯示

### 5. 邊界情況測試
- 空購物車狀態顯示
- 單一商品的購物車
- 大量商品的購物車（>10 項）
- 數量為 0 時的處理
- 快速多次點擊添加按鈕的處理

## Acceptance Criteria

### 基本建構與執行
1. **Build 成功**：執行 `npm run build` 能成功建構前端專案，無編譯錯誤
2. **Dev 模式執行**：執行 `npm run dev` 能成功啟動開發伺服器，應用程式可在瀏覽器正常載入

### 功能完整性
3. **購物車核心功能**：能夠新增商品到購物車、修改數量、移除商品、清空購物車
4. **導覽列整合**：購物車圖示顯示於導覽列，徽章即時顯示商品總數
5. **計算邏輯正確**：小計、折扣（5%）、運費（$25 或免運費）、總計計算無誤
6. **持久化運作**：購物車資料在頁面重新整理後保留

### 使用者體驗
7. **深淺色模式支援**：所有購物車相關 UI 在深色與淺色模式下正常顯示
8. **空購物車處理**：空購物車時顯示適當提示訊息
9. **響應式設計**：購物車頁面在不同螢幕尺寸下正常顯示

## Decisions

### 技術決策
✅ **使用前端 localStorage 存儲**：不需要後端 API，簡化開發流程
✅ **運費金額 $25**：超過 $150 免運費
✅ **實現折扣功能**：固定 5% 折扣
✅ **結帳按鈕行為**：暫時顯示「功能開發中」提示

### 架構決策
✅ **Context API 管理狀態**：與現有的 AuthContext 和 ThemeContext 保持一致
✅ **購物車項目結構**：包含 productId, name, price, quantity, imageUrl
✅ **即時計算**：小計、折扣、運費、總計在 Context 中計算並提供
✅ **圖片路徑**：使用前端 assets 資料夾，假設產品圖片已存在

### 範圍界定
✅ **包含**：購物車頁面、導覽列圖示、數量管理、運費/折扣計算、深淺色模式
❌ **排除**：優惠券實際功能、後端 API 整合、結帳流程、訂單提交、庫存檢查
❌ **排除**：使用者登入狀態關聯（暫時不區分不同使用者的購物車）

### 樣式決策
✅ **參考附圖設計**：深色主題的表格式布局、綠色按鈕
✅ **深色/淺色模式**：使用 `${darkMode ? 'bg-dark text-light' : 'bg-white text-gray-800'}` 模式
✅ **過渡效果**：使用 `transition-colors duration-300`
✅ **主題色彩**：綠色按鈕 (`bg-primary`)、灰色表格邊框

## Further Considerations

### 1. 產品圖片來源
**問題**：購物車需要顯示產品圖片，但目前產品資料可能沒有圖片 URL 欄位。
- **Option A**：假設產品資料中有 `imgName` 欄位，構建圖片路徑為 `/src/assets/products/${imgName}`
- **Option B**：在添加到購物車時從產品組件傳遞圖片 URL
- **Option C**：使用佔位符圖片，等待產品資料完善
**建議**：Option A，參考現有 Product 模型結構

### 2. 優惠券功能範圍
**問題**：附圖顯示優惠券輸入框，但用戶未明確要求實現。
- **Option A**：僅實現 UI，不處理邏輯（保留可擴展性）
- **Option B**：完全實現優惠券驗證和折扣應用
**建議**：Option A，保持簡潔

### 3. 數量限制
**問題**：是否需要設定單一商品的數量上限？
- **Option A**：不限制，允許任意正整數
- **Option B**：設定合理上限（例如 99）並顯示錯誤訊息
**建議**：Option A，簡化實現
