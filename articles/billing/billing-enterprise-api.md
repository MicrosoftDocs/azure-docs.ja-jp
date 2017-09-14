---
title: Azure Billing Enterprise API |Microsoft Docs
description: "Enterprise Azure の顧客がプログラムで消費データを取り出せるようにする Reporting API について説明します。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: e3a5f9bcd6b54a51c29df649f1ae8ac185b153a1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>企業ユーザー向けの Reporting API の概要
Reporting API を使用すると、Enterprise Azure の顧客はプログラムで消費量および課金データを希望のデータ分析ツールに取り出すことができます。 

## <a name="enabling-data-access-to-the-api"></a>API へのデータ アクセス
* **API キーの生成または取得** - エンタープライズ ポータルにログインして、「ヘルプ - Reporting API」のチュートリアルに従います。 このヘルプ記事の最初のセクションで、指定した加入契約に対応する API キーの生成方法や取得方法を説明しています。
* **API でのキーのパス**-呼び出しごとに API キーを渡して認証と承認を受ける必要があります。 次のプロパティは、HTTP ヘッダーに置かれている必要があります。

|Request Header Key | 値|
|-|-|
|承認| 次の形式で値を指定します:**bearer {API_KEY}** <br/> 例: bearer eyr....09|

## <a name="consumption-apis"></a>Consumption API
下記の API では、[こちら](https://consumption.azure.com/swagger/ui/index)の Swagger エンドポイントを使用できます。これによって、API のイントロスペクションが容易になり、[AutoRest](https://github.com/Azure/AutoRest) または [Swagger CodeGen](http://swagger.io/swagger-codegen/) を使用したクライアント SDK の生成機能が有効になります。 2014 年 5 月 1 日以降のデータは、この API で使用できます。 

* **Balance and Summary** - [Balance and Summary API](billing-enterprise-api-balance-summary.md) を使用すると、残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーが提供されます。

* **Usage Details** - [Usage Detail API](billing-enterprise-api-usage-detail.md) を使用すると、毎日の消費量の内訳と加入契約別の推定料金が提示されます。 この結果には、インスタンス、測定、および部署に関する情報も含まれています。 この API では、請求期間または指定された開始日と終了日によってクエリを実行できます。 

* **Marketplace Store Charge** - [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) を使用すると、指定された請求期間または開始日と終了日 (1 回払いの料金は含まず) の使用量に基づく marketplace 料金の日別内訳が返されます。

* **Price Sheet** - [Price Sheet API](billing-enterprise-api-pricesheet.md) を使用すると、指定された加入契約と請求期間に対する各測定に適用可能な課金率が提供されます。 

## <a name="helper-apis"></a>Helper API
 **請求期間の一覧表示** - [Billing Periods API](billing-enterprise-api-billing-periods.md) を使用すると、指定された加入契約の消費量データが含まれる請求期間の一覧が逆時系列順に返されます。 各期間には、BalanceSummary、UsageDetails、Marketplace Charges および Price Sheet の 4 セットのデータの API ルートを示すプロパティが含まれています。


## <a name="api-response-codes"></a>API 応答コード  
|応答の状態コード|メッセージ|説明|
|-|-|-|
|200| OK|エラーなし|
|401| 権限がありません| API キーが検出されない、正しくない、有効期限が切れている、など|
|404| 使用不可| レポートのエンドポイントが見つからない|
|400| 正しくない要求| 無効なパラメーター – 日付範囲、EA 番号など|
|500| サーバー エラー| 要求の処理中に予期しないエラーが発生した| 










