You can upload a certificate chain and a private key to use on your own, for example, when configuring a web server on a VM.

To get the contents of a certificate:

{% list tabs %}

- CLI

   The command will display a certificate chain and a private key and save their contents to the `--chain` and `--key` files, respectively.

   * `--id`: Certificate ID; make sure you set either the `--id` or `--name` flag.
   * `--name`: Name of the certificate; make sure you set either the `--id` or `--name` flag.
   * `--chain`: (Optional) File to save the certificate chain to in PEM format.
   * `--key`: (Optional) File to save the private key to, in PEM format.


   ```
   yc certificate-manager certificate content \
     --id fpqcsmn76v82fi446ri7 \
     --chain certificate_full_chain.pem \
     --key private_key.pem
   ```

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   To get the contents of a custom certificate using {{ TF }}:

   1. In the {{ TF }} configuration file, describe the parameters of the resources you want to create:

      
      ```
      data "yandex_cm_certificate_content" "cert_by_id" {
        certificate_id = "<certificate_ID>"
      }

      output "certificate_chain" {
        value = data.yandex_cm_certificate_content.cert_by_id.certificates
      }

      output "certificate_key" {
        value     = data.yandex_cm_certificate_content.cert_by_id.private_key
        sensitive = true
      }
      ```



      Where:

      * `data "yandex_cm_certificate_content"`: Description of the data source for the certificate contents:
         * `certificate_id`: Certificate ID
      * `output` sections: Output variables such as `certificate_chain` with a certificate chain and a `certificate_key` private key:
         * `value`: Returned value
         * `sensitive`: Label data as sensitive

      For more information about the `yandex_cm_certificate_content` data source parameters, see the [provider documentation]({{ tf-provider-datasources-link }}/datasource_cm_certificate_content).

   1. Create resources:

      {% include [terraform-validate-plan-apply](../../_tutorials/terraform-validate-plan-apply.md) %}

      {{ TF }} will create all the required resources. To check the results, run these commands:

      * Get a certificate chain:

         ```bash
         terraform output certificate_chain
         ```

      * Get the private key value:

         ```bash
         terraform output -raw certificate_key
         ```

- API

   To get the certificate contents, use the [get](../../certificate-manager/api-ref/CertificateContent/get.md) REST API method for the [CertificateContent](../../certificate-manager/api-ref/CertificateContent/) resource or the [CertificateContentService/Get](../../certificate-manager/api-ref/grpc/certificate_content_service.md#Get) gRPC API call.

{% endlist %}

{% note info %}

To view the certificate contents, assign the `certificate-manager.certificates.downloader` role to the service account.

{% endnote %}