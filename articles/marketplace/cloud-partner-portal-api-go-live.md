---
title: Go Live API - Azure Marketplace
description: Go Live API は、オファーのライブ一覧表示プロセスを開始します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87292947"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

この API は、運用環境にアプリをプッシュするためのプロセスを開始します。 この操作には、通常、長い時間がかかります。 この呼び出しでは、[Publish](./cloud-partner-portal-api-publish-offer.md) API 操作の通知メール リストが使用されます。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI パラメーター
--------------

|  **名前**      |   **説明**                                                           | **データの種類** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 取得するオファーの発行元 ID (例: `contoso`)       |  String       |
| offerId        | 取得するオファーのオファー ID                                   |  String       |
| api-version    | API の最新バージョン                                                   |  Date         |
|  |  |  |

## <a name="header"></a>ヘッダー
------

|  **名前**       |     **Value**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 承認   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>本文の例

### <a name="response"></a>Response

#### <a name="migrated-offers"></a>移行されたオファー

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>移行されていないオファー

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>応答ヘッダー

|  **名前**             |      **Value**                                                            |
|  --------             |      ----------                                                           |
| 場所    |  この操作の状態を取得する相対パス            |
|  |  |

### <a name="response-status-codes"></a>応答状態コード

| **コード** |  **説明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 要求は正常に受け入れられました。 応答には、操作の状態を追跡するための場所が含まれています。 |
|  400     | `Bad/Malformed request` - 追加のエラー情報が応答本文に含まれます。 |
|  404     |  `Not found` - 指定のエンティティが存在しません。                                       |
|  |  |
