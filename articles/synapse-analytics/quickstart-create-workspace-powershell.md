---
title: 'クイックスタート: Azure PowerShell を使用して Synapse ワークスペースを作成する'
description: このガイドの手順に従い、Azure PowerShell を使用して、Azure Synapse ワークスペースを作成します。
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675757"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure Synapse ワークスペースを作成する

Azure PowerShell は、PowerShell から Azure リソースを直接管理するためのコマンドレットのセットです。 ブラウザーで、Azure Cloud Shell を使用して操作することができます。 macOS、Linux、または Windows にインストールすることもできます。

このクイックスタートでは、Azure PowerShell を使用して Synapse ワークスペースを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

- [Azure Data Lake Storage Gen2 ストレージ アカウント](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure Synapse ワークスペースは、選択した ADLS Gen2 アカウントの読み取りと書き込みを行うことができる必要があります。 プライマリ ストレージ アカウントとしてリンクするストレージ アカウントについては、「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)」の説明に従って、その作成時に **階層型名前空間** を有効にする必要があります。

Cloud Shell の使用を選択した場合、詳細については「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。

### <a name="install-the-azure-powershell-module-locally"></a>Azure PowerShell モジュールをローカルにインストールする

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

Azure PowerShell を使用した認証の詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

### <a name="install-the-azure-synapse-powershell-module"></a>Azure Synapse PowerShell モジュールをインストールする

> [!IMPORTANT]
> **Az.Synapse** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Azure PowerShell を使用して Azure Synapse ワークスペースを作成する

1. Azure Synapse ワークスペース用のリソースを作成するために、必要な環境変数を定義します。

   |        変数名        |                                                 説明                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | 既存の ADLS Gen2 ストレージ アカウントの名前。                                                           |
   | StorageAccountResourceGroup | 既存の ADLS Gen2 ストレージ アカウントのリソース グループの名前。                                             |
   | FileShareName               | 既存のストレージ ファイル システムの名前。                                                                  |
   | SynapseResourceGroup        | Azure Synapse リソース グループの新しい名前を選択します。                                                    |
   | リージョン                      | いずれかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/#overview)を選択します。 |
   | SynapseWorkspaceName        | 新しい Azure Synapse ワークスペースの一意の名前を選択します。                                                  |
   | SqlUser                     | 新しいユーザー名の値を選択します。                                                                          |
   | SqlPassword                 | セキュリティで保護されたパスワードを選択します。                                                                                   |
   | ClientIP                    | PowerShell を実行しているシステムのパブリック IP アドレス。                                             |
   |                             |                                                                                                             |

1. Azure Synapse ワークスペースのコンテナーとしてリソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Azure Synapse ワークスペースを作成します。

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Azure Synapse ワークスペースの Web URL と開発 URL を取得します。

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. ご使用のマシンから Azure Synapse ワークスペースへのアクセスを許可するファイアウォール規則を作成します。

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. 環境変数 `WorkspaceWeb` に格納された Azure Synapse ワークスペースの Web URL アドレスを開いてワークスペースにアクセスします。

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse ワークスペース Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、Azure Synapse ワークスペースを削除します。

> [!WARNING]
> Azure Synapse ワークスペースを削除すると、分析エンジンと、含まれている SQL プールとワークスペース メタデータのデータベースに格納されているデータが削除されます。 以後、SQL または Apache Spark のエンドポイントには接続できません。 すべてのコード成果物が削除されます (クエリ、ノートブック、ジョブ定義、およびパイプライン)。 ワークスペースを削除しても、そのワークスペースにリンクされている Data Lake Store Gen2 のデータには影響 **しません**。

この記事で作成した Azure Synapse ワークスペースが不要になった場合は、次の例を実行して削除できます。

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>次のステップ

次に、データの分析と探索を開始するために、[SQL プールを作成](quickstart-create-sql-pool-studio.md)するか、[Apache Spark プールを作成](quickstart-create-apache-spark-pool-studio.md)することができます。