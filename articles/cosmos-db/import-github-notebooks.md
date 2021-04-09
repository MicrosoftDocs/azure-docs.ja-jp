---
title: GitHub リポジトリから Azure Cosmos DB にノートブックをインポートして実行する
description: Github に接続して、GitHub リポジトリから Azure Cosmos アカウントにノートブックをインポートする方法について説明します。 インポートした後は、それらを実行して編集し、変更内容を GitHub に保存することができます。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690653"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>GitHub リポジトリから Azure Cosmos DB にノートブックをインポートする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos アカウントの[ノートブック サポートを有効にする](enable-notebooks.md)と、新しいノートブックを作成したり、ローカル コンピューターから新しいノートブックをアップロードしたり、GitHub アカウントから既存のノートブックをインポートしたりできます。 この記事では、ノートブック ワークスペースを Github に接続して、GitHub リポジトリから Azure Cosmos アカウントにノートブックをインポートする方法について説明します。 インポートした後は、それらを実行し、変更を加え、変更内容を GitHub に保存することができます。

## <a name="get-notebooks-from-github"></a>GitHub からノートブックを取得する

自分の GitHub リポジトリやその他のパブリック GitHub リポジトリに接続し、Azure Cosmos DB でノートブックを読み取ったり、作成したり、共有したりすることができます。 次の手順を使用して GitHub アカウントに接続します。

1. [Azure portal](https://portal.azure.com/) にサインインし、Azure Cosmos アカウントに移動します。

1. **[データ エクスプローラー]** タブを開きます。このタブには、既存のデータベース、コンテナー、ノートブックがすべて表示されます。

1. **[GitHub への接続]** メニュー項目 選択します。

1. 表示されるタブで、**パブリック リポジトリ** のみに接続するか、**パブリック リポジトリとプライベート リポジトリ** に接続するかを選択できます。  必要なオプションを選択した後、 **[アクセスを承認する]** を選択します。 Azure Cosmos DB から GitHub アカウントのリポジトリにアクセスするには、承認が必要です。

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Azure Cosmos DB から GitHub リポジトリにアクセスすることの承認":::

1. "github.com" のWeb ページにリダイレクトされます。このページで、承認を確認できます。 **[AzureCosmosDBNotebooks の承認]** ボタンを選択し、プロンプトで GitHub アカウントのパスワードを入力します。

1. 承認が成功すると、自動的に Azure Cosmos アカウントに戻ります。 これで、GitHub アカウントからすべてのパブリック/プライベート リポジトリを表示できるようになります。 一覧からリポジトリを選択するか、URL を使用して直接リポジトリを追加することができます。

1. 必要なリポジトリを選択すると、リポジトリ エントリが **[固定解除されたリポジトリ]** セクションから **[固定されたリポジトリ]** セクションに移動します。 必要であれば、そのリポジトリのどのブランチからノートブックをインポートするかを指定することもできます。

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="リポジトリとブランチを選択する":::

1. **[OK]** を選択して、インポート操作を完了します。 選択したブランチで利用できるすべてのノートブックが、Azure Cosmos アカウントにインポートされます。

GitHub アカウントと統合した後は、自分の Azure Cosmos アカウント内のリポジトリとノートブックの一覧は自分だけに表示されます。 このことは、そのAzure Cosmos DB アカウントに複数のユーザーがログインし、各自のアカウントを追加した場合でも当てはまります。 言い換えれば、複数のユーザーが同じ Azure Cosmos アカウントを使用して、ノートブック ワークスペースを GitHub に接続することは可能です。 ただし、各ユーザーは、自分がインポートしたリポジトリとノートブックの一覧しか表示することができません。 他のユーザーがインポートしたノートブックは表示されません。

GitHub アカウントをノートブック ワークスペースから切断するには、 **[データ エクスプローラー]** タブを開き、 **[GitHub リポジトリ]** の横にある [`…`] を選択して、 **[GitHub からの切断]** を選択します。

## <a name="edit-a-notebook-and-push-changes-to-github"></a>ノートブックを編集して変更を GitHub にプッシュする

既存のノートブックを編集したり、新しいノートブックをリポジトリに追加したりして、変更を GitHub に保存することができます。

既存のノートブックを編集したら、 **[上書き保存]** を選択します。 表示されるダイアログ ボックスで、行った変更のコミット メッセージを入力できます。 **[コミット]** を選択すると、GitHub でノートブックが更新されます。 更新を確認するには、GitHub アカウントにサインインし、コミット履歴を確認します。

変更をコミットした後の通常の GitHub フローでは、通常、変更をリモートへとプッシュすることになります。 ただし、この場合は、コミット オプションによって、更新を GitHub にステージングし、コミットし、プッシュするという目的が果たされます。

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="ノートブックを編集して変更を GitHub にコミットする":::

ノートブックの編集後、[ノートブック ギャラリーにそれを公開](publish-notebook-gallery.md)できます。 

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB Jupyter ノートブック](cosmosdb-jupyter-notebooks.md)の長所について学習する。
* [ノートブックのサンプル ギャラリーを参照する](https://cosmos.azure.com/gallery.html)
* [Azure Cosmos DB ノートブック ギャラリーにノートブックを公開する](publish-notebook-gallery.md)
* [Python ノートブックの機能とコマンドを使用する](use-python-notebook-features-and-commands.md)
* [C# ノートブックの機能とコマンドを使用する](use-csharp-notebook-features-and-commands.md)
