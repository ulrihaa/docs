# Установка Filebeat

[Filebeat](https://www.elastic.co/beats/filebeat) — плагин, который позволяет собирать и передавать логи в экосистему Elastic Stack: {{ ES }}, Kibana, Logstash. [Filebeat](/marketplace/products/yc/filebeat) устанавливается в [кластер {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster), собирает логи кластера и [подов](../../concepts/index.md#pod), а затем отправляет их в сервис [{{ mes-full-name }}](../../../managed-elasticsearch/).

## Перед началом работы {#before-you-begin}

1. {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

1. Убедитесь, что кластер {{ managed-k8s-name }} расположен в той же [облачной сети](../../../vpc/concepts/network.md), что и [кластер {{ mes-name }}](../../../managed-elasticsearch/concepts/index.md).

## Установка с помощью {{ marketplace-full-name }} {#marketplace-install}

1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}**.
1. Нажмите на имя нужного кластера {{ managed-k8s-name }} и выберите вкладку ![image](../../../_assets/marketplace.svg) **{{ ui-key.yacloud.k8s.cluster.switch_marketplace }}**.
1. В разделе **Доступные для установки приложения** выберите [Filebeat](/marketplace/products/yc/filebeat) и нажмите кнопку **{{ ui-key.yacloud.marketplace-v2.button_use }}**.
1. Задайте настройки приложения:
   * **Пространство имен** — выберите [пространство имен](../../concepts/index.md#namespace) или создайте новое.
   * **Название приложения** — укажите название приложения, например `filebeat`.
   * **Имя пользователя {{ ES }}** — введите имя учетной записи, под которой Filebeat будет подключаться к кластеру {{ mes-name }}.
   * **Пароль для подключения к {{ ES }}** — введите пароль для учетной записи в кластере {{ mes-name }}.
   * **FQDN сервиса {{ ES }}** — укажите URL и порт для кластера {{ mes-name }}, например `https://c-c9q07rjo9c11********.rw.{{ dns-zone }}:9200`. Подробнее о подключении к кластеру {{ mes-name }} см. в [документации сервиса](../../../managed-elasticsearch/operations/cluster-connect.md).
1. Нажмите кнопку **{{ ui-key.yacloud.k8s.cluster.marketplace.button_install }}**.
1. Дождитесь перехода приложения в статус `{{ ui-key.yacloud.k8s.cluster.marketplace.label_release-status-DEPLOYED }}`.
1. [Подключитесь к веб-интерфейсу Kibana](../../../managed-elasticsearch/quickstart.md#connect-kibana) и убедитесь, что события кластера {{ managed-k8s-name }} начали поступать.

## Установка с помощью Helm-чарта {#helm-install}

1. {% include [Установка Helm](../../../_includes/managed-kubernetes/helm-install.md) %}
1. {% include [Install and configure kubectl](../../../_includes/managed-kubernetes/kubectl-install.md) %}
1. Для установки [Helm-чарта](https://helm.sh/docs/topics/charts/) с Filebeat выполните команду:

   ```bash
   export HELM_EXPERIMENTAL_OCI=1 && \
   helm pull oci://{{ mkt-k8s-key.yc_filebeat.helmChart.name }} \
     --version {{ mkt-k8s-key.yc_filebeat.helmChart.tag }} \
     --untar && \
   helm install \
     --namespace <пространство_имен> \
     --create-namespace \
     --set app.url='<URL_и_порт_кластера_Managed_Service_for_Elasticsearch>' \
     --set app.username='<имя_пользователя_в_кластере_{{ ES }}>' \
     --set app.password='<пароль_пользователя_в_кластере_{{ ES }}>' \
     filebeat ./filebeat/
   ```

   Эта команда также создаст новое пространство имен, необходимое для работы Filebeat.
1. Убедитесь, что под Filebeat перешел в состояние `Running`:

   ```bash
   kubectl get pods --namespace=filebeat -l app=filebeat-filebeat -w
   ```

1. [Подключитесь к веб-интерфейсу Kibana](../../../managed-elasticsearch/quickstart.md#connect-kibana) и убедитесь, что события кластера {{ managed-k8s-name }} начали поступать.

## См. также {#see-also}

* [Документация Filebeat](https://www.elastic.co/guide/en/beats/filebeat/master/index.html).
* [Документация {{ mes-name }}](../../../managed-elasticsearch/).