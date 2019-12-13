---
title: Azure-SSIS Integration Runtime を SQL Database のフェールオーバー用に構成する
description: この記事では、Azure SQL Database geo レプリケーションを使用して SSISDB データベース用に Azure-SSIS Integration Runtime とフェールオーバーを構成する方法について説明します
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928494"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Azure SQL Database geo レプリケーションを使用して Azure-SSIS Integration Runtime とフェールオーバーを構成する

この記事では、Azure SQL Database geo レプリケーションを使用して、SSISDB データベース用に Azure-SSIS Integration Runtime を構成する方法について説明します。 フェールオーバーが発生したときに、セカンダリ データベースにより Azure-SSIS IR の動作を維持できます。

SQL Database の geo レプリケーションとフェールオーバーの詳細については、[アクティブ geo レプリケーションと自動フェールオーバー グループの概要](../sql-database/sql-database-geo-replication-overview.md)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>シナリオ 1 - Azure-SSIS IR が読み取り/書き込みリスナー エンドポイントを指している

### <a name="conditions"></a>条件

このセクションは、次の条件に該当する場合に適用されます。

- Azure-SSIS IR がフェールオーバー グループの読み取り/書き込みリスナー エンドポイントを指している。

  AND

- SQL Database サーバーが仮想ネットワーク サービス エンドポイント規則で "*構成されていない*"。

### <a name="solution"></a>解決策

フェールオーバーが発生すると、Azure-SSIS IR に対して透過的になります。 Azure-SSIS IR は、フェールオーバー グループの新しいプライマリに自動的に接続します。

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>シナリオ 2 - Azure-SSIS IR がプライマリ サーバー エンドポイントを指している

### <a name="conditions"></a>条件

このセクションは、次の条件のいずれか 1 つが該当する場合に適用されます。

- Azure-SSIS IR がフェールオーバー グループのプライマリ サーバー エンドポイントを指している。 このエンドポイントはフェールオーバーが発生すると変わります。

  または

- Azure SQL Database サーバーが仮想ネットワーク サービス エンドポイント規則で構成されている。

  または

- データベース サーバーが仮想ネットワークで構成された SQL Database Managed Instance である。

### <a name="solution"></a>解決策

フェールオーバーが発生すると、次のことを行う必要があります。

1. Azure-SSIS IR を停止します。

2. 新しいプライマリ エンドポイントおよび新しいリージョンの仮想ネットワークをポイントするように IR を再構成する。

3. IR を再起動する。

以降のセクションで、これらの手順について詳しく説明します。

### <a name="prerequisites"></a>前提条件

- Azure SQL Database サーバーが同時に停止した場合に備えて、サーバーのディザスター リカバリーを有効にしていることを確認します。 詳細情報については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」を参照してください。

- 現在のリージョンで仮想ネットワークを使用している場合は、新しいリージョン内の別の仮想ネットワークを使用して、Azure-SSIS 統合ランタイムを接続する必要があります。 詳細情報については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

- カスタム セットアップを使用している場合は、カスタム セットアップ スクリプトと関連ファイルを格納する BLOB コンテナー用の別のSAS URI を準備して、停止中も引き続きアクセスできるようにする必要があります。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

### <a name="steps"></a>手順

Azure-SSIS IR を停止し、IR を新しいリージョンに切り替え、再び開始するには、以下の手順を実行します。

1. 元のリージョンで IR を停止します。

2. PowerShell で次のコマンドを呼び出して、新しい設定で IR を更新します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    この PowerShell コマンドの詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。

3. IR を再び開始します。

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>シナリオ 3 - 既存の SSISDB (SSIS カタログ) の新しい Azure-SSIS IR へのアタッチ

現在のリージョンで ADF または、AZURE-SSIS IR の障害が発生した場合、新しいリージョンで新しい Azure-SSIS IR を使用して SSISDB が動作し続けるようにすることができます。

### <a name="prerequisites"></a>前提条件

- 現在のリージョンで仮想ネットワークを使用している場合は、新しいリージョン内の別の仮想ネットワークを使用して、Azure-SSIS 統合ランタイムを接続する必要があります。 詳細情報については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

- カスタム セットアップを使用している場合は、カスタム セットアップ スクリプトと関連ファイルを格納する BLOB コンテナー用の別のSAS URI を準備して、停止中も引き続きアクセスできるようにする必要があります。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

### <a name="steps"></a>手順

Azure-SSIS IR を停止し、IR を新しいリージョンに切り替え、再び開始するには、以下の手順を実行します。

1. ストアド プロシージャを実行して、SSISDB を **\<new_data_factory_name\>** または **\<new_integration_runtime_name\>** にアタッチさせます。
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. 新しいリージョンに **\<new_data_factory_name\>**  という名前の新しいデータ ファクトリを作成します。 詳細については、データ ファクトリの作成を参照してください。

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    この PowerShell コマンドの詳細については、[PowerShell を使用した Azure データ ファクトリの作成](quickstart-create-data-factory-powershell.md)に関するページを参照してください

3. Azure PowerShell を使用して、新しいリージョンに **\<new_integration_runtime_name\>** という名前の新しい Azure-SSIS IR を作成します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    この PowerShell コマンドの詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。

4. IR を再び開始します。

## <a name="next-steps"></a>次の手順

Azure SSIS IR の以下のその他の構成オプションを検討してください。

- [Azure-SSIS 統合ランタイムを高パフォーマンス用に構成する](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 統合ランタイムの設定をカスタマイズする](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS 統合ランタイムの Enterprise Edition をプロビジョニングする](how-to-configure-azure-ssis-ir-enterprise-edition.md)
