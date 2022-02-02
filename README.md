# Celery-report
**Celery** — это простая, гибкая и надежная распределенная система для обработки большого количества сообщений, предоставляющая операции с инструментами, необходимыми для обслуживания такой системы. Это очередь задач, ориентированная на обработку в реальном времени, а также поддерживающая планирование задач.

Celery имеет открытый исходный код и распространяется под лицензией BSD Licensei. 

**Что умеет Celery** (для каких задач в основном используется):

  - Выполнять задания асинхронно или синхронно
  - Выполнять периодические задания
  - Выполнять отложенные задания
  - Распределенное выполнение (может быть запущен на N серверах)
  - В пределах одного worker'а возможно конкурентное выполнение нескольких задач(одновременно)
  - Выполнять задание повторно, если вылез exception
  - Ограничивать количество заданий в единицу времени (rate limit, для задания или глобально)
  - Routing заданий (какому worker'у что делать)
  - Несложно мониторить выполнение заданий
  - Выполнять подзадания
  - Присылать отчеты об exception'ах на email
  - Проверять выполнилось ли задание

### Как работает Celery

Рассмотрим коротко, из чего состоит архитектура Celery.

В Celery задачи создают так называемые **“продюсеры” (producers)**. Ими может выступать Celery beat (cron), который будет генерировать таски ежеминутно, ежечасно — как мы запрограммируем, либо же задачи будут генерироваться из приложения (не обязательно Django) по событию: когда пользователь что-то нажал на фронте, на бэкенде сгенерировалась задача. 

Таски попадают в очередь в **брокер** (Redis или RabbitMQ например). В брокере можно организовать несколько очередей и присвоить каждой приоритет, исходя из важности.

**Consumers** — собственно, **воркеры (workers)** — это исполнители задач, которые забирают и выполняют таски из очередей в брокере. Для каждого воркера очередь настроена индивидуально. Воркеры могут возвращать или не возвращать результаты. Если результаты выполнения задачи нам не нужны, можем их игнорировать. Если нужны, они будут храниться в бэкенде Celery в виде данных типа success и hash и информации о времени (длительности) выполнения задачи. Как бэкенд также может использоваться Redis.

![image](https://evergreens.com.ua/assets/images/articles/celery-flower/how_it_works-min.png)

## Как установить Celery в проекте
*на примере Django проекта*
1) install Celery
2) Создать и настроить файл Celery.py в корне проекта по соседству с файлом settings.py (если не Джанго, то делать по аналогии)

![Screenshot from 2022-02-02 19-25-26](https://user-images.githubusercontent.com/68247479/152194797-89b5302d-c9c6-4836-b249-aa43a119c16a.png)
3) install Redis/MQRabbit в качестве брокера
4) Сконфигурировать брокер в фале настроек (settings.py in Django)

![Screenshot from 2022-02-02 19-34-52](https://user-images.githubusercontent.com/68247479/152196769-26d3e773-c099-4964-a1b8-a0ce305e2dbd.png)

