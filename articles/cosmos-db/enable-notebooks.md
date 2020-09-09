---
title: Azure Cosmos DB アカウントでノートブックを有効にする (プレビュー)
description: Azure Cosmos DB の組み込みノートブックを使用すると、ポータル内からデータを分析して視覚化することができます。 この記事では、Cosmos アカウントに対してこの機能を有効にする方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 4c485bf6b9eb34e68e399c24e51286428f47586f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261904"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB アカウントのノートブックを有効にする (プレビュー)

> [!IMPORTANT]
> Azure Cosmos DB 用の組み込みノートブックは、現在、次の Azure リージョンで使用できます。オーストラリア東部、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、英国南部、西ヨーロッパ、米国西部 2。 ノートブックを使用するには、[ノートブックを使用して新しいアカウントを作成する](#enable-notebooks-in-a-new-cosmos-account)か、これらのリージョンのいずれかの[既存のアカウントでノートブックを有効にします](#enable-notebooks-in-an-existing-cosmos-account)。

Azure Cosmos DB の組み込みの Jupyter ノートブックを使用すると、Azure portal のデータを分析して視覚化することができます。 この記事では、Azure Cosmos DB アカウントに対してこの機能を有効にする方法について説明します。

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>新しい Cosmos アカウントでノートブックを有効にする

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[リソースの作成]**  >  **[データベース]**  >  **[Azure Cosmos DB]** を選択します。
1. **[Azure Cosmos DB アカウントの作成]** ページで、 **[Notebooks]** を選択します。 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Azure Cosmos DB の作成ブレードで [Notebooks] オプションを選択する":::

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

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB Jupyter ノートブック](cosmosdb-jupyter-notebooks.md)の長所について学習する
