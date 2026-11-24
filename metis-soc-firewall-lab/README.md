# metis-soc-firewall-lab

METIS「Linux 主機」關卡：SOC 日誌判讀 + 防火牆阻斷（初學者友善版）。

學生流程（只要 4 個指令）：
```
cat notes.txt
grep 401 access.log        # 找出一直登入失敗的來源 IP
block 198.51.100.23        # 在防火牆封鎖它
sudo verify_fw             # 驗證，取得 FLAG
```

## 在關卡編輯器怎麼填

| 欄位 | 內容 |
|---|---|
| 標題 | SOC 事件應變：日誌判讀與防火牆阻斷 |
| 難度／預估時間 | easy／15m |
| 標籤 | `linux, soc, firewall, log-analysis` |
| 任務說明 | 你是 SOC 值班人員。shop 網站登入頁遭到暴力破解，請從存取日誌找出攻擊來源，並在防火牆規則中阻斷它，且不能誤擋正常使用者。 |
| 目標 → 類型 | Linux 主機 |
| 映像 | `ghcr.io/linsolving85/metis-soc-firewall-lab:<最新版本>` |

### 第 1 題：找出攻擊來源（配分 40）
- 題目標題：找出攻擊來源
- 題目說明：打開「目標主機」終端機，輸入 `cat notes.txt` 閱讀值班交接，再從日誌 `access.log` 找出暴力破解登入頁的攻擊來源 IP。
- 答案：`198.51.100.23`
- 提示 1（扣 0.1）：狀態碼 401 代表登入失敗。暴力破解的特徵，是同一個來源反覆失敗。
- 提示 2（扣 0.3）：輸入 `grep 401 access.log`，找出現次數最多的那個 IP。

### 第 2 題：阻斷攻擊來源（配分 60）
- 題目標題：阻斷攻擊來源
- 題目說明：用 `block <IP>` 在防火牆封鎖攻擊來源，再輸入 `sudo verify_fw` 驗證，取得 FLAG。注意不可誤封正常客戶、合作夥伴或內網。
- 答案：`FLAG{soc_block_attacker_ok}`（不要勾「忽略大小寫」）
- 提示 1（扣 0.1）：封鎖指令格式是 `block 203.0.113.9`（換成你找到的 IP）。
- 提示 2（扣 0.3）：只封攻擊者的單一 IP。封錯了可以用 `unblock <IP>` 解除，`rules` 可以查看目前規則。

## 映像裡有什麼
| 路徑 | 擁有者／權限 | 用途 |
|---|---|---|
| `/home/student/notes.txt` | student / 644 | 值班交接與指令說明 |
| `/home/student/access.log` | student / 644 | 存取日誌（41 行，其中攻擊者 401 共 15 行） |
| `/home/student/firewall.rules` | student / 644 | 防火牆規則檔 |
| `/usr/local/bin/block, unblock, rules` | root / 755 | 簡化輸入的指令 |
| `/usr/local/sbin/verify_fw` | root / 700 | 驗證腳本，Flag 在裡面，學生讀不到 |

`student` 只能用 sudo 執行 `verify_fw`。腳本拒絕符號連結，也不回顯規則內容。
`.bashrc` 加了 `cd ~`，所以終端機一開就在家目錄。

## 發布
推 `v*` tag 會由 `.github/workflows/build-image.yml` 自動建置並推送到 ghcr；完成後把套件可見度設成 Public。

## 參考解法
`grep 401 access.log` → `198.51.100.23` 出現 15 次，客戶 `192.0.2.50` 只有 2 次（打錯密碼）。
`block 198.51.100.23` → `sudo verify_fw`。
