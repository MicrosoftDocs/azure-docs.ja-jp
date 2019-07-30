---
title: Azure Billing Enterprise API |Microsoft Docs
description: Enterprise Azure の顧客がプログラムで消費データを取り出せるようにする Reporting API について説明します。
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443211"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>企業ユーザー向けの Reporting API の概要
Reporting API を使用すると、Enterprise Azure の顧客はプログラムで消費量および課金データを希望のデータ分析ツールに取り出すことができます。 Enterprise 顧客は、Azure の [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) を締結し、交渉によって年額コミットメントを決定しています。Azure リソースのカスタム価格が提示されます。

## <a name="enabling-data-access-to-the-api"></a>API へのデータ アクセス
* **API キーを取得または生成** - エンタープライズ ポータルにログインし、[レポート] > [使用状況のダウンロード] > [API アクセス キー] に移動して API キーを生成または取得します。
* **API でのキーのパス**-呼び出しごとに API キーを渡して認証と承認を受ける必要があります。 次のプロパティは、HTTP ヘッダーに置かれている必要があります。

|Request Header Key | 値|
|-|-|
|Authorization| 次の形式で値を指定します:**bearer {API_KEY}** <br/> 例: bearer eyr....09| 

## <a name="consumption-apis"></a>Consumption API
下記の API では、[こちら](https://consumption.azure.com/swagger/ui/index)の Swagger エンドポイントを使用できます。これによって、API のイントロスペクションが容易になり、[AutoRest](https://github.com/Azure/AutoRest) または [Swagger CodeGen](https://swagger.io/swagger-codegen/) を使用したクライアント SDK の生成機能が有効になります。 2014 年 5 月 1 日以降のデータは、この API で使用できます。 

* **Balance and Summary** - [Balance and Summary API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) を使用すると、残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーが提供されます。

* **Usage Details** - [Usage Detail API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) を使用すると、毎日の消費量の内訳と加入契約別の推定料金が提示されます。 この結果には、インスタンス、測定、および部署に関する情報も含まれています。 この API では、請求期間または指定された開始日と終了日によってクエリを実行できます。 

* **Marketplace Store Charge** - [Marketplace Store Charge API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) を使用すると、指定された請求期間または開始日と終了日 (1 回払いの料金は含まず) の使用量に基づく marketplace 料金の日別内訳が返されます。

* **Price Sheet** - [Price Sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) を使用すると、指定された加入契約と請求期間に対する各測定に適用可能な課金率が提供されます。

* **Reserved Instance Details** - [Reserved Instance 使用量 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) を使用すると、予約インスタンス購入の使用状況が返されます。 [Reserved Instance 料金 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) を使用すると、行われた課金トランザクションが表示されます。 

## <a name="data-freshness"></a>データの鮮度
上記のすべての API の応答では、Etag が返されます。 Etag の変更は、データが更新されたことを示します。  同じパラメーターを使用した同じ API の後続の呼び出しでは、キャプチャされた Etag と、http 要求のヘッダー内のキー “If-None-Match” を渡します。 データがそれ以上更新されていない場合は、応答の状態コードが "NotModified" になり、データは返されません。 Etag の変更がある場合、API は、要求された期間の完全なデータセットを返します。

## <a name="helper-apis"></a>Helper API
 **請求期間の一覧表示** - [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) を使用すると、指定された加入契約の消費量データが含まれる請求期間の一覧が逆時系列順に返されます。 各期間には、BalanceSummary、UsageDetails、Marketplace Charges および Price Sheet の 4 セットのデータの API ルートを示すプロパティが含まれています。


## <a name="api-response-codes"></a>API 応答コード   
|応答の状態コード|Message|説明|
|-|-|-|
|200| OK|エラーなし|
|401| 権限がありません| API キーが検出されない、正しくない、有効期限が切れている、など|
|404| 使用不可| レポートのエンドポイントが見つからない|
|400| 正しくない要求| 無効なパラメーター – 日付範囲、EA 番号など|
|500| サーバー エラー| 要求の処理中に予期しないエラーが発生した| 









