---
title: Azure Migrate プロジェクトの作成と管理
description: Azure Migrate でプロジェクトを検索、作成、管理、および削除します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 750cf0a938fffd4e5bfe25811bb276f7cb1b5583
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757561"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate プロジェクトの作成と管理

この記事では、[Azure Migrate](migrate-services-overview.md) プロジェクトを作成、管理、および削除する方法について説明します。 従来の Azure Migrate プロジェクトを使用している場合は、それらのプロジェクトを削除し、手順に従って新しい Azure Migrate プロジェクトを作成してください。 従来の Azure Migrate プロジェクトまたはコンポーネントは Azure Migrate にアップグレードできません。 作成プロセスを開始する前に、[よくあるご質問](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version)を参照してください。

Azure Migrate のプロジェクトは、評価または移行しようとしている環境から収集された検出、評価、移行のメタデータを格納するために使用されます。 プロジェクト内で、検出された資産を追跡し、評価を作成して、Azure への移行を調整することができます。  

## <a name="verify-permissions"></a>アクセス許可の確認

次を行い、Azure Migrate プロジェクトを作成するための正しいアクセス許可があることを確認します。

1. Azure portal で関連するサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それを選択してアクセス許可を表示します。 *共同作成者* または *所有者* のアクセス許可を持っている必要があります。 


## <a name="create-a-project-for-the-first-time"></a>プロジェクトを始めて作成する

Azure サブスクリプション内に新しい Azure Migrate プロジェクトを設定します。

1. Azure portal で *Azure Migrate* を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で **[サーバーの評価と移行]** を選択します。

    ![概要にあるサーバーを評価して移行するためのオプション](./media/create-manage-projects/assess-migrate-servers.png)

4. **[サーバー]** で **[プロジェクトの作成]** を選択します。

    ![プロジェクトの作成を開始するボタン](./media/create-manage-projects/create-project.png)

5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。
    - 地理的な場所は、オンプレミスのマシンから収集されたメタデータを格納するためにのみ使用されます。 移行では、任意のターゲット リージョンを選択できます。 
    - [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

8. **[作成]** を選択します。

   ![プロジェクト設定の入力ページ](./media/create-manage-projects/project-details.png)


Azure Migrate プロジェクトがデプロイされるまで数分待ちます。

## <a name="create-a-project-in-a-specific-region"></a>特定のリージョンにプロジェクトを作成する

ポータルで、プロジェクトを作成する地理的な場所を選択することができます。 特定の Azure リージョン内にプロジェクトを作成する場合は、次の API コマンドを使用してそのプロジェクトを作成します。

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>追加のプロジェクトの作成

Azure Migrate プロジェクトが既にあり、追加のプロジェクトを作成する場合は、次の手順を実行します。  

1. [Azure パブリック ポータル](https://portal.azure.com)または [Azure Government](https://portal.azure.us) で、**Azure Migrate** を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

   ![Azure Migrate プロジェクトを変更する](./media/create-manage-projects/switch-project.png)

3. 新しいプロジェクトを作成するには、 **[click here]\(ここをクリック\)** を選択します。


## <a name="find-a-project"></a>プロジェクトを探す

次のようにプロジェクトを検索します。

1. [Azure portal](https://portal.azure.com) で *Azure Migrate* を検索します。
2. Azure Migrate ダッシュボードの **[サーバー]** で、右上隅の **[変更]** を選択します。

    ![既存の Azure Migrate プロジェクトに切り替える](./media/create-manage-projects/switch-project.png)

3. 適切なサブスクリプションと Azure Migrate プロジェクトを選択します。


### <a name="find-a-legacy-project"></a>レガシ プロジェクトを検索する

[以前のバージョン](migrate-services-overview.md#azure-migrate-versions)の Azure Migrate でプロジェクトを作成した場合は、次のようにプロジェクトを検索します。

1. [Azure portal](https://portal.azure.com) で *Azure Migrate* を検索します。
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
- 以前のバージョンの Azure Migrate を使用している場合は、プロジェクトが作成された Azure リソース グループを開きます。 削除する移行プロジェクトを選択します (リソースの種類は **移行プロジェクト** です)。
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
