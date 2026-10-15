# 00 總覽

## 這套系統為什麼存在
公司有「資安監控（SIEM）」與「工控資料（OT／Kepware）」相關系統。LibreNMS 負責回答一個基本問題：**這些主機與設備現在還活著嗎？** 它不分析攻擊，只看「活不活」與「忙不忙」。

## 環境分工

| 系統 | 角色 | 狀態 |
|---|---|---|---|
| **LibreNMS**（本文件） | 巡邏員：看設備與服務是否在線 | 運作中 |
| **Wazuh** | 資安警衛：收集日誌、偵測可疑行為 | 運作中，被 LibreNMS 監控 |
| **Kepware** | OT 資料轉接站（OPC UA 提供） | pware | 被 LibreNMS 做「Ping + 埠」檢查 |
| **Cisco C9800** | 無線 AP 控制台 | 被 LibreNMS 以 SNMP 監控 |
| **Microsoft Teams** | 告警通知出口 | — | 所有告警都送這裡 |

## 資料流向（簡圖）

```
 [nms 主機] LibreNMS
     │  定時 Ping／SNMP／TCP 埠檢查
     ├──► nms & wazuh 主機（SNMP）
     ├──► Cisco C9800 AP 控制台（SNMP）
     ├──► Kepware 主機（Ping + TCP 49320）
     └──► 異常時 ──► Microsoft Teams
```

## 本系統「做不到」的事
1. **看不到 Kepware 內部**：只知道主機通、埠有開，不知道資料有沒有正常傳送。
2. **看不到 AP 離線**：離線 AP 會從控制台清單消失，詳見 [01-devices.md](01-devices.md)。
3. **不分析資安事件**：那是 Wazuh 的工作。

## 責任分工
- 誰收 Teams 告警：Alice
- 誰處理設備問題：???
- 上級／窗口：jrlai