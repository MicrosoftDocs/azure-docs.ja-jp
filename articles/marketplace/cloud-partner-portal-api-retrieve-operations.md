---
title: 操作の取得 API - Azure Marketplace
description: オファーのすべての操作を取得するか、指定した operationId の特定の操作を取得するための API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271945"
---
# <a name="retrieve-operations"></a>操作の取得

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

オファーのすべての操作を取得するか、指定した operationId の特定の操作を取得します。 クライアントは、クエリ パラメーターを使用して実行中の操作にフィルターを適用できます。

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>URI パラメーター

|  **名前**          |      **説明**                                                                                           | **データの種類** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  パブリッシャー ID。たとえば、`Contoso`                                                                   |  String       |
|  offerId           |  オファー ID                                                                                              |  String       |
|  operationId       |  オファーの操作を一意に識別する GUID。 operationId はこの API を使用して取得できます。また、[オファーの発行](./cloud-partner-portal-api-publish-offer.md) API など、実行時間の長い任意の操作に対する応答の HTTP ヘッダーでも返されます。  |   Guid   |
|  api-version       | API の最新バージョン |    Date      |
|  |  |  |

## <a name="header"></a>ヘッダー

|  **名前**          |  **Value**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  承認     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>本文の例

### <a name="response"></a>Response

#### <a name="get-operations"></a>GET 操作

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET 操作

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>応答本文のプロパティ

|  **名前**                    |  **説明**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 操作を一意に識別する GUID                                                       |
|  submissionType              | 報告されているオファーの操作の種類を特定します。たとえば、`Publish/GoLive`      |
|  createdDateTime             | 操作が作成された UTC 日時                                                       |
|  lastActionDateTime          | 操作に対する最終更新の UTC 日時                                       |
|  status                      | 操作の状態。`not started` \| `running` \| `failed` \| `completed` のいずれか。 `running` 状態にできるのは、一度に 1 つの操作だけです。 |
|  error                       | 失敗した操作のエラー メッセージ                                                               |
|  |  |

### <a name="response-step-properties"></a>応答ステップのプロパティ

|  **名前**                    |  **説明**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | この操作の推定所要時間 |
| id | ステップ プロセスの一意識別子 |
| description | 手順の説明 |
| stepName | ステップのフレンドリ名 |
| status | ステップの状態。`notStarted` \| `running` \| `failed` \| `completed` のいずれか |
| messages | ステップ中に発生した通知または警告。 文字列の配列 |
| progressPercentage | ステップの進行状況を示す 0 から 100 までの整数 |
| | |

### <a name="response-status-codes"></a>応答状態コード

| **コード**  |   **説明**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 要求が正常に処理され、要求された操作が返されました。        |
|  400      | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                    |
|  403      | `Forbidden` - クライアントは、指定の名前空間にアクセスできません。                          |
|  404      | `Not found` - 指定のエンティティが存在しません。                                                 |
|  |  |
