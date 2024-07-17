# Установка Davinci Resolve 18.6.6 в Ubuntu 24.04 

Распаковываем установщик:

```bash
./DaVinci_Resolve_18.6.6_Linux.run --appimage-extract
```

Появится папка `squashfs-root`. Переходим в нее:

```bash
 cd squashfs-root/
```

Удаляем устаревшие библиотеки:

```bash
sed -i '/libapr1/d; /libaprutil1/d; /libasound2/d; /libglib2.0-0/d' ./AppRun
```

Устанавливаем

```bash
sudo ./AppRun -i
```

Установщик может ругаться на нехватку библиотек. Устанавливаем библиотеки и запускаем установщик еще раз. Должно успешно установиться.

```bash
sudo apt install -y libxcb-composite0 libxcb-cursor0 libxcb-xinerama0 libxcb-xinput0
```

Пробуем запустить:

```bash
/opt/resolve/bin/resolve
```

Видим ошибки, запускаем скрипт:

```bash
sudo ./fix_resolve.sh
```

Запускаем еще раз. Видим белое (или черное) окно вместо заставки.

```bash
/opt/resolve/bin/resolve
```

Останавливаем процесс в консоли ctrl+c и запускаем через иконку. Теперь должно всё работать.
