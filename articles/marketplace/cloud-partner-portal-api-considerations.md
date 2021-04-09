---
title: API の考慮事項 - Azure Marketplace
description: マーケットプレース API を使用する際のバージョン管理、エラー処理、認可の問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035282"
---
# <a name="api-considerations"></a>API の考慮事項

<a name="api-versioning"></a>API のバージョン管理
--------------

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](./cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

API の複数のバージョンが同時に利用可能である可能性があります。 クライアントは、`api-version` パラメーターをクエリ文字列の一部として提供して、起動するバージョンを指定する必要があります。

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

不明または無効な API バージョンが指定された要求に対する応答は、HTTP コード 400 になります。 このエラーは、既知の API バージョンのコレクションを応答本文で返します。

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>エラー
------

API はエラーに応答する際、対応する HTTP 状態コードと、必要に応じて追加情報を、JSON 形式でシリアル化された応答に含めます。
エラー (特に 400 クラスのエラー) が発生したときには、根本的な原因を修正する前に要求を再試行しないようにしてください。 たとえば、上記のサンプル応答では、要求を再送信する前に API バージョンのパラメーターを修正します。

<a name="authorization-header"></a>Authorization header (Authorization ヘッダー)
--------------------

このリファレンスのすべての API では、Azure Active Directory (Azure AD) から取得したベアラー トークンと共に Authorization ヘッダーを渡す必要があります。 有効な応答を受信するにはこのヘッダーが必要です。存在しない場合は、`401 Unauthorized` エラーが返されます。 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
