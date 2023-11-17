# How to recognize long audio files in {{ speechkit-name }}

The service can recognize speech [in different ways](../stt/index.md#stt-ways). The example below demonstrates an audio file recognition using the [asynchronous recognition](../stt/transcribation.md) API. This API is subject to the following limitations:

* Maximum audio duration: {{ stt-long-audioLength }}
* Maximum file size: {{ stt-long-fileSize }}

## Getting started {#before-you-begin}

{% include [transcribation-before-you-begin](../../_includes/speechkit/transcribation-before-you-begin.md) %}

If you do not have an LPCM audio file, you can download a [sample file](https://{{ s3-storage-host }}/speechkit/speech.pcm).

## Speech recognition {#speech-recognition}

{% list tabs %}

- cURL

   {% include [async-recognition](../../_includes/speechkit/async-recognition.md) %}

{% endlist %}

#### See also {#what-is-next}

* [{#T}](../stt/index.md)
* [{#T}](../stt/api/transcribation-api.md)
* [{#T}](../concepts/auth.md)
* [{#T}](../stt/api/transcribation-ogg.md)
