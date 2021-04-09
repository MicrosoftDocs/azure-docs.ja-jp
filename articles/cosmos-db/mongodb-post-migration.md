---
title: Azure Cosmos DB の MongoDB 用 API での移行後の最適化手順
description: このドキュメントでは、MongoDB から Azure Cosmos DB の MongoDB 用 API に移行した後の最適化手法について説明します。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/14/2021
ms.author: chrande
ms.openlocfilehash: ce0facaba43e9cc8e37b19fbd3dfc840067d278f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560397"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API 使用時の移行後の最適化手順
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB データベースに格納されているデータを Azure Cosmos DB の MongoDB 用 API に移行した後、Azure Cosmos DB に接続してデータを管理できます。 このガイドでは、移行後に考慮すべき手順を提供します。 移行手順については、[MongoDB を Azure Cosmos DB の MongoDB 用 API に移行するためのチュートリアル](../dms/tutorial-mongodb-cosmos-db.md)をご覧ください。

このガイドでは、以下の方法について説明します。

- [アプリケーションを接続する](#connect-your-application)
- [インデックス作成ポリシーを最適化する](#optimize-the-indexing-policy)
- [Azure Cosmos DB の MongoDB 用 API のグローバル配布を構成する](#globally-distribute-your-data)
- [整合性レベルを設定する](#set-consistency-level)

> [!NOTE]
> アプリケーション レベルの唯一必須の移行後手順は、新しい Azure Cosmos DB アカウントを指すようにアプリケーションの接続文字列を変更することです。 他の移行手順はすべて推奨の最適化項目です。
>

## <a name="connect-your-application"></a>アプリケーションを接続する

1. 新しいウィンドウで、[Azure portal](https://www.portal.azure.com/) にサインインします。
2. [Azure portal](https://www.portal.azure.com/) の左側のウィンドウで **[すべてのリソース]** メニューを開き、データを移行した Azure Cosmos DB アカウントを見つけます。
3. **[接続文字列]** ブレードを開きます。 右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。
4. アプリケーションの構成 (または、その他の関連する場所) で接続情報を使用し、アプリで Azure Cosmos DB の MongoDB 用 API 接続を反映させます。
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="接続文字列の設定が示されているスクリーンショット。":::

詳細については、「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」ページを参照してください。

## <a name="optimize-the-indexing-policy"></a>インデックス作成ポリシーを最適化する

すべてのデータ フィールドのインデックスは、Azure Cosmos DB にデータを移行中に、既定で自動的に作成されます。 多くの場合、この既定のインデックス作成ポリシーは許容されます。 一般的に、インデックスを削除すると書き込み要求が最適化され、既定のインデックス作成ポリシー (インデックス自動作成など) を取得すると読み取り要求が最適化されます。

インデックス作成の詳細については、[Azure Cosmos DB の MongoDB 用 API のデータ インデックス作成](mongodb-indexing.md)に関するページと [Azure Cosmos DB のインデックス作成](index-overview.md)に関するページを参照してください。

## <a name="globally-distribute-your-data"></a>データをグローバルに分散する

Azure Cosmos DB は世界中のすべての [Azure リージョン](https://azure.microsoft.com/regions/#services)で利用できます。 Azure Cosmos DB アカウントに既定の一貫性レベルを選択したら、(グローバル配信の必要性に基づいて) 1 つまたは複数の Azure リージョンを関連付けることができます。 高可用性とビジネス継続性のために、少なくとも 2 つのリージョンで実行することを常にお勧めします。 Azure Cosmos DB でマルチリージョン デプロイのコストを最適化するためのヒントは[こちら](optimize-cost-regions.md)でご確認いただけます。

データをグローバルに分散する方法については、[Azure Cosmos DB の MongoDB 用 API へのデータのグローバル分散](tutorial-global-distribution-mongodb.md)に関するページをご覧ください。

## <a name="set-consistency-level"></a>整合性レベルを設定する

Azure Cosmos DB では、5 つの[整合性レベル](consistency-levels.md)が明確に定義されています。 MongoDB と Azure Cosmos DB の各種整合性レベル間でマッピングする方法については、「[整合性レベルと Azure Cosmos DB API](./consistency-levels.md)」を参照してください。 既定の整合性レベルは、セッション整合性レベルです。 整合性レベルの変更は任意であり、アプリに合わせて最適なレベルを選択できます。 Azure portal を使用して整合性レベルを変更する方法:

1. [設定] の **[既定の整合性]** に進みます。
2. [整合性レベル](consistency-levels.md)を選択します。

ほとんどのユーザーは、整合性レベルを既定のセッション整合性設定のままにします。 しかしながら、[さまざまな整合性レベルでの可用性とパフォーマンスのトレードオフ](./consistency-levels.md)があります。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)
* [Studio 3T を使用して Azure Cosmos DB アカウントに接続する](mongodb-mongochef.md)
* [Azure Cosmos DB の MongoDB 用 API を使用して読み取りをグローバルに配布する方法](mongodb-readpreference.md)
* [Azure Cosmos DB の MongoDB 用 API でデータを期限切れにする](mongodb-time-to-live.md)
* [Azure Cosmos DB の整合性レベル](consistency-levels.md)
* [Azure Cosmos DB のインデックス作成](index-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)