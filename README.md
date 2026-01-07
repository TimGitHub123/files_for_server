# Полностью собранный docker-compose для развертывания на вашем личном сервере

## **Минимальные cистемные требования**
- ОЗУ	16 Gb (лучше дать сразу 20 Gb, Spark пошустрее будет)
- CPU	4 ядра (тут тоже можно поиграться)
- HDD	32 Gb

## **Нюансы**
- Для Airflow, Metabase и Confluence создается по одному контейнеру PosgreSQL для каждого
- Можно попробовать перед созданием контейнера создавать БД Metabase и Airflow в одном экземпляре PosgreSQL
- У Airflow и Metabase включены органичения памяти, чтобы их снять удали полностью раздел **deploy** со всеми подразделами

## **Процесс развертывания**

Заходим через PUTTY на ПК с Ubuntu. По умолчанию мы будем в папке пользователя **/home/ubuntu**

Создаем папку **infrahome** для нашего проекта
```bash
mkdir infrahome
```

Переходим в папку командой только для просмотра файлов
```bash
cd infrahome
```

Через WinSCP загружаем папки и файлы из архива 
```bash
00_img_airflow_trino.zip
```

В терминале PUTTY перемещаемся на одну папку выше, то есть мы будем по адресу **/home/ubuntu**
```bash
cd ..
```

Предоставляем все права доступа для папки нашего проекта, чтоб airflow имел полный перечень прав на все ее содержимое
```bash
sudo chmod -R 777 ./infrahome/
```

Опять переходим внутрь папки **/infrahome**
```bash
cd infrahome
```

Запускаем песочницу
```bash
sudo docker compose up -d
```
Дожидаемся сборки всех контейнеров (~2-3 минуты)

Опять переходим на уровень выше
```bash
cd ..
```

Снова на всякий случай предоставляем все права для папки нашего проекта, потому что могут быть проблемы с Airflow
```bash
sudo chmod -R 777 ./infrahome/
```
Поздравляем, развертывание завершено!

## **Получение IP сервера для доступа к инструментам**

Получить IP сервера можно следующей командой
```bash
wget -qO- eth0.me
```

IP используется для доступа ко всем инструментам, развернутым на сервере

## **IP для доступа к инструментам**

**Инструменты**
| № |		 Название 	  |			    Адрес	       		|    login	 |    pass    | 
|---|:----------------|:------------------------|:-----------|:-----------|
| 1 |	Airflow		    	|	http://\<ваш_IP\>:8081/	| airflow    | airflow    |
| 2 |	S3 MiniO	    	| http://\<ваш_IP\>:9001/	| minioadmin | minioadmin |
| 3 |	bi_postgres   	|	\<ваш_IP\> port 5432    | postgres   | postgres   |
| 4 |	Metabase	  	  |	http://\<ваш_IP\>:3000/	|            | 		        |
| 5 |	Trino   		    |	http://\<ваш_IP\>:8080/ | admin      |   	        |
| 6 |	Spark-iceberg	  |	http://\<ваш_IP\>:8888/ |    		     |  	 	      |
| 7 |	Rest			      |	http://\<ваш_IP\>:8181/	|    		     |    	 	    |
| 8 | SuperSet        | http://\<ваш_IP\>:8089/ | superset   | superset   |
| 9 | Confluence      | http://\<ваш_IP\>:8095/ |            |            |

**Базы данных**
| № |		 Название 	   |			    Адрес	       		|    login	 |    pass    |      База       |
|---|:-----------------|:-------------------------|:-----------|:-----------|:----------------|
| 1 | Postgres Airflow | \<ваш_IP\> port 5433     | airflow    | airflow    | airflow         |
|	2 | Meta_postgres  	 | \<ваш_IP\> port 5434     | metabase   | metabase   | metabase        |
|	3 | Postgres-iceberg | \<ваш_IP\> port 5435	    | iceberg    | iceberg    | iceberg_catalog |
|	4 | bi_postgres    	 | \<ваш_IP\> port 5436     | postgres   | postgres   | postgres        |

## **Остановка. перезапуск, удаление контейнеров и образов**

Если что-то пошло не так, и требуется остановка, пересборка (для Airflow) и повторный запуск контейнеров:
- Перейдите в директорию **/infrahome**
- выполните последовательно следующие команды в терминале
```bash
docker-compose down 
```

```bash
docker-compose build 
```

```bash
docker-compose up -d 
```

## **Дополнительные возможности**

Посмотреть использование памяти каждым контейнером
```bash
sudo docker stats
```

Удалить все проекты находясь в папке с файлом docker-compose
```bash
sudo docker compose down --rmi all --volumes --remove-orphans
```

Очистить все volumes (Выполняйте команду осторожно, последствия необратимы!)
```bash
docker system prune -a --volumes
```

Посмотреть неиспользуемые сети
```bash
docker network prune
```

Посмотреть список того, что будет удалено (просмотр без удаления)
```bash
docker system df
docker system df -v
```

Показать неиспользуемые ресурсы
```bash
docker container ls -a --filter status=exited
docker images -f dangling=true
docker volume ls -f dangling=true
docker network ls --filter type=custom
```
