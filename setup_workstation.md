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

Если сервис запущен, то всё хорошо. Если нет, то проверяем файл сервиса (бывает, что это симлинк на `/dev/null`):

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

## Копируем забекапленные настройки

```bash
# сохраняем так:
# cp -r /home/roman/ /NAS/UNSORTED/
# Но лучше бекапить выборочно. Ибо кеши всякие не нужны, 
# а, например, Файрфокс (открытые страницы/сохраненные пароли) так не подхватывает настройки

sudo cp -r /NAS/UNSORTED/roman/ /home/
sudo chown -R roman /home/roman/
```

## Настройка Ubuntu

`gsettings` надстройка над `dconf`. Можно `sudo apt install -y dconf-editor`, и визуально менять настройки. Плюс, наглядно видно, какие настройки вообще есть.

Добавляем русскую раскладку клавиатуры, в дополнение к английской:

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

## Устанавливаем и настраиваем софт

```bash
sudo apt install -y net-tools

sudo apt install -y build-essential libssl-dev libffi-dev

sudo apt install -y tree

sudo apt install -y curl

sudo apt install -y ffmpeg

sudo apt install -y git
cp /NAS/tech/config/git/.gitconfig ~/
cp /NAS/tech/config/git/.gitignore ~/

echo "deb http://repo.yandex.ru/yandex-disk/deb/ stable main" | sudo tee -a /etc/apt/sources.list.d/yandex-disk.list > /dev/null && \
wget http://repo.yandex.ru/yandex-disk/YANDEX-DISK-KEY.GPG -O- | sudo apt-key add - && \
sudo apt-get update && \
sudo apt-get install -y yandex-disk
```

Зависимости для AppImage

```bash
sudo add-apt-repository universe
sudo apt install libfuse2t64
```

Python

```bash
sudo apt install -y python3-pip python3-venv python3-setuptools python3-wheel python3-dev python3-tk
```

Node.js через nvm (Node Version Manager)

```bash
# install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# download and install Node.js (you may need to restart the terminal)
nvm install 22

node -v

npm -v
```

### Десктопные приложения

```bash
sudo apt install -y doublecmd-gtk libunrar5
cp -r /NAS/tech/config/doublecmd ~/.config/doublecmd
# backup doublecmd config
# cp -r ~/.config/doublecmd/ /NAS/tech/config/

sudo apt install -y filezilla
# настройки живут в .config/filezilla
# надо бэкапить перед обновлением, а потом накатывать
# сейчас есть копия в /NAS/tech/config/filezilla

sudo apt install -y qbittorrent

sudo apt install -y vlc
# настройки живут в .config/vlc

sudo apt install -y libreoffice-writer
sudo apt install -y libreoffice-calc

# можно не устанавливать, просто запустить /NAS/tech/soft/telegram/Telegram
# Телеграм сам добавит себя в меню программ 
sudo snap install telegram-desktop
```

VS Code:

```bash
sudo snap install code --classic
code --install-extension streetsidesoftware.code-spell-checker
code --install-extension streetsidesoftware.code-spell-checker-russian
# Enable in VSCode: F1 > Enable Russian Spell Checker Dictionary
# Настройки пользователя живут в ~/.config/Code/User/settings.json
# Надо бэкапить и восстанавливать
```

XnView:

```bash
sudo apt install -y /NAS/tech/install/xnview/XnViewMP-1.8.0-linux-x64.deb
mkdir ~/.config/xnviewmp
cp -r /NAS/tech/config/xnviewmp/xnview.ini ~/.config/xnviewmp/xnview.ini
```

Google Chrome:

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

sudo apt install -y ./google-chrome-stable_current_amd64.deb
```

Virtualbox:

```bash
sudo apt install -y /NAS/tech/install/virtualbox/virtualbox-7.0_7.0.18-162988~Ubuntu~noble_amd64.deb 
```

OBS Studio:

```bash
sudo add-apt-repository ppa:obsproject/obs-studio
sudo apt update
sudo apt install ffmpeg obs-studio
```
