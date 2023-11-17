To work with the {{ speechkit-name }} API, you need to pass authentication. The authentication method depends on the account type:

{% list tabs %}

- Yandex or federated account

   1. Get an IAM token for your [Yandex account](../../iam/operations/iam-token/create.md) or [federated account](../../iam/operations/iam-token/create-for-federation.md).
   1. Get the [ID of the folder](../../resource-manager/operations/folder/get-id.md) for which your account has the `{{ roles-speechkit-stt }}`, `{{ roles-speechkit-tts }}`, or higher roles.
   1. {% include [iam-token-usage](../iam-token-usage-speechkit-v3.md) %}

- Service account

   {{ speechkit-name }} supports two authentication methods based on service accounts:

   * Using an [IAM token](../../iam/concepts/authorization/iam-token.md):

      1. [Get an IAM token](../../iam/operations/iam-token/create-for-sa.md).
      1. Specify the obtained IAM token in the `Authorization` header in the following format:

         ```
         Authorization: Bearer <IAM token>
         ```

   * With [API keys](../../iam/concepts/authorization/api-key).

      {% include [api-keys-disclaimer](../iam/api-keys-disclaimer.md) %}

      1. [Get an API key](../../iam/operations/api-key/create.md).
      1. Specify the obtained API key in the `Authorization` header in the following format:

         ```
         Authorization: Api-Key <API key>
         ```

   Do not specify the folder ID in your requests, as the service uses the folder in which the service account was created.

{% endlist %}
