---
title: Go Live | Microsoft Docs
description: Go Live API は、オファーのライブ一覧表示プロセスを開始します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594680"
---
<a name="go-live"></a>Go Live
=======

この API は、運用環境にアプリをプッシュするためのプロセスを開始します。 この操作には、通常、長い時間がかかります。 この呼び出しでは、[Publish](./cloud-partner-portal-api-publish-offer.md) API 操作の通知メール リストが使用されます。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI パラメーター
--------------

|  **Name**      |   **説明**                                                           | **データの種類** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 取得するオファーの発行元 ID (例: `contoso`)       |  String       |
| offerId        | 取得するオファーのオファー ID                                   |  String       |
| api-version    | API の最新バージョン                                                   |  Date         |
|  |  |  |


<a name="header"></a>ヘッダー
------

|  **Name**       |     **値**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Authorization   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>本文の例
------------

### <a name="response"></a>Response

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>応答ヘッダー

|  **Name**             |      **値**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  操作の現在の状態を確認するためのクエリに使用する URL            |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード** |  **説明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 要求は正常に受け入れられました。 応答には、操作の状態を追跡するための場所が含まれています。 |
|  400     | `Bad/Malformed request` - 追加のエラー情報が応答本文に含まれます。 |
|  404     |  `Not found` - 指定のエンティティが存在しません。                                       |
|  |  |
