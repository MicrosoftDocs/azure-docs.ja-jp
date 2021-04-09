---
title: ソース管理の統合
description: Azure Repos (Git および GitHub) を使用したネイティブなソース管理の統合による、専用 SQL プールのエンタープライズ クラスのデータベース DevOps エクスペリエンス。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 0864ab90f35e4af0443b418bc139ee5f8a7db665
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585538"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールを対象としたソース管理の統合

このチュートリアルでは、SQL Server Data Tools (SSDT) データベース プロジェクトをソース管理と統合する方法について概説します。  ソース管理の統合は、Azure Synapse Analytics の専用 SQL プール リソースを使用して継続的インテグレーションと継続的デプロイのパイプラインを構築するための最初の手順です。

## <a name="before-you-begin"></a>開始する前に

- [Azure DevOps 組織](https://azure.microsoft.com/services/devops/)にサインアップします
- [作成と接続](create-data-warehouse-portal.md)に関するチュートリアルを実行します
- [Visual Studio 2019 をインストール](https://visualstudio.microsoft.com/vs/older-downloads/)します

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps を設定して接続する

1. Azure DevOps 組織内で、Azure Repo リポジトリを介して SSDT データベース プロジェクトをホストするプロジェクトを作成します。

   ![プロジェクトの作成](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Create Project")

2. Visual Studio を開き、 **[接続の管理]** を選択して、手順 1 の Azure DevOps 組織とプロジェクトに接続します。

   ![接続の管理](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "接続の管理")

3. **[接続の管理]** 、 **[Connect to a project]\(プロジェクトに接続\)** の順に選択してプロジェクトに接続します。
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "接続する")


4. 手順 1 で作成したプロジェクトを探し、 **[接続]** を選択します。
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "接続する")


3. プロジェクトからお使いのローカル コンピューターに Azure DevOps リポジトリを複製します

   ![リポジトリの複製](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "リポジトリをクローンする")

Visual Studio を使用したプロジェクトの接続の詳細については、「[チーム エクスプローラーのプロジェクトに接続する](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true)」を参照してください。 Visual Studio を使用したリポジトリの複製に関するガイダンスは、「[既存の Git リポジトリを複製する](/azure/devops/repos/git/clone?tabs=visual-studio)」をご確認ください。 

## <a name="create-and-connect-your-project"></a>プロジェクトを作成して接続する

1. Visual Studio で、**ローカルの複製されたリポジトリ** に、ディレクトリとローカル Git リポジトリの両方を含む新しい SQL Server データベース プロジェクトを作成します。

   ![新しいプロジェクトの作成](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "新しいプロジェクトの作成")  

2. 空の sqlproject を右クリックし、お使いのデータ ウェアハウスをデータベース プロジェクトにインポートします。

   ![プロジェクトのインポート](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "プロジェクトのインポート")  

3. Visual Studio のチーム エクスプローラーで、ローカル Git リポジトリに対する変更をコミットします。

   ![コミット](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. これで、複製されたリポジトリで変更がローカルにコミットされたので、Azure DevOps プロジェクトの Azure Repo リポジトリに変更を同期してプッシュします。

   ![同期とプッシュ - ステージング](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "同期とプッシュ - ステージング")

   ![同期とプッシュ](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "同期とプッシュ")  

## <a name="validation"></a>検証

1. Visual Studio SQL Server Data Tools (SSDT) から対象のデータベース プロジェクトのテーブル列を更新して、変更が Azure Repo にプッシュされたことを確認します。

   ![更新列を確認する](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "更新列を確認する")

2. 変更をコミットしてローカル リポジトリから Azure Repo にプッシュします。

   ![変更をプッシュする](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "変更をプッシュする")

3. Azure Repo リポジトリに変更がプッシュされたことを確認します。

   ![確認](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "変更を確認する")

4. (**省略可能**) SSDT を使用し、Schema Compare を使用してターゲット専用 SQL プールへの変更を更新し、Azure Repo リポジトリおよびローカル リポジトリ内のオブジェクト定義に確実に専用 SQL プールが反映されるようにします。

## <a name="next-steps"></a>次のステップ

- [専用 SQL プールのための開発](sql-data-warehouse-overview-develop.md)