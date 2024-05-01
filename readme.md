# DatabaseClient
___
## Описание
DatabaseClient - это инструмент, предназначенный для автоматизации 
процесса установки и настройки базы данных PostgreSQL в контейнере 
Docker на удаленном хосте. Он предоставляет функциональность для 
проверки доменных имен или IP-адресов, установления SSH-соединений, 
установки и настройки PostgreSQL, а также выполнения запросов к 
базе данных.   
Результатом выполнения данного кода является установка PostgreSQL 
в контейнере Docker на удаленном хосте, а также вывод результата запроса `SELECT 1`к 
созданной базе данных
___
## Установка и пример использования 

1. `git clone https://github.com/Arbuzzopnik/Test-Tasks.git`  
2. `pip install requirements.txt`
3. `python database_client.py <hostname>`

### Пример использования 
```
python database_client.py 192.168.56.103
python database_client.py somehost
```
___
## Требования к удаленному хосту
1. На удаленном хосте должен быть установлен и запущен SSH сервер.  
2. У пользователя, под которым планируется подключаться, должны быть права 
на подключение по SSH.
3. Должны быть настроены SHH ключи. На локальном компьютере, с 
которого производится установка, должен быть соответствующий приватны ключ, 
а на удаленном хосте — соответствующий публичный ключ.
4. На удаленном хосте должен быть доступен порт 5432.
5. На удаленном хосте должен быть установлен Docker.
6. Пользователь, от имени которого производится установка, должен быть в группе Docker.
7. У удаленного хоста должен быть доступ в интернет.
___
## Вопросы, возникшие в процессе разработки, а также принятые решения
### Отказ от передачи паролей 
В целях выполнения требований информационной безопасности, было принято 
решение не использовать пароли в коде (за исключением пароля для 
пользователя при создании базы данных). Отсюда вытекает реализация с 
подключением исключительно по SSH ключам, а также наличием пользователя в группе
 Docker.
### Использование Docker
Так как в задании не было указано, какой операционной системой должен
обладать удаленный хост, было принято решение использовать Docker.  
Контейнеры Docker могут быть развернуты на любой операционной системе, 
которая поддерживает Docker, что делает данный выбор идеальным решением для данной задачи.
### Описание модели DbConfig
Для конфигурации базы данных было принято решение использовать 
 Pydantic, данный инструмент обеспечивает автоматическую валидацию 
входных данных, что помогает предотвратить ошибки, связанные с 
неправильной конфигурацией базы данных.
### Имя пользователя и конфигурация базы данных
Так как в задании к выполнению данного проекта было строго указано
> Приложение принимает один параметр - ip или адрес удаленного хоста

возник вопрос о выборе пользователя, от имени которого проводится подключение
и установка.  

Единственный возможный вариант, пришедший мне в голову, позволяющий подключаться к доступному хосту
от имени одного и тоже пользователя, был вариант с подключением от имени
root. Однако данная практика считается неправильной с точки зрения ИБ, поэтому
было принято решение отказаться от данного способа.

Исходя из всего вышеперечисленного, было принято решение указать имя пользователя явно
как admin `username = "admin"` в 150 строчке кода. Если бы данного ограничения 
не было, я бы организовал вызов программы следующим образом:  

`python database_client.py <hostname> <username>`,  
где username (имя пользователя) задавалось бы как:  

`username = sys.argv[2]`

Из данного ограничения также вытекает проблема конфигурации базы данных,
которая в данном проекте задана явно и выглядит следующим образом:
``` 
db_config = DbConfig(container_name='postgres',
                     db_name='test_db',
                     user='admin',
                     password='pass')
```
Эти параметры задаются явно и при желании их можно изменить отредактировав
непосредственно код.
___




 
