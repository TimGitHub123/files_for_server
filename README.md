Заходим через PUTTY на ПК с Ubuntu
по умолчанию мы будем в папке пользователя /home/ubuntu
** Обращаю внимание, что для Airflow и Metabase создается по одному контейнеру PosgreSQL для каждого
** Можно попробовать перед созданием контейнера как то создавать БД metabase и airflow в одном экземпляре PosgreSQL
*** Также обращаю внимание, что у Airflow и Metabase включены органичения памяти, чтобы их снять удали полностью раздел deploy со всеми подразделами

** Минимальные Системные требования **
ОЗУ		16 Gb (лучше дать сразу 20 Gb, Spark пошустрее будет)
CPU		4 ядра (тут тоже можно поиграться)
HDD		32 Gb

# Создаем папку для нашего проекта, к примеру **infrahome** (все кавычки только для визуальной простоты и выделения слов, **в работе и командах не использовать !!!**
```bash
mkdir infrahome
```
переходим в нее командой только для просмотра файлов:
```bash
cd infrahome
```

Через WinSCP грузим папки и файлы из архива 
```bash
00_img_airflow_trino.zip
```
В терминале PUTTY перемещаемся на одну папку выше командой **cd ..**
то есть мы будем по адресу **/home/ubuntu**

# Даем все права на папку, чтоб airflow имел права на всё, что внутри
```bash
sudo chmod -R 777 ./infrahome/
```
** переходим внутрь папки infrahome
```bash
cd infrahome
```

#запускаем песочницу!
sudo docker compose up -d

** обязательно после загрузки а запуска всех контейнеров дать права на все папку внутри директории infrahome, иначе Airflow будет постоянно перезапускаться. Сперва переходим снова на уровень выше:
```bash
cd ..
```

# Снова даем все права, чтобы airflow имел права на всё, что внутри если вдруг airflow не заведется
```bash
sudo chmod -R 777 ./simple_pet/
```

IP для инструментов:
|====|==============================================|============|============|=================|
|  № |		Название 	|			Адрес			|    login	 |     pass   |        База     |
|====|==============================================|============|============|=================|
|  1 |	Airflow			|	http://11.11.1.22:8081/	|   airflow  |  airflow   |                 |
|  2 |	S3 MiniO		|   http://11.11.1.22:9001/	| minioadmin | minioadmin |                 |
|  3 |	bi_postgres  	|	11.11.1.22 port 5432    |   postgres |  postgres  |                 |
|  4 |	metabase		|	http://11.11.1.22:3000/	|    		 | 		      |                 |
|  5 |	trino   		|	http://11.11.1.22:8080/ |    admin   |   	      |                 |
|  6 |	spark-iceberg	|	http://11.11.1.22:8888/ |    		 |  	 	  |                 |
|  7 |	rest			|	http://11.11.1.22:8181/	|    		 |   	 	  |                 |
|	 |					|							|    		 |   		  |                 |
|	 | postgres airflow	|	  11.11.1.22 port 5433  |  airflow   |  airflow   | airflow         |
|	 | meta_postgres	|	  11.11.1.22 port 5434  |  metabase  |  metabase  | metabase        |
|	 | postgres-iceberg	|	  11.11.1.22 port 5435	|  iceberg   |   iceberg  | iceberg_catalog |
|	 | bi_postgres  	|	  11.11.1.22 port 5436  |  postgres  |  postgres  | postgres        |
|====|==================|===========================|============|============|=================|

** Для перезапуска инфраструктуры, если что-то пошло не так:**

```bash
docker-compose down && docker-compose build && docker-compose up -d  
```


Команды для Docker
# покажет использование памяти каждым контейнером.
sudo docker stats

# Удалить все проекты находясь в папке с файлом docker-compose
sudo docker compose down --rmi all --volumes --remove-orphans

# С очисткой volumes (осторожно!)
docker system prune -a --volumes


# Неиспользуемые сети
docker network prune

# Что будет удалено (просмотр без удаления)
docker system df
docker system df -v

# Показать неиспользуемые ресурсы
docker container ls -a --filter status=exited
docker images -f dangling=true
docker volume ls -f dangling=true
docker network ls --filter type=custom