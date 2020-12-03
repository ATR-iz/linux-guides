# Настройка Gitea
### Установка
Обновляем репозитории
```sh
sudo apt update
```

Если необходимо, удалаем MySQL и/или MariaDB
```sh
sudo apt-get purge mysql*                                                                  
sudo apt-get purge mariadb*
```

Устанавливаем MariaDB и git
```sh
sudo apt install mariadb-server git
```

### Настройка доступа к БД
```sh
sudo mysql_secure_installation
```

Enter current password for root (enter for none) :
> Вводим пароль для БД. Если это первый запуск, то введенный пароль будет сохранен как новый.

Change root password? - N
> Изменение пароля для root

Remove anonymous users? - Y
> Удаление анонимных пользователей имеет важное значение для безопасности данных, поскольку не позволяет людям входить на сервер MYSQL без создания учетной записи пользователя.

Disallow root login remotely? - Y
> Запрет удаленного входа в систему root затрудняет взлом вашей базы данных. Чтобы выполнить вход в систему с правами root, вам нужно будет находиться непосредственно на устройстве. Удаленное подключение к базе данных SQL от имени пользователя root будет отклонено.

Remove test database and access to it? - Y
> Удаляет тестовые данные, которые в работе не нужны.

### Настройка БД
Открываем консоль БД и вводим пароль установленный ранее.
```sh
sudo mysql -u root -p
```
Создаем новую базу данных gitea
```sh
CREATE DATABASE giteadb;
```

Создаем пользователя giteauser с паролем hellogitea
```sh
CREATE USER 'giteauser'@'localhost' IDENTIFIED BY 'hellogitea';
```

Даем все привелегии базы giteadb для пользователя giteauser
```sh
GRANT ALL PRIVILEGES ON giteadb.* TO 'giteauser'@'localhost';
```

Очищаем таблицу привелегий
```sh
FLUSH PRIVILEGES;
```

Выходим из консоли
```sh
exit
```

### Установка Gitea
Создаем нового пользователя git. Параметр --disabled-login означает что никто не сможет залогиниться под этим пользователем.
```sh
sudo adduser --disabled-login git
```

Переходим в директорию пользователя git
```sh
cd /home/git
```

Создаем папку gitea
```sh
mkdir gitea
```

Переходим в созданную дирректорию
```sh
cd /home/git/gitea
```

Скачиваем gitea
```sh
wget https://dl.gitea.io/gitea/1.12.5/gitea-1.12.5-linux-arm-6 -O gitea
```

Устанавливаем файл исполняемым
```sh
chmod +x gitea
```

Пользователь git получает права для всех созданных файлов
```sh
sudo chown -R git:git /home/git
```

Создаем сервис для запуска gitea
```sh
sudo nano /etc/systemd/system/gitea.service
```
В файле указываем следующий текст
```sh
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target

[Service]
# Modify these two values ​​and uncomment them if you have
# repos with lots of files and get to HTTP error 500 because of that
###
# LimitMEMLOCK=infinity
# LimitNOFILE=65535
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/home/git/gitea
ExecStart=/home/git/gitea/gitea web
Restart=always
Environment=USER=git 
HOME=/home/git

[Install]
WantedBy=multi-user.target
```

Активируем созданный сервис
```sh
sudo systemctl enable gitea.service
```

Запускаем сервис
```sh
sudo systemctl start gitea.service
```

### Настройка Gitea
После запуска сервиса необходимо открыть веб интерфейс по адресу:
```sh
http://<IPADDRESS>:3000
```

При первом запуске откроется страница настроек. 

> В поле 'User' необходимо указать пользователя созданного в бд (giteauser).  
> В поле 'Password' указать соответсвующий пароль (hellogitea).  
> В поле 'Database name' указать имя базы данных (giteadb).  
> В поле 'Application URL' указать ip адресс сервера.  

Нажать кнопку _'Install Gitea'_ для сохранения настроек.

**_Gitea готова для использования. Осталось только зарегистрировать нового пользователя._**