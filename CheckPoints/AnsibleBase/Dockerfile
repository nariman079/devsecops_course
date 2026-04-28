FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV TERM=xterm-256color

# Установка пакетов
RUN apt-get update && \
    apt-get install -y openssh-server sudo nano git nginx curl wget bash procps && \
    # Скачиваем ttyd
    wget -O /usr/local/bin/ttyd https://github.com/tsl0922/ttyd/releases/download/1.7.4/ttyd.x86_64 && \
    chmod +x /usr/local/bin/ttyd && \
    # Настройка SSH
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    # Очистка
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Установка пароля root
RUN echo 'root:student123' | chpasswd

EXPOSE 22 80 7681

# ЗАПУСК:
# 1. SSH
# 2. ttyd без пароля (-a убран), с флагом --writable, запускаем /bin/bash
CMD service ssh start && \
    /usr/local/bin/ttyd -p 7681 --writable /bin/bash