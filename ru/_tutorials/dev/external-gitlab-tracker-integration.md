# Интеграция {{ GL }} с {{ tracker-full-name }}

В [задачах {{ tracker-name }}](../../tracker/about-tracker.md#zadacha) можно автоматически добавлять ссылки на [Merge Requests]({{ gl.docs }}/ee/user/project/merge_requests/) из {{ GL }}, указывая [ключ](../../tracker/glossary.md#key) нужной задачи в названии или описании нового Merge Request. Ссылки будут размещаться в разделе [{{ ui-key.startrek.ui_components_issue-links_ExternalLinksCollapse.external-relations }}](../../tracker/external-links.md).

Дополнительно можно включить автоматическое создание комментариев в задаче с информацией о Merge Requests. Автоматическое комментирование доступно только для [{{ mgl-name }}](../../managed-gitlab/).

Чтобы настроить интеграцию с {{ GL }} в {{ tracker-name }}:

1. [Подключите {{ tracker-full-name }}](#enable-tracker).
1. [Создайте инстанс {{ GL }}](#create-gitlab).
1. [(Опционально) Создайте проект {{ GL }}](#create-project).
1. [Создайте хук для интеграции](#create-hook).
1. [Протестируйте хук](#test-hook).
1. [Проверьте результат](#check-result).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).

## Перед началом работы {#before-you-begin}

{% include [before-you-begin](../_tutorials_includes/before-you-begin.md) %}

## Подключите {{ tracker-full-name }} {#enable-tracker}

[Подключите {{ tracker-name }}](../../tracker/enable-tracker.md) к вашей [организации {{ yandex-cloud }}](../../organization/).

## Создайте инстанс {{ GL }} {#create-gitlab}

Создайте [инстанс {{ mgl-name }}](../../managed-gitlab/concepts/index.md#instance) или [виртуальную машину](../../compute/concepts/vm.md) с [образом](../../compute/concepts/image.md) {{ GL }}.

{% list tabs %}


- Инстанс {{ mgl-name }}

    Создайте инстанс {{ mgl-name }} [согласно инструкции](../../managed-gitlab/quickstart.md#instance-create).


- ВМ с образом {{ GL }}

    Запустите {{ GL }} на ВМ с [публичным IP-адресом](../../vpc/concepts/address.md#public-addresses).

    {% include [create-gitlab](../../_includes/managed-gitlab/create.md) %}

{% endlist %}

## (Опционально) Создайте проект {{ GL }} {#create-project}

Чтобы настроить интеграцию для отдельного проекта {{ GL }}, создайте его.

{% list tabs %}

- Инстанс {{ mgl-name }}

    1. Авторизуйтесь в веб-интерфейсе [инстанса {{ mgl-name }}](../../managed-gitlab/concepts/index.md#instance).
    1. Нажмите кнопку **Create a project**.
    1. Нажмите кнопку **Create blank project**.
    1. Заполните поля:
        * **Project name** — укажите имя проекта.
        * **Project URL** — выберите пользователя-администратора в поле рядом с FQDN инстанса {{ mgl-name }}.

        Остальные поля оставьте без изменений.
    1. Нажмите кнопку **Create project**.

- ВМ с образом {{ GL }}

    1. На странице сервиса {{ compute-full-name }} выберите созданную [ВМ](../../compute/concepts/vm.md) и скопируйте ее [публичный IP-адрес](../../vpc/concepts/address.md#public-addresses).
    1. [Подключитесь](../../compute/operations/vm-connect/ssh.md) к ВМ по протоколу SSH.
    1. Получите пароль администратора {{ GL }} с помощью команды ВМ:

        ```bash
        sudo cat /etc/gitlab/initial_root_password
        ```

    1. Скопируйте пароль из строки `Password` (исключая пробелы) в буфер обмена или отдельный файл.
    1. Откройте в браузере ссылку `http://<публичный_IP-адрес_ВМ>`. Откроется веб-интерфейс {{ GL }}.
    1. Войдите в систему с учетной записью администратора:
        * **Username or email** — `root`.
        * **Password** — пароль, скопированный ранее.

        Если вы не можете войти, [сбросьте пароль учетной записи администратора]({{ gl.docs }}/ee/security/reset_user_password.html#reset-your-root-password).
    1. [Смените пароль учетной записи администратора]({{ gl.docs }}/ee/user/profile/user_passwords.html#change-your-password).
    1. Повторно войдите в систему с учетной записью администратора, используя новый пароль.
    1. Выберите **Create a project**.
    1. Укажите имя проекта.
    1. Нажмите кнопку **Create project**.

{% endlist %}

## Создайте хук для интеграции {#create-hook}

{% include notitle [create hook](../../_includes/managed-gitlab/create-hook.md) %}

## Протестируйте хук {#test-hook}

{% include notitle [test hook](../../_includes/managed-gitlab/test-hook.md) %}

## Проверьте результат {#check-result}

1. [Создайте задачу](../../tracker/user/create-ticket.md#create-task) в {{ tracker-name }}.
1. [Создайте Merge Request]({{ gl.docs }}/ee/user/project/merge_requests/creating_merge_requests.html) в {{ GL }} и укажите в его названии или описании [ключ задачи](../../tracker/glossary.md#key).
1. Убедитесь, что в задаче {{ tracker-name }} в разделе [{{ ui-key.startrek.ui_components_issue-links_ExternalLinksCollapse.external-relations}}](../../tracker/external-links.md) появилась ссылка на Merge Request из {{ GL }}.

    Если вы использовали {{ mgl-name }} и включили автоматическое комментирование при [создании хука](#create-hook), то в задаче появится комментарий со ссылкой на Merge Request и информацией о нем.

## Удалите созданные ресурсы {#clear-out}

Удалите ресурсы, которые вы больше не будете использовать, чтобы за них не списывалась плата:

1. [Удалите инстанс {{ mgl-name }}](../../managed-gitlab/operations/instance/instance-delete.md) или [созданную ВМ с образом {{ GL }}](../../compute/operations/vm-control/vm-delete.md).
1. Если вы зарезервировали публичный статический IP-адрес для ВМ, освободите и [удалите его](../../vpc/operations/address-delete.md).
