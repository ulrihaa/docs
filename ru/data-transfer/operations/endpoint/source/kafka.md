---
title: "Как настроить эндпоинт-источник {{ KF }} в {{ data-transfer-full-name }}"
description: "Из статьи вы узнаете, как задать настройки при создании или изменении эндпоинта-источника {{ KF }} в {{ data-transfer-full-name }}."
---

# Настройка эндпоинта-источника {{ KF }}

При [создании](../index.md#create) или [изменении](../index.md#update) эндпоинта вы можете задать:

* Настройки подключения к [кластеру {{ mkf-full-name }}](#managed-service) или [пользовательской инсталляции](#on-premise), в т. ч. на базе виртуальных машин {{ compute-full-name }}. Эти параметры обязательные.
* [Дополнительные параметры](#additional-settings).

## Кластер {{ mkf-name }} {#managed-service}


{% note warning %}

Для создания или редактирования эндпоинта управляемой базы данных вам потребуется [роль `{{ roles.mkf.viewer }}`](../../../../managed-kafka/security/index.md#mkf-viewer) или примитивная [роль `viewer`](../../../../iam/concepts/access-control/roles.md#viewer), выданная на каталог кластера этой управляемой базы данных.

{% endnote %}


Подключение с указанием идентификатора кластера в {{ yandex-cloud }}.

{% list tabs %}

- Консоль управления

    {% include [Managed Kafka UI](../../../../_includes/data-transfer/necessary-settings/ui/managed-kafka.md) %}

{% endlist %}

## Пользовательская инсталляция {#on-premise}

Подключение к кластеру {{ KF }} с явным указанием сетевых адресов и портов хостов-брокеров.

{% list tabs %}

- Консоль управления

    {% include [On premise Kafka UI](../../../../_includes/data-transfer/necessary-settings/ui/on-premise-kafka.md) %}

{% endlist %}

## Дополнительные параметры {#additional-settings}

{% include [Note processing order](../../../../_includes/data-transfer/notes/kafka-yds-evhub-processing-order.md) %}

{% list tabs %}

- Консоль управления

    
    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaSourceAdvancedSettings.transformer.title }}**

        Правила, по которым входящий поток обрабатывается с помощью функции {{ sf-name }}:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.cloud_function.title }}** — выберите одну из функций, созданных в сервисе [{{ sf-full-name }}](../../../../functions/).

            
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.service_account_id.title }}** — выберите или [создайте](../../../../iam/operations/sa/create.md) сервисный аккаунт, от имени которого будет запускаться функция обработки.


        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.number_of_retries.title }}** — укажите количество попыток вызова функции обработки.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_size.title }}** — укажите размер буфера (в байтах), при заполнении которого данные будут переданы функции обработки.

            Максимальный размер буфера — 3,5 МБ. Подробнее об ограничениях, действующих при работе с функциями в сервисе {{ sf-name }}, читайте в [соответствующем разделе](../../../../functions/concepts/limits.md).

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_flush_interval.title }}** — укажите длительность интервала (в секундах), по истечении которого данные из потока должны быть переданы функции обработки.

            {% note info %}

            Если буфер заполнится или истечет интервал отправки, данные будут переданы функции обработки.

            {% endnote %}

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.invocation_timeout.title }}** — укажите допустимое время ожидания ответа от функции обработки (в секундах).

        {% note warning %}

        Значения в полях **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.buffer_flush_interval.title }}** и **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataTransformationOptions.invocation_timeout.title }}** указываются с постфиксом `s`, например, `10s`.

        {% endnote %}


    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaSourceAdvancedSettings.converter.title }}**:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.format.title }}** — выберите один из доступных форматов:
            * `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_JSON.title }}`
            * `{{ ui-key.yc-data-transfer.data-transfer.console.form.object_storage.console.form.object_storage.ObjectStorageSerializationFormatUI.OBJECT_STORAGE_SERIALIZATION_FORMAT_CSV.title }}`

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.data_schema.title }}** — задайте схему в виде списка полей или загрузите файл с описанием схемы в формате JSON.
            
            {% include notitle [json-schema-sample](../../../../_includes/data-transfer/fields/common/ui/json-schema-sample.md) %}

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.add_rest_column.title }}** — выберите эту опцию, чтобы поля, отсутствующие в схеме, попадали в колонку `_rest`.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.null_keys_allowed.title }}** — выберите эту опцию, чтобы разрешить значение `null` в ключевых колонках.


{% endlist %}
