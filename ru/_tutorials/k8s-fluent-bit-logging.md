{% note info %}

Включить отправку логов [кластера {{ managed-k8s-name }}](../managed-kubernetes/concepts/index.md#kubernetes-cluster) в [{{ cloud-logging-full-name }}](../logging/) можно с помощью настройки `master logging` при [создании](../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create.md) или [изменении](../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-update.md) кластера. Настройка доступна только в CLI, {{ TF }} и API.

{% endnote %}

Обработчик логов [Fluent Bit](https://fluentbit.io/) позволяет транслировать логи кластера {{ managed-k8s-name }} в сервис {{ cloud-logging-name }}. Для передачи логов используется модуль [Fluent Bit plugin for {{ cloud-logging-full-name }}](https://github.com/yandex-cloud/fluent-bit-plugin-yandex).

Чтобы настроить передачу логов:
1. [Подготовьте кластер {{ managed-k8s-name }}](#configure-cluster).
1. [Установите и настройте Fluent Bit](#fluent-bit-install).

## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../_includes/cli-install.md) %}

   {% include [default-catalogue](../_includes/default-catalogue.md) %}

1. [Создайте сервисный аккаунт](../iam/operations/sa/create.md), необходимый для работы Fluent Bit.
   1. [Назначьте ему роль](../iam/operations/sa/assign-role-for-sa.md) `logging.writer`.
   1. Создайте для него [авторизованный ключ](../iam/operations/authorized-key/create.md) и сохраните его в файл `key.json`:

      ```bash
      yc iam key create \
        --service-account-name <имя_сервисного_аккаунта_для_Fluent_Bit> \
        --output key.json
      ```

1. [Создайте лог-группу](../logging/operations/create-group.md).
1. [Создайте кластер {{ managed-k8s-name }}](../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-create.md) и [группу узлов](../managed-kubernetes/operations/node-group/node-group-create.md) любой подходящей конфигурации с [версией {{ k8s }}](../managed-kubernetes/concepts/release-channels-and-updates.md) не ниже 1.21.

1. {% include [Install and configure kubectl](../_includes/managed-kubernetes/kubectl-install.md) %}

## Установите и настройте Fluent Bit {#fluent-bit-install}

{% list tabs %}

- С помощью {{ yandex-cloud }}

  1. Установите Fluent Bit согласно [инструкции](../managed-kubernetes/operations/applications/fluentbit.md).
  1. [Проверьте передачу логов](../logging/operations/read-logs.md) кластера {{ managed-k8s-name }} в {{ cloud-logging-name }}.

- Вручную

  1. Создайте объекты, необходимые для работы Fluent Bit:

     {% list tabs %}

     - Для кластеров с версией {{ k8s }} до 1.21 включительно:

       ```bash
       kubectl create namespace logging && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-service-account.yaml && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role.yaml && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role-binding.yaml
       ```
     
     - Для кластеров с версией {{ k8s }} с 1.22 и выше:

       ```bash
       kubectl create namespace logging && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-service-account.yaml && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role-1.22.yaml && \
       kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role-binding-1.22.yaml
       ```

     {% endlist %}

  1. Создайте секрет, содержащий ключ [сервисного аккаунта](../iam/concepts/users/service-accounts.md):

     ```bash
     kubectl create secret generic secret-key-json \
       --from-file=key.json \
       --namespace logging
     ```

  1. Скачайте конфигурационный файл `config.yaml`:

   ```bash
   wget https://raw.githubusercontent.com/knpsh/yc-logging-fluent-bit-example/main/config.yaml
   ```

  1. Укажите параметры трансляции логов в блоке `data.output-elasticsearch.conf` файла `config.yaml`:

     ```yaml
     ...
       output-elasticsearch.conf: |
         [OUTPUT]
           Name            yc-logging
           Match           *
           group_id        <идентификатор_лог-группы>
           resource_id     <опционально_идентификатор_кластера_{{ k8s }}>
           message_key     log
           authorization   iam-key-file:/etc/secret/key.json
     ...
     ```

     Идентификатор [лог-группы](../logging/concepts/log-group.md) можно получить со [списком лог-групп в каталоге](../logging/operations/list.md).

     При необходимости укажите [дополнительные настройки](https://github.com/yandex-cloud/fluent-bit-plugin-yandex#configuration-parameters) Fluent Bit.
  1. Создайте объекты Fluent Bit:

     ```bash
     kubectl apply -f config.yaml
     ```

     Результат:

     ```text
     configmap/fluent-bit-config created
     daemonset.apps/fluent-bit created
     ```

  1. Убедитесь, что под Fluent Bit перешел в состояние `Running`:

     ```bash
     kubectl get pods -n logging
     ```

  1. [Проверьте передачу логов](../logging/operations/read-logs.md) кластера {{ managed-k8s-name }} в {{ cloud-logging-name }}.

{% endlist %}

## Удалите созданные ресурсы {#clear-out}

Некоторые ресурсы платные. Чтобы за них не списывалась плата, удалите ресурсы, которые вы больше не будете использовать:
1. [Удалите кластер {{ managed-k8s-name }}](../managed-kubernetes/operations/kubernetes-cluster/kubernetes-cluster-delete.md).
1. Если вы зарезервировали для кластера {{ managed-k8s-name }} [публичный статический IP-адрес](../vpc/concepts/address.md#public-addresses), [удалите его](../vpc/operations/address-delete.md).
1. [Удалите лог-группу](../logging/operations/delete-group.md).