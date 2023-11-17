# Управление ролями и пользователями

{{ GP }} управляет правами доступа к базе данных с помощью двух типов ролей:

* Пользователь — это роль, которая может авторизоваться в базе данных.
* Групповая роль — это роль, в состав которой входят другие роли.
​
​Подробнее см. в разделе [{#T}](../concepts/cluster-users.md).

Пользователь-администратор создается вместе с кластером {{ mgp-name }} и автоматически получает роль администратора `mdb_admin`. [Подключитесь к базе данных](connect.md) от его имени, чтобы:

* [Посмотреть список ролей](#list).
* [Создать роль](#create).
* [Настроить групповые роли](#group-roles).
* [Изменить атрибуты роли](#attributes).
* [Настроить привилегии роли](#privileges).
* [Удалить роль](#remove).

Подробнее о командах взаимодействия с ролями см. в [документации {{ GP }}]({{ gp.docs.vmware }}/6/greenplum-database/ref_guide-sql_commands-sql_ref.html).

## Посмотреть список ролей {#list}

{% list tabs %}

* SQL

    Выполните команду:

    ```sql
    SELECT rolname FROM pg_roles;
    ```

    Чтобы посмотреть список ролей с их привилегиями, выполните команду:

    ```sql
    SELECT
        rolname,
        rolsuper,
        rolcreatedb,
        rolcreaterole,
        rolinherit,
        rolcanlogin,
        rolconnlimit,
        rolcreaterextgpfd
    FROM pg_roles;
    ```

    Результат:

    ```text
        rolname     | rolsuper | rolcreatedb | rolcreaterole | rolinherit | rolcanlogin | rolconnlimit | rolcreaterextgpfd
    ----------------+----------+-------------+---------------+------------+-------------+--------------+-------------------
     mdb_admin      | f        | f           | f             | t          | f           |           -1 | f
     gpadmin        | t        | t           | t             | t          | t           |           -1 | t
     monitor        | t        | f           | f             | t          | t           |           -1 | f
     user1          | f        | t           | t             | t          | t           |           -1 | t
    ```

    Где:

    * **t** — наличие привилегии.
    * **f** — отсутствие привилегии.

{% endlist %}

## Создать роль {#create}

{% list tabs %}

* SQL

    Выполните команду:

    ```sql
    CREATE ROLE <имя_роли> <список_атрибутов>;
    ```

    Доступные атрибуты см. в разделе [{#T}](../concepts/cluster-users.md#attributes).

{% endlist %}

## Настроить групповые роли {#group-roles}

{% list tabs %}

* SQL

    Чтобы добавить роль в состав групповой роли, выполните команду:

    ```sql
    GRANT <имя_групповой_роли> TO <список_ролей_через_запятую>;
    ```

    Имена ролей можно получить со [списком ролей в кластере](#list).

    Атрибуты `LOGIN`, `SUPERUSER`, `CREATEDB`, `CREATEROLE`, `CREATEEXTTABLE`, и `RESOURCE QUEUE` не наследуются. Чтобы воспользоваться всеми атрибутами групповой роли, выполните от имени роли в составе команду:

    ```sql
    SET ROLE <имя_групповой_роли>;
    ```

    Чтобы удалить роль из состава групповой роли, выполните команду:

    ```sql
    REVOKE <имя_групповой_роли> FROM <список_ролей_через_запятую>;
    ```

{% endlist %}

## Изменить атрибуты роли {#attributes}

{% list tabs %}

* SQL

    Выполните команду:

    ```sql
    ALTER ROLE <имя_роли> <список_атрибутов>;
    ```

    Доступные атрибуты см. в разделе [{#T}](../concepts/cluster-users.md#attributes).

{% endlist %}

## Настроить привилегии роли {#privileges}

{% list tabs %}

* SQL

    Чтобы выдать привилегии роли, выполните команду:

    ```sql
    GRANT <список_привилегий_через_запятую> ON <имя_объекта> TO <имя_роли>;
    ```

    Доступные привилегии см. в разделе [{#T}](../concepts/cluster-users.md#privileges).

    Чтобы отозвать привилегии у роли, выполните команду:

    ```sql
    REVOKE <список_привилегий_через_запятую> ON <имя_объекта> FROM <имя_роли>;
    ```

    Вместо перечисления всех привилегий объекта используйте `ALL PRIVILEGES`.

    Чтобы отозвать все привилегии всех объектов роли, выполните команду:

    ```sql
    DROP OWNED BY <имя_роли>;
    ```

{% endlist %}

## Удалить роль {#remove}

Перед удалением роли удалите все объекты, которыми она владеет, или переназначьте их права собственности и отзовите все привилегии для других объектов.

{% list tabs %}

* SQL

    Выполните команду:

    ```sql
    DROP ROLE <имя_роли>;
    ```

{% endlist %}

## Примеры

### Создание роли

Создайте роль с тестовыми характеристиками:

* С именем `greenplum_user`.
* С атрибутами `CREATEDB` и `CREATEROLE`.
* С возможностью авторизации в системе с защищенным паролем `password123`.

Выполните команду:

```sql
CREATE ROLE greenplum_user CREATEDB CREATEROLE LOGIN ENCRYPTED PASSWORD 'password123';
```

Проверьте, что новая роль с заданными атрибутами появилась в списке:

```sql
SELECT
    rolname,
    rolsuper,
    rolcreatedb,
    rolcreaterole,
    rolinherit,
    rolcanlogin,
    rolconnlimit,
    rolcreaterextgpfd
FROM pg_roles;
```

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}
