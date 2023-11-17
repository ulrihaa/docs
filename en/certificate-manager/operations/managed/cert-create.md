# Adding a Let's Encrypt® certificate

You can use a [certificate](../../concepts/managed-certificate.md) from {{ certificate-manager-name }} in the [specified](../../concepts/services.md) {{ yandex-cloud }} services only.

To add a new Let's Encrypt® [certificate](../../concepts/managed-certificate.md):

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) to add a certificate to.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_certificate-manager }}**.
   1. Click **{{ ui-key.yacloud.certificate-manager.button_add }}**.
   1. In the menu that opens, select **{{ ui-key.yacloud.certificate-manager.action_request }}**.
   1. In the window that opens, enter a name for your certificate in the **{{ ui-key.yacloud.certificate-manager.metadata.field_name }}** field.
   1. (Optional) In the **Description** field, enter a description for the certificate.
   1. In the **{{ ui-key.yacloud.certificate-manager.request.field_domains }}** field, list the domains to issue a certificate for.

      Domain names may contain a mask, e.g., `*.example.com`. If so, select `DNS` as the type of domain rights check. For more information, see [Check rights for domain](../../concepts/challenges#dns).
   1. Select the [type of check for domain rights](../../concepts/challenges.md): `{{ ui-key.yacloud.certificate-manager.request.challenge-type_label_dns }}` or `{{ ui-key.yacloud.certificate-manager.request.challenge-type_label_http }}`.
   1. Click **{{ ui-key.yacloud.certificate-manager.request.button_request }}**.

- CLI

   {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

   1. View a description of the command:

      ```bash
      yc certificate-manager certificate request --help
      ```

   1. Run the following command:

      ```bash
      yc certificate-manager certificate request \
        --name mymanagedcert \
        --domains example.com
      ```

      Where:
      * `--name`: Certificate name
      * `--domains`: Certificate domains

      Result:

      ```bash
      id: fpq6gvvm6piu********
      folder_id: b1g7gvsi89m3********
      created_at: "2020-09-15T08:49:11.533771Z"
      ...
      - example.com
      status: VALIDATING
      updated_at: "2020-09-15T08:49:11.533771Z"
      ```

- {{ TF }}

   {% include [terraform-install](../../../_includes/terraform-install.md) %}

   1. In the {{ TF }} configuration file, describe the parameters of the resource to create:

      ```hcl
      resource "yandex_cm_certificate" "le-certificate" {
        name    = "<certificate_name>"
        domains = ["<domain>"]

        managed {
        challenge_type = "<type_of_domain_owner_check>"
        }
      }
      ```

      Where:
      * `domains`: List of domains to create a certificate for.
      * `challenge_type`: [Type of domain rights check](../../concepts/challenges.md) to be [passed](cert-validate.md) by the domain owner. The possible values include:
         * `DNS_CNAME`: Create a [DNS record](../../../dns/concepts/resource-record.md) in [CNAME](../../../dns/concepts/resource-record.md#cname-cname) format with the specified value. Method recommended for automatic certificate renewal.
         * `DNS_TXT`: Create a DNS record in TXT format with the specified value.
         * `HTTP`: Place the specified value in the specified URL.

      For more information about the `yandex_cm_certificate` resource parameters, see the [{{ TF }} provider documentation]({{ tf-provider-resources-link }}/cm_certificate).
   1. Create resources:

      {% include [terraform-validate-plan-apply](../../../_tutorials/terraform-validate-plan-apply.md) %}

   This will create a certificate in the specified folder. You can check the new certificate and its configuration using the [management console]({{ link-console-main }}) or this [CLI](../../../cli/quickstart.md) command:

   ```bash
   yc certificate-manager certificate get <certificate_name>
   ```

- API

   To add a certificate, use the [requestNew](../../api-ref/Certificate/requestNew.md) REST API method for the [Certificate](../../api-ref/Certificate/) resource or the [CertificateService/RequestNew](../../api-ref/grpc/certificate_service.md#RequestNew) gRPC API call.

{% endlist %}

A new certificate appears in the certificate list with the `Validating` status. This status means that a Let's Encrypt® certificate was requested and you need to pass a [domain rights check](cert-validate.md) for it to be successfully processed.

{% note info %}

{% include [checking-domain-rights-cname](../../../_includes/certificate-manager/checking-domain-rights-cname.md) %}

{% endnote %}