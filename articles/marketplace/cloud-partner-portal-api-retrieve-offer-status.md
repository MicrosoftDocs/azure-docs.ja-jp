---
title: プランの状態の取得 - Azure Marketplace
description: プランの現在の状態を取得するための API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272013"
---
# <a name="retrieve-offer-status"></a>プランの状態の取得

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

プランの現在の状態が取得されます。

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター

|  **名前**       |   **説明**                            |  **データの種類** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | パブリッシャー ID。たとえば、`Contoso`  |     String     |
|  offerId        | プランを一意に識別する GUID      |     String     |
|  api-version    | API の最新バージョン                        |     Date       |
|  |  |

## <a name="header"></a>ヘッダー


|  名前           |  値               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  承認  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>本文の例

### <a name="response"></a>Response

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
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
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>応答本文のプロパティ

|  **名前**             |    **説明**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | プランの状態。 使用可能な値の一覧については、下の「[オファーの状態](#offer-status)」を参照してください。 |
|  messages             | プランに関連付けられている一連のメッセージ                                                    |
|  steps                | プラン発行時にプランが受ける一連の手順                      |
|  estimatedTimeFrame   | この手順の完了に必要な時間の見積もり。わかりやすい形式                       |
|  id                   | 手順の識別子                                                                         |
|  stepName             | 手順の名前                                                                               |
|  description          | 手順の説明                                                                        |
|  status               | 手順の状態。 使用可能な値の一覧については、下の「[手順の状態](#step-status)」を参照してください。    |
|  messages             | 手順に関連付けられている一連のメッセージ                                                          |
|  processPercentage    | 手順の完了率                                                              |
|  previewLinks         | *現在、実装されていません*                                                                    |
|  liveLinks            | *現在、実装されていません*                                                                    |
|  notificationEmails   | パートナー センターに移行されるプランでは非推奨となりました。 移行されるプランの通知メールは、[アカウント設定] の販売者の連絡先情報で指定された電子メールに送信されます。<br><br>移行されないプランの場合、操作の進捗状況を通知するメール アドレスのコンマ区切りの一覧です        |
|  |  |

### <a name="response-status-codes"></a>応答状態コード

| **コード** |   **説明**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - 要求が正常に処理され、プランの現在の状態が返されました。 |
|  400     | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                 |
|  404     | `Not found` - 指定のエンティティが存在しません。                                                |
|  |  |

### <a name="offer-status"></a>オファーの状態

|  **名前**                    |    **説明**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | オファーは発行されていません。                          |
|  NotStarted                  | 新しいオファーですが、開始されていません。                            |
|  WaitingForPublisherReview   | オファーは発行元の承認を待っています。                 |
|  実行中                     | オファー提出が処理されています。                     |
|  成功                   | オファー提出の処理が完了しています。               |
|  Canceled                    | オファー提出が取り消されました。                           |
|  失敗                      | オファーを提出できませんでした。                                 |
|  |  |

### <a name="step-status"></a>手順の状態

|  **名前**                    |    **説明**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | 手順が開始されていません。                        |
|  InProgress                  | 手順を実行しています。                             |
|  WaitingForPublisherReview   | 手順は発行元の承認を待っています。      |
|  WaitingForApproval          | 手順はプロセスの承認を待っています。        |
|  Blocked                     | 手順はブロックされています。                             |
|  拒否                    | 手順は拒否されています。                            |
|  完了                    | 手順は完了しました。                            |
|  Canceled                    | 手順は取り消されました。                           |
|  |  |
