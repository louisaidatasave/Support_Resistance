# Support_Resistance

# MT5 Forex Analyzer - Support & Resistance Analysis System

[![Python](https://img.shields.io/badge/Python-3.7%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-success.svg)]()

> 🚀 **24小時自動化MT5交易分析系統** - 三階段智能分析：支撐阻力識別 → 進階分析 → 整合報告

一個專為MetaTrader 5 (MT5) 設計的全自動交易技術分析系統，提供多商品、多時間框架的支撐阻力分析、費波納契共振檢測、歷史驗證與智能報告生成。

---

## 📋 目錄

- [核心功能](#-核心功能)
- [系統架構](#-系統架構)
- [快速開始](#-快速開始)
- [使用指南](#-使用指南)
- [三階段工作流程](#-三階段工作流程)
- [商品支援](#-商品支援)
- [技術規格](#-技術規格)
- [專案結構](#-專案結構)
- [故障排除](#-故障排除)
- [貢獻指南](#-貢獻指南)
- [許可證](#-許可證)

---

## ✨ 核心功能

### 🎯 自動化分析
- ⏰ **24小時運行**: 每5分鐘自動執行完整分析週期
- 🔄 **多商品支援**: 同時處理86個金融商品
- 📊 **9個時間框架**: 1M, 5M, 15M, 30M, 1H, 4H, 1D, 1W, 1MN
- 🧩 **774個獨立策略**: 每個商品-時間框架組合獨立分析

### 📈 三階段處理流程

#### Phase 1: 支撐阻力分析
- 🎯 智能識別30條關鍵支撐阻力線
- 💪 強度評分與觸及次數統計
- 📐 距離現價計算與排序
- 📊 極值點群組分析（5組）

#### Phase 2: 進階分析
- 🔢 **費波納契共振分析** (純Python實作)
  - 提取5組極值點
  - 計算時間框架特定參數
  - 識別共振區域與強度排名
- ✅ **驗證模組** (DuckDB + 時間分析)
  - 從30條線中篩選最近上下各1條
  - 歷史K棒數據回測驗證
  - 時間衰減與時效性分析

#### Phase 3: 整合報告
- 📋 自動生成JSON格式整合報告
- ⏰ 每5分鐘更新報告
- 🗂️ 自動清理舊報告（保留最新20個）
- 📊 提供摘要統計與排名

### 🛡️ 可靠性特性
- 💾 **斷點續傳**: 支援從中斷點恢復執行
- 📝 **完整日誌**: 系統、階段、錯誤分類日誌
- 🔍 **錯誤恢復**: 自動重試失敗任務
- 📈 **進度追蹤**: 即時顯示執行進度

---

## 🏗️ 系統架構

```
┌─────────────────────────────────────────────────────────┐
│                     Main Controller                      │
│              (24h Automation + Scheduler)                │
└─────────────────────────────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│   Phase 1    │   │   Phase 2    │   │   Phase 3    │
│  SR Analysis │──▶│   Advanced   │──▶│  Reporting   │
│  (774 files) │   │   Analysis   │   │ & Dashboard  │
└──────────────┘   └──────────────┘   └──────────────┘
        │                   │                   │
        ▼                   ▼                   ▼
   30條SR線          費波納契共振          整合報告JSON
   5組極值點          驗證 + 時間          可視化輸出
```

### 數據流向
```
MT5商品列表 → Phase1策略檔案 → JSON輸出 → Phase2分析模組 → 
data_exchange目錄 → Phase3報告引擎 → 最終報告輸出
```

---

## 🚀 快速開始

### 系統需求

```plaintext
- Python 3.7+
- Windows / Linux / macOS
- 8GB+ RAM (推薦)
- 10GB+ 磁碟空間
```

### 安裝步驟

1. **克隆專案**
```bash
git clone https://github.com/yourusername/MT5_Forex_Analyzer.git
cd MT5_Forex_Analyzer/Support_Resistance
```

2. **建立虛擬環境**
```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate
```

3. **安裝依賴套件**
```bash
pip install -r requirements.txt
```

4. **配置環境變數**
```bash
# 複製環境變數範本
cp config/environment.example .env

# 編輯 .env 檔案，填入您的配置
# 注意：不要提交 .env 到 Git
```

5. **驗證安裝**
```bash
python tools/test_env.py
```

### 首次運行

```bash
# 啟動主程式（會顯示商品選擇介面）
python main.py

# 或直接使用腳本
python scripts/run_phase1.py  # 只執行Phase 1
python scripts/run_phase2.py  # 只執行Phase 2
python scripts/run_phase3.py  # 只執行Phase 3
```

---

## 📖 使用指南

### 互動式商品選擇

啟動系統後，您可以：

1. **選擇商品** - 從86個商品中選擇要分析的標的
   ```
   輸入數字: 1 或 1,5,10
   輸入範圍: 1-10
   全選: all
   清除: clear
   開始: start
   ```

2. **24小時自動化**
   - 系統會每5分鐘自動執行一次完整分析
   - 背景運行，不影響其他操作
   - 按 Ctrl+C 安全停止

### 查看結果

```bash
# 查看Phase1結果
ls phase1_sr_analysis/results/XAUUSD/1H/

# 查看Phase2結果
ls phase2_advanced_analysis/data_exchange/

# 查看Phase3報告
ls phase3_reporting/output/XAUUSD/
```

### 生成報告

```bash
# 生成單一商品報告
python phase3_reporting/generate_report.py XAUUSD

# 生成所有商品報告
python phase3_reporting/generate_report.py

# 清理舊報告（保留最新20個）
python phase3_reporting/generate_report.py --clean --keep 20
```

---

## 🔄 三階段工作流程

### Phase 1: 支撐阻力分析
```
輸入: MT5商品列表 + 時間框架
處理: 774個獨立策略檔案並行執行
輸出: result/{symbol}/{timeframe}/analysis.json
```

**關鍵輸出欄位:**
- `lines[]` - 30條支撐阻力線
- `extreme_levels.groups[]` - 5組極值點
- `metadata` - 時間框架、當前價格等資訊

### Phase 2: 進階分析

#### 2.1 費波納契模組
```python
# 純Python實作，不使用外部庫
輸入: extreme_levels.groups[] (5組極值點)
處理: 計算費波納契回撤與擴展
輸出: data_exchange/fib_output.json
```

#### 2.2 驗證模組
```python
# DuckDB查詢 + 時間分析
輸入: lines[] (30條線) + 歷史K棒數據
處理: 篩選最近上下各1條 + 歷史驗證
輸出: data_exchange/validation_output.json
```

### Phase 3: 整合報告
```
輸入: fib_output.json + validation_output.json
處理: 數據聚合 + 統計分析
輸出: output/{symbol}/final_report_{timestamp}.json
```

---

## 🌍 商品支援

系統支援86個金融商品，涵蓋多個資產類別：

| 類別 | 數量 | 範例 |
|------|------|------|
| 外匯主要貨幣對 | 7 | EURUSD, GBPUSD, USDJPY, USDCHF |
| 外匯次要貨幣對 | 15 | EURJPY, GBPJPY, EURGBP |
| 外匯異國貨幣對 | 10 | USDTRY, USDZAR, USDMXN |
| 貴金屬 | 2 | XAUUSD (黃金), XAGUSD (白銀) |
| 能源 | 2 | XTIUSD (原油), XNGUSD (天然氣) |
| 指數 | 10 | US500, US30, NAS100, UK100 |
| 加密貨幣 | 5 | BTCUSD, ETHUSD, LTCUSD |
| 商品期貨 | 20+ | 農產品、金屬、能源 |

完整列表請參閱: `docs/01_MT5_symbols_list.csv`

---

## 🔧 技術規格

### 核心技術棧
```yaml
語言: Python 3.7+
資料處理: Polars, DuckDB, NumPy, Pandas
資料庫: DuckDB (查詢優化)
排程: Schedule
日誌: Logging, ColorLog
配置: YAML, JSON, Python-dotenv
測試: Pytest
```

### 效能指標
- **處理速度**: ~50個策略檔案/分鐘
- **記憶體使用**: 通常 < 2GB
- **報告生成**: 0.5-1秒/商品
- **並行處理**: 最多4個工作線程

### 檔案格式
- **配置**: JSON, CSV, YAML
- **結果輸出**: JSON
- **日誌**: TXT (UTF-8)
- **報告**: JSON, HTML (未來)

---

## 📁 專案結構

```
Support_Resistance/
│
├── main.py                      # 🚀 主控制程式
├── requirements.txt             # 📦 依賴套件列表
├── .env                         # 🔒 環境變數（不上傳Git）
├── .gitignore                   # 🚫 Git忽略檔案
│
├── config/                      # ⚙️ 配置目錄
│   ├── env_manager.py          # 環境變數管理器
│   ├── phase_config.json       # 階段配置
│   └── timeframes.csv          # 時間框架定義
│
├── docs/                        # 📚 文件目錄
│   ├── README.md               # 文件總覽
│   ├── ARCHITECTURE.md         # 系統架構
│   └── 01_MT5_symbols_list.csv # 商品列表
│
├── phase1_sr_analysis/          # 📊 Phase 1
│   ├── strategies/             # 774個策略檔案
│   │   ├── XAUUSD/
│   │   ├── EURUSD/
│   │   └── ...
│   └── results/                # SR分析結果
│
├── phase2_advanced_analysis/    # 🔬 Phase 2
│   ├── fibonacci_module/       # 費波納契模組
│   ├── validation_module/      # 驗證模組
│   └── data_exchange/          # 模組間數據交換
│
├── phase3_reporting/            # 📋 Phase 3
│   ├── generate_report.py      # 報告生成腳本
│   ├── report_engine.py        # 報告引擎
│   └── output/                 # 最終報告
│
├── tools/                       # 🛠️ 工具集
│   ├── progress_tracker.py     # 進度追蹤
│   ├── error_diagnostics.py    # 錯誤診斷
│   └── system_health_checker.py # 系統健康檢查
│
├── logs/                        # 📝 日誌目錄
│   ├── phase1/
│   ├── phase2/
│   ├── phase3/
│   └── system/
│
└── tests/                       # 🧪 測試目錄
    ├── test_phase1/
    ├── test_phase2/
    └── test_integration/
```

---

## 🐛 故障排除

### 常見問題

#### 1. 模組導入錯誤
```bash
# 問題: ModuleNotFoundError
# 解決:
pip install -r requirements.txt
```

#### 2. 環境變數未設定
```bash
# 問題: 環境變數讀取失敗
# 解決:
cp config/environment.example .env
# 然後編輯 .env 檔案
```

#### 3. Phase1 策略檔案不存在
```bash
# 問題: 找不到策略檔案
# 解決: 確認策略檔案結構
python tools/strategy_files_validator.py
```

#### 4. Phase2 輸出檔案格式錯誤
```bash
# 問題: JSON格式不正確
# 解決: 驗證輸出格式
python tools/data_integrity_validator.py
```

#### 5. 記憶體不足
```bash
# 問題: MemoryError
# 解決: 調整並行處理數量
# 編輯 config/phase_config.json
"PHASE1_MAX_WORKERS": 2  # 減少為2
```

### 日誌檢查

```bash
# 查看系統日誌
tail -f logs/system/main.log

# 查看Phase1日誌
tail -f logs/phase1/execution.log

# 查看錯誤日誌
grep ERROR logs/system/main.log
```

### 診斷工具

```bash
# 系統健康檢查
python tools/system_health_checker.py

# 檢查策略檔案完整性
python tools/strategy_files_validator.py

# 檢查Phase1輸出
python tools/phase1_diagnostic_tool.py

# 檢查資料完整性
python tools/data_integrity_validator.py
```

---

## 🤝 貢獻指南

我們歡迎所有形式的貢獻！

### 如何貢獻

1. Fork 本專案
2. 建立特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 開啟 Pull Request

### 開發規範

- 遵循 PEP 8 代碼風格
- 添加適當的註釋和文檔字符串
- 為新功能編寫測試
- 更新相關文檔

### 報告問題

請使用 [GitHub Issues](https://github.com/yourusername/MT5_Forex_Analyzer/issues) 報告錯誤或建議新功能。

---

## 📄 許可證

本專案採用 MIT 許可證 - 詳見 [LICENSE](LICENSE) 文件

---

## 🙏 致謝

- MetaTrader 5 平台
- Python 開源社群
- 所有貢獻者和使用者

---

## 📞 聯絡方式

- **專案主頁**: [GitHub Repository](https://github.com/yourusername/MT5_Forex_Analyzer)
- **問題追蹤**: [GitHub Issues](https://github.com/yourusername/MT5_Forex_Analyzer/issues)
- **文檔**: [Wiki](https://github.com/yourusername/MT5_Forex_Analyzer/wiki)

---

## 🎯 未來規劃

- [ ] Web Dashboard 視覺化介面
- [ ] 即時警報系統
- [ ] 機器學習預測模組
- [ ] 多語言支援（英文、簡體中文）
- [ ] Docker 容器化部署
- [ ] REST API 介面
- [ ] 行動端應用程式

---

<p align="center">
  <strong>⭐ 如果這個專案對您有幫助，請給我們一個星星！⭐</strong>
</p>

<p align="center">
  Made with ❤️ by MT5 Forex Analyzer Team
</p>
