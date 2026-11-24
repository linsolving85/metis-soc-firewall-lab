# METIS Linux 主機關卡：SOC 日誌判讀 + 防火牆阻斷。平台會讓容器保持運作，所以不需要 CMD。
FROM debian:stable-slim

RUN useradd -m -s /bin/bash student \
    && echo 'cd ~' >> /home/student/.bashrc

COPY --chown=student:student --chmod=0644 home/notes.txt home/access.log home/firewall.rules /home/student/

# 簡化輸入的防火牆指令與驗證腳本（免 sudo 直接執行）
COPY --chmod=0755 block unblock rules verify_fw /usr/local/bin/
