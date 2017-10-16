---
title: "Azure Data Factory の統合ランタイムを監視する | Microsoft Docs"
description: "Azure Data Factory のさまざまな種類の統合ランタイムを監視する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: spelluru
ms.openlocfilehash: f2e1957a02d72d79a245ec3be705ba46dcc41e2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory の統合ランタイムを監視する  
**統合ランタイム**は、異なるネットワーク環境間のさまざまなデータ統合機能を提供するために Azure Data Factory によって使われるコンピューティング インフラストラクチャです。 3 種類の統合ランタイムが Data Factory によって提供されています。

- Azure 統合ランタイム
- セルフホステッド統合ランタイム
- Azure-SSIS 統合ランタイム

統合ランタイム (IR) のインスタンスの状態を取得するには、次の PowerShell コマンドを実行します。 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

コマンドレットは、統合ランタイムの種類ごとに異なる情報を返します。 この記事では、統合ランタイムの種類ごとにプロパティと状態を説明します。  

## <a name="azure-integration-runtime"></a>Azure 統合ランタイム
Azure 統合ランタイムのコンピューティング リソースは、Azure で完全かつ柔軟に管理されます。 次の表では、**Get-AzureRmDataFactoryV2IntegrationRuntime** コマンドによって返されるプロパティについて説明します。

### <a name="properties"></a>プロパティ
次の表では、Azure 統合ランタイム用のコマンドレットによって返されるプロパティについて説明します。

| プロパティ | Description |
-------- | ------------- | 
| 名前 | Azure 統合ランタイムの名前です。 |  
| 状態 | Azure 統合ランタイムの状態です。 | 
| 場所 | Azure 統合ランタイムの場所です。 Azure 統合ランタイムの場所について詳しくは、[統合ランタイムの概要](concepts-integration-runtime.md)に関する記事をご覧ください。 |
| DataFactoryName | Azure 統合ランタイムが属しているデータ ファクトリの名前です。 | 
| ResourceGroupName | データ ファクトリが属しているリソース グループの名前です。  |
| Description | 統合ランタイムの説明です。  |

### <a name="status"></a>状態
次の表では、Azure 統合ランタイムの状態を示します。

| 状態 | コメント/シナリオ | 
| ------ | ------------------ |
| オンライン | Azure 統合ランタイムはオンラインであり、使うことができる状態です。 | 
| オフライン | Azure 統合ランタイムは、内部エラーのためオフラインになっています。 |

## <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム
このセクションでは、Get-AzureRmDataFactoryV2IntegrationRuntime コマンドレットによって返されるプロパティについて説明します。 

> [!NOTE] 
> 返されるプロパティと状態には、セルフホステッド統合ランタイム全体と、ランタイム内の各ノードに関する情報が含まれます。  

### <a name="properties"></a>プロパティ

次の表では、**各ノード**の監視プロパティについて説明します。

| プロパティ | Description | 
| -------- | ----------- | 
| 名前 | セルフホステッド統合ランタイムおよびそれに関連付けられているノードの名前です。 ノードとは、セルフホステッド統合ランタイムがインストールされているオンプレミスの Windows コンピューターです。 |  
| 状態 | セルフホステッド統合ランタイム全体および各ノードの状態です。 (たとえば、オンライン/オフライン/制限/ など)。これらの状態については、次のセクションをご覧ください。 | 
| バージョン | セルフホステッド統合ランタイムおよび各ノードのバージョンです。 セルフホステッド統合ランタイムのバージョンは、グループ内のノードで多数を占めるバージョンに基づいて決定されます。 セルフホステッド統合ランタイムの設定の中にさまざまなバージョンのノードがある場合は、セルフホステッド統合ランタイムと同じバージョン番号を持つノードのみが適切に機能します。 それ以外のノードは制限モードであり、手動で更新する必要があります (自動更新に失敗する場合のみ)。 | 
| 使用可能なメモリ | セルフホステッド統合ランタイムのノードで使用可能なメモリです。 この値は、ほぼリアルタイムのスナップショットです。 | 
| CPU 使用率 | セルフホステッド統合ランタイムのノードの CPU 使用率です。 この値は、ほぼリアルタイムのスナップショットです。 |
| ネットワーク (入力/出力) | セルフホステッド統合ランタイムのノードのネットワーク使用率です。 この値は、ほぼリアルタイムのスナップショットです。 | 
| 同時実行ジョブ (実行中/制限) | 各ノードで実行されるタスクまたはジョブの数。 この値は、ほぼリアルタイムのスナップショットです。 上限は、各ノードの最大の同時実行ジョブ数を表します。 この値は、マシンのサイズに基づいて定義されます。 CPU/ メモリ/ ネットワークは使用率を超えておらず、アクティビティがタイムアウトになっている高度なシナリオでは、上限を引き上げて、同時実行ジョブの実行回数をスケールアップできます。この機能は、単一ノードのセルフホステッド統合ランタイムでも使うことができます。 |
| 役割 | 複数ノードのセルフホステッド統合ランタイムには、ディスパッチャーとワーカーの 2 種類のロールがあります。 ノードはすべてワーカーであり、全部がジョブの実行に使用できることを意味します。 ディスパッチャー ノードは 1 つだけです。このノードは、クラウド サービスからタスク/ジョブをプルし、異なるワーカー ノードにディスパッチするために使われます。 ディスパッチャー ノードはワーカー ノードでもあります。 |

プロパティの一部の設定は、セルフホステッド統合ランタイムに複数のノードがあると (スケールアウト シナリオ)、より大きな意味を持つようになります。 
  
### <a name="status-per-node"></a>状態 (ノードごと)
次の表では、セルフホステッド統合ランタイムのノードの状態を示します。

| 状態 | Description |
| ------ | ------------------ | 
| オンライン | ノードはデータ ファクトリ サービスに接続されています。 |
| オフライン | ノードはオフラインです。 |
| アップグレード中 | ノードは自動でアップデートされています。 |
| 制限あり | 接続の問題に起因します。 HTTP ポート 8050 の問題、サービス バス接続の問題、または資格情報の同期の問題に起因する可能性があります。 |
| 非アクティブ | ノードは、多数を占める他のノードの構成とは違う構成になっています。 |

他のノードに接続できない場合、ノードが非アクティブになることがあります。

### <a name="status-overall-self-hosted-integration-runtime"></a>状態 (セルフホステッド統合ランタイム全体)
次の表では、セルフホステッド統合ランタイムの状態を示します。 この状態は、ランタイムに属しているすべてのノードの状態によって決まります。 

| 状態 | Description |
| ------ | ----------- | 
| Need Registration | このセルフホステッド統合ランタイムにはノードがまだ登録されていません。 |
| オンライン | すべてのノードがオンラインです。 |
| オフライン | オンラインのノードはありません。 |
| 制限あり | このセルフホステッド統合ランタイムには、正常な状態ではないノードがあります。 この状態は、一部のノードがダウンしている可能性を警告します。 この状態は、ディスパッチャー/ワーカー ノードでの資格情報の同期の問題に起因する可能性があります。 |

セルフホステッド統合ランタイムのプロパティの詳細と、コマンドレット実行時のプロパティのスナップショット値を含む JSON ペイロードを取得するには、**Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** コマンドレットを使います。

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

サンプル出力 (このセルフホステッド統合ランタイムには 2 つのノードが関連付けられているものとします):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイム
Azure-SSIS 統合ランタイムは、SSIS パッケージ実行専用の Azure 仮想マシン (ノード) の、完全に管理されたクラスターです。 Azure Data Factory の他の活動は実行しません。 プロビジョニングが済むと、そのプロパティを照会し、全体/ノード固有の状態を監視できます。

### <a name="properties"></a>プロパティ

| プロパティ/状態 | Description |
| --------------- | ----------- |
| CreateTime | Azure-SSIS 統合ランタイムが作成された UTC 時刻です。 |
| Nodes | Azure-SSIS 統合ランタイムで割り当て済み/使用可能のノードと、ノード固有の状態 (開始中/使用可能/リサイクル中/使用不可) および対処が必要なエラーです。 |
| OtherErrors | Azure-SSIS 統合ランタイムのノード固有ではない対処が必要なエラーです。 |
| LastOperation | 対処が必要なエラーが発生した Azure-SSIS 統合ランタイムでの前回の開始/停止操作が失敗した場合の結果です。 |
| 状態 | Azure-SSIS 統合ランタイムの全体的な状態 (初期/開始中/開始済み/停止中/停止済み) です。 |
| 場所 | Azure-SSIS 統合ランタイムの場所です。 |
| NodeSize | Azure-SSIS 統合ランタイムの各ノードのサイズです。 |
| NodeCount | Azure-SSIS 統合ランタイムのノードの数です。 |
| MaxParallelExecutionsPerNode | Azure-SSIS 統合ランタイムのノードごとの並列実行の数です。 |
| CatalogServerEndpoint | SSISDB をホストするための既存の Azure SQL Database/マネージ インスタンス サーバーのエンドポイントです。 |
| CatalogAdminUserName | 既存の Azure SQL Database/マネージ インスタンス サーバーの管理者のユーザー名です。 データ ファクトリ サービスはこの情報を使って、自動的に SSISDB を準備および管理します。 |
| CatalogAdminPassword | 既存の Azure SQL Database/マネージ インスタンス サーバーの管理者のパスワードです。 |
| CatalogPricingTier | 既存の Azure SQL Database サーバーによってホストされている SSISDB の価格レベルです。  Azure SQL マネージ インスタンスがホストする SSISDB には適用されません。 |
| VNetId | Azure-SSIS 統合ランタイムが参加する仮想ネットワーク (VNet) のリソース ID です。 |
| サブネット | Azure-SSIS 統合ランタイムが参加するサブネット名です。 |
| ID | Azure-SSIS 統合ランタイムのリソース ID です。 |
| 型 | Azure-SSIS 統合ランタイムの種類 (マネージ/セルフホステッド) です。 |
| ResourceGroupName | データ ファクトリおよび Azure-SSIS 統合ランタイムが作成された Azure リソース グループの名前です。 |
| DataFactoryName | Azure データ ファクトリの名前です。 |
| 名前 | Azure-SSIS 統合ランタイムの名前です。 |
| Description | Azure-SSIS 統合ランタイムの説明です。 |

  
### <a name="status-per-node"></a>状態 (ノードごと)

| 状態 | Description |
| ------ | ----------- | 
| Starting | このノードは準備中です。 |
| 使用可能 | このノードは、SSIS パッケージをデプロイ/実行できる状態です。 |
| Recycling | このノードは、修復/再起動中です。 |
| 使用不可 | このノードは、SSIS パッケージをデプロイ/実行できる状態ではなく、解決できる可能性がある対処が必要なエラー/問題が発生しています。 |

### <a name="status-overall-azure-ssis-integration-runtime"></a>状態 (Azure-SSIS 統合ランタイム全体)

| 全体的な状態 | Description | 
| -------------- | ----------- | 
| Initial | Azure-SSIS 統合ランタイムのノードは、割り当て/準備が行われていません。 | 
| Starting | Azure-SSIS 統合ランタイムのノードは、割り当て/準備が行われている最中であり、課金が開始されています。 |
| 開始済み | Azure-SSIS 統合ランタイムのノードは、割り当て/準備が済んでおり、SSIS パッケージをデプロイ/実行できます。 |
| 停止中  | Azure-SSIS 統合ランタイムのノードは解放されています。 |
| 停止済み | Azure-SSIS 統合ランタイムのノードが解放され、課金は停止しています。 |

Azure-SSIS 統合ランタイムについて詳しくは、以下の記事をご覧ください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL マネージ インスタンス (プライベート プレビュー) の使い方と、IR を VNet に参加させる方法が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [Azure-SSIS IR を VNet に参加させる](join-azure-ssis-integration-runtime-virtual-network.md):  この記事では、Azure 仮想ネットワーク (VNet) への Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が VNet に参加できるように Azure Portal を使って VNet を構成する手順も説明されています。 

## <a name="next-steps"></a>次のステップ
パイプラインを監視するさまざまな方法について、次の記事をご覧ください。 

- [クイック スタート: データ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)。
- [Azure Monitor を使ってデータ ファクトリのパイプラインを監視する](monitor-using-azure-monitor.md)