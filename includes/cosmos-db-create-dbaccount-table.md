---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 941e2ee7b8eba957d970998324156c50a86439af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94504144"
---
1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューで、 **[リソースの作成]** を選択します。
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Azure portal でリソースを作成する":::
   
3. **[新規]** ページで、 **[データベース]**  >  **[Azure Cosmos DB]** の順に選択します。
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Azure portal の [データベース] ウィンドウ":::
   
3. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    設定|値|説明
    ---|---|---
    サブスクリプション|該当するサブスクリプション|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|**[新規作成]** 、アカウント名|**[新規作成]** を選択します。 その後、自分のアカウントの新しいリソース グループの名前を入力します。 簡略化のため、Azure Cosmos DB アカウント名と同じ名前を使用します。 
    アカウント名|一意の名前|自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。<br><br>アカウント名に使用できるのは、小文字、数字、ハイフン (-) のみで、長さは 3 文字から 31 文字の範囲にする必要があります。
    API|テーブル|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、5 種類の API が用意されています。ドキュメント データベース用の Core (SQL)、グラフ データベース用の Gremlin、ドキュメント データベース用の MongoDB、Azure Table、Cassandra です。 API ごとに別のアカウントを作成する必要があります。 <br><br>このクイックスタートでは Table API で動作するテーブルを作成するので、**Azure Table** を選択します。 <br><br>[Table API の詳細](../articles/cosmos-db/table-introduction.md)|
    場所|ユーザーに最も近いリージョン|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。
    容量モード|プロビジョニング スループットまたはサーバーレス|[プロビジョニング スループット](../articles/cosmos-db/set-throughput.md) モードでアカウントを作成するには、 **[Provisioned throughput]\(プロビジョニング スループット\)** を選択します。 [サーバーレス](../articles/cosmos-db/serverless.md) モードでアカウントを作成するには、 **[サーバーレス]** を選択します。

    追加の料金を回避するために、 **[geo 冗長性]** オプションと **[マルチ リージョン書き込み]** オプションを **[無効]** のままにし、 **[ネットワーク]** および **[タグ]** セクションをスキップすることができます。

5. **[確認および作成]** を選択します。 検証が完了したら、 **[作成]** を選択してアカウントを作成します。 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="Azure Cosmos DB の新しいアカウント ページ":::

6. アカウントの作成には数分かかります。 "**デプロイが進行中です**" というメッセージが表示されます。 デプロイが完了するまで待ってから、 **[リソースに移動]** を選択します。

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Azure portal の通知ウィンドウ":::

