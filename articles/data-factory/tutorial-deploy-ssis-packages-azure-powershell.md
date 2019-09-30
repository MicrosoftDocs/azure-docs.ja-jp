---
title: PowerShell を使用した Azure-SSIS 統合ランタイムのプロビジョニング | Microsoft Docs
description: Azure 上で SSIS パッケージをデプロイして実行できるように、PowerShell を使用して Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484773"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>PowerShell を使用して Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする

このチュートリアルでは、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を Azure Data Factory (ADF) にプロビジョニングする手順について説明します。 Azure-SSIS IR では、Azure SQL Database サーバー/マネージド インスタンスによってホストされている SSIS カタログ (SSISDB) にデプロイされたパッケージ (プロジェクト デプロイ モデル) と、ファイル システム/ファイル共有/Azure Files にデプロイされたパッケージ (パッケージ デプロイ モデル) の実行がサポートされます。 Azure-SSIS IR がプロビジョニングされると、SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) などの使い慣れたツールや `dtinstall`/`dtutil`/`dtexec` などのコマンド ライン ユーティリティを使用して、Azure でパッケージをデプロイして実行できます。 このチュートリアルでは、次の手順を実行します。

> [!NOTE]
> この記事では、Azure PowerShell を使用して Azure-SSIS IR をプロビジョニングします。 Azure portal/ADF アプリを使用して Azure-SSIS IR をプロビジョニングするには、[Azure-SSIS IR のプロビジョニングに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)をご覧ください。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * 完全なスクリプトを確認します
> * SSIS パッケージのデプロイ

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事をご覧ください。
- **Azure SQL Database サーバー (省略可能)** 。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure portal で作成しておいてください。 その後、ADF によって、このデータベース サーバーに SSISDB が作成されます。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンをまたがることなく SSISDB に実行ログを書き込むことができます。 
    - SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択するときのガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プールとマネージド インスタンスの比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関する記事を参照してください。 SSISDB をホストするために、仮想ネットワーク内に仮想ネットワーク サービス エンドポイント/マネージド インスタンスを伴う Azure SQL Database サーバーを使用するか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 仮想ネットワーク サービス エンドポイント/マネージド インスタンスで Azure SQL Database サーバーを使用して SSISDB をホストする場合、これは該当しません。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。
    - サーバー管理者の資格情報による SQL 認証または ADF のマネージド ID による Azure Active Directory (AAD) 認証を使用して、データベース サーバーに接続できます。  後者の場合、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。
    - データベース サーバーに SSISDB がまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB の使用はサポートされていません。
- **Azure PowerShell**。 PowerShell スクリプトを実行して Azure-SSIS IR をプロビジョニングする場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-Az-ps)に関する記事の指示に従ってください。

> [!NOTE]
> - ADF と Azure-SSIS IR が現在使用可能な Azure リージョンの一覧については、[リージョン別の ADF と SSIS IR の利用可能性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関する記事を参照してください。 

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE の起動

管理特権を使用して **Windows PowerShell ISE** を起動します。 

## <a name="create-variables"></a>変数の作成

次のスクリプトをコピーして貼り付け、変数の値を指定します。 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
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
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>サインインとサブスクリプションの選択

サインインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>データベース サーバーへの接続の検証

Azure SQL Database サーバーを検証するには、次のスクリプトを追加します。 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

スクリプトの一部として Azure SQL データベースを作成するには、次の例を参照してください。 

まだ定義されていない変数の値を設定します。 例: SSISDBServerName、FirewallIPAddress。 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

リソース グループが既に存在する場合は、このコードをスクリプトにコピーしないでください。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Data Factory を作成する。

Data Factory を作成するには、次のコマンドを実行します。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>統合ランタイムの作成

Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。

SSISDB を使用しない場合は、CatalogServerEndpoint、CatalogPricingTier、CatalogAdminCredential の各パラメーターを省略できます。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>統合ランタイムの起動

Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> この処理は、カスタムのセットアップ時間があればそれを除いて、5 分以内で完了するはずです。
>
> SSISDB を使用する場合、ADF サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 
> 
> Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントのインストールもできます。[Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください。

## <a name="full-script"></a>完全なスクリプト

このセクション内の PowerShell スクリプトでは、SSIS パッケージを実行する Azure-SSIS IR のインスタンスを構成します。 このスクリプトを正常に実行すると、Azure で SSIS パッケージをデプロイして実行できます。

1. Windows PowerShell Integrated Scripting Environment (ISE) を起動します。
2. ISE のコマンド プロンプトで次のコマンドを実行します。  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. このセクションの PowerShell スクリプトをコピーして ISE に貼り付けます。
4. スクリプトの先頭ですべてのパラメーターに適切な値を指定します。
5. スクリプトを実行します。 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
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
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR の監視と管理

Azure-SSIS IR の監視と管理の詳細については、次の記事をご覧ください。 

- [Azure-SSIS IR の監視](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS IR の管理](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ

SSISDB を使用する場合は、そこにパッケージをデプロイし、データベース サーバーにそのサーバー エンドポイント経由で接続する SSDT ツールまたは SSMS ツールを使用して Azure-SSIS IR でそれらのパッケージを実行できます。 パブリック エンドポイントを持つ Azure SQL Database サーバー/マネージド インスタンスの場合、サーバー エンドポイントの形式はそれぞれ `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342` です。 SSISDB を使用しない場合、パッケージをファイル システム/ファイル共有/Azure Files にデプロイして、`dtinstall`/`dtutil`/`dtexec` コマンド ライン ユーティリティを使用して Azure-SSIS IR で実行できます。 詳しくは、[SSIS パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 どちらの場合も、ADF パイプラインの SSIS パッケージの実行アクティビティを使用して、デプロイしたパッケージを Azure-SSIS IR で実行することもできます。[ファースト クラスの ADF アクティビティとしての SSIS パッケージ実行の呼び出し](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。

SSIS ドキュメントの次の記事もご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * 完全なスクリプトを確認します
> * SSIS パッケージのデプロイ

Azure-SSIS Integration Runtime のカスタマイズの詳細については、次の記事に進んでください。

> [!div class="nextstepaction"]
>[Azure-SSIS IR のカスタマイズ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)