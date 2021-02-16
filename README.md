# "Шпаргалка" (CheatSheet) по установке Home Assistant Supervised в систему  Debian на неттоп (домашний сервер), ноутбук, нетбук, десктоп,  моноблок...
>Инструкция для тех, у кого по какой-либо причине _(не установили библиотеки, не обновили систему, не установили docker...)_ не получилось установить Home Assistant Supervised по этой официальной инструкции: https://github.com/home-assistant/supervised-installer

![alt](https://cs13.pikabu.ru/post_img/2021/02/10/11/1612982998195652270.png)

>Почему я считаю **Home Assistant Supervised** лучшим вариантом, из всех возможных методов установки, в начале 2021 года - написал [в начале этой статьи](https://pikabu.ru/story/ustanovkahome_assistant_supervised_v_sistemu_kali_linux_debian_nanettop_domashniy_server_noutbuk_desktop_ili_dazhe_virtualbox_8014550)

#### Подготовим систему.

- Для получения списка и обновления новых пактов, выполните:

`sudo apt-get update && sudo apt-get upgrade -y`

- Удаляем возможно оставшиеся зависимости пакетов:

`sudo apt autoremove -y`

- Устанавливаем необходимые пакеты, библиотеки, зависимости:

`sudo apt-get install -y software-properties-common apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat`

#### На этом этапе нам нужно установить Docker в Linux.

- Для этого вводим в терминал поочерёдно следующие команды

`sudo apt install -y docker.io`

`sudo systemctl enable docker --now`

- Если вы хотите добавить себя в группу Docker, чтобы использовать Docker без sudo:

`sudo usermod -aG docker $USER`

Так что тот факт, что веб-сайт Docker ничего не пишет про установку **docker.io** - ровным счётом ничего не значит! ))) Но, если вы по своим личным убеждениям хотите установить версию Docker Engine на Debian (Kali Linux), то переходите на сайт Docker к официальной инструкции - [Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/)


#### Устанавливаем Home Аssistant Supervised.

- Деактивируем и отключаем ModemManager:

`sudo systemctl disable ModemManager`

`sudo systemctl stop ModemManager`

- На всякий случай перезагружаем систему:

`sudo reboot`

- Загружаем скрипт **"supervised-installer"**:

`curl -Lo installer.sh https://raw.githubusercontent.com/home-assistant/supervised-installer/master/installer.sh`

- Запускаем установку способом, где установщик сам выбирает директории (папки для установки Home Аssistant Supervised):

`sudo bash installer.sh`

![alt](https://cs12.pikabu.ru/post_img/2021/02/11/7/1613040575122830823.png)

Через несколько минут (от 1 до 30, в зависимости от вашего "железа") после запуска скрипта установки,  Home Assistant будет доступен по адресу **ipaddress:8123**.

## Если Home Assistant недоступен или не запускается по адресу ipaddress:8123

Если через 20-30 минут Home Assistant всё ещё не доступен по адресу ipaddress:8123, точнее установка не завершилась полностью, то нужно  смотреть лог с возможными ошибками, нажав на голубой "пульсирующий кружок" и наблюдать на странице процесс установки.

![alt](https://cs14.pikabu.ru/post_img/2021/02/11/7/1613039012185733520.png)

Рассмотрим вариант решения возможной ошибки (ошибок):

>**ERROR (SyncWorker_0)** [supervisor.docker.interface] Can't install homeassistant/qemux86-64-homeassistant:2021.1.5 -> 404 Client Error for http+docker://localhost/v1.41/images/homeassistant/qemux86-64-homeassistant:2021.1.5/json: Not Found ("no such image: homeassistant/qemux86-64-homeassistant:2021.1.5: No such image: homeassistant/qemux86-64-homeassistant:2021.1.5").

>**WARNING (MainThread)** [supervisor.homeassistant.core] Error on Home Assistant installation. Retry in 30sec

>**ERROR (MainThread)** [asyncio] Task was destroyed but it is pending!
task: <Task pending name='Task-243' coro=<process_lock.<locals>.wrap_api() running at /usr/src/supervisor/supervisor/utils/__init__.py:33> wait_for=<Future pending cb=[<TaskWakeupMethWrapper object at 0x7f3e4a7cc670>()]>>

Она может возникнуть, когда при установке своей операционной системы на этапе **"Разбиение на разделы"** вы выбрали отдельные разделы для **/home и /var /tmp** Обычно программа автоматической установки "жадничает" выделяет для тома /var - 2-3 Гб согласно своей логике. Мы можем почистить этот том от "мусора" или увеличить его объём. Подробно о том, как это сделать я написал в этой статье: [Увеличение / Уменьшение логических томов LVM на Linux + очистка системы - памяти (cache) Kali Linux / Debian / Ubuntu / CentOS](https://pikabu.ru/story/uvelichenie__umenshenie_logicheskikh_tomov_lvm_nalinux__ochistka_sistemyi__pamyati_cachekali_linux__debian__ubuntu_centos_8012985)

Пример решения этой "проблемы":

`sudo lvextend -L +5G /dev/mapper/Kali--VM--vg-var -r`

![alt](https://cs14.pikabu.ru/post_img/2021/02/11/7/1613040330133263108.png)

После этого снова запускаем скрипт установки Home Аssistant Supervised:

`sudo bash installer.sh`

Заходим в браузере по указанному в скрипте адресу с любого устройства в своей локальной сети. Там должна открыться такая страничка (ниже). Вводим свои данные (регистрируемся) и настраиваем/тестируем систему....

![alt](https://cs14.pikabu.ru/post_img/2021/02/11/7/1613040911138466628.png)

**В С Ё ! ! ! ) ) )**
