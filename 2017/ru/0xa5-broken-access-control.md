# A5:2017 Недостатки контроля доступа

| Источники угроз/Векторы атак | Недостатки безопасности  | Последствия |
| -- | -- | -- |
| Зависит от прил. : Сложность эксплуатации 2 | Распространенность 2 : Сложность обнаружения 2 | Технические 3 : Для бизнеса ? |
| Эксплуатация контроля доступа является основным навыком злоумышленников. Инструменты [SAST](https://wiki.owasp.org/index.php/Source_Code_Analysis_Tools) и [DAST](https://wiki.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) могут обнаружить отсутствие контроля доступа, но не могут проверить его работоспособность при его наличии. Наличие контроля доступа можно обнаружить вручную, а его отсутствие можно обнаружить автоматически в некоторых фреймворках.| Уязвимости, связанные с контролем доступа, довольно распространены из-за отсутствия автоматического обнаружения и эффективного функционального тестирования разработчиками. Контроль доступа обычно не проверяется автоматическими статическими или динамическими тестами. Тестирование вручную — наилучший способ обнаружения отсутствия или неэффективности контроля доступа, включая методы HTTP (GET, PUT и т. п.), контроллеры, прямые ссылки на объекты и т. д. | Технические последствия: выполнение злоумышленником действий с правами пользователя или администратора; использование пользователем привилегированных функций; создание, просмотр, обновление или удаление любых записей. Последствия для бизнеса зависят от требований к защите приложения и данных. |

## Является ли приложение уязвимым?

Контроль доступа предполагает наличие политики, определяющей права пользователей. Обход ограничений доступа обычно приводит к несанкционированному разглашению, изменению или уничтожению данных, а также выполнению непредусмотренных полномочиями бизнес-функций. Наиболее распространенные уязвимости контроля доступа включают:

* обход ограничений доступа путем изменения URL, внутреннего состояния приложения или HTML-страницы, а также с помощью специально разработанных API;
* возможность изменения первичного ключа для доступа к записям других пользователей, включая просмотр или редактирование чужой учетной записи;
* повышение привилегий. Выполнение операций с правами пользователя, не входя в систему, или с правами администратора, войдя в систему с правами пользователя;
* манипуляции с метаданными, например повторное воспроизведение или подмена токенов контроля доступа JWT или куки-файлов, а также изменение скрытых полей для повышения привилегий или некорректное аннулирование JWT;
* несанкционированный доступ к API из-за некорректной настройки междоменного использования ресурсов (CORS);
* доступ неаутентифицированных пользователей к страницам, требующим аутентификации, или доступ непривилегированных пользователей к привилегированным страницам. Доступ к API с отсутствующим контролем привилегий для POST-, PUT- и DELETE-методов/запросов.

## Как предотвратить

Контроль доступа эффективен только при реализации через проверенный код на стороне сервера или беcсерверный API, где атакующий не может изменять проверки прав доступа или метаданные. Рекомендуется:

* запрещать доступ по умолчанию, за исключением открытых ресурсов;
* реализовать механизмы контроля доступа и использовать их во всех приложениях, а также минимизировать междоменное использование ресурсов;
* контролировать доступ к моделям, используя владение записями, а не возможность пользователей создавать, просматривать, обновлять или удалять любые записи;
* использовать модели доменов для реализации специальных ограничений, относящихся к приложениям;
* отключить вывод списка каталогов веб-сервера, а также обеспечить отсутствие метаданных файлов (например, .git) и файлов резервных копий в корневых веб-каталогах;
* регистрировать сбои контроля доступа и уведомлять администраторов при необходимости (например, если сбои повторяются);
* ограничивать частоту доступа к API и контроллерам для минимизации ущерба от инструментов автоматизации атак;
* аннулировать токены JWT на сервере после выхода из системы.

Разработчики и инженеры по контролю качества ПО должны проводить функциональную проверку контроля доступа и тестировать интеграцию.

## Примеры сценариев атак

**Сценарий №1**: Приложение использует непроверенные данные в SQL-вызове, который обращается к информации об учетной записи:

```
  pstmt.setString(1, request.getParameter("acct"));
  ResultSet results = pstmt.executeQuery();
```

Злоумышленник изменяет в браузере параметр 'acct' для отправки желаемого номера учетной записи. Без должной проверки атакующий может получить доступ к учетной записи любого пользователя.

`http://example.com/app/accountInfo?acct=notmyacct`

**Сценарий №2**: Злоумышленник задает в браузере целевой URL. Для доступа к странице администрирования требуются права администратора.

```
  http://example.com/app/getappInfo
  http://example.com/app/admin_getappInfo
```

Уязвимость существует, если пользователь без аутентификации может получить доступ к этим страницам или если пользователь без прав администратора может получить доступ к странице администрирования.

## Ссылки

### OWASP

* [Проактивная защита OWASP: Контроль доступа](https://wiki.owasp.org/index.php/OWASP_Proactive_Controls#6:_Implement_Access_Controls)
* [Стандарт подтверждения безопасности приложений OWASP: V4 Контроль доступа](https://wiki.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab=Home)
* [Руководство OWASP по тестированию: Проверка авторизации](https://wiki.owasp.org/index.php/Testing_for_Authorization)
* [Памятка OWASP: Контроль доступа](https://wiki.owasp.org/index.php/Access_Control_Cheat_Sheet)

### Сторонние

* [CWE-22: Некорректные ограничения путей для каталогов (Подмена пути)](https://cwe.mitre.org/data/definitions/22.html)
* [CWE-284: Некорректное управление доступом (Авторизация)](https://cwe.mitre.org/data/definitions/284.html)
* [CWE-285: Некорректная авторизация](https://cwe.mitre.org/data/definitions/285.html)
* [CWE-639: Обход авторизации, используя значение ключа пользователя](https://cwe.mitre.org/data/definitions/639.html)
* [PortSwigger: Эксплуатация некорректно настроенного междоменного использования ресурсов](https://portswigger.net/blog/exploiting-cors-misconfigurations-for-bitcoins-and-bounties)