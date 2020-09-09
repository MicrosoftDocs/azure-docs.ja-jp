---
title: Azure Migrate プロジェクトの作成と管理
description: Azure Migrate でプロジェクトを検索、作成、管理、および削除します。
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: d60868f9d0d4c60291cfd92a9e8d11fd3f9a42b9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071803"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate プロジェクトの作成と管理

この記事では、[Azure Migrate](migrate-services-overview.md) プロジェクトを作成、管理、および削除する方法について説明します。


## <a name="create-a-project-for-the-first-time"></a>プロジェクトを始めて作成する

Azure Migrate を初めて設定するときは、プロジェクトを作成して、評価または移行ツールを追加します。 [この手順に従って](how-to-add-tool-first-time.md)、初回設定を行います。

## <a name="create-additional-projects"></a>追加のプロジェクトの作成

Azure Migrate プロジェクトが既にあり、追加のプロジェクトを作成する場合は、次の手順を実行します。  

1. [Azure パブリック ポータル](https://portal.azure.com)または [Azure Government](https://portal.azure.us) で、**Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

   ![Azure Migrate プロジェクトを変更する](./media/create-manage-projects/switch-project.png)

3. 新しいプロジェクトを作成するには、 **[click here]\(ここをクリック\)** を選択します。

   ![2 つ目の Azure Migrate プロジェクトを作成する](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>プロジェクトを探す

次のようにプロジェクトを検索します。

1. [Azure portal](https://portal.azure.com) で **Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

    ![既存の Azure Migrate プロジェクトに切り替える](./media/create-manage-projects/switch-project.png)

3. 適切なサブスクリプションと Azure Migrate プロジェクトを選択します。


[以前のバージョン](migrate-services-overview.md#azure-migrate-versions)の Azure Migrate でプロジェクトを作成した場合は、次のようにプロジェクトを検索します。

1. [Azure portal](https://portal.azure.com) で **Azure Migrate** を検索します。
2. 以前のバージョンでプロジェクトを作成した場合は、Azure Migrate ダッシュボードに古いプロジェクトを参照するバナーが表示されます。 そのバナーを選択します。

    ![既存のプロジェクトにアクセスする](./media/create-manage-projects/access-existing-projects.png)

3. 古いプロジェクトの一覧を確認します。


## <a name="delete-a-project"></a>プロジェクトを削除する

次のように削除します。

1. プロジェクトが作成された Azure リソース グループを開きます。
2. リソース グループ ページで **[非表示の型の表示]** を選択します。
3. 削除する移行プロジェクトと、その関連付けられているリソースを選択します。
    - リソースの種類は **Microsoft.Migrate/migrateprojects** です。
    - リソース グループが Azure Migrate プロジェクトだけで使用されている場合は、リソース グループ全体を削除できます。

以下の点に注意してください。

- 削除すると、プロジェクトと、検出されたマシンに関するメタデータの両方が削除されます。
- 以前のバージョンの Azure Migrate を使用している場合は、プロジェクトが作成された Azure リソース グループを開きます。 削除する移行プロジェクトを選択します (リソースの種類は**移行プロジェクト**です)。
- Azure Log Analytics ワークスペースで依存関係の解析を使用している場合:
    - Log Analytics ワークスペースを Server Assessment ツールに関連付けている場合、ワークスペースは自動的には削除されません。 同じ Log Analytics ワークスペースが、複数のシナリオで使用されている可能性があります。
    - Log Analytics ワークスペースを削除する場合は、手動で行います。
- プロジェクトの削除は元に戻すことができません。 削除されたオブジェクトは回復できません。

### <a name="delete-a-workspace-manually"></a>ワークスペースを手動で削除する

1. プロジェクトに関連付けられている Log Analytics ワークスペースを参照します。

    - Azure Migrate プロジェクトを削除していない場合は、 **[Essentials]\(基礎\)**  >  **[Server Assessment]\(Server Assessment\)** でワークスペースへのリンクを見つけることができます。
       ![LA ワークスペース](./media/create-manage-projects/loganalytics-workspace.png)。
       
    - 既に Azure Migrate プロジェクトを削除した場合は、Azure portal の左側のウィンドウの **[リソース グループ]** を選択して、ワークスペースを見つけます。
       
2. [こちらの手順に従って](../azure-monitor/platform/delete-workspace.md)、ワークスペースを削除します。

## <a name="next-steps"></a>次のステップ

[評価](how-to-assess.md)または[移行](how-to-migrate.md)ツールを Azure Migrate プロジェクトに追加します。
