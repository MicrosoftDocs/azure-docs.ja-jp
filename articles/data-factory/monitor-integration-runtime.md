---
title: Azure Data Factory で統合ランタイムを監視する
description: Azure Data Factory のさまざまな種類の統合ランタイムを監視する方法について説明します。
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740029"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory の統合ランタイムを監視する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**統合ランタイム** は、異なるネットワーク環境間のさまざまなデータ統合機能を提供するために Azure Data Factory (ADF) によって使われるコンピューティング インフラストラクチャです。 3 種類の統合ランタイムが Data Factory によって提供されています。

- Azure 統合ランタイム
- セルフホステッド統合ランタイム
- Azure-SQL Server Integration Services (SSIS) Integration Runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

統合ランタイム (IR) のインスタンスの状態を取得するには、次の PowerShell コマンドを実行します。 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

コマンドレットは、統合ランタイムの種類ごとに異なる情報を返します。 この記事では、統合ランタイムの種類ごとにプロパティと状態を説明します。  

## <a name="azure-integration-runtime"></a>Azure 統合ランタイム

Azure 統合ランタイムのコンピューティング リソースは、Azure で完全かつ柔軟に管理されます。 次の表では、**Get-AzDataFactoryV2IntegrationRuntime** コマンドによって返されるプロパティについて説明します。

### <a name="properties"></a>Properties

次の表では、Azure 統合ランタイム用のコマンドレットによって返されるプロパティについて説明します。

| プロパティ | 説明 |
-------- | ------------- | 
| 名前 | Azure 統合ランタイムの名前です。 |  
| State | Azure 統合ランタイムの状態です。 | 
| 場所 | Azure 統合ランタイムの場所です。 Azure 統合ランタイムの場所について詳しくは、[統合ランタイムの概要](concepts-integration-runtime.md)に関する記事をご覧ください。 |
| DataFactoryName | Azure 統合ランタイムが属しているデータ ファクトリの名前です。 | 
| ResourceGroupName | データ ファクトリが属しているリソース グループの名前です。  |
| 説明 | 統合ランタイムの説明です。  |

### <a name="status"></a>Status

次の表では、Azure 統合ランタイムの状態を示します。

| Status | コメント/シナリオ | 
| ------ | ------------------ |
| オンライン | Azure 統合ランタイムはオンラインであり、使うことができる状態です。 | 
| オフライン | Azure 統合ランタイムは、内部エラーのためオフラインになっています。 |

## <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム

このセクションでは、Get-AzDataFactoryV2IntegrationRuntime コマンドレットによって返されるプロパティについて説明します。 

> [!NOTE] 
> 返されるプロパティと状態には、セルフホステッド統合ランタイム全体と、ランタイム内の各ノードに関する情報が含まれます。  

### <a name="properties"></a>Properties

次の表では、**各ノード** の監視プロパティについて説明します。

| プロパティ | 説明 | 
| -------- | ----------- | 
| 名前 | セルフホステッド統合ランタイムおよびそれに関連付けられているノードの名前です。 ノードとは、セルフホステッド統合ランタイムがインストールされているオンプレミスの Windows コンピューターです。 |  
| Status | セルフホステッド統合ランタイム全体および各ノードの状態です。 例:オンライン、オフライン、制限あり、など。これらの状態については、次のセクションをご覧ください。 | 
| Version | セルフホステッド統合ランタイムおよび各ノードのバージョンです。 セルフホステッド統合ランタイムのバージョンは、グループ内のノードで多数を占めるバージョンに基づいて決定されます。 セルフホステッド統合ランタイムの設定の中にさまざまなバージョンのノードがある場合は、セルフホステッド統合ランタイムと同じバージョン番号を持つノードのみが適切に機能します。 それ以外のノードは制限モードであり、手動で更新する必要があります (自動更新に失敗する場合のみ)。 | 
| 使用可能なメモリ | セルフホステッド統合ランタイムのノードで使用可能なメモリです。 この値は、ほぼリアルタイムのスナップショットです。 | 
| CPU 使用率 | セルフホステッド統合ランタイムのノードの CPU 使用率です。 この値は、ほぼリアルタイムのスナップショットです。 |
| ネットワーク (入力/出力) | セルフホステッド統合ランタイムのノードのネットワーク使用率です。 この値は、ほぼリアルタイムのスナップショットです。 | 
| 同時実行ジョブ (実行中/制限) | **実行中**。 各ノードで実行されるタスクまたはジョブの数。 この値は、ほぼリアルタイムのスナップショットです。 <br/><br/>**上限**。 上限は、各ノードの最大の同時実行ジョブ数を表します。 この値は、マシンのサイズに基づいて定義されます。 CPU、メモリ、ネットワークが十分に活用されていないときでも、アクティビティがタイムアウトになる高度なシナリオでは、上限を引き上げ、同時実行ジョブの実行回数をスケールアップできます。 この機能は、単一ノードのセルフホステッド統合ランタイムでも使うことができます。 |
| Role | 複数ノードのセルフホステッド統合ランタイムには、ディスパッチャーとワーカーの 2 種類のロールがあります。 ノードはすべてワーカーであり、全部がジョブの実行に使用できることを意味します。 ディスパッチャー ノードは 1 つだけです。このノードは、クラウド サービスからタスク/ジョブをプルし、異なるワーカー ノードにディスパッチするために使われます。 ディスパッチャー ノードはワーカー ノードでもあります。 |

プロパティの一部の設定は、セルフホステッド統合ランタイムに複数のノードがあるとき (つまり、スケール アウト シナリオで)、より大きな意味を持つようになります。

#### <a name="concurrent-jobs-limit"></a>同時実行ジョブの上限

同時実行ジョブの上限の既定値は、コンピューターのサイズに基づいて設定されます。 この値の計算に使用される要素は、コンピューターの RAM 量と CPU コア数によって変わります。 コアやメモリが多ければ多いほど、同時実行ジョブの既定の上限が高くなります。

ノード数を増やすことでスケールアウトします。 ノード数を増やすとき、同時実行ジョブの上限は、利用可能なすべてのノードの同時実行ジョブ上限値を足したものになります。  たとえば、1 ノードで最大 12 のジョブを同時実行できる場合、同じようなノードを 3 つ追加すると、(4 x 12 で) 最大 48 のジョブを同時実行できます。 既定値では各ノードのリソース使用率が低い場合にのみ、同時実行ジョブの上限を増やすことをお勧めします。

算出された既定値は Azure Portal でオーバーライドできます。 [作成者] > [接続] > [統合ランタイム] > [編集] > [ノード] > [Modify concurrent job value per node]\(ノード別の同時実行ジョブ値を変更する\) の順に選択します。 PowerShell [update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) コマンドを使用することもできます。
  
### <a name="status-per-node"></a>状態 (ノードごと)

次の表では、セルフホステッド統合ランタイムのノードの状態を示します。

| Status | 説明 |
| ------ | ------------------ | 
| オンライン | ノードはデータ ファクトリ サービスに接続されています。 |
| オフライン | ノードはオフラインです。 |
| アップグレード中 | ノードは自動でアップデートされています。 |
| 制限あり | 接続の問題に起因します。 HTTP ポート 8060 の問題、サービス バス接続の問題、または資格情報の同期の問題に起因する可能性があります。 |
| 非アクティブ | ノードは、多数を占める他のノードの構成とは違う構成になっています。 |

他のノードに接続できない場合、ノードが非アクティブになることがあります。

### <a name="status-overall-self-hosted-integration-runtime"></a>状態 (セルフホステッド統合ランタイム全体)

次の表では、セルフホステッド統合ランタイムの状態を示します。 この状態は、ランタイムに属しているすべてのノードの状態によって決まります。 

| Status | 説明 |
| ------ | ----------- | 
| Need Registration | このセルフホステッド統合ランタイムにはノードがまだ登録されていません。 |
| オンライン | すべてのノードがオンラインです。 |
| オフライン | オンラインのノードはありません。 |
| 制限あり | このセルフホステッド統合ランタイムには、正常な状態ではないノードがあります。 この状態は、一部のノードがダウンしている可能性を警告します。 この状態は、ディスパッチャー/ワーカー ノードでの資格情報の同期の問題に起因する可能性があります。 |

セルフホステッド統合ランタイムのプロパティの詳細と、コマンドレット実行時のプロパティのスナップショット値を含む JSON ペイロードをフェッチするには、**Get-AzDataFactoryV2IntegrationRuntimeMetric** コマンドレットを使います。

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
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

Azure-SSIS IR は、SSIS パッケージ実行専用の Azure 仮想マシン (VM またはノード) の、フル マネージドのクラスターです。 さまざまな方法を使用して、Azure-SSIS IR 上での SSIS パッケージの実行を呼び出すことができます。たとえば、Azure 対応 SQL Server Data Tools (SSDT)、AzureDTExec コマンド ライン ユーティリティ、SQL Server Management Studio (SSMS) または SQL Server エージェントでの T-SQL、ADF パイプラインでの SSIS パッケージの実行アクティビティなどがあります。 Azure-SSIS IR では、その他の ADF アクティビティは実行されません。 プロビジョニングが完了すると、Azure PowerShell、Azure portal、および Azure Monitor を介して、全体またはノード固有のプロパティと状態を監視できます。

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Azure PowerShell を使用して Azure-SSIS Integration Runtime を監視する

次の Azure PowerShell コマンドレットを使用して、Azure-SSIS IR の全体またはノード固有のプロパティと状態を監視します。

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Properties

次の表では、Azure-SSIS IR 用の上記のコマンドレットによって返されるプロパティについて説明します。

| プロパティ/状態              | 説明                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Azure-SSIS IR が作成された UTC 時刻です。 |
| Nodes                        | Azure-SSIS IR で割り当て済み、または使用可能のノードと、ノード固有の状態 (開始中、使用可能、リサイクル中、使用不可) および対処が必要なエラーです。 |
| OtherErrors                  | Azure-SSIS IR のノード固有ではない対処が必要なエラーです。 |
| LastOperation                | 対処が必要なエラーが発生した Azure-SSIS IR での前回の開始または停止操作が失敗した場合の結果です。 |
| State                        | Azure-SSIS IR の全体的な状態 (初期、開始中、開始済み、停止中、停止済み) です。 |
| 場所                     | Azure-SSIS IR の場所です。 |
| NodeSize                     | Azure-SSIS IR の各ノードのサイズです。 |
| NodeCount                    | Azure-SSIS IR のノードの数です。 |
| MaxParallelExecutionsPerNode | Azure-SSIS IR のノードごとの並列実行の最大数です。 |
| CatalogServerEndpoint        | SSIS カタログ (SSISDB) をホストするための既存の Azure SQL Database サーバーまたはマネージド インスタンスのエンドポイントです。 |
| CatalogAdminUserName         | 既存の Azure SQL Database サーバーまたはマネージド インスタンスの管理者のユーザー名です。 この情報を使って、ADF によって自動的に SSISDB が準備および管理されます。 |
| CatalogAdminPassword         | 既存の Azure SQL Database サーバーまたはマネージド インスタンスの管理者のパスワードです。 |
| CatalogPricingTier           | Azure SQL Database サーバーによってホストされている SSISDB の価格レベルです。  Azure SQL Managed Instance がホストする SSISDB には適用されません。 |
| VNetId                       | Azure-SSIS IR を参加させる仮想ネットワークのリソース ID です。 |
| Subnet                       | Azure-SSIS IR を参加させるサブネット名です。 |
| id                           | Azure-SSIS IR のリソース ID です。 |
| Type                         | Azure-SSIS IR の IR の種類 (マネージドまたはセルフホステッド) です。 |
| ResourceGroupName            | ADF および Azure-SSIS IR が作成された Azure リソース グループの名前です。 |
| DataFactoryName              | ADF の名前です。 |
| 名前                         | Azure-SSIS IR の名前です。 |
| 説明                  | Azure-SSIS IR の説明です。 |
  
#### <a name="status-per-azure-ssis-ir-node"></a>状態 (Azure-SSIS IR ノード単位)

次の表では、Azure-SSIS IR ノードの可能な状態を示します。

| ノード固有の状態 | 説明 |
| -------------------- | ----------- | 
| 開始中             | このノードは準備中です。 |
| 利用可能            | このノードは、SSIS パッケージをデプロイ/実行できる状態です。 |
| Recycling            | このノードは、修復/再起動中です。 |
| 使用不可          | このノードは、SSIS パッケージをデプロイまたは実行できる状態ではなく、解決できる可能性がある対処が必要なエラーまたは問題が発生しています。 |

#### <a name="status-overall-azure-ssis-ir"></a>状態 (Azure-SSIS IR 全体)

次の表では、Azure-SSIS IR 全体の可能な状態を示します。 全体的な状態は、Azure-SSIS IR に属するすべてのノードの状態の組み合わせに応じて異なります。 

| 全体的な状態 | 説明 | 
| -------------- | ----------- | 
| Initial        | Azure-SSIS IR のノードは、割り当てまたは準備が行われていません。 | 
| 開始中       | Azure-SSIS IR のノードは、割り当てまたは準備が行われている最中であり、課金が開始されています。 |
| Started        | Azure-SSIS IR のノードは、割り当てまたは準備が済んでおり、SSIS パッケージをデプロイまたは実行できます。 |
| 停止中       | Azure-SSIS IR のノードの解放が進行中です。 |
| 停止済み        | Azure-SSIS IR のノードが解放され、課金は停止しています。 |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Azure portal で Azure-SSIS Integration Runtime を監視する

Azure portal で Azure-SSIS IR を監視するには、ADF UI の **[監視]** ハブの **[統合ランタイム]** ページに移動します。そこでは、すべての統合ランタイムを確認できます。

![すべての統合ランタイムを監視する](media/monitor-integration-runtime/monitor-integration-runtimes.png)

次に、Azure-SSIS IR の名前を選択して監視ページを開きます。そこでは、全体またはノード固有のプロパティと状態を確認できます。 このページには、Azure-SSIS IR の全般、デプロイ、および詳細の設定の構成方法に応じて、さまざまな情報や機能のタイルが表示されます。

**[種類]** と **[リージョン]** の各情報タイルには、Azure-SSIS IR の種類とリージョンがそれぞれ表示されます。

**[ノード サイズ]** 情報タイルには、Azure-SSIS IR の SKU (SSIS edition_VM tier_VM シリーズ)、CPU コア数、ノードあたりの RAM のサイズが表示されます。 

**[RUNNING / REQUESTED NODE(S)]\(実行中または要求されたノード\)** 情報タイルでは、現在実行中のノード数が、Azure-SSIS IR に対して以前に要求されたノードの合計数と比較されます。

**[DUAL STANDBY PAIR / ROLE]\(デュアル スタンバイ ペアまたはロール\)** 情報タイルには、事業継続とディザスター リカバリー (BCDR) のために Azure SQL Database または Managed Instance フェールオーバー グループと同期して機能するデュアル スタンバイ Azure-SSIS IR ペアの名前と、Azure-SSIS IR の現在のプライマリおよびセカンダ リロールが表示されます。 SSISDB フェールオーバーが発生すると、プライマリとセカンダリの Azure-SSIS IR のロールが入れ替わります ([BCDR 用の Azure-SSIS IR の構成](./configure-bcdr-azure-ssis-integration-runtime.md)に関する記事を参照)。

機能タイルの詳細については、以下を参照してください。

![Azure-SSIS IR の監視](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>[状態] タイル

Azure-SSIS IR 監視ページの **[状態]** タイルには、 **[実行中]** や **[停止済み]** など、全体的な状態が表示されます。 **[実行中]** という状態を選択すると、Azure-SSIS IR を停止するライブの **[停止]** ボタンがあるウィンドウがポップアップ表示されます。 **[停止済み]** という状態を選択すると、Azure-SSIS IR を開始するライブの **[開始]** ボタンがあるウィンドウがポップアップ表示されます。 ポップアップ ウィンドウには、Azure-SSIS IR 上で実行される [SSIS パッケージの実行] アクティビティを含む ADF パイプラインを自動生成する **[SSIS パッケージの実行]** ボタン ([ADF パイプラインでの [SSIS パッケージの実行] アクティビティとしての SSIS パッケージの実行](./how-to-invoke-ssis-package-ssis-activity.md)に関するページを参照してください) と **[リソース ID]** テキスト ボックスがあります。ここで Azure-SSIS IR リソース ID (`/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR`) をコピーできます。 ADF と Azure-SSIS IR の名前を含む Azure-SSIS IR リソース ID のサフィックスによってクラスター ID が形成されます。これを使用して、独立系ソフトウェア ベンダー (ISV) から Premium またはライセンス SSIS コンポーネントを追加で購入し、それらを Azure-SSIS IR にバインドすることができます ([ Azure-SSIS IR への Premium またはライセンス コンポーネントのインストール](./how-to-develop-azure-ssis-ir-licensed-components.md)に関するページを参照してください)。

![Azure-SSIS IR の監視 - 状態タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>[SSISDB SERVER ENDPOINT]\(SSISDB サーバー エンドポイント\) タイル

Azure SQL Database サーバーまたはマネージド インスタンスによってホストされている SSISDB にパッケージが格納されたプロジェクト配置モデルを使用している場合は、Azure-SSIS IR 監視ページに **[SSISDB SERVER ENDPOINT]\(SSISDB サーバー エンドポイント\)** タイルが表示されます ([Azure-SSIS IR デプロイ設定の構成](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)に関するページを参照してください)。 このタイルでは、Azure SQL Database サーバーまたはマネージド インスタンスを示すリンクを選択してウィンドウをポップアップ表示し、そのウィンドウでテキスト ボックスからサーバー エンドポイントをコピーし、SSMS から接続してパッケージのデプロイ、構成、実行、管理を行うときに使用できます。 そのポップアップ ウィンドウで、 **[See your Azure SQL Database or managed instance settings]\(Azure SQL Database またはマネージド インスタンスの設定を表示\)** リンクを選択して、Azure portal で SSISDB の再構成やサイズ変更を行うこともできます。

![Azure-SSIS IR の監視 - SSISDB タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>[PROXY / STAGING]\(プロキシ/ステージング\) タイル

セルフホステッド IR (SHIR) をダウンロードしてインストールし、Azure-SSIS IR のプロキシとして構成してオンプレミスのデータにアクセスすると、Azure-SSIS IR の監視ページに **[PROXY / STAGING]\(プロキシ/ステージング\)** タイルが表示されます ([Azure-SSIS IR のプロキシとしての SHIR の構成](./self-hosted-integration-runtime-proxy-ssis.md)に関するページを参照してください)。 このタイルでは、SHIR を指定するリンクを選択して、監視ページを開くことができます。 また、ステージング用の Azure Blob Storage を指定する別のリンクを選択して、リンクされたサービスを再構成することもできます。

#### <a name="validate-vnet--subnet-tile"></a>[VALIDATE VNET / SUBNET]\(VNET/サブネットの検証\) タイル

Azure-SSIS IR を VNet に参加させると、Azure-SSIS IR 監視ページに **[VALIDATE VNET / SUBNET]\(VNET/サブネットの検証\)** タイルが表示されます (「[Azure-SSIS Integration Runtime を仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください)。 このタイルでは、VNet とサブネットを示すリンクを選択してウィンドウをポップアップ表示し、そのウィンドウで VNet のリソース ID (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet`) とサブネット名をテキスト ボックスからコピーすることができます。さらに、Azure-SSIS IR で必要とされる受信/送信ネットワークのトラフィックと管理が妨げられないことを確認するために、VNet とサブネットの構成を検証することができます。

![Azure-SSIS IR の監視 - 検証タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>[接続の診断] タイル

Azure-SSIS IR 監視ページの **[接続の診断]** タイルで **[テスト接続]** リンクを選択してウィンドウをポップアップ表示し、そのウィンドウで、完全修飾ドメイン名 (FQDN) または IP アドレスと指定されたポートを使用して、Azure-SSIS IR と関連するパッケージ、構成、またはデータ ストアとの間の接続を確認できます ([Azure-SSIS IR からの接続のテスト](./ssis-integration-runtime-diagnose-connectivity-faq.md)に関するページを参照してください)。

![Azure-SSIS IR の監視 - 診断タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>[STATIC PUBLIC IP ADDRESSES]\(静的パブリック IP アドレス\) タイル

Azure-SSIS IR にご自分の静的パブリック IP アドレスを使用すると、Azure-SSIS IR の監視ページに **[STATIC PUBLIC IP ADDRESSES]\(静的パブリック IP アドレス\)** タイルが表示されます ([ Azure-SSIS IR に自分の静的パブリック IP アドレスを使用する](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)方法に関するページを参照してください)。 このタイルでは、Azure-SSIS IR の 1 つ目または 2 つ目の静的パブリック IP アドレスを指定するリンクを選択し、ウィンドウをポップアップ表示することができます。ここで、テキスト ボックスからリソース ID (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress`) をコピーすることができます。 また、ポップアップ ウィンドウで **[See your first/second static public IP address settings]\(1 つ目または 2 つ目の静的パブリック IP アドレス設定を表示する\)** リンクを選択し、Azure portal で 1 つ目または 2 つ目の静的パブリック IP アドレスを管理することもできます。

![Azure-SSIS IR の監視 - 静的タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>[PACKAGE STORES]\(パッケージ ストア\) タイル

Azure SQL Managed Instance によってホストされ、Azure-SSIS IR パッケージ ストアを介して管理されるファイル システム、Azure Files、または SQL Server データベース (MSDB) にパッケージが格納されたパッケージ配置モデルを使用している場合は、Azure-SSIS IR 監視ページに **パッケージ ストア** タイルが表示されます ([Azure-SSIS IR デプロイ設定の構成](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)に関するページを参照してください)。 このタイルでは、Azure-SSIS IR に接続されているパッケージ ストアの数を示すリンクを選択してウィンドウをポップアップ表示し、そのウィンドウで、Azure SQL Managed Instance によってホストされているファイル システム、Azure Files、または MSDB 上の Azure-SSIS IR パッケージ ストアに関連するリンクされたサービスを再構成できます。

![Azure-SSIS IR の監視 - パッケージ タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>[エラー] タイル

Azure-SSIS IR の開始、停止、メンテナンス、アップグレードに問題がある場合は、Azure-SSIS IR 監視ページに **[エラー]** タイルが追加で表示されます。 このタイルでは、Azure-SSIS IR によって生成されるエラーの数を示すリンクを選択してウィンドウをポップアップ表示し、それらのエラーの詳細を確認してコピーし、トラブルシューティング ガイドで推奨されているソリューションを見つけることができます ([Azure-SSIS IR のトラブルシューティング](./ssis-integration-runtime-management-troubleshoot.md)に関するページを参照してください)。

![Azure-SSIS IR の監視 - エラー タイル](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Azure Monitor を使用して Azure-SSIS Integration Runtime を監視する

Azure Monitor で Azure-SSIS IR を監視する方法については、「[Azure Monitor による SSIS 操作の監視](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)」を参照してください。

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの詳細情報

Azure-SSIS 統合ランタイムについて詳しくは、以下の記事をご覧ください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](./tutorial-deploy-ssis-packages-azure.md): この記事は、Azure-SSIS IR を作成し、Azure SQL Database を使用して SSIS カタログ (SSISDB) をホストするための詳細な手順を示しています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)。 この記事では、このチュートリアルを発展させて、Azure SQL Managed Instance を使用して SSISDB をホストする手順について説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を開始、停止、または削除する方法について説明されています。 また、ノードを追加してスケールアウトする方法についても説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、仮想ネットワークに Azure-SSIS IR を参加させる手順について説明されています。

## <a name="next-steps"></a>次のステップ
パイプラインを監視するさまざまな方法について、次の記事をご覧ください。 

- [クイック スタート: データ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)。
- [Azure Monitor を使ってデータ ファクトリのパイプラインを監視する](monitor-using-azure-monitor.md)
