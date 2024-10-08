# OWASP Top 10

OWASP (Open Web Application Security Project) - это международная некоммерческая организация, сосредоточенная 
на анализе и улучшении безопасности программного обеспечения, а также на классификации векторов атак и уязвимостей.

OWASP Top 10 - список из 10-и самых опасных векторов атак на Web-приложения

## 1. Инъекции — Injections

SQL-инъекция (SQLi) - это уязвимость веб-безопасности, которая позволяет злоумышленнику вмешиваться в запросы, 
которые приложение делает к своей базе данных. 

Эта уязвимость является следствием недостаточной проверки данных, поступающих от пользователя. Это позволяет 
злоумышленнику «подсунуть», например, в веб-формы, специально подготовленные запросы, которые «обманут» приложение 
и позволят прочитать или записать нелегитимные данные.

Например, если ввести имя пользователя `administrator'--` и пустой пароль, то получится следующий запрос:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

Этот запрос возвращает пользователя, чье имя пользователя - `administrator`, и успешно регистрирует атакующего 
как этого пользователя.

## 2. Недочеты системы аутентификации и хранения сессий (Broken Authentication and Session Management)

В случае, если, например, токен украдет злоумышленник, а в системе не были реализованы проверки, скажем, 
IP-адреса сессии, или проверки наличия более одного соединения в одной сессии, злоумышленник сможет получить 
доступ в систему с правами аккаунта пользователя. 

## 3. Межсайтовый скриптинг – XSS (Cross Site Scripting)

Аббревиатура XSS расшифровывается как Cross-Site Scripting (межсайтовый скриптинг). Если особо не погружаться 
в детали, смысл атаки заключается во внедрении вредоносного кода в страницу. Атака не затрагивает серверную часть, 
но напрямую влияет на клиентскую — на пользователей уязвимого сервиса.

Для выполнения JavaScript-кода на странице (в контексте фронтенда) необходимо разместить его между тегами 
<script></script>. Атака XSS в этом и заключается. Злоумышленник внедряет в страницу зловредный JavaScript-код. 
Пользователь переходит на эту страницу, код выполняется и конфиденциальные данные пользователя отправляются 
злоумышленнику.

Пример:

Представьте персональный блог, в котором все зарегистрированные пользователи могут оставлять комментарии к публикациям.

Всё работает прекрасно, пока не появляется пользователь, который хочет не просто обсуждать публикации, а украсть 
доступ к учётным записям других пользователей блога. Для этого злоумышленник в поле комментарий пишет какой-то текст, 
тег <script>, а в нём немного JavaScript-кода. Если не происходит никакой фильтрации, то после публикации такого 
комментария, код будет выполняться у всех пользователей, посетивших страницу.

А что опасного может быть в этом коде? Часто разработчики используют cookie для хранения идентификатора сессии 
пользователя. Что такое идентификатор? Это строка с уникальным набором символов, позволяющих отличить одного 
авторизованного пользователя от другого.

Зловредный код может прочитать это значение и передать на сервер злоумышленника при помощи AJAX-запроса. В этом и 
заключается смысл XSS-атаки. Собрав такие идентификаторы, злоумышленник может подставить их себе. Если разработчик 
не предусмотрел дополнительной защиты, то злоумышленник сможет войти в приложение под учётной записью пользователя.

## 4. Небезопасные прямые ссылки на объекты (Insecure Direct Object References)

Суть уязвимости заключается в том, что при выводе каких-либо конфиденциальных данных, например личных сообщений 
или учетных карточек клиентов, для доступа к объекту используется идентификатор, который передается в открытом 
виде в адресной строке браузера, и не реализована проверка прав доступа к объектам. Например, есть страница, 
которая отображает личное сообщение и она имеет адрес вида:

`mysite.ru/read_message.jsp?id=123654`

Перебирая число после "id=" можно будет читать чужие личные сообщения.

## 5. Небезопасная конфигурация (Security Misconfiguration)

Это использование дефолтной конфигурации всех компонентов инфраструктуры: компонентов приложения, веб-сервера, 
сервера баз данных и самой платформы. Настройки компонентов сервера по-умолчанию зачастую небезопасны и открывают 
возможности к атакам. Например, кража сессионной cookie через JavaScript при XSS-атаке становится возможна 
благодаря выключенной по-умолчанию настройке cookie_httponly.

Программное обеспечение должно быть в актуальном состоянии: уязвимости находят каждый день в самых различных 
программных компонентах – операционной системе, web-серверах, серверах баз данных, почтовых серверах и т.д.

## 6. Незащищенность критичных данных (Sensitive Data Exposure)

Самый простой пример – передача данных по протоколу HTTP. Дело в том, что данные передаваемые по протоколу HTTP 
никак не шифруются, а при прохождении данных от компьютера пользователя до Web-сервера, данные пройдут достаточно 
много различных узлов: маршрутизатор офиса или домашний роутер, маршрутизатор провайдера, маршрутизатор на канале, 
маршрутизатор в дата-центре хостинг-провайдера сервера и так далее. На каждом из этих узлов может затаиться зловред, 
так называемый сниффер, программа, которая считывает весь трафик и передает злоумышленнику. А последний просматривает 
полученные данные на предмет персональных данных и данных кредитных карт.

## 7. Отсутствие функций контроля доступа (Missing Function Level Access Control)

Суть уязвимости, как следует из названия, заключается в отсутствии проверки наличия надлежащего доступа к 
запрашиваемому объекту.

## 8. Межсайтовая подделка запроса (Cross-Site Request Forgery, CSRF/XSRF)

## 9. Использование компонентов с известными уязвимостями (Using Components with Known Vulnerabilities)

Уязвимости в программных компонентах: библиотеках, фреймворках, СУБД, веб-сервера, ОС.

## 10. Непроверенные переадресации и пересылки (Unvalidated Redirects and Forwards)

Без соответствующих проверок, атакующий может использовать такие страницы для переадресации жертвы на подложный сайт, 
который, к примеру, может иметь очень схожий или неотличимый интерфейс, но украдет ваши данные кредитной карты или 
другие критичные конфиденциальные данные.

## Источники

- [Top 10 Web Application Security Risks](https://owasp.org/www-project-top-ten)
- [OWASP TOP-10: практический взгляд на безопасность веб-приложений](https://habr.com/ru/companies/simplepay/articles/258499/)
- [SQL-инъекции для самых маленьких](https://habr.com/ru/articles/725134)
- [XSS-уязвимости и как их избежать](https://htmlacademy.ru/blog/js/xss)
- [HTTP-куки](https://developer.mozilla.org/ru/docs/Web/HTTP/Cookies)
