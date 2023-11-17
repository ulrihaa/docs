Чтобы создать правило:

1. Включите опцию **{{ ui-key.yacloud.storage.bucket.lifecycle.field_status }}**. При помощи этой опции вы можете включить или выключить правило, не удаляя его из конфигурации.
1. Заполните поля:
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_description }}** — опишите правило в произвольной форме.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_prefix }}** — часть [ключа](../concepts/object.md#key) объекта необходимой длины, взятая от его начала. По префиксу отбираются объекты, попадающие под действие правила. Если под действие правила должны попадать все объекты, то отметьте **{{ ui-key.yacloud.storage.bucket.lifecycle.field_no-prefix }}**.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_max-size }}** — правило срабатывает для всех объектов, размер которых меньше либо равен указанному.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_min-size }}** — правило срабатывает для всех объектов, размер которых больше либо равен указанному.
1. Выберите и настройте нужные типы действий, выполняемые с объектами при срабатывании правила:
   * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_type-expiration }}` — удаление любых объектов из бакета:

     * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expiration-days }}` — срабатывает через указанное в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** количество дней после загрузки объекта.
     * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expiration-date }}` — срабатывает в дату, указанную в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_date }}**.
     * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expired-object-delete-marker }}` — срабатывает после того, как у объекта осталась только текущая версия.

   * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_type-transition }}` — перемещение любых объектов из стандартного (`STANDARD`) в холодное (`COLD`), из холодного в ледяное (`ICE`) или из стандартного в ледяное хранилище:

     * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expiration-days }}` — срабатывает через указанное в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** количество дней после загрузки объекта.
     * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expiration-date }}` — срабатывает в дату, указанную в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_date }}**.

   * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_type-version-expiration }}` — удаление нетекущих версий объектов из бакета. Срабатывает через указанное в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** количество дней после того, как версия объекта стала нетекущей.
   * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_type-version-transition }}` — перемещение нетекущих версий объектов из стандартного (`STANDARD`) в холодное (`COLD`) хранилище. Срабатывает через указанное в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** количество дней после того, как версия объекта стала нетекущей.
   * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_type-abort-incomplete-multipart-upload }}` — удаление всех частей незавершенных составных загрузок из бакета. Срабатывает через указанное в поле **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** количество дней после загрузки объекта.

1. Нажмите **{{ ui-key.yacloud.storage.bucket.lifecycle.button_save }}**.

Вы можете добавить одновременно несколько правил. Чтобы добавить новое правило нажмите **{{ ui-key.yacloud.storage.bucket.lifecycle.label_add-lifecycle-settings }}** и повторите шаги инструкции, указанные выше.