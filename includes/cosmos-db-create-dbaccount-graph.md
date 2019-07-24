---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 975bb5ee194e7bd9538660878cbed20c943fcf52
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53286181"
---
1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. **[リソースの作成]** > **[データベース]** > **[Azure Cosmos DB]** の順にクリックします。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 

    設定|値|説明
    ---|---|---
    サブスクリプション|該当するサブスクリプション|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|新規作成<br><br>その後、ID で指定したものと同じ一意の名前を入力|**[新規作成]** を選択します。 その後、自分のアカウントの新しいリソースグループの名前を入力します。 簡略化するには、ID と同じ名前を使用します。 
    アカウント名|一意の名前を入力します|自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、一意の ID を使用してください。<br><br>ID に使用できるのは、英小文字、数字、ハイフン (-) のみです。 長さは 3 文字から 31 文字でなければなりません。
    API|Gremlin (グラフ)|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、5 種類の API が用意されています。ドキュメント データベース用の Core(SQL)、グラフ データベース用の Gremlin、ドキュメント データベース用の MongoDB、Azure Table、Cassandra です。 現在は、API ごとに別のアカウントを作成する必要があります。 <br><br>このクイックスタートでは Gremlin API に対応するテーブルを作成するので、**Gremlin (グラフ)** を選択します。 <br><br>[Graph API に関する詳細を参照してください](../articles/cosmos-db/graph-introduction.md)。|
    場所|ユーザーに最も近いリージョンを選択|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。

    **[確認および作成]** を選択します。 **[ネットワーク]** セクションと **[タグ]** セクションはスキップできます。 

    ![Azure Cosmos DB の [新しいアカウント] ブレード](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. アカウントの作成には数分かかります。 ポータルに "**Azure Cosmos DB アカウントが作成されました**" ページが表示されるまで待機します。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)