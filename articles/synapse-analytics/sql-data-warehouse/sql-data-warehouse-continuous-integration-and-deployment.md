---
title: 継続的インテグレーションとデプロイ
description: エンタープライズ クラス データベースの DevOps エクスペリエンスと、Azure Pipelines を使用した継続的インテグレーションおよび継続的デプロイの組み込みサポートによってデータ ウェアハウジングを実現します。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 725e8165f8a7bdb654f61d7257867a2d0bf17110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213569"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>データ ウェアハウジングのための継続的インテグレーションと継続的デプロイ

この簡単なチュートリアルでは、SQL Server Data tools (SSDT) データベース プロジェクトを Azure DevOps と統合し、Azure Pipelines を利用して継続的インテグレーションとデプロイを設定する方法の概要を説明します。 このチュートリアルは、データ ウェアハウジングのための継続的インテグレーションと継続的デプロイのパイプラインを構築するための 2 番目の手順です。

## <a name="before-you-begin"></a>開始する前に

- [ソース管理の統合のチュートリアル](sql-data-warehouse-source-control-integration.md)を実行します

- Azure DevOps を設定して接続する

## <a name="continuous-integration-with-visual-studio-build"></a>Visual Studio ビルドを使用した継続的インテグレーション

1. Azure Pipelines に移動し、新しいビルド パイプラインを作成します。

      ![新しいパイプライン](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "新しいパイプライン")

2. ソース コード リポジトリ (Azure Repos Git) を選択し、.NET デスクトップ アプリ テンプレートを選択します。

      ![パイプラインの設定](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "パイプラインの設定")

3. エージェントの適切なプールを使用するように YAML ファイルを編集します。 YAML ファイルは、次のようになります。

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

この時点で、ソース管理リポジトリの master ブランチにチェックインすると、データベース プロジェクトの Visual Studio ビルドが成功したことが自動的にトリガーされる単純な環境ができています。 ローカル データベース プロジェクトに変更を加え、その変更を master ブランチにチェックインして、オートメーション全体が正常に機能していることを検証します。

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Azure SQL Data Warehouse (または Database) デプロイ タスクを使用した継続的なデプロイ

1. [Azure SQL Database デプロイ タスク](/azure/devops/pipelines/targets/azure-sqldb)を使用して新しいタスクを追加し、ターゲット データ ウェアハウスへの接続に必要なフィールドに入力します。 このタスクを実行すると、前のビルド プロセスから生成された DACPAC がターゲット データ ウェアハウスにデプロイされます。 [Azure SQL Data Warehouse デプロイ タスク](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)を使用することもできます。

      ![デプロイ タスク](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "デプロイ タスク")

2. セルフホステッド エージェントを使用する場合は、SQL Data Warehouse 用の正しい SqlPackage.exe を使用するように環境変数を設定してください。 パスは次のようになります。

      ![環境変数](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "環境変数")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   パイプラインを実行して検証します。 ローカルで変更を行い、自動的なビルドとデプロイを生成する必要があるソース管理に変更をチェックインすることができます。

## <a name="next-steps"></a>次のステップ

- [Synapse SQL プール MPP アーキテクチャ](massively-parallel-processing-mpp-architecture.md)について理解を深める
- [SQL プールの簡易作成](create-data-warehouse-portal.md)
- [サンプル データを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
- [ビデオ](sql-data-warehouse-videos.md)を確認する
