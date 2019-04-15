---
title: Azure Stack ストレージ用のツール | Microsoft Docs
description: Azure Stack Storage のデータ転送ツールについて説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 4385e982b2a1da52ae55acf50c601108863c452a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905955"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Azure Stack ストレージのデータ転送ツールの使用

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Microsoft Azure Stack は、ディスク、BLOB、テーブル、キュー、およびアカウント管理機能のストレージ サービスのセットを提供します。 Azure Stack ストレージのデータを管理または移動する場合は、Azure ストレージ ツールのセットを使用することができます。 この記事では、使用可能なツールの概要について説明します。

お客様の要件によって、次のツールのうちどれが最適であるかが決まります。

* [AzCopy](#azcopy)

    ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができるダウンロード可能なストレージ固有のコマンドライン ユーティリティです。

* [Azure PowerShell](#azure-powershell)

    タスク ベースのコマンドライン シェルであり、特にシステム管理用に設計されたスクリプト言語です。

* [Azure CLI](#azure-cli)

    オープン ソースでクロスプラットフォームの Azure および Azure Stack Platform で使用できるコマンド群が提供されます。

* [Microsoft ストレージ エクスプローラー](#microsoft-azure-storage-explorer)

    ユーザー インターフェイスを備えた使いやすいスタンドアロンのアプリ。

* [blobfuse](#blobfuse)

    ストレージ アカウント内の既存のブロック BLOB データに Linux ファイル システム経由でアクセスできるようにする、Azure Blob Storage 用の仮想ファイル システム ドライバーです。 

Azure と Azure Stack の間のストレージ サービスの違いにより、次のセクションで説明されている各ツールにはいくつかの固有の要件があります。 Azure Stack ストレージと Azure ストレージの間の比較については、「[Azure Stack ストレージ:違いと考慮事項](azure-stack-acs-differences.md)」をご覧ください。

## <a name="azcopy"></a>AzCopy

AzCopy は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure の Blob Storage と Table Storage との間でデータをコピーするために設計されたコマンドライン ユーティリティです。 ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができます。

### <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール

AzCopy ユーティリティには、2 つのバージョンがあります。Windows 上の AzCopy と Linux 上の AzCopy です。

 - **AzCopy on Windows**
    - Azure Stack 用のサポートされているバージョンの AzCopy をダウンロードします。 Azure と同様の方法で、Azure Stack 上で AzCopy をインストールして使用することができます 詳細については、[Windows での AzCopy](../../storage/common/storage-use-azcopy.md) に関するページを参照してください。
        - 1811 更新プログラム以降のバージョンの場合は、[AzCopy 7.3.0 をダウンロード](https://aka.ms/azcopyforazurestack20171109)します。
        - それより前のバージョン (1802 から 1809 までの更新プログラム) では、[AzCopy 7.1.0 をダウンロード](https://aka.ms/azcopyforazurestack20170417)します。

 - **AzCopy on Linux**

    - Azure と同様の方法で、Azure Stack 上で AzCopy をインストールして使用することができます 詳細については、[Linux での AzCopy](../../storage/common/storage-use-azcopy-linux.md) に関するページを参照してください。
    - 以前のバージョン (1802 から 1809 までの更新プログラム) については、「[AzCopy 7.1 以前のバージョンのインストール手順](../../storage/common/storage-use-azcopy-linux.md#installation-steps-for-azcopy-71-and-earlier-versions)」を参照してください。

### <a name="azcopy-command-examples-for-data-transfer"></a>データ転送するための AzCopy コマンドの例

以下の例では、Azure Stack BLOB との間でデータをコピーする代表的なシナリオを紹介しています。 詳細については、[Windows 上の AzCopy](../../storage/common/storage-use-azcopy.md) と [Linux 上の AzCopy](../../storage/common/storage-use-azcopy-linux.md) に関するページを参照してください。

### <a name="download-all-blobs-to-a-local-disk"></a>すべての BLOB をローカル ディスクにダウンロードする

** Windows**

```shell
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```

**Linux**

```bash
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

### <a name="upload-single-file-to-virtual-directory"></a>1 つのファイルを仮想ディレクトリにアップロードする

** Windows**

```shell
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

```bash
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
```

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Azure と Azure Stack ストレージ間でデータを移動します

Azure ストレージと Azure Stack との間の非同期のデータ転送がサポートされていません。 **/SyncCopy** または **--sync-copy** オプションを使用して転送を指定する必要があります。

** Windows**

```shell
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

**Linux**

```bash
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
```

### <a name="azcopy-known-issues"></a>Azcopy の既知の問題

 - ファイル ストレージは、Azure Stack で使用できないために、ファイル ストアに関する AzCopy 操作は使用できません。
 - Azure ストレージと Azure Stack との間の非同期のデータ転送がサポートされていません。 **/SyncCopy** オプションを使用して転送を指定し、データをコピーすることができます。
 - Linux バージョンの Azcopy は、1802 更新プログラム以降のバージョンのみをサポートしています。 Table service はサポートしていません。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell は、Azure と Azure Stack の両方ででサービスを管理するためのコマンドレットを提供するモジュールです。 タスク ベースのコマンドライン シェルであり、特にシステム管理用に設計されたスクリプト言語です。

### <a name="install-and-configure-powershell-for-azure-stack"></a>PowerShell for Azure Stack のインストールと構成

Azure Stack を使用するには、Azure Stack と互換性のある Azure PowerShell モジュールが必要です。 詳細については、「[Install PowerShell for Azure Stack](azure-stack-powershell-install.md)」(PowerShell for Azure Stack のインストール) および「[Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md)」(Azure Stack ユーザーの PowerShell 環境を構成する) を参照してください。

### <a name="powershell-sample-script-for-azure-stack"></a>Azure Stack 用の PowerShell サンプル スクリプト 

このサンプルでは、[PowerShell for Azure Stack を正常にインストール](azure-stack-powershell-install.md)していることを前提としています。 このスクリプトは、構成を完了し、ローカルの PowerShell 環境にアカウントを追加するために Azure Stack テナントの資格情報を要求するために役立ちます。 次に、スクリプトは、既定の Azure サブスクリプションを設定して、Azure 内に新しいストレージ アカウントを作成し、この新しいストレージ アカウントに新しいコンテナーを作成して、既存の画像ファイル (BLOB) をこのコンテナーにアップロードします。 このスクリプトにより、コンテナー内のすべての BLOB がリストされると、ローカル コンピューターに新しい格納先ディレクトリが作成され、画像ファイルがダウンロードされます。

1. [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。
2. [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。
3. **Windows PowerShell ISE** を開いて、**管理者として実行し**、**[ファイル]** > **[新規作成]** の順にクリックして、新しいスクリプト ファイルを作成します。
4. 次のスクリプトをコピーして新しいスクリプト ファイルに貼り付けます。
5. 構成設定に基づいてスクリプト変数を更新します。
   > [!NOTE]
   > このスクリプトは、**AzureStack_Tools** のルート ディレクトリで実行する必要があります。

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>PowerShell の既知の問題

Azure Stack の現在の互換性のある Azure PowerShell モジュールのバージョンは、ユーザー操作の場合は 1.2.11 です。 これは Azure PowerShell の最新バージョンとは異なります。 この違いは、ストレージ サービスの操作に影響します。

バージョン 1.2.11 では `Get-AzureRmStorageAccountKey` の戻り値の形式に `Key1` と `Key2` という 2 つのプロパティがありますが、Azure 最新バージョンは、すべてのアカウント キーが含まれる配列を返します。

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

詳細については、[Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey) を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 macOS、Linux、および Windows 上にインストールし、コマンド ラインから実行できます。

Azure CLI は、コマンド ラインから Azure リソースを管理したり、Azure Resource Manager を操作対象とする自動化スクリプトを作成したりするために最適化されています。 豊富なデータ アクセスを含む、Azure Stack Portal にあるものと同じ機能の多くを使用できます。

Azure Stack には、Azure CLI バージョン 2.0 以降が必要です。 Azure Stack と Azure CLI のインストールと構成の詳細については、「[Install and configure Azure Stack CLI](azure-stack-version-profiles-azurecli2.md)」(Azure Stack CLI のインストールと構成) を参照してください。 Azure CLI を使用して Azure Stack ストレージ アカウント内のリソースを操作するいくつかのタスクを実行する方法の詳細については、「[Azure ストレージでの Azure CLI の使用](../../storage/storage-azure-cli.md)」を参照してください

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure Stack 用の Azure CLI サンプル スクリプト

CLI のインストールと構成が完了したら、次の手順を試し、Azure Stack ストレージ リソースと対話する小さなシェル サンプル スクリプトを操作することができます。 スクリプトを実行すると、次のアクションが行われます。

* ストレージ アカウントに新しいコンテナーを作成する。
* 既存のファイルを (BLOB として) コンテナーにアップロードする。
* コンテナー内のすべての BLOB を一覧表示する。
* 指定したローカル コンピューター上の宛先にファイルをダウンロードする。

このスクリプトを実行する前に、ターゲットの Azure Stack に正常に接続およびサインインできるようにしておきます。

1. 好みのテキスト エディターを開き、前述のスクリプトをコピーしてエディターに貼り付けます。
2. 構成の設定を反映するようにスクリプトの変数を更新します。
3. 必要な変数を更新したら、スクリプトを保存してエディターを終了します。 スクリプト名を **my_storage_sample.sh** に指定したと仮定して、次のステップに移ります。
4. 必要に応じてスクリプトを実行可能ファイルとしてマークします。 `chmod +x my_storage_sample.sh`
5. スクリプトを実行します。 たとえば Bash の場合は次のようになります。 `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー

Microsoft Azure ストレージ エクスプローラーは、Microsoft のスタンドアロン アプリです。 これを使用すると、Windows、macOS、Linux のコンピューターで Azure ストレージと Azure Stack ストレージ両方のデータを簡単に操作できます。 Azure Stack ストレージ データを簡単に管理する方法が必要な場合は、Microsoft Azure ストレージ エクスプローラーの使用を検討してください。

* Azure Stack を操作する Azure ストレージ エクスプローラーの構成について詳しくは、「[Azure Stack サブスクリプションに Microsoft Azure ストレージ エクスプローラーを接続する](azure-stack-storage-connect-se.md)」をご覧ください。
* Microsoft Azure ストレージ エクスプローラーについて詳しくは、「[Storage Explorer の概要](../../vs-azure-tools-storage-manage-with-storage-explorer.md)」をご覧ください

## <a name="blobfuse"></a>blobfuse 

[blobfuse](https://github.com/Azure/azure-storage-fuse) は、ストレージ アカウント内の既存のブロック BLOB データに Linux ファイル システム経由でアクセスできるようにする、Azure Blob Storage 用の仮想ファイル システム ドライバーです。 Azure Blob Storage はオブジェクト ストレージ サービスであるため、階層的な名前空間を持っていません。 blobfuse は、フォワードスラッシュ `/` を区切り記号として使用して、仮想ディレクトリ スキームによってこの名前空間を提供します。 blobfuse は、Azure と Azure Stack の両方で動作します。 

Linux 上で blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法の詳細については、「[blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)」を参照してください。 

Azure Stack の場合、マウントを準備する手順で Storage アカウントの資格情報を構成する際に、accountName、accountKey/sasToken、containerName に加えて **blobEndpoint** を指定する必要があります。 

Azure Stack 開発キットでは、blobEndpoint を `myaccount.blob.local.azurestack.external` にする必要があります。 Azure Stack 統合システムでは、エンドポイントが不明の場合は、クラウド管理者に問い合わせてください。 

accountKey と sasToken は一度に 1 つずつしか構成できないことに注意してください。 ストレージ アカウント キーを指定した資格情報構成ファイルの形式を次に示します。 

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

共有アクセス トークンを指定した資格情報構成ファイルの形式を次に示します。

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>次の手順

* [Azure Stack サブスクリプションにストレージ エクスプローラーを接続する](azure-stack-storage-connect-se.md)
* [ストレージ エクスプローラーの概要](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 互換ストレージ: 違いと考慮事項](azure-stack-acs-differences.md)
* [Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)
