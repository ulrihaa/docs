# Управление потоками данных

Вы можете [посмотреть](#list-data-streams) список [потоков](../concepts/glossary.md#stream-concepts), [изменить](#edit-data-stream) их настройки, [создать](#create-data-stream) новый поток или [удалить](#delete-data-stream) существующий.

## Создать поток данных {#create-data-stream}

{% include [create-stream-via-console](../../_includes/data-streams/create-stream-via-console.md) %}

## Посмотреть список потоков {#list-data-streams}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), для которого хотите получить список потоков данных.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-streams }}**. Откроется список потоков.

{% endlist %}

## Изменить настройки потока {#edit-data-stream}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором хотите изменить настройки потока данных.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-streams }}**.
  1. Нажмите значок ![horizontal-ellipsis](../../_assets/console-icons/ellipsis.svg) в строке нужного потока данных и выберите **{{ ui-key.yacloud.common.edit }}**.
  1. Настройте параметры потока данных:
     1. Выберите необходимое число сегментов.
     1. Задайте максимальную пропускную способность сегмента в секунду.
     1. Выберите [режим тарификации](../../data-streams/pricing.md).
     1. Выберите режим хранения данных:
        * `{{ ui-key.yacloud.data-streams.label_data-storage-size-limit }}` — укажите максимальный объем хранения данных.
        * `{{ ui-key.yacloud.data-streams.label_data-storage-time-limit }}` — укажите максимальное [время хранения данных](../../data-streams/concepts/glossary.md#retention-time) в потоке.
  1. Нажмите кнопку **{{ ui-key.yacloud.data-streams.button_update-stream }}**.

{% endlist %}

## Удалить поток {#delete-data-stream}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором хотите удалить поток данных.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-streams }}**.
  1. Нажмите значок ![horizontal-ellipsis](../../_assets/console-icons/ellipsis.svg) в строке нужного потока данных и выберите пункт **{{ ui-key.yacloud.common.delete }}**.
  1. Подтвердите удаление.

{% endlist %}