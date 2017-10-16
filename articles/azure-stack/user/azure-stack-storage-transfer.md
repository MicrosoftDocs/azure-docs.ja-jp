---
title: "Azure Stack Storage のツール"
description: "Azure Stack Storage のデータ転送ツールについて説明します。"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>Azure Stack Storage のツール

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Microsoft Azure Stack は、ディスク、BLOB、テーブル、キュー、およびアカウント管理機能のストレージ サービスのセットを提供します。 Azure Stack Storage のデータを管理または移動する場合は、Azure Storage のツールのセットを使用ですることができます。 この記事では、使用可能なツールについて簡単に説明します。

最適なツールは、お客様の要件によって異なります。
* [AzCopy](#azcopy)

    ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができるダウンロード可能なストレージ固有のコマンドライン ユーティリティです。

* [Azure PowerShell](#azure-powershell)

    タスク ベースのコマンドライン シェルであり、特にシステム管理用に設計されたスクリプト言語です。

* [Azure CLI](#azure-cli)

    オープン ソースでクロスプラットフォームの Azure および Azure Stack Platform で使用できるコマンド群が提供されます。

* [Microsoft Azure Storage Explorer (プレビュー)](#microsoft-azure-storage-explorer)

    ユーザー インターフェイスを備えた使いやすいスタンドアロンのアプリ。

Azure と Azure Stack の間の記憶域サービスの違いにより、次のセクションで説明されている各ツールにはいくつかの固有の要件があります。 Azure Stack Storage と Azure Storage の間の比較については、「[Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md)」(Azure Stack Storage: 違いと考慮事項) を参照してください。


## <a name="azcopy"></a>AzCopy
AzCopy は、最適なパフォーマンスの単純なコマンドを使用して、Microsoft Azure BLOB とテーブルのストレージ間でデータをコピーするために設計されたコマンドライン ユーティリティです。 ストレージ アカウント内のあるオブジェクトから別のオブジェクトにデータをコピーしたり、ストレージ アカウント間でコピーしたりすることができます。 AzCopy には、AzCopy on Windows と AzCopy on Linux という 2 つのバージョンがあります。 Azure Stack は、Windows バージョンのみをサポートします。 
 
### <a name="download-and-install-azcopy"></a>AzCopy のダウンロードとインストール 
Azure Stack 用の AzCopy のサポートされている Windows バージョンを[ダウンロード](https://aka.ms/azcopyforazurestack)します。 Azure と同様の方法で、Azure Stack 上で AzCopy をインストールして使用することができます 詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../../storage/common/storage-use-azcopy.md)」を参照してください。 

### <a name="azcopy-command-examples-for-data-transfer"></a>データ転送するための AzCopy コマンドの例
以下の例では、Azure Stack BLOB との間でデータをコピーするさまざまなシナリオを紹介しています。 詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../../storage/storage-use-azcopy.md)」を参照してください。 
#### <a name="download-all-blobs-to-local-disk"></a>すべての BLOB をローカル ディスクにダウンロードします。
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>1 つのファイルを仮想ディレクトリにアップロードする 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Azure と Azure Stack Storage 間でデータを移動します。 
Azure Storage と Azure Stack との間の非同期のデータ転送がサポートされていません。 `/SyncCopy` オプションで転送を指定する必要があります。 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Azcopy の既知の問題
* ファイル ストレージは、Azure Stack で使用できないために、ファイル ストレージに関する AzCopy 操作は使用できません。
* Azure Storage と Azure Stack との間の非同期のデータ転送がサポートされていません。 `/SyncCopy` オプションを使用して転送を指定し、データをコピーすることができます。
* Azure Stack Storage では、Azcopy の Linux バージョンはサポートされていません。 

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
6. 注: このスクリプトには、ダウンロードした **AzureStack_Tools** のルートの下で実行する必要があります。 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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
Azure Stack の現在の互換性のある Azure PowerShell モジュールのバージョンは 1.2.10 です。 これは Azure PowerShell の最新バージョンとは異なります。 この違いは、ストレージ サービスの操作に影響します。

* バージョン 1.2.10 では `Get-AzureRmStorageAccountKey` の戻り値の形式に `Key1` と `Key2` という 2 つのプロパティがありますが、Azure 最新バージョンは、すべてのアカウント キーが含まれる配列を返します。
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   詳細については、[Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0) を参照してください。

## <a name="azure-cli"></a>Azure CLI
Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 macOS、Linux、および Windows 上にインストールし、コマンド ラインから実行できます。 

Azure CLI は、コマンド ラインから Azure リソースを管理したり、Azure Resource Manager を操作対象とする自動化スクリプトを作成したりするために最適化されています。 豊富なデータ アクセスを含む、Azure Stack Portal にあるものと同じ機能の多くを使用できます。

Azure Stack には、Azure CLI バージョン 2.0 が必要です。 Azure Stack と Azure CLI のインストールと構成の詳細については、「[Install and configure Azure Stack CLI](azure-stack-connect-cli.md)」(Azure Stack CLI のインストールと構成) を参照してください。 Azure CLI 2.0 を使用して Azure Stack Storage アカウント内のリソースを操作するいくつかのタスクを実行する方法の詳細については、「[Azure ストレージでの Azure CLI2.0 の使用](../../storage/storage-azure-cli.md)」を参照してください。

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure Stack 用の Azure CLI サンプル スクリプト 
CLI のインストールと構成が完了したら、次の手順を試し、Azure Stack Storage リソースと対話する小さなシェル サンプル スクリプトを操作することができます。 スクリプトは、最初にストレージ アカウントに新しいコンテナーを作成し、既存のファイル (および BLOB) をそのコンテナーにアップロードし、コンテナー内のすべての BLOB を一覧表示し、最後に、指定したローカル コンピューター上の宛先にファイルをダウンロードします。 このスクリプトを実行する前に、ターゲット Azure Stack に正常に接続してログインしていることを確認してください。 
1. 好みのテキスト エディターを開き、前述のスクリプトをコピーしてエディターに貼り付けます。
2. 構成の設定を反映するようにスクリプトの変数を更新します。 
3. 必要な変数を更新したら、スクリプトを保存してエディターを終了します。 次の手順では、スクリプト名に my_storage_sample.sh 指定したと仮定します。
4. 必要に応じてスクリプトを実行可能ファイルとしてマークします。`chmod +x my_storage_sample.sh`
5. スクリプトを実行します。 たとえば Bash の場合は次のようになります。 `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

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

Microsoft Azure Storage Explorer は、Microsoft のスタンドアロン アプリです。 このツールは、Windows、macOS、および Linux で Azure Storage および Azure Stack Storage の両方のデータを簡単に操作できます。 簡単に、Azure Stack Storage データを簡単に管理する方法が必要な場合は、Microsoft Azure Storage Explorer の使用を検討してください。

Azure Stack を操作する Microsoft Azure Storage Explorer の構成の詳細については、「[Connect Storage Explorer to an Azure Stack subscription](azure-stack-storage-connect-se.md)」 (Microsoft Azure Storage Explorer を Azure Stack サブスクリプションに接続する) を参照してください。

Microsoft Azure Storage Explorer の詳細については、「[Microsoft Azure Storage Explorer (プレビュー) の概要](../../vs-azure-tools-storage-manage-with-storage-explorer.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stack サブスクリプションに Microsoft Azure Storage Explorer を接続する](azure-stack-storage-connect-se.md)
* [Microsoft Azure Storage Explorer (プレビュー) の概要](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 互換ストレージ: 違いと考慮事項](azure-stack-acs-differences.md)
* [Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)

