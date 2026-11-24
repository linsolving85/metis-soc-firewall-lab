# metis-soc-firewall-lab

METIS「Linux 主機」關卡：SOC 日誌判讀 + 防火牆阻斷。學生以 `student` 登入，從 `~/access.log` 找出暴力破解的來源，
把 `DROP <IP>` 寫進 `~/firewall.rules`，執行 `sudo verify_fw` 驗證後取得 Flag。

## 在關卡編輯器怎麼填

| 欄位 | 內容 |
|---|---|
| id | `soc-firewall-lab` |
| 標題 | SOC 事件應變：日誌判讀與防火牆阻斷 |
| 難度／預估時間 | easy／20m |
| 標籤 | `linux, soc, firewall, log-analysis` |
| 任務說明 | 你是 SOC 值班人員。shop 網站登入頁遭到暴力破解，請從存取日誌找出攻擊來源，並在防火牆規則中阻斷它，且不能誤擋正常使用者。 |
| 目標 → 類型 | Linux 主機 |
| 映像 | `ghcr.io/linsolving85/metis-soc-firewall-lab:v1.0.0`（或 job summary 印出的 `@sha256:` 完整名稱） |
| 登入使用者／shell | `student`／`/bin/bash` |

### 第 1 題：找出攻擊來源（配分 40）
- 題目說明：打開「目標主機」終端機（登入後位於 `/`，請先執行 `cd ~`），閱讀 `~/notes.txt`，從 `~/access.log` 找出暴力破解登入頁的攻擊來源 IP。
- 答案：`198.51.100.23`
- 提示 1（扣 0.1）：暴力破解會讓同一個來源反覆登入失敗（狀態碼 401）。
- 提示 2（扣 0.3）：`grep ' 401' ~/access.log | awk '{print $3}' | sort | uniq -c | sort -rn`，次數遠高於其他人的就是攻擊者。

### 第 2 題：阻斷攻擊來源（配分 60）
- 題目說明：把攻擊來源寫入 `~/firewall.rules`（每行 `DROP <IP>`），再執行 `sudo verify_fw`，取得 FLAG。注意不可誤封正常客戶、合作夥伴或內網。
- 答案：`FLAG{soc_block_attacker_ok}`（不要勾「忽略大小寫」）
- 提示 1（扣 0.1）：用 `echo "DROP <IP>" >> ~/firewall.rules` 或編輯器新增一行。
- 提示 2（扣 0.3）：只封攻擊者的單一 IP；封整個網段會誤擋同網段的合作夥伴。

## 映像裡有什麼
| 路徑 | 擁有者／權限 | 用途 |
|---|---|---|
| `/home/student/notes.txt` | student / 644 | 任務說明 |
| `/home/student/access.log` | student / 644 | 存取日誌（約 120 行） |
| `/home/student/firewall.rules` | student / 644 | 學生要編輯的規則檔 |
| `/usr/local/sbin/verify_fw` | root / 700 | 驗證腳本，Flag 在裡面，學生讀不到 |

`student` 只能用 sudo 執行 `verify_fw`（`/etc/sudoers.d/student` 僅開放這一個指令）。腳本會拒絕符號連結，也不會回顯規則內容。

## 發布
1. 在 GitHub 建立 **Public** repo `metis-soc-firewall-lab`，推上這些檔案。
2. 打 tag 觸發建置：`git tag v1.0.0 && git push origin v1.0.0`
3. 到 repo 的 Packages，把 `metis-soc-firewall-lab` 的可見度改成 **Public**。
4. 在 Actions 的 job summary 複製含 `@sha256:` 的完整名稱，貼到編輯器的映像欄位，執行自動檢查與試玩。

## 本機確認
```
docker build -t soc-fw:dev .
docker run --rm -it -u student -w /home/student soc-fw:dev bash
# 在容器內：
echo "DROP 198.51.100.23" >> firewall.rules && sudo verify_fw
```

## 參考解法
`cd ~` 後執行 `grep ' 401' access.log | awk '{print $3}' | sort | uniq -c | sort -rn` → 攻擊者 `198.51.100.23`（60 次）。
`echo "DROP 198.51.100.23" >> ~/firewall.rules`，再 `sudo verify_fw`。
