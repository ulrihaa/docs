# Балансировщик нагрузки

Балансировщик нагрузки служит для приема входящего трафика и передачи его на эндпоинты бэкендов, заданные в [целевых группах](target-group.md). Маршрутизация запросов происходит по правилам, описанным в [HTTP-роутерах](http-router.md), подключенных к [обработчикам](#listener.md) балансировщика. Настройки подачи трафика на бэкенды конфигурируются в [группах бэкендов](backend-group.md), который создаются поверх целевых групп.

Балансировщик хранит список эндпоинтов, куда должен направляться поступающий трафик и снимает TLS-шифрование перед отправкой трафика на бэкенды. При работе с TLS балансировщик использует современные протоколы (TLSv1.2, TLSv1.3) и шифры. Если балансировщик нагрузки будет обслуживать несколько доменов, можно сконфигурировать разные сертификаты и разные [HTTP-роутеры](http-router.md) для разных доменов, используя механизм TLS SNI.

Для удобства и безопасности можно использовать балансировщик вместе с сервисом {{ certificate-manager-full-name }} для хранения ваших TLS-сертификатов. Также можно задействовать сервисы {{ monitoring-full-name }} для мониторинга обработки запросов.

## Расположение балансировщика {#lb-location}

При создании балансировщика указываются [сеть](../../vpc/concepts/network.md) и [подсети](../../vpc/concepts/network.md#subnet) в трех [зонах доступности](../../overview/concepts/geo-scope.md). В этих подсетях будут располагаться узлы балансировщика нагрузки. Трафик на бэкенды приложения будет поступать от узлов балансировщика в этих подсетях. Если ВМ бэкендов входят в [группы безопасности](../../vpc/concepts/security-groups.md), то следует разрешить получение трафика из этих подсетей на портах ВМ, где приложение обслуживает трафик.

Балансировщик можно отключить в выбранных зонах доступности. В этом случае внешний трафик перестанет поступать на узлы балансировщика в этих зонах доступности. При этом узлы балансировщика в других зонах продолжат подавать трафик на бэкенды в зонах, где балансировщик был отключен, если настройки [локализации трафика](backend-group.md#locality) позволяют это.

## Обработчик {#listener}

Обработчик определяет, на каких портах, адресах и по каким протоколам балансировщик будет принимать трафик. Обработчик передает трафик на бэкенды приложения согласно правилами маршрутизации, указанным в [HTTP-роутере](http-router.md). Один балансировщик может обслуживать и обычный, и шифрованный трафик на разных портах, а также иметь публичные и внутренние IP-адреса на разных обработчиках.

### Пример {#listener-example}

Обработчик может описывать прием HTTP-трафика на 80-м порту и делать перенаправление на 443 порт и протокол HTTPS. Обработчик получит HTTP-запрос от клиент и вернет ответ с HTTP-кодом 302. Дальнейшие запросы будут поступать уже на порт 443 по протоколу HTTPS. 

Если используется HTTPS-обработчик, то необходимо указать [сертификат](../../certificate-manager/concepts/imported-certificate.md) из {{ certificate-manager-name }} который будет использоваться для терминирования TLS.