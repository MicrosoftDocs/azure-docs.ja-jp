---
title: 操作のキャンセル API - Microsoft コマーシャル マーケットプレース
description: 現在プランで進行中の操作をキャンセルするための API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87287924"
---
# <a name="cancel-operation"></a>操作を取り消す

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

この API は、現在プランで進行中の操作をキャンセルします。 この API に渡す `operationId` は、[操作の取得 API](./cloud-partner-portal-api-retrieve-operations.md) を使用して取得します。 キャンセルは同期操作であるのが一般的ですが、一部の複雑なシナリオでは、既存の操作をキャンセルするために新しい操作が必要になることがあります。 このケースでは、状態を照会するために使用すべき操作の場所が HTTP 応答本文に格納されます。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI パラメーター

--------------

|  **名前**    |      **説明**                                  |    **データの種類**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  パブリッシャー ID (例: `contoso`)         |   String          |
| offerId      |  オファー ID                                     |   String          |
| api-version  |  API の現在のバージョン                               |    Date           |
|  |  |  |

## <a name="header"></a>ヘッダー
------

|  **名前**              |  **Value**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  承認         |  Bearer <実際のトークン> |
|  |  |

## <a name="body-example"></a>本文の例
------------

### <a name="request"></a>Request

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>要求本文のプロパティ

|  **名前**                |  **説明**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 発行操作の進捗状況を通知するメール ID の、コンマ区切りの一覧です。 |
|  |  |

### <a name="response"></a>Response

#### <a name="migrated-offers"></a>移行されたオファー

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>移行されていないオファー

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>応答ヘッダー

|  **名前**             |    **Value**                       |
|  ---------            |    ----------                      |
| 場所    | この操作の状態を取得する相対パス。 |
|  |  |

### <a name="response-status-codes"></a>応答状態コード

| **コード**  |  **説明**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK 要求は正常に処理され、操作は同期的にキャンセルされました。 |
|  202      | 受理されました。 要求は正常に処理され、操作のキャンセル処理が行われています。 キャンセル操作の場所は、応答ヘッダーで返されます。 |
|  400      | 要求が無効であるか、形式が正しくありません。 エラーの応答本文にさらに情報が含まれている場合があります。  |
|  403      | アクセスは禁止されています。 クライアントは、要求に指定されている名前空間にアクセスできません。 |
|  404      | 見つかりません。 指定のエンティティが存在しません。 |
|  |  |
