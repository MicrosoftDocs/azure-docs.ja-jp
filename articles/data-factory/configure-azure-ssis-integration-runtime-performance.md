---
title: Azure-SSIS Integration Runtime のパフォーマンスを構成する
description: Azure-SSIS 統合ランタイムのプロパティを高パフォーマンス用に構成する方法について説明します。
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: 15aac35a7ebc505e76ddfd0c538c4fddb7b2d9ff
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930538"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Azure-SSIS 統合ランタイムを高パフォーマンス用に構成する

この記事では、Azure-SSIS 統合ランタイム (IR) を高パフォーマンス用に構成する方法について説明します。 Azure-SSIS IR を使うと、SQL Server Integration Services (SSIS) パッケージを Azure にデプロイして実行できます。 Azure-SSIS IR について詳しくは、「[Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime)」をご覧ください。 Azure での SSIS パッケージのデプロイと実行については、「[Lift and shift SQL Server Integration Services workloads to the cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)」(SQL Server Integration Services のワークロードをクラウドにリフト アンド シフトする) をご覧ください。

> [!IMPORTANT]
> この記事には、SSIS 開発チームのメンバーによって行われた社内テストにおけるパフォーマンスの結果と観察結果が含まれています。 他の環境では結果が異なる場合があります。 コストとパフォーマンスの両方に影響する構成設定を確定する前に、独自のテストを実行してください。

## <a name="properties-to-configure"></a>構成するプロパティ

構成スクリプトの次の部分では、Azure-SSIS 統合ランタイムを作成するときに構成できるプロパティが示されています。 PowerShell の完全なスクリプトと説明については、「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure-powershell.md)」をご覧ください。

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** は、統合ランタイムの worker ノードの場所です。 worker ノードは、Azure SQL データベース上の SSIS カタログ データベース (SSISDB) への常時接続を維持します。 **AzureSSISLocation** は、SSISDB をホストする SQL Database サーバーと同じ場所に設定します。このようにすると、統合ランタイムの動作が可能な限り効率的になります。

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory (Azure-SSIS IR を含みます) は、以下のオプションをサポートします。
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

SSIS エンジニアリング チームによる非公式な社内テストでは、A シリーズより D シリーズの方が SSIS パッケージの実行に適しています。

-   D シリーズの価格性能比は A シリーズより高く、v3 シリーズの価格性能比は v2 シリーズより高くなります。
-   D シリーズのスループットは同じ価格の A シリーズより高く、v3 シリーズのスループットは同じ価格の v2 シリーズより高くなります。
-   Azure-SSIS IR の v2 シリーズ ノードはカスタム設定には適していないため、代わりに v3 シリーズ ノードを使用してください。 既に v2 シリーズ ノードを使用している場合は、できるだけ早く v3 シリーズ ノードを使用するように切り替えてください。
-   E シリーズは、他のマシンよりも高いメモリ対 CPU 比を提供するメモリ最適化 VM サイズです。対象のパッケージで大量のメモリが必要になる場合は、E シリーズ VM を選択することを検討してください。

### <a name="configure-for-execution-speed"></a>実行速度が上がるように構成する
実行するパッケージの数が多くなく、パッケージをすばやく実行したい場合は、次の表の情報を使って、シナリオに適した仮想マシンの種類を選びます。

このデータは、1 つの worker ノードでの 1 つのパッケージの実行を表します。 このパッケージは、Azure Blob Storage から名と姓の列を含むレコードを 300 万件読み込み、フル ネームの列を生成して、フル ネームが 20 文字より長いレコードを Azure Blob Storage に書き込みます。

![SSIS 統合ランタイム パッケージの実行速度](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>全体的なスループットが上がるように構成する

実行するパッケージの数が多く、全体的なスループットが最も重要な場合は、次の表の情報を使って、シナリオに適した仮想マシンの種類を選びます。

![SSIS 統合ランタイムの全体的な最大スループット](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** は、統合ランタイムのスケーラビリティを調整します。 統合ランタイムのスループットは、**AzureSSISNodeNumber** に比例します。 最初は **AzureSSISNodeNumber** に小さい値は設定した後、統合ランタイムのスループットを監視しながら、シナリオに合った値に調整します。 worker ノードの数の再構成については、「[Azure-SSIS 統合ランタイムを管理する](manage-azure-ssis-integration-runtime.md)」をご覧ください。

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

既に強力な worker ノードを使ってパッケージを実行している場合、**AzureSSISMaxParallelExecutionsPerNode** の値を大きくすると、統合ランタイムの全体的なスループットが上がる可能性があります。 Standard_D1_v2 ノードでは、ノードあたり 1 ～ 4 の並列実行がサポートされています。 その他のすべての種類のノードでは、ノードあたり 1 から max(2 x コア数, 8) の並列実行がサポートされています。 サポートされている最大値を超える **AzureSSISMaxParallelExecutionsPerNode** を希望する場合は、サポート チケットを開いてください。Microsoft によって最大値が引き上げられた後、Azure PowerShell を使用して **AzureSSISMaxParallelExecutionsPerNode** を更新する必要があります。
パッケージのコストと、worker ノードの次の構成に基づいて、適切な値を予測できます。 詳しくは、「[汎用仮想マシンのサイズ](../virtual-machines/windows/sizes-general.md)」をご覧ください。

| Size             | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク パフォーマンス (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000/46/23                                             | 2 / 2x500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 4 / 4x500                         | 2/1,500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 8 / 8x500                         | 4/3,000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 16 / 16x500                       | 8/6,000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000/80/40                                             | 8 / 8x500                         | 4/1,000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000/160/80                                            | 16 / 16x500                       | 8/2,000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000/46/23                                             | 4/6 x 500                         | 2/1,000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000/93/46                                             | 8/12 x 500                        | 2/2,000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000/187/93                                           | 16/24 x 500                       | 4/4,000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000/375/187                                          | 32/48 x 500                        | 8/8,000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000/750/375                                          | 32/96 x 500                       | 8/1,6000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96,000/1,000/500                                         | 32/192 x 500                      | 8/30,000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000/46/23                                             | 4/6 x 500                         | 2/1,000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000/93/46                                             | 8/12 x 500                        | 2/2,000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000/187/93                                           | 16/24 x 500                       | 4/4,000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000/375/187                                          | 32/48 x 500                       | 8/8,000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000/750/375                                          | 32/96 x 500                       | 8/1,6000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96,000/1,000/500                                         | 32/192 x 500                      | 8/30,000                                      |

**AzureSSISMaxParallelExecutionsPerNode** プロパティの適切な値の設定に関するガイドラインは次のとおりです。 

1. 最初は小さな値に設定します。
2. 値を少しだけ大きくして、全体的なスループットが向上するかどうかを確認します。
3. 全体的なスループットが最大値に達したら、値を増やすのを止めます。

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** は、Azure SQL データベースの SSIS カタログ データベース (SSISDB) の価格レベルです。 この設定は、IR インスタンス内の worker の最大数、パッケージの実行をキューに入れる速度、実行ログを読み込む速度に影響します。

-   パッケージの実行をキューに入れる速度および実行ログを読み込む速度が重要でない場合は、最低のデータベース価格レベルを選んでかまいません。 Basic 価格レベルの Azure SQL Database は、1 つの統合ランタイム インスタンスで 8 個の worker をサポートします。

-   worker の数が 8 より多い場合、またはコアの数が 50 を超える場合は、Basic より強力なデータベースを選びます。 そうしないと、データベースが統合ランタイム インスタンスのボトルネックになり、全体的なパフォーマンスが低下します。

-   ログ レベルが "詳細" に設定されている場合は、s3 などのより強力なデータベースを選択してください。 非公式の社内テストによると、s3 価格レベルは、2 ノード、並列実行数 128、"詳細" ログ レベルでの SSIS パッケージの実行をサポートできます。

データベースの価格レベルは、Azure Portal でわかる[データベース トランザクション単位](../sql-database/sql-database-what-is-a-dtu.md) (DTU) の使用状況情報に基づいて調整することもできます。

## <a name="design-for-high-performance"></a>高パフォーマンス用の設計
Azure で実行するための SSIS パッケージの設計は、オンプレミスで実行するためのパッケージの設計とは異なります。 複数の独立したタスクを同じパッケージにまとめるのではなく、Azure SSIS IR での実行効率を上げるために複数のパッケージに分割します。 相互の完了を待機する必要がないように、パッケージごとにパッケージ実行を作成します。 このアプローチでは、Azure-SSIS 統合ランタイムのスケーラビリティのメリットがあり、全体的なスループットが向上します。

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについてさらに学習します。 「[Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime)」をご覧ください。
