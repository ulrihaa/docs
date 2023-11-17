# Text recognition from PDF files

You can [recognize text](../../concepts/ocr/index.md) from a PDF file using the [OCR API](../../ocr/api-ref/index.md) or [Vision API](../../vision/api-ref/index.md). The OCR API is an updated and revised interface with enhanced [features](../../concepts/limits.md#vision-limits), including multi-column text recognition.

## Getting started {#before-you-begin}

{% include [curl](../../../_includes/curl.md) %}

{% include [ai-before-beginning](../../../_includes/vision/ai-before-beginning.md) %}

## Recognizing text from a PDF file through the OCR API {#ocr-api-recognition}

Text recognition from a PDF file is implemented through OCR API methods, such as [TextRecognition.recognize](../../ocr/api-ref/TextRecognition/recognize.md) for single-page PDF files and [TextRecognitionAsync.recognize](../../ocr/api-ref/TextRecognitionAsync/recognize.md) for multi-page ones.

1. Prepare a PDF file for recognition. Make sure its size does not exceed {{ ocr-max-filesize }} and a single file contains no more than 200 pages.

1. Encode the PDF file as Base64:

   {% include [base64-encode-command](../../../_includes/vision/base64-encode-command-pdf.md) %}

1. Create a file with the request body (for example, `body.json`).

   **body.json:**
   ```json
   {
     "mimeType": "application/pdf",
     "languageCodes": ["*"],
     "model": "page",
     "content": "<base64-encoded_PDF_file>"
   }
   ```

   In the `content` property, specify the PDF file contents [encoded](../base64-encode.md) as Base64.

   For the service to automatically detect the text language, specify the `"languageCodes": ["*"]` property in the configuration.

1. Send your request:

   {% list tabs %}

   - Single-page PDF file

      {% include [send-request](../../../_includes/vision/send-request_ocr.md) %}

      The result will consist of recognized blocks of text, lines, and words with their positions on the PDF file's page.

      {% cut "Result" %}

      ```json
      {
        "result": {
          "text_annotation": {
            "width": "3312",
            "height": "4683",
            "blocks": [
              {
                "bounding_box": {
                  "vertices": [
                    {
                      "x": "373",
                      "y": "371"
                    },
                    {
                      "x": "373",
                      "y": "580"
                    },
                    {
                      "x": "1836",
                      "y": "580"
                    },
                    {
                      "x": "1836",
                      "y": "371"
                    }
                  ]
                },
                "lines": [
                  {
                    "bounding_box": {
                      "vertices": [
                        {
                          "x": "373",
                          "y": "371"
                        },
                        {
                          "x": "373",
                          "y": "430"
                        },
                        {
                          "x": "1836",
                          "y": "430"
                        },
                        {
                          "x": "1836",
                          "y": "371"
                        }
                      ]
                    },
                    "alternatives": [
                      {
                        "text": "Page №1, line 1",
                        "words": [
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "373",
                                  "y": "358"
                                },
                                {
                                  "x": "373",
                                  "y": "444"
                                },
                                {
                                  "x": "967",
                                  "y": "444"
                                },
                                {
                                  "x": "967",
                                  "y": "358"
                                }
                              ]
                            },
                            "text": "Page",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1014",
                                  "y": "358"
                                },
                                {
                                  "x": "1014",
                                  "y": "444"
                                },
                                {
                                  "x": "1278",
                                  "y": "444"
                                },
                                {
                                  "x": "1278",
                                  "y": "358"
                                }
                              ]
                            },
                            "text": "№1,",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1303",
                                  "y": "358"
                                },
                                {
                                  "x": "1303",
                                  "y": "444"
                                },
                                {
                                  "x": "1718",
                                  "y": "444"
                                },
                                {
                                  "x": "1718",
                                  "y": "358"
                                }
                              ]
                            },
                            "text": "line",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1765",
                                  "y": "358"
                                },
                                {
                                  "x": "1765",
                                  "y": "444"
                                },
                                {
                                  "x": "1836",
                                  "y": "444"
                                },
                                {
                                  "x": "1836",
                                  "y": "358"
                                }
                              ]
                            },
                            "text": "1",
                            "entity_index": "-1"
                          }
                        ]
                      }
                    ]
                  },
                  {
                    "bounding_box": {
                      "vertices": [
                        {
                          "x": "373",
                          "y": "520"
                        },
                        {
                          "x": "373",
                          "y": "580"
                        },
                        {
                          "x": "1836",
                          "y": "580"
                        },
                        {
                          "x": "1836",
                          "y": "520"
                        }
                      ]
                    },
                    "alternatives": [
                      {
                        "text": "Page №1, line 2",
                        "words": [
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "373",
                                  "y": "508"
                                },
                                {
                                  "x": "373",
                                  "y": "594"
                                },
                                {
                                  "x": "967",
                                  "y": "594"
                                },
                                {
                                  "x": "967",
                                  "y": "508"
                                }
                              ]
                            },
                            "text": "Page",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1014",
                                  "y": "507"
                                },
                                {
                                  "x": "1014",
                                  "y": "593"
                                },
                                {
                                  "x": "1277",
                                  "y": "593"
                                },
                                {
                                  "x": "1277",
                                  "y": "507"
                                }
                              ]
                            },
                            "text": "№1,",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1302",
                                  "y": "507"
                                },
                                {
                                  "x": "1302",
                                  "y": "593"
                                },
                                {
                                  "x": "1718",
                                  "y": "593"
                                },
                                {
                                  "x": "1718",
                                  "y": "507"
                                }
                              ]
                            },
                            "text": "line",
                            "entity_index": "-1"
                          },
                          {
                            "bounding_box": {
                              "vertices": [
                                {
                                  "x": "1765",
                                  "y": "507"
                                },
                                {
                                  "x": "1765",
                                  "y": "593"
                                },
                                {
                                  "x": "1836",
                                  "y": "593"
                                },
                                {
                                  "x": "1836",
                                  "y": "507"
                                }
                              ]
                            },
                            "text": "2",
                            "entity_index": "-1"
                          }
                        ]
                      }
                    ]
                  }
                ],
                "languages": [
                  {
                    "language_code": "ru"
                  }
                ]
              }
            ],
            "entities": []
          },
          "page": "0"
        }
      }
      ```

      {% endcut %}

   - Multi-page PDF file

      * {% include [send-request](../../../_includes/vision/send-request_ocr-async.md) %}

         Result:

         ```json
         {
           "id": "cfrtr5q0hdhl********",
           "description": "OCR async recognition",
           "created_at": "2023-10-24T09:12:48Z",
           "created_by": "ajeol2afu1js********",
           "modified_at": "2023-10-24T09:12:48Z",
           "done": false,
           "metadata": null
         }
         ```

         Save the recognition operation `id` that you received in the response.

      * Send a recognition request using the [getRecognition](../../ocr/api-ref/TextRecognitionAsync/getRecognition.md) method:

         ```bash
         export IAM_TOKEN=<IAM_token>
         curl -X GET \
             -H "Content-Type: application/json" \
             -H "Authorization: Bearer ${IAM_TOKEN}" \
             -H "x-folder-id: <folder_ID>" \
             -H "x-data-logging-enabled: true" \
             https://ocr.{{ api-host }}/ocr/v1/getRecognition?operationId=<operation_ID> \
             -o output.json
         ```

         Where:
         * `<IAM_token>`: Previously obtained IAM token.
         * `<folder_ID>`: Previously obtained folder ID.
         * `<operation_ID>`: Previously obtained recognition operation ID.

         The result will consist of recognized blocks of text, lines, and words with their positions on the PDF file's page. The result of recognizing each page is given in a separate `result` section.


         {% cut "Result" %}

         ```json
         {
           "result": {
             "text_annotation": {
               "width": "3312",
               "height": "4683",
               "blocks": [
                 {
                   "bounding_box": {
                     "vertices": [
                       {
                         "x": "373",
                         "y": "371"
                       },
                       {
                         "x": "373",
                         "y": "580"
                       },
                       {
                         "x": "1836",
                         "y": "580"
                       },
                       {
                         "x": "1836",
                         "y": "371"
                       }
                     ]
                   },
                   "lines": [
                     {
                       "bounding_box": {
                         "vertices": [
                           {
                             "x": "373",
                             "y": "371"
                           },
                           {
                             "x": "373",
                             "y": "430"
                           },
                           {
                             "x": "1836",
                             "y": "430"
                           },
                           {
                             "x": "1836",
                             "y": "371"
                           }
                         ]
                       },
                       "alternatives": [
                         {
                           "text": "Page №1, line 1",
                           "words": [
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "373",
                                     "y": "358"
                                   },
                                   {
                                     "x": "373",
                                     "y": "444"
                                   },
                                   {
                                     "x": "967",
                                     "y": "444"
                                   },
                                   {
                                     "x": "967",
                                     "y": "358"
                                   }
                                 ]
                               },
                               "text": "Page",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1014",
                                     "y": "358"
                                   },
                                   {
                                     "x": "1014",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1278",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1278",
                                     "y": "358"
                                   }
                                 ]
                               },
                               "text": "№1,",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1303",
                                     "y": "358"
                                   },
                                   {
                                     "x": "1303",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "358"
                                   }
                                 ]
                               },
                               "text": "line",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1765",
                                     "y": "358"
                                   },
                                   {
                                     "x": "1765",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "358"
                                   }
                                 ]
                               },
                               "text": "1",
                               "entity_index": "-1"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "bounding_box": {
                         "vertices": [
                           {
                             "x": "373",
                             "y": "520"
                           },
                           {
                             "x": "373",
                             "y": "580"
                           },
                           {
                             "x": "1836",
                             "y": "580"
                           },
                           {
                             "x": "1836",
                             "y": "520"
                           }
                         ]
                       },
                       "alternatives": [
                         {
                           "text": "Page №1, line 2",
                           "words": [
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "373",
                                     "y": "508"
                                   },
                                   {
                                     "x": "373",
                                     "y": "594"
                                   },
                                   {
                                     "x": "967",
                                     "y": "594"
                                   },
                                   {
                                     "x": "967",
                                     "y": "508"
                                   }
                                 ]
                               },
                               "text": "Page",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1014",
                                     "y": "507"
                                   },
                                   {
                                     "x": "1014",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1277",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1277",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "№1,",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1302",
                                     "y": "507"
                                   },
                                   {
                                     "x": "1302",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "line",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1765",
                                     "y": "507"
                                   },
                                   {
                                     "x": "1765",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "2",
                               "entity_index": "-1"
                             }
                           ]
                         }
                       ]
                     }
                   ],
                   "languages": [
                     {
                       "language_code": "ru"
                     }
                   ]
                 }
               ],
               "entities": []
             },
             "page": "0"
           }
         }
         {
           "result": {
             "text_annotation": {
               "width": "3312",
               "height": "4683",
               "blocks": [
                 {
                   "bounding_box": {
                     "vertices": [
                       {
                         "x": "371",
                         "y": "371"
                       },
                       {
                         "x": "371",
                         "y": "580"
                       },
                       {
                         "x": "1836",
                         "y": "580"
                       },
                       {
                         "x": "1836",
                         "y": "371"
                       }
                     ]
                   },
                   "lines": [
                     {
                       "bounding_box": {
                         "vertices": [
                           {
                             "x": "371",
                             "y": "371"
                           },
                           {
                             "x": "371",
                             "y": "430"
                           },
                           {
                             "x": "1820",
                             "y": "430"
                           },
                           {
                             "x": "1820",
                             "y": "371"
                           }
                         ]
                       },
                       "alternatives": [
                         {
                           "text": "Page №2, line 1",
                           "words": [
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "371",
                                     "y": "357"
                                   },
                                   {
                                     "x": "371",
                                     "y": "444"
                                   },
                                   {
                                     "x": "964",
                                     "y": "444"
                                   },
                                   {
                                     "x": "964",
                                     "y": "357"
                                   }
                                 ]
                               },
                               "text": "Page",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "993",
                                     "y": "357"
                                   },
                                   {
                                     "x": "993",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1292",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1292",
                                     "y": "357"
                                   }
                                 ]
                               },
                               "text": "№2,",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1317",
                                     "y": "357"
                                   },
                                   {
                                     "x": "1317",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1701",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1701",
                                     "y": "357"
                                   }
                                 ]
                               },
                               "text": "line",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1748",
                                     "y": "357"
                                   },
                                   {
                                     "x": "1748",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1820",
                                     "y": "444"
                                   },
                                   {
                                     "x": "1820",
                                     "y": "357"
                                   }
                                 ]
                               },
                               "text": "1",
                               "entity_index": "-1"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "bounding_box": {
                         "vertices": [
                           {
                             "x": "373",
                             "y": "520"
                           },
                           {
                             "x": "373",
                             "y": "580"
                           },
                           {
                             "x": "1836",
                             "y": "580"
                           },
                           {
                             "x": "1836",
                             "y": "520"
                           }
                         ]
                       },
                       "alternatives": [
                         {
                           "text": "Page №2, line 2",
                           "words": [
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "373",
                                     "y": "507"
                                   },
                                   {
                                     "x": "373",
                                     "y": "594"
                                   },
                                   {
                                     "x": "967",
                                     "y": "594"
                                   },
                                   {
                                     "x": "967",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "Page",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1014",
                                     "y": "507"
                                   },
                                   {
                                     "x": "1014",
                                     "y": "594"
                                   },
                                   {
                                     "x": "1277",
                                     "y": "594"
                                   },
                                   {
                                     "x": "1277",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "№2,",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1302",
                                     "y": "507"
                                   },
                                   {
                                     "x": "1302",
                                     "y": "594"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "594"
                                   },
                                   {
                                     "x": "1718",
                                     "y": "507"
                                   }
                                 ]
                               },
                               "text": "line",
                               "entity_index": "-1"
                             },
                             {
                               "bounding_box": {
                                 "vertices": [
                                   {
                                     "x": "1765",
                                     "y": "506"
                                   },
                                   {
                                     "x": "1765",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "593"
                                   },
                                   {
                                     "x": "1836",
                                     "y": "506"
                                   }
                                 ]
                               },
                               "text": "2",
                               "entity_index": "-1"
                             }
                           ]
                         }
                       ]
                     }
                   ],
                   "languages": [
                     {
                       "language_code": "ru"
                     }
                   ]
                 }
               ],
               "entities": []
             },
             "page": "1"
           }
         }
         ```

         {% endcut %}

   {% endlist %}

1. To get the recognized words from the PDF file, find all the values with the `text` property.

{% include [coordinate-definition-issue-note](../../../_includes/vision/coordinate-definition-issue-note.md) %}


## Recognizing text from a PDF file through the Vision API {#vision-api-recognition}

Text recognition from a PDF file is implemented in the [batchAnalyze](../../vision/api-ref/Vision/batchAnalyze.md) Vision API method.

1. The PDF file must contain up to 8 pages. If there are more pages, split it into files with 8 pages or less.
1. Encode the PDF file as Base64:

   {% include [base64-encode-command](../../../_includes/vision/base64-encode-command-pdf.md) %}

1. Create a file with the request body (for example, `body.json`).

   **body.json:**
   ```json
   {
       "folderId": "<folder_ID>",
       "analyze_specs": [{
           "content": "<base64-encoded_PDF_file>",
           "mime_type": "application/pdf",
           "features": [{
               "type": "TEXT_DETECTION",
               "text_detection_config": {
                   "language_codes": ["*"]
               }
           }]
       }]
   }
   ```

   Where:
   * `folderId`: [ID of any folder](../../../resource-manager/operations/folder/get-id.md) for which your account has the `{{ roles-vision-user }}` role or higher.
   * `content`: PDF file contents [encoded](../base64-encode.md) as Base64.

1. {% include [send-request](../../../_includes/vision/send-request.md) %}

1. To get all the recognized words from the image, find all the lines with the `text` property, e.g., using the [grep](https://www.gnu.org/software/grep/) utility:

   {% list tabs %}

   - Bash

      ```bash
      grep -o "\"text\":\s\".*\"" output.json
      ```

      Result:

      ```text
      "text": "PENGUINS"
      "text": "CROSSING"
      "text": "SLOW"
      ```

   - CMD

      ```bash
      findstr text output.json
      ```

      Result:

      ```text
      "text": "PENGUINS"
      "text": "CROSSING"
      "text": "SLOW"
      ```

   - PowerShell

      ```powershell
      Select-String -Pattern '\"text\":\s\".*\"' -Path .\output.json
      ```

      Result:

      ```text
      output.json:1:      "text": "PENGUINS"
      output.json:2:      "text": "CROSSING"
      output.json:3:      "text": "SLOW"
      ```

   {% endlist %}