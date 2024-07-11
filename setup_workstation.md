# Настройка рабочей станции

## Настраиваем NAS

Прежде всего надо настроить проводное соединение:
- адрес: `192.168.1.1`
- маска: `255.255.255.0`

### Ставим необходимые пакеты

```bash
sudo apt install -y nfs-common
systemctl status nfs-common
```

Eсли сервис запущен, то всё хорошо. Если нет, то проверяем файл сервиса (бывает, что это симлинк на `/dev/null`):

```bash
file /lib/systemd/system/nfs-common.service
```

Тогда удаляем файл, прописываем сервис в автозагрузку и сразу стартуем (ключ `--now`):

```bash
sudo rm /lib/systemd/system/nfs-common.service
sudo systemctl enable --now nfs-common
```

Проверяем, что сервис запущен:

```bash
systemctl status nfs-common
```

В последних версиях Убунты (23.10, 24.04) обычно показывает, что сервис  `inactive (dead)`, но NAS при этом монтируется и всё работает. Пока забил и не разбирался.

### Монтируем NAS

Создаем папку в корне диска, монтируем туда NAS, добавляем монтирование в автозагрузку и создаем закладку в файловом менеджере:

```bash
sudo mkdir /NAS
sudo mount --types nfs 192.168.1.2:/NAS /NAS
sudo sh -c "echo '192.168.1.2:/NAS /NAS nfs defaults 0 0' >> /etc/fstab"
echo "file:///NAS NAS" >> ~/.config/gtk-3.0/bookmarks
```

## Настройка Ubunutu

`gsettings` надстройка над `dconf`. Можно `sudo apt install -y dconf-editor`, и визуально менять настройки. Плюс, наглядно видно, какие настройки вообще есть.

Добавляем русскую ракладку клавиатуры, в дополнение к английской:

```bash
gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'us'), ('xkb', 'ru')]"

gsettings set org.gnome.desktop.input-sources per-window true
```

Меняем цвет и обои рабочего стола (чтобы удалить картинку нужно передать пустой путь `""`):

```bash
gsettings set org.gnome.desktop.background picture-uri "file:///NAS/tech/config/system/green-forest-wallpaper.jpg"

gsettings set org.gnome.desktop.background primary-color '#336677'
```

Настраиваем размера док-панели:

```bash
gsettings set org.gnome.shell.extensions.dash-to-dock dash-max-icon-size 24
```

Подключаем папку со шрифтами:

```bash
ln --symbolic /NAS/tech/fonts/ .fonts
```
