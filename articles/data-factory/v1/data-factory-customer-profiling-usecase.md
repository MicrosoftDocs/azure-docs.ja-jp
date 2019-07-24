---
title: 使用事例 - 顧客プロファイル
description: Azure Data Factory を使用して、データ駆動型ワークフロー (パイプライン) を作成し、顧客のゲーム会社をプロファイリングする方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bb7d6531da330bcfbf6de786ffb19984cfd1964e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017431"
---
# <a name="use-case---customer-profiling"></a>使用事例 - 顧客プロファイル
Azure Data Factory は、ソリューション アクセラレータの Cortana Intelligence Suite の実装に使用されている数多くあるサービスの 1 つです。  Cortana Intelligence の詳細については、 [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics)を参照してください。 このドキュメントでは、Azure Data Factory が一般的な分析の問題を解決する方法を理解するのに役立つ簡単な使用事例を説明します。

## <a name="scenario"></a>シナリオ
Contoso は、ゲーム機、携帯デバイス、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。 プレーヤーがこれらのゲームをプレイすると、使用パターン、ゲームのスタイル、およびユーザーの基本設定を追跡する大量のログ データが生成されます。  人口統計、地域、製品データと組み合わせて、Contoso は分析を実行し、プレーヤーのエクスペリエンスを拡張する方法をガイドし、ユーザーをアップグレードおよびゲーム内購入のターゲットにできます。 

Contoso の目標は、プレーヤーのゲーム履歴プロファイルに基づいてアップセルおよびクロスセルの機会を識別し、ビジネスの成長を促進して、より優れたエクスペリエンスを顧客に提供する魅力的な機能を追加することです。 この使用事例では、ビジネスの一例としてゲーム会社を取り上げています。 その会社は、プレーヤーの行動に合わせてゲームを最適化したいと考えています。 ここで説明する原則は、商品やサービスに対する顧客の関心を引き、顧客のエクスペリエンスを強化したいと考えているすべての企業に適用できます。

このソリューションでは、Contoso が、最近開始したマーケティング キャンペーンの効果を評価したいと考えています。 未処理のゲーム ログから開始し、そのログを処理しながら、地理位置情報のデータで強化し、広告参照データに結合していきます。そして、最後に Azure SQL Database にコピーして、キャンペーンの影響を分析します。

## <a name="deploy-solution"></a>ソリューションのデプロイ
このシンプルな使用事例にアクセスして試すにあたり必要となるのは、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)、[Azure Blob Storage アカウント](../../storage/common/storage-quickstart-create-account.md)、および [Azure SQL Database](../../sql-database/sql-database-get-started.md) です。 顧客プロファイリング パイプラインは、Data Factory のホーム ページにある **[サンプル パイプライン]** タイルからデプロイします。

1. データ ファクトリを作成するか、既存のデータ ファクトリを開きます。 データ ファクトリを作成する手順については、「[Data Factory を使用した Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)」を参照してください。
2. データ ファクトリの **[Data Factory]** ブレードで、**[サンプル パイプライン]** タイルをクリックします。

    ![サンプル パイプライン タイル](./media/data-factory-samples/SamplePipelinesTile.png)
3. **[サンプル パイプライン]** ブレードで、デプロイする **[顧客プロファイリング]** をクリックします。

    ![サンプル パイプライン ブレード](./media/data-factory-samples/SampleTile.png)
4. このサンプルの構成設定を指定します。 たとえば、Azure ストレージ アカウントの名前とキー、Azure SQL サーバーの名前、データベース、ユーザー ID、パスワードを指定します。

    ![サンプル ブレード](./media/data-factory-samples/SampleBlade.png)
5. 構成設定の指定が完了したら **[作成]** をクリックして、サンプルのパイプラインと、そのパイプラインで使用するリンクされたサービスとテーブルを作成またはデプロイします。
6. 先程 **[サンプル パイプライン]** ブレードでクリックしたサンプルのタイルに、デプロイの状態が表示されます。

    ![[デプロイ ステータス]](./media/data-factory-samples/DeploymentStatus.png)
7. サンプルのタイルに "**デプロイに成功しました**" メッセージが表示されたら、**[サンプル パイプライン]** ブレードを閉じます。  
8. **[Data Factory]** ブレードで、リンクされたサービス、データ セット、パイプラインがデータ ファクトリに追加されたことを確認します。  

    ![[Data Factory] ブレード](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>ソリューションの概要
この単純な使用事例は、Azure Data Factory を使用してデータを取得、準備、変換、分析、および発行する方法の例として使用できます。

![エンド ツー エンド ワークフロー](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

この図は、デプロイ後にデータ パイプラインが Azure Portal にどのように表示されるかを示しています。

1. **PartitionGameLogsPipeline** は、未処理のゲーム イベントを BLOB ストレージから読み取り、年、月、日に基づくパーティションを作成します。
2. **EnrichGameLogsPipeline** は、パーティション分割されたゲーム イベントを geo コードの参照データと結合し、IP アドレスを対応する地理的場所にマップすることによってデータを強化します。
3. **AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータを使用し、そのデータを広告データと共に処理して、マーケティング キャンペーンの有効性を含む最終的な出力を作成します。

この事例では、Data Factory を使用して、入力データをコピーし、データを変換および処理し、Azure SQL Database に最終データを出力する、アクティビティを調整しています。  また、UI を使用して、データ パイプラインのネットワークを視覚化し、管理し、ステータスを監視できます。

## <a name="benefits"></a>メリット
ゲーム会社は、ユーザー プロファイルの分析を最適化してビジネス目標と一致させることで、迅速に使用状況パターンを収集し、マーケティング キャンペーンの効果を分析できます。

