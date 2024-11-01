# Домашнее задание к занятию «Конфигурация приложений»

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба конейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Пишу Deployment приложения, состоящего из контейнеров nginx и multitool.

Применяю манифест Deployment и вижу, что один из контейнеров пода не запустился:

![image](https://github.com/user-attachments/assets/8f798135-be33-4497-823e-4c38290f2c20)

2. Причина заключается в том, что стандартный порт, который используется в multitool уже занят, поэтому нужно использовать альтернативный порт. Этот альтернативный порт укажу в виде переменной `HTTP_PORT` в ConfigMap и укажу значение переменной равное `1180`.

3. Применю манифест ConfigMap и проверю изменилось ли состояние контейнера multitool в созданном поде:

![image](https://github.com/user-attachments/assets/2c9145da-0e2a-4ec9-bf8c-709e9864683b)

Контейнер запустился, так как проблемная ситуация с портом контейнера была устранена.

4. Сделаю простую веб-страницу и подключу её к Nginx с помощью ConfigMap. Для этого модернизирую Deployment, добавив в него volumeMounts ссылающийся на путь по умолчанию для nginx, где находится индексная страница - /usr/share/nginx/html/, а также сошлюсь на сам ConfigMap. Подключаю Service и применяю ConfigMap.

Проверю, покажет ли nginx из контейнера пода созданную простую индексную страницу и из браузера:

![image](https://github.com/user-attachments/assets/e152d3a8-f1c0-4ed0-b3cc-6b06cd651eda)
![image](https://github.com/user-attachments/assets/8dd412b1-57e7-4416-ba8b-c4f72be7d03e)

Текст индексной страницы, написанной мной в ConfigMap и текст индексной страницы из контейнера пода одинаковы, следовательно она взята именно из содержимого ConfigMap.

5. Ссылка на манифест [Deployment](https://github.com/PatKolzin/kuber-2.3/blob/main/src/deployment.yaml)

   Ссылка на манифест [Service](https://github.com/PatKolzin/kuber-2.3/blob/main/src/service.yaml)
   
   Ссылка на манифест [ConfigMap](https://github.com/PatKolzin/kuber-2.3/blob/main/src/configmap.yaml)

   ------

### 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Пишу манифест Deployment приложения, состоящего из Nginx

Манифест похож на тот, что использовался в первом задании. Также используется volumeMounts, указывающий на расположение стандартной индексной страницы nginx.

2. Создаю собственную веб-страницу и подключаю её как ConfigMap к приложению. В Deployment ссылаюсь на имя, указанное в ConfigMap.

3. Выпускаю самоподписной сертификат SSL и создаю Secret для использования сертификата:

![image](https://github.com/user-attachments/assets/2823fade-ea6c-4e40-b288-1ae48ba8ccdc)

4. Создаю Ingress и необходимый Service, подключаю к нему SSL. Добавляю в /etc/hosts адрес сайта. Проверяю доступ к сайту по HTTPS:

![image](https://github.com/user-attachments/assets/85d80e90-0554-4d73-9c92-eca1bdf3f8d9)

![image](https://github.com/user-attachments/assets/0a6e016b-56fa-485d-b892-e578042e98b8)

Сайт открывается, текст страницы взят из ConfigMap. Сертификат сайта подключен:

![image](https://github.com/user-attachments/assets/0ec26aef-2bf7-4bdf-b8e0-b19ff7ba19a0)

5. Ссылка на манифест [Deployment](https://github.com/PatKolzin/kuber-2.3/blob/main/src/deployment-nginx.yaml)

   Ссылка на манифест [Service](https://github.com/PatKolzin/kuber-2.3/blob/main/src/service-nginx.yaml)
   
   Ссылка на манифест [ConfigMap](https://github.com/PatKolzin/kuber-2.3/blob/main/src/configmap-nginx.yaml)

   Ссылка на манифест [Ingress](https://github.com/PatKolzin/kuber-2.3/blob/main/src/ingress-nginx.yaml)
