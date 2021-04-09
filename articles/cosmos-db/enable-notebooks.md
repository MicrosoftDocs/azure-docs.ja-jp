---
title: Azure Cosmos DB アカウントでノートブックを有効にする (プレビュー)
description: Azure Cosmos DB の組み込みノートブックを使用すると、ポータル内からデータを分析して視覚化することができます。 この記事では、Cosmos アカウントに対してこの機能を有効にする方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692778"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB アカウントのノートブックを有効にする (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 用の組み込みノートブックは、現在、[29 の リージョン](#supported-regions)で使用できます。 ノートブックを使用するには、[新しい Cosmos アカウントを作成する](#create-a-new-cosmos-account)か、これらのリージョンのいずれかの[既存のアカウントでノートブックを有効にします](#enable-notebooks-in-an-existing-cosmos-account)。 

Azure Cosmos DB の組み込みの Jupyter ノートブックを使用すると、Azure portal のデータを分析して視覚化することができます。 この記事では、Azure Cosmos DB アカウントに対してこの機能を有効にする方法について説明します。

## <a name="create-a-new-cosmos-account"></a>新しい Cosmos アカウントを作成する
2021 年 2 月 10 日以降、[サポートされているリージョン](#supported-regions)のいずれかで作成された新しい Azure Cosmos アカウントで、自動的にノードブックが有効になります。 ノートブックを有効にするために必要な追加の構成はありません。 次の手順を使用して新しいアカウントを作成します。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[リソースの作成]**  >  **[データベース]**  >  **[Azure Cosmos DB]** を選択します。
1. アカウントの基本設定を入力します。

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB の新しいアカウント ページ":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[ネットワーク]** オプションと **[タグ]** オプションはスキップできます。 
1. アカウントの設定を確認し、 **[作成]** を選択します。 アカウントの作成には数分かかります。 ポータル ページに "**デプロイが完了しました**" と表示されるまで待ちます。

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure portal の [通知] ペイン":::

1. **[リソースに移動]** を選択し、Azure Cosmos DB アカウント ページに移動します。

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB アカウント ページ":::

1. **[データ エクスプローラー]** ウィンドウに移動します。 これで、ノートブック ワークスペースが表示されます。

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="新しい Azure Cosmos DB ノートブック ワークスペース":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>既存の Cosmos アカウントでノートブックを有効にする

既存のアカウントでノートブックを有効にすることもできます。 この手順は、アカウントごとに 1 回だけ行う必要があります。

1. ご利用の Cosmos アカウントで **[データ エクスプローラー]** ウィンドウに移動します。
1. **[ノートブックを有効にする]** を選択します。

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Data Explorer で新しいノートブック ワークスペースを作成する":::

1. これにより、新しいノートブック ワークスペースを作成するように求めるメッセージが表示されます。 **[セットアップの完了]** を選択します。
1. これで、ご利用のアカウントでノートブックを使用できるようになりました。

## <a name="create-and-run-your-first-notebook"></a>最初のノートブックを作成して実行する

ノートブックを使用できることを確認するには、[サンプル ノートブック] でいずれかのノートブックを選択します。 これにより、ノートブックのコピーがご利用のワークスペースに保存され、それを開きます。

この例では、**GettingStarted.ipynb** を使用します。

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="GettingStarted.ipynb ノートブックの表示":::

ノートブックを実行するには
1. Python コードが含まれている最初のコード セルを選択します。
1. **[実行]** を選択して、セルを実行します。 **Shift + Enter** キーを使用して、コードを実行することもできます。
1. リソース ウィンドウを更新して、作成されたデータベースとコンテナーを表示します。

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="作業の開始のノートブックを実行する":::

また、 **[新しいノートブック]** を選択して新しいノートブックを作成したり、 **[マイ ノートブック]** メニューから **[ファイルのアップロード]** を選択したりすることで、既存のノートブック (.ipynb) ファイルをアップロードしたりすることもできます。 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="新しいノートブックを作成またはアップロードする":::

## <a name="supported-regions"></a>サポートされているリージョン
Azure Cosmos DB 用の組み込みノートブックは、現在、29 の Azure リージョンで使用できます。 これらのリージョンで作成された新しい Azure Cosmos アカウントでは、ノートブックが自動的に有効になります。 ノートブックは、お客様のアカウントで無料で利用できます。 

- オーストラリア中部
- オーストラリア中部 2
- オーストラリア東部
- オーストラリア南東部
- ブラジル南部
- カナダ中部
- カナダ東部
- インド中部
- 米国中部
- 米国東部
- 米国東部 2
- フランス中部
- フランス南部
- ドイツ北部
- ドイツ中西部
- 西日本
- 韓国南部
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 東南アジア
- スイス北部
- アラブ首長国連邦中部
- 英国南部
- 英国西部
- 米国中西部
- 西ヨーロッパ
- インド西部
- 米国西部 2

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md) の長所について学習する
* [ノートブックのサンプル ギャラリーを参照する](https://cosmos.azure.com/gallery.html)
* [Azure Cosmos DB ノートブック ギャラリーにノートブックを公開する](publish-notebook-gallery.md)
* [Python ノートブックの機能とコマンドを使用する](use-python-notebook-features-and-commands.md)
* [C# ノートブックの機能とコマンドを使用する](use-csharp-notebook-features-and-commands.md)
* [GitHub リポジトリからノートブックをインポートする](import-github-notebooks.md)
