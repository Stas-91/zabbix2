# Домашнее задание к занятию "`«Git»`" - `Помельников Станислав`


### Задание 1

Зарегистрируйте аккаунт на GitHub.
Создайте новый отдельный публичный репозиторий. Обязательно поставьте галочку в поле «Initialize this repository with a README».
Склонируйте репозиторий, используя https протокол git clone ....
Перейдите в каталог с клоном репозитория.
Произведите первоначальную настройку Git, указав своё настоящее имя и email: git config --global user.name и git config --global user.email johndoe@example.com.
Выполните команду git status и запомните результат.
Отредактируйте файл README.md любым удобным способом, переведя файл в состояние Modified.
Ещё раз выполните git status и продолжайте проверять вывод этой команды после каждого следующего шага.
Посмотрите изменения в файле README.md, выполнив команды git diff и git diff --staged.
Переведите файл в состояние staged или, как говорят, добавьте файл в коммит, командой git add README.md.
Ещё раз выполните команды git diff и git diff --staged.
Теперь можно сделать коммит git commit -m 'First commit'.
Сделайте git push origin master.
В качестве ответа добавьте ссылку на этот коммит в ваш md-файл с решением.



### Задание 2

`Создайте файл docker-compose.yml и внесите туда первичные настройки:`

`version;`  
`services;`  
`volumes;`  
`networks.`  

`При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.`


### Задание 3

1. `Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.`
2. `Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus).`
3. `Обеспечьте внешний доступ к порту 9090 c докер-сервера.`


### Задание 4

1. `Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.`
2. `Обеспечьте внешний доступ к порту 9091 c докер-сервера.`


### Задание 5

1. `Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.`
2. `Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana).`
3. `Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.`
4. `Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.`

### Задание 6

1. `Настройте поочередность запуска контейнеров.`
2. `Настройте режимы перезапуска для контейнеров.`
3. `Настройте использование контейнерами одной сети.`
4. `Запустите сценарий в detached режиме.`


### Задание 7

1. `Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.`
2. `Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.`
3. `Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).`
4. `Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.`

В качестве решения приложите:  

docker-compose.yml целиком;  
скриншот команды docker ps после запуске docker-compose.yml;  
скриншот графика, постоенного на основе вашей метрики.  

```
version: '3.7'

volumes:
  prometheus_data: {}
  grafana_data: {}

networks:
  PomelnikovSV-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16

services:
  prometheus:
    image: prom/prometheus:v2.36.2
    container_name: PomelnikovSV-netology-prometheus
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    ports:
      - 9090:9090
    links:
      - pushgateway:pushgateway
    depends_on:
      - pushgateway
    networks:
      - PomelnikovSV-my-netology-hw
    restart: always

  grafana:
    image: grafana/grafana
    container_name: PomelnikovSV-netology-grafana
    user: "472"
    depends_on:
      - prometheus
    ports:
      - 80:3000
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/custom.ini:/etc/grafana/custom.ini
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/custom.ini
    networks:
      - PomelnikovSV-my-netology-hw
    restart: always

  pushgateway:
    image: prom/pushgateway
    container_name: PomelnikovSV-netology-pushgateway
    restart: always
    expose:
      - 9091
    ports:
      - "9091:9091"
    networks:
      - PomelnikovSV-my-netology-hw

```
![start_docker](img/start_docker_compose.jpg)
![pushgateway](img/pushgateway.jpg)

### Задание 8

1. `Остановите и удалите все контейнеры одной командой.`

В качестве решения приложите скриншот консоли с проделанными действиями.

![stop_docker](img/stop_docker_compose.jpg)

### Задание 9

1. `Создайте конфигурацию docker-compose для Alertmanager с именем контейнера <ваши фамилия и инициалы>-netology-alertmanager.`
2. `Добавьте необходимые тома с данными и конфигурацией, сеть, режим и очередность запуска.`
3. `Обновите конфигурацию Prometheus (необходимые изменения ищите в презентации или документации) и перезапустите его.`

В качестве решения приложите скриншот с событием из Alertmanager.  

![alert](img/alert_z9.jpg)

### Задание 10

1. `Запустите свой сценарий на чистом железе без предзагруженных образов.`

   Опишите выполненный вами процесс развертывания сценария.  
   Как вы думаете зачем может понадобиться такой способ развертывания?  

Сценарий развертывания:  
  - Установка Docker и Docker Compose  
  - Создание конфигурационных файлов (prometheus.yml, alert.rules, alertmanager.yml, custom.ini)  
  - Создание файла docker-compose.yml  
  - Запуск контейнеров  
  - Проверка сервисов  
  - Проверка срабатывания оповещений  
   

Такой способ развертывания с использованием Docker Compose для Prometheus, Grafana и Alertmanager можно использовать для множества задач, связанных с мониторингом и управлением системами. Вот несколько примеров:

1. Мониторинг инфраструктуры
Использование Prometheus для мониторинга серверов, виртуальных машин, контейнеров и других компонентов инфраструктуры. Grafana используется для визуализации метрик, а Alertmanager для управления оповещениями и уведомлениями.

2. Мониторинг приложений
Сбор метрик с различных приложений и сервисов, таких как веб-серверы, базы данных и другие критически важные компоненты. Prometheus позволяет собирать метрики, а Grafana предоставляет удобный интерфейс для анализа и визуализации этих данных.

3. Настройка оповещений
Настройка и управление правилами оповещений для различных метрик. Например, вы можете настроить оповещения для уведомления команды о высоком использовании CPU, проблемах с доступностью сервисов, превышении лимитов памяти и других важных событиях.

4. Тестирование и разработка
Использование данного стека для разработки и тестирования приложений. Разработчики могут легко развернуть инфраструктуру мониторинга для своих приложений, чтобы отслеживать их поведение и производительность в реальном времени.

5. Обучение и демонстрации
Использование для обучения и демонстрации возможностей мониторинга и управления оповещениями. Легкость развертывания с помощью Docker Compose делает этот стек отличным выбором для проведения учебных курсов и семинаров.

6. Локальный мониторинг
Развертывание мониторинга на локальной машине для личного использования. Например, мониторинг домашнего сервера или маленькой лаборатории.


