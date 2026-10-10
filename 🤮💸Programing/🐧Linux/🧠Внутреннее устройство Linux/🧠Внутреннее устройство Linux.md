---
tags: [moc, linux]
---

# 🧠 Внутреннее устройство Linux

Связано с [[🤮💸Programing]]

Хаб раздела: как Linux работает изнутри. Визуальная карта — [[🔎 Linux Internals Navigation.canvas|canvas]].

- [[🏛️Уровни системы и ядро]] — слои, user space vs kernel space, кольца защиты
- [[📞Системные вызовы и модули]] — syscall, strace, монолитное ядро, modprobe
- [[🥾Загрузка системы]] — BIOS/UEFI, GRUB, ядро, initramfs
- [[⚙️systemd]] — unit'ы, targets, systemctl, journalctl, свой сервис
- [[🧵Процессы]] — состояния, зомби, сигналы, nice
- [[🧊Namespaces и cgroups]] — изоляция и лимиты — основа Docker
- [[💾Память]] — виртуальная память, swap, OOM
- [[📂Файловая система]] — FHS, /proc и /sys, mount, fstab
- [[🔐Inode и права доступа]] — inode, hard/soft links, chmod, setuid

#moc #linux
