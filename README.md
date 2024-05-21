# Дипломный проект.
SkillFactory / Group 45
<H1> Спринт 1. </H1>
<H2> Создать Kubernets Cluster </H2>


Опишите инфраструктуру будущего проекта в виде кода с инструкциями по развертке, нужен кластер Kubernetes и служебный сервер (будем называть его srv). <br>

<H3>Задание 1. Выбираем облачный провайдер и инфраструктуру. </h3>

- В качествет облачного провайдера был выбран <b> Yandex Cloud</b> <br>
- В качестве описания серверной инфраструктуры в облаке был выбран <b>Terraform</b> <br>
- В качестве K8S cluster был выбран <b>K3S</b> <br>
- Cluster K8S будет разворачиваться при помощи <b>Ansible</b> <br>


<H4> Установка и первоначальная настройка сервера <b> SRV </b> </H4>
<b>1. Устанавливаем в Yandex Cloud сервер SRV </b> <br>
Задача данного сервера: <br>
a) На данном сервере будем хранить весь наш проект и управлять им. <br>
b) Из данного сервера будем разворачивать <b> K8S cluster </b> управление будет происходить при помощи <b>Kubectl и Helm </b>.<br>
с) На данном сервере будет выполняться <b> CI/CD piplines </b>. <br>
d) Master и Worker ноды будем разворачивать в <b> Yandex Cloud</b> при помощи <b>Terraform .</b> <br>
e) Мониторинг SRV сервера и K8S кластера.<br>
h) Отправка оповещаний в telegram о доступности нашего приложения.  <br> 
<br>
<b> 2. Первоначальная настройка сервера SRV </b>.  <br>
a) Устанавливаем все нужные для достижения нашей цели приложения: <br>
   - Создаём ssh ключ в моём случаи <b> "~/.ssh/id_rsa.pub" </b> для terraform и ansible. 
   - Python3, PIP3, GIT, остальные зависимости.  
   - Устанавливаем Helm и Kubectl
   - Ansible
   - Устанавливаем <b> Terraform </b> по инструкции: <a href="https://cloud.yandex.ru/docs/tutorials/infrastructure-management/terraform-quickstart"> Инструкция по установке и настройке Terraform в Ya облаке</a>. <br>
b) Создаём папку для нашего проекта. <br>
c) Делаем git clone проекта в нашу папку. <br>
<code># git clone https://github.com/awerton89/dip_sf.git </code><br>
<br>
<b>Задание 3. Начинаем настройку и установку кластера K8S. </b><br> 
   a) Настраиваем Terraform. Переходим в папку с манифестом Terraform который разворачивает серверную инфраструктуру для нашего K8S кластера.<br>
<code># cd terraform </code> <br>
   - Переименовываем файл который будет хранить наши данные для подключения к Yandex Cloude: <br>
<code># cp private.auto.tfvars.example private.auto.tfvars </code> <br>
   - Открываем файл private.auto.tfvars и вносим наши данные для авторизации Yandex Cloude <br>
<code>#nano private.auto.tfvars </code> <br> <br>
<table> <tr><td>(Для информации какие данные нужно вносить)<br>
● yc_token – OAuth-токен для доступа к API  <br>
(https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token) <br>
● yc_cloud_id – ID облака (скопировать из консоли управления) <br>
● yc_folder_id – ID каталога (скопировать из консоли управления) <br>
</td></tr></table>
   - Terraform манифест называется <b> "k8s-cluster.tf" </b> он имеет код согласно задачи, для создания 2 нод кластера K8S Master и Worker. <br>
Но можно изменить настройки и увеличить количество нод, также добавить ingress ноды. Но согласно задачи нужны 2 ноды.<br>
   - Инициализируем Terraform команандой:
<code> terraform init </code> <br>
   - Если всё было правильно настроено, то инициализация должна пройти без ошибок.  <br>
b) Подготавливаем настройки для нашего K8S кластера который будет установлен на нодах. Все настройки хранятся в папке <b>/k3s_install</b>. <br>
   - Устанавливаем требуемые приложения: <br>
<code> # pip3 install -r kubespray/requirements.txt </code> <br>
с) Запускаем установку K8S кластера. <br>
- Yandex Cloud:
<img src="https://github.com/awerton89/dip_sf/blob/main/images/K8S_cluster.jpg">
<H3>4)Кластер установлен и настроен. Готов к деплою приложения. Спринт 1. закончен.</H3>
<h1> Спринт 2. </h1>
<H2> Цель: </H2>
Cобрать и задеплоить приложение из нашего Git в созданный кластер Kubernetes <br>
<H2> Задача: </H2> 
1) Клонируем репозиторий, собираем его на сервере srv.  <br>
2) Описываем приложение в Helm-чарт.  <br>
3) Описываем стадию деплоя в Helm. <br>

<H3>Задание 1. Клонируем репозиторий, собираем его на сервере srv.</H3>
- Kлонируем приложение в свой github.<br> 
- Исправляем все ошибки в Docker фалах приложения, настройках приложения и изменяем логин и пароль. <br>
- Чувствительные данные такие как логин и пароль и т.д вынес в отдельный файл "/data/app.var" и добавил в gitignor.
- Собираем Docker образ и запускаем приложение в Docker. <br>
<code># Docker-compose up -d </code> <br>
- В резальтате чего создаются 2 докер контенера, Python приложение и БД к нему <br>
- Проверяем приложение на SRV сервере:
<img src="https://github.com/awerton89/dip_sf/tree/main/images/Docker_App.JPG">
 - Подготавливаем CI/CD для автоматизации сборки образа, нашего приложения и деплоя его в Docker registry. <br>
 - В качестве Docker registry буду использовать Dockerhub. 
 - В качестве CI/CD будем использовать Github <br>
 - Создаём файл, для нашего Pipeline - <a href=https://github.com/awerton89/dip_sf/blob/main/workflows/proj.yaml </b></a> в котором будем описывать этапы сборки образа приложения, а в дальнейшем, деплоя в K8S кластер. <br>  
 - Создаём первую стадию Pipeline для приложения - build <br>
 - Создаём нужные нам переменные для хранения чувствительных данных и другой информации: REG_USER, REG_PASSWORD, TAG.
 - На первом этапе Pipeline должен, войти в DockerHub,на основании Docker файлов создать образ приложения и присвоить ему тэг из переменной "TAG" и запушить наше приложение с тэгом в DockerHub. <br>
 - Сдедующим шагом, создаём манифесты, для деплоя приложения в Kubespray, на основе Docker образов приложения: <br>
 - Деплоим приложение в K8S кластер
 - Результат деплоя: <br>
 <img src="https://github.com/awerton89/dip_sf/tree/main/images/Kube_app.JPG">
<H3>Задание 2. Описываем приложение в Helm-чарт.</H3>
- На основании написаных манифестов создаём <b>helm chart</b>.  <br>
- Путь к helm chart: https://github.com/awerton89/dip_sf/tree/main/chart <br>
-  Создаём <b> namespace </b> для нашего приложения - <b>"diplom" </b>: <br>
<code># kubectl create namespace diplom </code><br>
-  Деплоим наше Helm chart в K8S кластер, в созданый namespace для нашего приложения "diplom"
-  Результат, наше приложение работает в K8S кластере
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Kube_app.JPG">
<H3>Задание 3. Описываем стадию деплоя в Helm.</H3>
- Создаём вторую стадию Pipeline для приложения - <b>"deploy" </b>.<br>
- Стадия Deploy должна на основе Helm chart, деплоить приложение в K8S кластер. Тригером является изменение тэга: <br>
- Результаты работы CI/CD Pipeline: <br>
  Образы в Dockerhub:
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Docker_hub_tag.JPG ">  
  Присвоение тэгов образам:
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/CICD_Helm.JPG">
 Результат работы Pipeline:
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/CICD_deploy_app.JPG">
 <H3>Pipeline работает согласно задачи спринта. Спринт 2 закончен.</H3>
<h1> Спринт 3. </h1>
<H2> Цель: </H2>
Настройка мониторинга и логирования. <br>
<H2> Задача: </H2> 
1) Настройка сборки логов.  <br>
2) Выбор метрик для мониторинга.  <br>
3) Настройка дашборда. <br>
4) Алертинг.<br>
<br>
<H3>Задание 1. Настройка сборки логов. </H3>
- Для сборки логов буду использовать стэк <b> Fluentd/Clickhouse/Loghouse </b>. <br>
- Устанавливать буду с помощью helm. <br>
- Путь к настройкам стэка: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/loghouse <br>
- Вначале добавим repo для Loghouse <br>
<code> # helm repo add loghouse https://flant.github.io/loghouse/charts/  </code><br>
- Устанавливаем стэк <b> Fluentd/Clickhouse/Loghouse </b> в namespace "loghouse". <br>
<code> # helm install --namespace loghouse --create-namespace -f monitor/loghouse/loghouse-values.yml loghouse loghouse/loghouse </code> <br>
- Проверяем в K8S кластере: <br>
<code> root@diplom:/home/odmin# kubectl get pods -n loghouse <br>
NAME                                                              READY   STATUS      RESTARTS   AGE  <br>
clickhouse-server-0                                               2/2     Running     0          76m  <br>
fluentd-kltjb                                                     1/1     Running     0          76m  <br>
fluentd-npdxz                                                     1/1     Running     0          76m  <br>
loghouse-f7c55bbf7-9kl2j                                          2/2     Running     0          76m   <br>
loghouse-migrate-tables-e07085a0-f7f6-42ce-a5c4-cad5d5773e467qr   0/1     Completed   0          76m  </code><br>
- Создаём Dashbourd для сборки логов нашего приложения и кластера K8S <br>
- Результат:
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Loghouse1.JPG">
<H3>Задание 2. Выбор метрик для мониторинга. </H3>   
- Для мониторнига сервера SRV, доступности нашего приложения "APP-DEP" и кластера "K8S" буду использовать стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b><br>
- Весь стэк буду разворачивать на SRV сервере в Docker-compose. <br>
- Путь к манифестам: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/Prometheus_stack <br>
- Для уставноки нужно перейти в каталог <b>/monitor/Prometheus_stack </b> и развернуть стэк командой:  <br>
 <code># docker-compose up -d </code> <br>
- В результате действия команды в докере, должен быть развёрнут полный стэк <b>Grafana\Prometheus\Blackbox\Node Exporter\Alertmanager </b>:<br>   
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/grafana1.JPG">
- Так же были созданы K8S манифесты для деплоя Prometheus и сбора метрик кластера <br>
- Путь к манифестам: https://github.com/Suirus777/skillfactory-diplom/tree/main/monitor/Prometheus_stack/k8s  <br>
- Был создан namespace "monitoring" и в него задеплоин манифест: <br>
<code># kubectl create namespace monitoring <br>
   #Kubectl apply -f .  </code> <br>
- Результат:  <br>
<code> root@diplom:/home/odmin# kubectl get pods -n monitoring                 <br>
NAME                                     READY   STATUS    RESTARTS   AGE      <br>
   prometheus-deployment-599bbd9457-xmdmz   1/1     Running   0          113m    </code>  <br>
- Заходим в Grafana и подключаемся к Prometheus на сервере SRV и в кластере K8S:<br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/pom_grafana.JPG"><br>
<b>- В итоге в кластре K8S будут работать ПОДЫ :</b> <br> 
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/All_PODS_K8S.JPG">
<H3>Задание 3. Настройка дашборда. </H3>
- Для сборки метрик буду использовать "Grafana" <br>
- Буду использовать 3 дашборда: <br>
 <img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Dashboards.JPG"> <br>
- Сбор метрик состояния сервера SRV при помощи "Node Exporter":<br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Node_exporter.JPG"><br>
- Сбор метрик о доступности нашего сайта (app-dep) при помощи "BlackBox":  <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/HTTP_Satus_APP_Grafana.JPG"> <br>
- Сбор метрик о состоянии K8S кластера и на podа - APP-DEP: <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/K8S_cluster_grafana.JPG"><br>
<H3>Задание 4. Алертинг. </H3>
- Для отправки алертов буду использовать <b> Telegram </b>. <br>
- Создал в Telegram нового бота - <b> SkillFactory_diplom </b> . <br>
- Был установлен и настроен Docker контенер для отправки сообщений в Telegram. <br> 
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/telegrambot.JPG">
- Все чувствительные данные были вынесены в отдельный файл "/data/tele.var" и добавлен в .gitignor. <br>
- Добавляем Telegram Bot в стэк Prometheus\Grafana: https://github.com/Suirus777/skillfactory-diplom/blob/main/monitor/Prometheus_stack/docker-compose.yml <br> 
- Настройка отправки сообщений: https://github.com/Suirus777/skillfactory-diplom/blob/main/monitor/Prometheus_stack/alertmanager/alertmanager.yml <br>
- Результат работы алертинга, отправка алертов о статусе APP-DEP в telegram: <br>
<img src="https://github.com/Suirus777/skillfactory-diplom/blob/main/images/Telegram_alert.JPG"> 
<h2> Спринт 3. Закончен. </h2>
<h1> Все задания выполнены, диплом закончен! Заранее спасибо!!! </h1>
<a href="http://158.160.4.27"> Адресс работающего приложения!!!!!!!!!!! </a>
