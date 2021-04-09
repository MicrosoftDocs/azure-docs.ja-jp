---
title: ナレッジ ストアでのプロジェクションを定義する
titleSuffix: Azure Cognitive Search
description: Power BI または Azure ML で使用するために、ナレッジ ストアにエンリッチされたドキュメントを射影する方法について、一般的なパターンの例を示します。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97509922"
---
# <a name="how-to-shape-and-export-enrichments"></a>エンリッチメントを整形してエクスポートする方法

プロジェクションとは、ナレッジ ストア内のエンリッチされたドキュメントの物理表現です。 エンリッチされたドキュメントを効果的に使用するには、構造が必要になります。 この記事では、構造とリレーションシップの両方について取り上げ、プロジェクションのプロパティを構築する方法と、作成したプロジェクションの種類間でデータを関連付ける方法について説明します。 

プロジェクションを作成するには、[Shaper スキル](cognitive-search-skill-shaper.md)を使用してカスタム オブジェクトを作成するか、またはプロジェクション定義内でインライン整形の構文を使用して、データを整形します。 

1 つのデータ シェイプは、射影する対象データをすべて含み、ノードの階層として形成されます。 この記事では、レポート、分析、ダウンストリーム処理に役立つ物理構造に射影できるようデータを整形するための手法をいくつか説明します。 

この記事で取り上げる例は、こちらの [REST API サンプル](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)にあります。ダウンロードして、HTTP クライアントで実行することができます。

## <a name="introduction-to-projection-examples"></a>プロジェクションの例の概要

[プロジェクション](knowledge-store-projection-overview.md)には、次の 3 つの種類があります。

+ テーブル
+ Objects
+ ファイル

テーブル プロジェクションは、Azure Table Storage に格納されます。 オブジェクトおよびファイル プロジェクションは Blob Storage に書き込まれます。オブジェクト プロジェクションは JSON ファイルとして保存され、ソース ドキュメントの内容のほか、あらゆるスキル出力やエンリッチメントを含むことができます。 エンリッチメント パイプラインでは、画像などのバイナリを抽出することもできます。これらのバイナリは、ファイル プロジェクションとして射影されます。 バイナリ オブジェクトがオブジェクト プロジェクションとして射影された場合、関連付けられているメタデータのみが JSON BLOB として保存されます。 

データ シェイプとプロジェクションの共通部分を理解するために、基礎として次のスキルセットを使用して、各種の構成を確認します。 このスキルセットでは、未加工の画像とテキスト コンテンツが処理されます。 プロジェクションは、目的のシナリオに対して、ドキュメントのコンテンツとスキルの出力から定義されます。

> [!IMPORTANT] 
> プロジェクションを試すときは、確実にコスト管理できるように、[インデクサー キャッシュ プロパティを設定](search-howto-incremental-index.md)すると便利です。 インデクサー キャッシュが設定されていない場合、プロジェクションを編集すると、ドキュメント全体が再度エンリッチされます。 キャッシュが設定されていて、そのプロジェクションだけが更新された場合、以前にエンリッチされたドキュメントに対してスキルセットが実行されて新しい Cognitive Services の料金が発生することはありません。

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

このスキルセットを使用した場合、null に設定された `knowledgeStore` を基に、最初の例では、`knowledgeStore` オブジェクトの内容を設定します。これは、他のシナリオで使用できる表形式データ構造を作成するプロジェクションを使って構成します。 

## <a name="projecting-to-tables"></a>テーブルへの射影

Azure Storage 内のテーブルへの射影は、Power BI などのツールを使ったレポート作成や分析に役立ちます。 Power BI は、テーブルからの読み取りを行い、プロジェクション時に生成されたキーに基づくリレーションシップを検出できます。 ダッシュボードを作成する場合、関連したデータがあれば、そのタスクが簡単になります。 

ドキュメントから抽出したキー フレーズをワード クラウドとして視覚化するダッシュボードを作成してみましょう。 適切なデータ構造を作成するには、Shaper スキルをスキルセットに追加して、ドキュメントに固有の詳細やキー フレーズを含むカスタム シェイプを作成します。 このカスタム シェイプは `pbiShape` という名前で、`document` ルート ノードに存在します。

> [!NOTE] 
> テーブル プロジェクションは Azure Storage テーブルであり、Azure Storage によって課せられた容量の制限で制御されています。 詳細については、[Table Storage の制限](/rest/api/storageservices/understanding-the-table-service-data-model)に関するページを参照してください。 エンティティ サイズは 1 MB を超えることはできず、また、単一のプロパティは 64 KB を超えられないことを知っておくと便利です。 これらの制約によって、テーブルは、多数の小さなエンティティの格納に適したソリューションになっています。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Shaper スキルを使用してカスタム シェイプを作成する

テーブル ストレージに射影できるカスタム シェイプを作成します。 カスタム シェイプがない場合、1 つのプロジェクションでは単一のノード (出力ごとに 1 つのプロジェクション) しか参照できません。 カスタム シェイプを作成することで、さまざまな要素を新しい論理的なまとまりに集約して、それを単一のテーブルとして射影したり、テーブルのコレクション全体にスライスおよび分散したりできます。 

この例では、カスタム シェイプによって、メタデータと識別されたエンティティおよびキー フレーズが結合されています。 オブジェクトは `pbiShape` と呼ばれ、`/document` の下に作成されます。 

> [!IMPORTANT] 
> 整形の目的は、すべてのエンリッチメント ノードが適切な形式の JSON で表されるようにすることです。これは、ナレッジ ストアへの射影に必要です。 これは特に、エンリッチメント ツリーに含まれるノードが適切な形式の JSON ではないときに当てはまります (たとえば、エンリッチメントが、文字列などのプリミティブ型の親である場合)。
>
> 最後の 2 つのノード (`KeyPhrases` および `Entities`) に注目してください。 これらは、`sourceContext` を使用して、有効な JSON オブジェクトにラップされています。 これが必要なのは、`keyphrases` と `entities` がプリミティブ型に対するエンリッチメントであり、射影できるようにするには、事前に有効な JSON に変換しておく必要があるためです。
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

前出の Shaper スキルをスキルセットに追加します。 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

テーブルに射影するために必要なすべてのデータを用意できたので、テーブル定義によって knowledgeStore オブジェクトを更新します。 この例では、`tableName`、`source`、`generatedKeyName` の各プロパティを設定することによって定義された 3 つのテーブルがあります。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

次の手順に従って作業を進めることができます。

1. ```storageConnectionString``` プロパティに有効な V2 汎用ストレージ アカウント接続文字列を設定します。  

1. PUT 要求を発行してスキルセットを更新します。

1. スキルセットの更新後、インデクサーを実行します。 

これで、3 つのテーブルを含む有効なプロジェクションができました。 これらのテーブルを Power BI にインポートすると、Power BI ではリレーションシップが自動的に検出されます。

次の例に進む前に、データのスライスと関連付けのメカニズムを理解するために、テーブル プロジェクションの特徴を振り返ってみましょう。

### <a name="slicing"></a>スライス 

スライスは、統合されたシェイプ全体を細かな構成要素に分割する手法です。 その結果、互いに独立しながら関連するテーブルが作成されます。これらのテーブルは個別に編集することができます。

この例では、`pbiShape`が統合されたシェイプ (エンリッチメント ノード) です。 プロジェクションの定義では、`pbiShape` が追加のテーブルにスライスされます。これによって、シェイプの構成要素である ```keyPhrases``` と ```Entities``` を取り出すことができます。 これは Power BI に適した形と言えます。それぞれのドキュメントには複数のエンティティとキーフレーズが関連付けられており、分類されたデータとしてエンティティとキーフレーズを見ることができれば、得られる分析情報も多くなるからです。

スライスの際には、親テーブル内の ```generatedKeyName``` を使用して子テーブル内に同じ名前の列を作成することで、親テーブルと子テーブルの間のリレーションシップが暗黙的に生成されます。 

### <a name="naming-relationships"></a>リレーションシップの名前付け

テーブル間で、またはプロジェクションの種類間であっても、データを関連付けるためには ```generatedKeyName``` および ```referenceKeyName``` プロパティが使用されます。 子テーブル/プロジェクション内の各行には、親を参照し返すプロパティが含まれています。 子での列またはプロパティの名前は、親からの ```referenceKeyName``` です。 ```referenceKeyName``` が指定されていない場合、サービスでは、親からの ```generatedKeyName``` を既定値とします。 

Power BI では、これらの生成されたキーを利用して、テーブル内のリレーションシップを検出します。 子テーブル内の列に別の名前を付ける必要がある場合は、親テーブル上に ```referenceKeyName``` プロパティを設定します。 例として、pbiDocument テーブル上で ```generatedKeyName``` を ID として設定し、```referenceKeyName``` を DocumentID として設定します。 これにより、pbiEntities テーブルと pbiKeyPhrases テーブルに、ドキュメント ID を含む DocumentID という名前の列が生成されます。

## <a name="projecting-to-objects"></a>オブジェクトへの射影

オブジェクト プロジェクションにはテーブル プロジェクションと同じ制限事項はなく、大きなドキュメントを射影する場合により適しています。 この例では、ドキュメント全体がオブジェクト プロジェクションとして送信されます。 オブジェクト プロジェクションは、1 つのコンテナー内では単一のプロジェクションに限定され、スライスすることはできません。

オブジェクト プロジェクションを定義するには、プロジェクション内で ```objects``` 配列を使用します。 Shaper スキルを使用して新しいシェイプを生成するか、またはオブジェクト プロジェクションのインライン整形を使用することができます。 テーブルの例ではシェイプの作成とスライスの手法を示しましたが、この例ではインライン整形の使用を例示します。 

インライン整形とは、プロジェクションへの入力の定義内で新しいシェイプを作成する機能です。 インライン整形では、Shaper スキルによって生成されるもの (この場合は `pbiShape`) と同じ匿名のオブジェクトが作成されます。 再利用する予定がないシェイプを定義する場合は、インライン整形が便利です。

projections プロパティは配列です。 この例では、新しいプロジェクション インスタンスを配列に追加しています。そこでは、インライン プロジェクションが knowledgeStore の定義に含まれています。 インライン プロジェクションを使用するときは、Shaper スキルを省略することができます。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>ファイルへの射影

ファイル プロジェクションは、ソース ドキュメントから抽出された画像か、エンリッチメント プロセスから射影できるエンリッチメントの出力となる画像です。 オブジェクト プロジェクションと同様に、ファイル プロジェクションは Azure Storage の BLOB として実装され、画像を格納します。 

ファイル プロジェクションを生成するには、プロジェクション オブジェクト内で `files` 配列を使用します。 この例では、ドキュメントから抽出されたすべての画像を `samplefile` というコンテナーに射影します。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>複数の種類への射影

シナリオが複雑になると、プロジェクションの種類間でコンテンツを射影する必要が生じる場合があります。 たとえば、キー フレーズやエンティティなどのデータをテーブルに射影する必要がある場合は、テキストとレイアウト テキストの OCR 結果をオブジェクトとして保存して、画像をファイルとして射影します。 

この例では、次のように変更してスキルセットを更新します。

1. ドキュメントごとに、1 行を含む 1 つのテーブルが作成されます。
1. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各キー フレーズがこのテーブル内の 1 行として識別されます。
1. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各エンティティがこのテーブル内の 1 行として識別されます。
1. 画像ごとにレイアウト テキストを含むオブジェクト プロジェクションが作成されます。
1. ファイル プロジェクションが作成され、抽出された各画像が射影されます。
1. ドキュメント テーブル、レイアウト テキストを含むオブジェクト プロジェクション、およびファイル プロジェクションへの参照を含む相互参照テーブルが作成されます。

これらの変更は、knowledgeStore の定義にも反映されます。 

### <a name="shape-data-for-cross-projection"></a>クロスプロジェクション用のデータの整形

これらのプロジェクションに必要なシェイプを取得するには、まず、`crossProjection` という整形されたオブジェクトを作成する新しい Shaper スキルを追加します。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>テーブル、オブジェクト、およびファイル プロジェクションの定義

統合された crossProjection オブジェクトから、オブジェクトを複数のテーブルにスライスし、OCR 出力を BLOB としてキャプチャした後、その画像を (Blob Storage 内にも) ファイルとして保存することができます。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

オブジェクト プロジェクションでは、プロジェクションごとにコンテナー名が必要になります。オブジェクト プロジェクションまたはファイル プロジェクションでは、コンテナーを共有することはできません。 

### <a name="relationships-among-table-object-and-file-projections"></a>テーブル、オブジェクト、およびファイル プロジェクションの間のリレーションシップ

この例では、プロジェクションの別の機能についても取り上げます。 同じプロジェクション オブジェクト内に複数の種類のプロジェクションを定義することにより、さまざまな種類 (テーブル、オブジェクト、ファイル) の間のリレーションシップが表現されます。 これにより、ドキュメントのテーブル行から開始して、オブジェクト プロジェクションのそのドキュメント内の画像のすべての OCR テキストを検索できます。 

データを関連付けたくない場合は、別のプロジェクション オブジェクト内でプロジェクションを定義します。 たとえば、次のスニペットでは、テーブルどうしは関連付けられますが、テーブルとオブジェクト (OCR テキスト) プロジェクションの間にリレーションシップはありません。 

異なるニーズに対応するために、異なるシェイプ内に同じデータを射影したい場合は、プロジェクション グループが便利です。 たとえば、1 つは Power BI ダッシュボードのためのプロジェクション グループ、もう 1 つはカスタム スキルにラップされた機械学習モデルのトレーニング用データをキャプチャするためのプロジェクション グループとします。

異なる種類のプロジェクションを構築する場合、ファイル プロジェクションとオブジェクト プロジェクションが最初に生成されてから、テーブルに対してパスが追加されます。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>一般的な問題

プロジェクションを定義するときには、予期しない結果が生じる可能性がある一般的な問題がいくつかあります。 ナレッジ ストアの出力が想定と異なる場合は、これらの問題をチェックしてください。

+ 文字列のエンリッチメントが有効な JSON に整形されていない。 たとえば、キー フレーズによって `merged_content` がエンリッチされるなど、文字列がエンリッチされた場合、エンリッチされたプロパティはエンリッチメント ツリー内で `merged_content` の子として表されます。 既定の表現は、適切な形式の JSON ではありません。 そのため射影時には必ず、名前と値を含む有効な JSON オブジェクトにエンリッチメントを変換してください。

+ ソース パスの末尾にある ```/*``` が省略される。 プロジェクションのソースが `/document/pbiShape/keyPhrases` の場合、キー フレーズの配列は単一のオブジェクトまたは行として射影されます。 ソース パスを `/document/pbiShape/keyPhrases/*` に設定した場合は、キー フレーズごとに単一の行またはオブジェクトが生成されます。

+ パス構文エラー。 パス セレクターでは大文字と小文字が区別されるため、セレクターに対して大文字と小文字を正しく使用していないと、入力が見つからないという警告が表示される場合があります。

## <a name="next-steps"></a>次のステップ

この記事の例では、プロジェクションの作成方法に関する一般的なパターンを紹介しました。 その概念を十分に理解したら、特定のシナリオを視野に入れて、いつでもプロジェクションを作成することができます。

新しい機能を調べる際には、次の手順としてインクリメンタル エンリッチメントを検討します。 インクリメンタル エンリッチメントはキャッシュに基づいており、スキルセットに対する変更の影響を受けないエンリッチメントを再利用することができます。 OCR や画像分析を含んだパイプラインでは、特に有益な手段となります。

> [!div class="nextstepaction"]
> [インクリメンタル エンリッチメントとキャッシュの概要](cognitive-search-incremental-indexing-conceptual.md)

プロジェクションの概要については、グループやスライシングなどの機能と、[スキルセットでそれらを定義する方法](knowledge-store-projection-overview.md)の詳細を参照してください

> [!div class="nextstepaction"]
> [ナレッジ ストアでのプロジェクション](knowledge-store-projection-overview.md)