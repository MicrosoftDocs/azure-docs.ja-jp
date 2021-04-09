---
title: リソースとクォータを管理する
titleSuffix: Azure Purview
description: Azure Purview のリソースにおけるクォータと制限、およびクォータの引き上げを要求する方法について説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938836"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Azure Purview を使用する、リソースのクォータの管理と引き上げ
 
Azure Purview はデータ ユーザーが使用するクラウド サービスです。 Azure Purview を使用して、クラウド環境とオンプレミス環境の両方にまたがる、データ資産全体のデータ ガバナンスを一元的に管理します。 ビジネス アナリストは、このサービスを利用し、意味のあるビジネス用語を使用して、関連性のあるデータを検索できます。 サブスクリプションの上限まで制限を引き上げるには、サポートにお問い合わせください。
 
## <a name="azure-purview-limits"></a>Azure Purview の制限
 
|**リソース**|  **既定の制限**  |**上限**|
|---|---|---|
|リージョンあたり、テナントあたりの Purview アカウント数 (すべてのサブスクリプションを結合)|3|サポートに問い合わせる|
|アカウントあたりの、スキャンに使用できる仮想コア数*|160|160|
|特定時点でのアカウントあたりの同時実行スキャン数。 この制限は、スキャンされるデータ ソースの種類に基づきます。*|5 | 10 |
|スキャンを実行できる最大時間|7 日|7 日|
|アカウントあたりの API 呼び出し数|4 個の容量ユニットのプラットフォーム サイズでは、1 月あたり 10 M API <br>16 個の容量ユニットのプラットフォーム サイズでは、1 月あたり 40 M API |4 個の容量ユニットのプラットフォーム サイズでは、1 月あたり 10 M API <br>16 個の容量ユニットのプラットフォーム サイズでは、1 月あたり 40 M API|
|アカウントあたりのストレージ|4 個の容量ユニットのプラットフォーム サイズでは 10 GB <br>16 個の容量ユニットのプラットフォーム サイズでは 40 GB |4 個の容量ユニットのプラットフォーム サイズでは 10 GB <br> 16 個の容量ユニットのプラットフォーム サイズでは 40 GB |
|アカウントあたりの資産のサイズ|100 M の物理資産 |サポートに問い合わせる|
|カタログ内の資産の最大サイズ|2 MB|2 MB|
|資産名と分類名の最大長|4 KB|4 KB|
|資産プロパティの名前と値の最大長|32 KB|32 KB|
|分類属性の名前と値の最大長|32 KB|32 KB|
|用語集の用語の最大数 (アカウントあたり)|100K|100K|
 
\* セルフホステッド統合ランタイムのシナリオは、上記の表で定義されている制限の範囲外です。 
 
## <a name="next-steps"></a>次のステップ
 
> [!div class="nextstepaction"]
>[チュートリアル: Azure Purview でデータをスキャンする](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[チュートリアル: ホーム ページを閲覧して資産を検索する](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[チュートリアル: 資産の参照とその系列の表示](tutorial-browse-and-view-lineage.md)
