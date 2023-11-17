# API асинхронного распознавания

## Как использовать API асинхронного распознавания {#how-to-use}

1. [Создайте сервисный аккаунт](../../../iam/operations/sa/create.md).
1. [Назначьте ему роли](../../../iam/operations/sa/assign-role-for-sa.md):

   * `{{ roles-speechkit-stt }}` — для распознавания речи;
   * `storage.uploader` — для загрузки аудиофайла в [бакет {{ objstorage-full-name }}](../../../storage/concepts/bucket.md);
   * (Опционально) `storage.configurer`, `kms.keys.encrypter` и `kms.keys.decrypter` — для шифрования и расшифровки объектов в бакете. Эти роли нужны, только если вы используете [шифрование в {{ objstorage-name }}](../../../storage/concepts/encryption.md).

1. Получите [IAM-токен](../../../iam/operations/iam-token/create-for-sa.md) или [API-ключ](../../../iam/operations/api-key/create.md) для вашего сервисного аккаунта, они понадобятся для авторизации в API. Передавайте их в каждом запросе в виде HTTP-заголовков:

   * при авторизации с IAM-токеном — `Authorization: Bearer <IAM-токен>`;
   * при авторизации с API-ключом — `Authorization: Api-Key <API-ключ>`.

1. [Создайте бакет {{ objstorage-full-name }}](../../../storage/operations/buckets/create.md).
1. [Загрузите аудиофайл в бакет](../../../storage/operations/objects/upload.md).
1. [Получите ссылку](../../../storage/operations/objects/link-for-download.md) на загруженный файл. Используйте эту ссылку в [теле запроса на распознавание речи](#sendfile-params).

   Для бакета с ограниченным доступом в ссылке присутствуют дополнительные query-параметры (после знака `?`). Эти параметры не нужно передавать в {{ speechkit-name }} — они игнорируются.

## Отправить файл на распознавание {#sendfile}

### Параметры в теле запроса {#sendfile-params}

Структура тела запроса:

```json
{
 "config": {
  "specification": {
   "languageCode": "string",
   "model": "string",
   "profanityFilter": "string",
   "literature_text": boolean,
   "audioEncoding": "string",
   "sampleRateHertz": integer,
   "audioChannelCount": integer,
   "rawResults": boolean
  }
 },
 "audio": {
  "uri": "string"
 }
}
```

#|
|| **Параметр** | **Описание** ||
|| config | **object**<br>Поле с настройками распознавания. ||
|| config.<br>specification | **object**<br>Настройки распознавания. ||
|| config.<br>specification.<br>languageCode | **string**<br>[Язык речи](../models.md#languages) в аудиозаписи для распознавания.<br/>Значение по умолчанию — `ru-RU`  — русский язык. ||
|| config.<br>specification.<br>model | **string**<br>[Языковая модель](../models.md#tags) для распознавания речи.<br>Значение параметра по умолчанию: `general`.<br>Выбор модели влияет на [стоимость использования](../../pricing.md#rules-stt-long). ||
|| config.<br>specification.<br>profanityFilter | **boolean**<br>Фильтр ненормативной лексики.<br/>Допустимые значения:<ul><li>`true` — замаскировать ненормативную лексику звездочками в результатах распознавания.</li><li>`false` (по умолчанию) — не маскировать ненормативную лексику.</li></ul> ||
|| config.<br>specification.<br>literature_text | **boolean**<br>Включает [режим нормализации](../normalization.md). ||
|| config.<br>specification.<br>audioEncoding | **string**<br>[Формат](../../formats.md) передаваемого аудио.<br/>Допустимые значения:<ul><li>`LINEAR16_PCM` — [LPCM без WAV-заголовка](../../formats.md#lpcm).</li><li>`OGG_OPUS` (по умолчанию) — формат [OggOpus](../../formats.md#oggopus).</li><li>`MP3` — формат [MP3](../../formats.md#MP3).</li></ul> ||
|| config.<br>specification.<br>sampleRateHertz | **integer** (int64)<br>Частота дискретизации передаваемого аудио.<br/>Этот параметр обязателен, если значение `format` равно `LINEAR16_PCM`. Допустимые значения:<ul><li>`48000` (по умолчанию) — частота дискретизации 48 кГц;</li><li>`16000` — частота дискретизации 16 кГц;</li><li>`8000` — частота дискретизации 8 кГц.</li></ul> ||
|| config.<br>specification.<br>audioChannelCount | **integer** (int64)<br>Количество каналов для аудиофайлов в [формате LPCM](../../formats.md#lpcm). По умолчанию используется значение `1`.<br>Не используйте это поле для аудиофайлов в формате [OggOpus](../../formats.md#oggopus) и [MP3](../../formats.md#MP3). Информация о количестве каналов уже содержится в этих файлах. ||
|| config.<br>specification.<br>rawResults | **boolean** <br>Флаг, указывающий, как писать числа.</br>Допустимые значения:<ul><li>`true` — писать прописью.</li><li>`false` (по умолчанию) — писать цифрами.</li></ul> ||
|| audio.<br>uri | **string**<br>URI аудиофайла для распознавания. Поддерживаются только ссылки на файлы, которые хранятся в [{{ objstorage-full-name }}](../../../storage/). ||
|#

### Ответ {#sendfile-response}

Если запрос был составлен правильно, сервис возвращает [объект Operation](../../../api-design-guide/concepts/operation.md), в котором содержится идентификатор операции распознавания (`id`):

```json
{
 "done": false,
 "id": "e03sup**********ht8g",
 "createdAt": "2019-04-21T22:49:29Z",
 "createdBy": "ajes08**********bhqq",
 "modifiedAt": "2019-04-21T22:49:29Z"
}
```

Используйте полученный идентификатор на следующем шаге.

## Получить результаты распознавания {#get-result}

Проверяйте результаты распознавания, используя полученный идентификатор. Количество запросов на проверку результатов [ограничено](../../concepts/limits.md), 1 минута одноканального аудио распознается примерно за 10 секунд.

{% note warning %}

Результаты распознавания хранятся на сервере {{ stt-long-resultsStorageTime }}. После этого вы не сможете запросить результаты распознавания, используя полученный идентификатор.

{% endnote %}

### Path-параметры {#get-result-params}

Параметр | Описание
----- | -----
operationId | Идентификатор операции, полученный при отправке запроса на распознавание.

### Ответ {#get-result-response}

В ответе на запрос возвращается [объект Operation](../../../api-design-guide/concepts/operation.md). Пример ответа:

```json
{
 "done": true,
 "response": {
  "@type": "type.googleapis.com/yandex.cloud.ai.stt.v2.LongRunningRecognitionResponse",
  "chunks": [
   {
    "alternatives": [
     {
      "words": [
       {
        "startTime": "0.879999999s",
        "endTime": "1.159999992s",
        "word": "при",
        "confidence": 1
       },
       {
        "startTime": "1.219999995s",
        "endTime": "1.539999988s",
        "word": "написании",
        "confidence": 1
       },
       ...
      ],
      "text": "при написании хоббита толкин обращался к мотивам скандинавской мифологии древней английской поэмы беовульф",
      "confidence": 1
     }
    ],
    "channelTag": "1"
   },
   ...
  ]
 },
 "id": "e03sup**********ht8g",
 "createdAt": "2019-04-21T22:49:29Z",
 "createdBy": "ajes08**********bhqq",
 "modifiedAt": "2019-04-21T22:49:36Z"
}
```

#|
|| **Параметр** | **Описание** ||
|| done | **boolean**
Содержит значение `true`, когда распознавание закончено. ||
|| response | **object**
Результаты асинхронного распознавания речи. ||
|| response.<br>@type | **string**
Тип ответа на запрос. ||
|| response.<br>chunks | **array**
Массив с результатами распознавания. ||
|| response.<br>chunks.<br>alternatives | **array**
Массив с вариантами распознанного текста. ||
|| response.<br>chunks.<br>alternatives.<br>words | **array**
Массив с распознанными словами и информацией о них. ||
|| response.<br>chunks.<br>alternatives.<br>words.<br>startTime | **string**
Время начала слова в аудиозаписи. Возможна погрешность в пределах 1–2 секунд. ||
|| response.<br>chunks.<br>alternatives.<br>words.<br>endTime | **string**
Время окончания слова в аудиозаписи. Возможна погрешность в пределах 1–2 секунд. ||
|| response.<br>chunks.<br>alternatives.<br>words.<br>word | **string**
Распознанное слово. Распознанные числа пишутся прописью, например не `12`, а `двенадцать`. ||
|| response.<br>chunks.<br>alternatives.<br>words.<br>confidence | **integer** (int64)
Поле не поддерживается, не используйте его. ||
|| response.<br>chunks.<br>alternatives.<br>text | **string**
Распознанный текст целиком. По умолчанию числа пишутся цифрами. Чтобы весь текст был прописью, укажите `true` в параметре `config.specification.rawResult`. ||
|| response.<br>chunks.<br>alternatives.<br>confidence | **integer** (int64)
Поле не поддерживается, не используйте его. ||
|| response.<br>chunks.<br>channelTag | **string**
Аудиоканал, для которого выполнено распознавание. ||
|| id | **string**
Идентификатор операции. Генерируется на стороне сервиса. ||
|| createdAt | [google.protobuf.Timestamp](https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/timestamp.proto)
Время запуска операции. Указывается в формате [RFC3339 (Timestamps)](https://www.ietf.org/rfc/rfc3339.txt). ||
|| createdBy | **string**
Идентификатор пользователя, запустившего операцию. ||
|| modifiedAt | [google.protobuf.Timestamp](https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/timestamp.proto)
Время последнего изменения ресурса. Указывается в формате [RFC3339 (Timestamps)](https://www.ietf.org/rfc/rfc3339.txt).
|#
