---
title: ソース管理の統合 | Microsoft Docs
description: Azure Repos (Git および GitHub) を使用したネイティブなソース管理の統合による、SQL Data Warehouse のエンタープライズ クラスのデータベース DevOps エクスペリエンス。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 10a10882efe05ef9e6bb86e54fcfcf8c5d73d225
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098745"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のソース管理の統合

このチュートリアルでは、SQL Server Data Tools (SSDT) データベース プロジェクトをソース管理と統合する方法について概説します。  ソース管理の統合は、SQL Data Warehouse を使用して継続的インテグレーションおよびデプロイ パイプラインを構築するための最初の手順です。 

## <a name="before-you-begin"></a>開始する前に

- [Azure DevOps 組織](https://azure.microsoft.com/services/devops/)にサインアップします
- [作成と接続](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)に関するチュートリアルを実行します
-  [Visual Studio 2019 をインストール](https://visualstudio.microsoft.com/vs/older-downloads/)します 

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps を設定して接続する

1. Azure DevOps 組織内で、Azure Repo リポジトリを介して SSDT データベース プロジェクトをホストするプロジェクトを作成します

   ![プロジェクトを作成する](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "プロジェクトを作成する")

2. Visual Studio を開き、[接続の管理] を選択して、手順 1 で作成した Azure DevOps 組織とプロジェクトに接続します

   ![[接続の管理]](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "[接続の管理]")

   ![接続](media/sql-data-warehouse-source-control-integration/3-connect.png "接続")

3. プロジェクトからお使いのローカル コンピューターに Azure Repo リポジトリを複製します

   ![リポジトリを複製する](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "リポジトリを複製する")

## <a name="create-and-connect-your-project"></a>プロジェクトを作成して接続する

1. Visual Studio で、**ローカルの複製されたリポジトリ**に、ディレクトリとローカル Git リポジトリの両方を含む新しい SQL Server データベース プロジェクトを作成します

   ![新しいプロジェクトを作成する](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "新しいプロジェクトを作成する")  

2. 空の sqlproject を右クリックし、お使いのデータ ウェアハウスをデータベース プロジェクトにインポートします

   ![プロジェクトをインポートする](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "プロジェクトをインポートする")  

3. Visual Studio のチーム エクスプローラーで、ローカル Git リポジトリに対するすべての変更をコミットします 

   ![コミット](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "コミット")  

4. これで、複製されたリポジトリで変更がローカルにコミットされたので、Azure DevOps プロジェクトの Azure Repo リポジトリに変更を同期してプッシュします。

   ![同期とプッシュ - ステージング](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "同期とプッシュ - ステージング")

   ![同期とプッシュ](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "同期とプッシュ")  

## <a name="validation"></a>検証

1. Visual Studio SQL Server Data Tools (SSDT) から対象のデータベース プロジェクトのテーブル列を更新して、変更が Azure Repo にプッシュされたことを確認します

   ![更新列を検証する](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "更新列を検証する")

2. 変更をコミットしてローカル リポジトリから Azure Repo にプッシュします

   ![変更をプッシュする](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "変更をプッシュする")

3. Azure Repo リポジトリに変更がプッシュされたことを確認します

   ![確認](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "変更を確認する")

4. (**オプション**) SSDT を使用して、スキーマ比較を使用し、お使いのターゲット データ ウェアハウスへの変更を更新して、Azure Repo リポジトリおよびローカル リポジトリ内のオブジェクト定義にお使いのデータ ウェアハウスが反映されていることを確認します

## <a name="next-steps"></a>次の手順

- [Azure SQL Data Warehouse 向けの開発](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

