# METIS Linux 主機關卡：SOC 日誌判讀 + 防火牆阻斷。平台會讓容器保持運作，所以不需要 CMD。
FROM debian:stable-slim

RUN apt-get update && apt-get install -y --no-install-recommends sudo \
    && rm -rf /var/lib/apt/lists/* \
    && useradd -m -s /bin/bash student

COPY --chown=student:student --chmod=0644 home/notes.txt home/access.log home/firewall.rules /home/student/

# 驗證腳本與 Flag 只有 root 讀得到；student 只能透過 sudo 執行它
COPY --chown=root:root --chmod=0700 verify_fw /usr/local/sbin/verify_fw
RUN echo 'student ALL=(root) NOPASSWD: /usr/local/sbin/verify_fw' > /etc/sudoers.d/student \
    && chmod 0440 /etc/sudoers.d/student
