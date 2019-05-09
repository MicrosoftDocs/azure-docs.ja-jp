---
title: Azure-SSIS 統合ランタイムのパフォーマンスを構成する | Microsoft Docs
description: Azure-SSIS 統合ランタイムのプロパティを高パフォーマンス用に構成する方法について説明します。
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576873"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Azure-SSIS 統合ランタイムを高パフォーマンス用に構成する

この記事では、Azure-SSIS 統合ランタイム (IR) を高パフォーマンス用に構成する方法について説明します。 Azure-SSIS IR を使うと、SQL Server Integration Services (SSIS) パッケージを Azure にデプロイして実行できます。 Azure-SSIS IR について詳しくは、「[Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime)」をご覧ください。 Azure での SSIS パッケージのデプロイと実行については、「[Lift and shift SQL Server Integration Services workloads to the cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)」(SQL Server Integration Services のワークロードをクラウドにリフト アンド シフトする) をご覧ください。

> [!IMPORTANT]
> この記事には、SSIS 開発チームのメンバーによって行われた社内テストにおけるパフォーマンスの結果と観察結果が含まれています。 他の環境では結果が異なる場合があります。 コストとパフォーマンスの両方に影響する構成設定を確定する前に、独自のテストを実行してください。

## <a name="properties-to-configure"></a>構成するプロパティ

構成スクリプトの次の部分では、Azure-SSIS 統合ランタイムを作成するときに構成できるプロパティが示されています。 PowerShell の完全なスクリプトと説明については、「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure-powershell.md)」をご覧ください。

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
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
-   Standard\_D4\_v2.

SSIS エンジニアリング チームによる非公式な社内テストでは、A シリーズより D シリーズの方が SSIS パッケージの実行に適しています。

-   価格に対するパフォーマンスは、A シリーズより D シリーズの方が高くなります。
-   D シリーズのスループットは、同じ価格の A シリーズより高くなります。

### <a name="configure-for-execution-speed"></a>実行速度が上がるように構成する
実行するパッケージの数が多くなく、パッケージをすばやく実行したい場合は、次の表の情報を使って、シナリオに適した仮想マシンの種類を選びます。

このデータは、1 つの worker ノードでの 1 つのパッケージの実行を表します。 このパッケージは、Azure Blob Storage から名と姓の列を含むレコードを 1,000 万件読み込み、フル ネームの列を生成して、フル ネームが 20 文字より長いレコードを Azure Blob Storage に書き込みます。

![SSIS 統合ランタイム パッケージの実行速度](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>全体的なスループットが上がるように構成する

実行するパッケージの数が多く、全体的なスループットが最も重要な場合は、次の表の情報を使って、シナリオに適した仮想マシンの種類を選びます。

![SSIS 統合ランタイムの全体的な最大スループット](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** は、統合ランタイムのスケーラビリティを調整します。 統合ランタイムのスループットは、**AzureSSISNodeNumber** に比例します。 最初は **AzureSSISNodeNumber** に小さい値は設定した後、統合ランタイムのスループットを監視しながら、シナリオに合った値に調整します。 worker ノードの数の再構成については、「[Azure-SSIS 統合ランタイムを管理する](manage-azure-ssis-integration-runtime.md)」をご覧ください。

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

既に強力な worker ノードを使ってパッケージを実行している場合、**AzureSSISMaxParallelExecutionsPerNode** の値を大きくすると、統合ランタイムの全体的なスループットが上がる可能性があります。 Standard_D1_v2 ノードでは、ノードあたり 1 ～ 4 の並列実行がサポートされています。 その他のすべての種類のノードでは、ノードあたり 1 ～ 8 の並列実行がサポートされています。
パッケージのコストと、worker ノードの次の構成に基づいて、適切な値を予測できます。 詳しくは、「[汎用仮想マシンのサイズ](../virtual-machines/windows/sizes-general.md)」をご覧ください。

| Size             | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク パフォーマンス (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000/46/23                                             | 2 / 2x500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 4 / 4x500                         | 2/1,500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 8 / 8x500                         | 4/3,000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 16 / 16x500                       | 8/6,000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000/80/40                                             | 8 / 8x500                         | 4/1,000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000/160/80                                            | 16 / 16x500                       | 8/2,000                                       |

**AzureSSISMaxParallelExecutionsPerNode** プロパティの適切な値の設定に関するガイドラインは次のとおりです。 

1. 最初は小さな値に設定します。
2. 値を少しだけ大きくして、全体的なスループットが向上するかどうかを確認します。
3. 全体的なスループットが最大値に達したら、値を増やすのを止めます。

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** は、Azure SQL Database の SSIS カタログ データベース (SSISDB) の価格レベルです。 この設定は、IR インスタンス内の worker の最大数、パッケージの実行をキューに入れる速度、実行ログを読み込む速度に影響します。

-   パッケージの実行をキューに入れる速度および実行ログを読み込む速度が重要でない場合は、最低のデータベース価格レベルを選んでかまいません。 Basic 価格レベルの Azure SQL Database は、1 つの統合ランタイム インスタンスで 8 個の worker をサポートします。

-   worker の数が 8 より多い場合、またはコアの数が 50 を超える場合は、Basic より強力なデータベースを選びます。 そうしないと、データベースが統合ランタイム インスタンスのボトルネックになり、全体的なパフォーマンスが低下します。

データベースの価格レベルは、Azure Portal でわかる[データベース トランザクション単位](../sql-database/sql-database-what-is-a-dtu.md) (DTU) の使用状況情報に基づいて調整することもできます。

## <a name="design-for-high-performance"></a>高パフォーマンス用の設計
Azure で実行するための SSIS パッケージの設計は、オンプレミスで実行するためのパッケージの設計とは異なります。 複数の独立したタスクを同じパッケージにまとめるのではなく、Azure SSIS IR での実行効率を上げるために複数のパッケージに分割します。 相互の完了を待機する必要がないように、パッケージごとにパッケージ実行を作成します。 このアプローチでは、Azure-SSIS 統合ランタイムのスケーラビリティのメリットがあり、全体的なスループットが向上します。

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについてさらに学習します。 「[Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime)」をご覧ください。
