---
title: "Deleting a stream in the AWS SDK"
description: "This guide describes how you can delete a stream in the AWS SDK."
---

# Deleting a stream in the AWS SDK

{% list tabs %}

- Python

  To delete a [stream](../../concepts/glossary.md#stream-concepts), use the `delete_stream` method. When you invoke this method, you should specify the following parameters:
  * Name of the stream to delete, e.g., `example-stream`.
  * [ID of the cloud](../../../resource-manager/operations/cloud/get-id.md) in which to delete a stream, e.g., `b1gi1kuj2dht********`.
  * {{ ydb-short-name }} database ID with the stream, e.g., `cc8028jgtuab********`.

  You also need to [configure](prepare.md) the AWS SDK and [assign](../../../iam/operations/sa/assign-role-for-sa.md) the service account the `yds.editor` role.

  To delete a stream with the parameters specified above:

  1. Create a file named `stream_delete.py` and copy the following code into it:

     ```python
     import boto3
     from pprint import pprint

     def delete_stream(cloud, database, stream_name):
       client = boto3.client('kinesis', endpoint_url="https://yds.serverless.yandexcloud.net")
       response = client.delete_stream(
         StreamName="/{{ region-id }}/{cloud}/{database}/{stream}".format(cloud=cloud,
                                                                       database=database,
                                                                       stream=stream_name)
       )
       return response

     if __name__ == '__main__':
       delete_stream_response = delete_stream(
         cloud="b1gi1kuj2dht********",
         database="cc8028jgtuab********",
         stream_name="example-stream")

       print("The stream has been deleted successfully")
       pprint(delete_stream_response)
      ```

  1. Run the program:

     ```bash
     python3 stream_delete.py
     ```

     Result:

     ```text
     The stream has been deleted successfully
     {
       'ResponseMetadata': {
         'HTTPHeaders': {
         'connection': 'keep-alive',
         'content-length': '3',
         'content-type': 'application/json',
         'date': ''
         'GMT',
         'server': 'nginx/1.19.5'
         },
       'HTTPStatusCode': 200,
       'RetryAttempts': 0
       }
     }
     ```

{% endlist %}