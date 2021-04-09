---
title: オファーの取得 API - Azure Marketplace
description: パブリッシャーの名前空間に含まれるオファーを要約した一覧を取得するための API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271962"
---
# <a name="retrieve-offers"></a>オファーを取得する

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

パブリッシャーの名前空間に含まれるオファーを要約した一覧を取得します。

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター

| **名前**         |  **説明**                         |  **データの種類** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | パブリッシャー ID。たとえば、`contoso` |   String    |
|  api-version     | API の最新バージョン                    |    Date        |
|  |  |

## <a name="header"></a>ヘッダー

|  **名前**        |         **Value**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  承認   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>本文の例

### <a name="response"></a>Response

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>応答本文のプロパティ

|  **名前**       |       **説明**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | オファーの種類を示します                                                                                           |
|  publisherId    | パブリッシャーを一意に示す ID                                                                      |
|  status         | オファーの状態。 使用可能な値の一覧については、下の「[オファーの状態](#offer-status)」を参照してください。                         |
|  id             | パブリッシャーの名前空間内のオファーを一意に示す GUID。                                                    |
|  version        | オファーの現在のバージョン。 バージョン プロパティはクライアントでは変更できません。 発行のたびにインクリメントされます。 |
|  definition     | ワークロードの実際の定義の要約ビューが含まれます。 詳細な定義を取得するには、[Retrieve specific offer](./cloud-partner-portal-api-retrieve-specific-offer.md) API を使用します。 |
|  changedTime    | オファーが最後に変更されたときの UTC 日時                                                                              |
|  |  |

### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 要求が正常に処理され、発行元のすべてのオファーがクライアントに返されました。  |
|  400      | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                                    |
|  403      | `Forbidden` - クライアントは、指定の名前空間にアクセスできません。                                          |
|  404      | `Not found` - 指定のエンティティが存在しません。                                                                 |
|  |  |

### <a name="offer-status"></a>オファーの状態

|  **名前**                    | **説明**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | オファーは発行されていません。                  |
|  NotStarted                  | 新しいオファーですが、開始されていません。                 |
|  WaitingForPublisherReview   | オファーは発行元の承認を待っています。         |
|  実行中                     | オファー提出が処理されています。             |
|  成功                   | オファー提出の処理が完了しています。       |
|  Canceled                    | オファー提出が取り消されました。                   |
|  失敗                      | オファーを提出できませんでした。                         |
|  |  |
