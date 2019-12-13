---
title: PowerShell で共有のセルフホステッド統合ランタイムを作成する
description: Azure Data Factory 内で共有のセルフホステッド統合ランタイムを作成する方法について説明します。これにより、複数のデータ ファクトリから統合ランタイムにアクセスできます。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: 2b83c61d05cbc9d84c74d03004839a21505519fa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928461"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で共有のセルフホステッド統合ランタイムを作成する

このガイドでは、Azure Data Factory で共有のセルフホステッド統合ランタイムを作成する方法について説明します。 これにより、共有のセルフホステッド統合ランタイムを他のデータ ファクトリで使用できます。

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Azure Data Factory の UI を使用して共有のセルフホステッド IR を作成する

Azure Data Factory の UI を使用して共有のセルフホステッド IR を作成するには、次の手順を実行します。

1. 共有されるセルフホステッド IR で、リンクされた IR を作成するデータ ファクトリへのアクセス許可を付与します。
      
    ![[共有] タブのアクセス許可を付与するためのボタン](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![アクセス許可を割り当てるための選択](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. 共有されるセルフホステッド IR のリソース ID をメモしておきます。
      
   ![リソース ID の場所](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. アクセス許可が付与されたデータ ファクトリで、新しいセルフホステッド IR (共有) を作成して、リソース ID を入力します。
      
   ![リンクされたセルフホステッド統合ランタイムを作成するためのボタン](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![名前とリソース ID のボックス](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Azure PowerShell を使用して共有のセルフホステッド IR を作成する

Azure PowerShell を使用して共有のセルフホステッド IR を作成するには、次の手順を実行します。 
1. データ ファクトリを作成します。 
1. セルフホステッド統合ランタイムを作成します。
1. セルフホステッド統合ランタイムを他のデータ ファクトリと共有します。
1. リンクされた統合ランタイムを作成します。
1. 共有を取り消します。

### <a name="prerequisites"></a>前提条件 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 

- **Azure PowerShell**。 「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」に記載されている手順に従います。 PowerShell を使用してスクリプトを実行し、他のデータ ファクトリと共有できるセルフホステッド統合ランタイムを作成できます。 

> [!NOTE]  
> 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)」ページで目的のリージョンを選択してください。

### <a name="create-a-data-factory"></a>Data Factory の作成

1. Windows PowerShell Integrated Scripting Environment (ISE) を起動します。

1. 変数を作成します。 次のスクリプトをコピーして貼り付けます。 **SubscriptionName** や **ResourceGroupName** などの変数を実際の値に置き換えます。 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. サインインしてサブスクリプションを選択します。 サインインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. リソース グループとデータ ファクトリを作成します。

    > [!NOTE]  
    > この手順は省略可能です。 既にデータ ファクトリがある場合は、この手順をスキップしてください。 

    [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを WestEurope の場所に作成します。 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Data Factory を作成するには、次のコマンドを実行します。 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

> [!NOTE]  
> この手順は省略可能です。 他のデータ ファクトリと共有するセルフホステッド統合ランタイムが既にある場合は、この手順をスキップしてください。

次のコマンドを実行して、セルフホステッド統合ランタイムを作成します。

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>統合ランタイムの認証キーを取得してノードを登録する

次のコマンドを実行して、セルフホステッド統合ランタイムの認証キーを取得します。

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

応答には、このセルフホステッド統合ランタイムの認証キーが含まれています。 このキーは、統合ランタイム ノードを登録するときに使用します。

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをインストールして登録する

1. セルフホステッド統合ランタイムのインストーラーを「[Azure Data Factory Integration Runtime](https://aka.ms/dmg)」からダウンロードします。

2. インストーラーを実行して、セルフホステッド統合をローカル コンピューターにインストールします。

3. 前の手順で取得した認証キーを使用して、新しいセルフホステッド統合を登録します。

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>セルフホステッド統合ランタイムを他のデータ ファクトリと共有する

#### <a name="create-another-data-factory"></a>別のデータ ファクトリを作成する

> [!NOTE]  
> この手順は省略可能です。 共有先のデータ ファクトリが既にある場合は、この手順をスキップしてください。

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>アクセス許可を付与する

作成して登録したセルフホステッド統合ランタイムにアクセスする必要があるデータ ファクトリに対してアクセス許可を付与します。

> [!IMPORTANT]  
> この手順はスキップしないでください。

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>リンクされたセルフホステッド統合ランタイムを作成する

次のコマンドを実行して、リンクされたセルフホステッド統合ランタイムを作成します。

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

これで、このリンクされた統合ランタイムを任意のリンクされたサービスで使用できます。 リンクされた統合ランタイムでは、共有された統合ランタイムを使用してアクティビティを実行します。

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>データ ファクトリからの統合ランタイムの共有を取り消す

共有された統合ランタイムからデータ ファクトリのアクセス権を取り消すには、次のコマンドを実行します。

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

既存のリンクされた統合ランタイムを削除するには、共有された統合ランタイムに対して次のコマンドを実行します。

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>次の手順

- [Azure Data Factory の統合ランタイムの概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)を確認します。

- [Azure portal 上でセルフホステッド統合ランタイムを作成する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)方法を確認します。
