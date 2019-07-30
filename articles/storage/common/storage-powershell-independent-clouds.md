---
title: Azure から独立しているクラウドでの Azure PowerShell によるストレージの管理 | Microsoft Docs
description: China Cloud、Government Cloud、および German Cloud でのAzure PowerShell を使用したストレージの管理
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69707eec0ea1f2260ee50a48ce1dcb82dc9ddd8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145864"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Azure から独立しているクラウドでの Azure PowerShell によるストレージの管理

ほとんどの人は、グローバルな Azure のデプロイに Azure パブリック クラウドを使用します。 主権などの理由から、Microsoft Azure の独立したデプロイもいくつかあります。 これらの独立したデプロイを「環境」と呼びます。 次の一覧は、現在使用可能な独立したクラウドを示しています。

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [中国の 21Vianet が運営する Azure China Cloud](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>独立したクラウドの使用 

いずれかの独立したクラウドの Azure Storage を使用するには、Azure パブリックではなく、そのクラウドに接続します。 Azure パブリックではなく、いずれかの独立したクラウドを使用するには:

* 接続する "*環境*" を指定します。
* 使用可能なリージョンを判断して、そのリージョンを使用します。
* Azure パブリックとは異なる適切なエンドポイント サフィックスを使用します。

例を実行するには、Azure PowerShell モジュール Az バージョン 0.7 以降が必要です。 PowerShell ウィンドウで、`Get-Module -ListAvailable Az` を実行して、バージョンを確認します。 何も表示されない、またはアップグレードが必要な場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 

## <a name="log-in-to-azure"></a>Azure にログインする

[Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) コマンドレットを実行し、使用できる Azure 環境を確認します。
   
```powershell
Get-AzEnvironment
```

接続するクラウドにアクセスできるアカウントにサインインし、環境を設定します。 次の例は、Azure Government Cloud を使用するアカウントにサインインする方法を示しています。   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

China Cloud にアクセスするには、**AzureChinaCloud** 環境を使用します。 German Cloud にアクセスするには、**AzureGermanCloud** を使用します。

この時点で、ストレージ アカウントまたは別のリソースを作成する場所の一覧が必要な場合は、選択したクラウドで使用できる場所を [Get-AzLocation](/powershell/module/az.resources/get-azlocation) でクエリできます。

```powershell
Get-AzLocation | select Location, DisplayName
```

次の表は、German Cloud に対して返される場所を示しています。

|Location | DisplayName |
|----|----|
| germanycentral | ドイツ中部|
| germanynortheast | ドイツ北東部 | 


## <a name="endpoint-suffix"></a>エンドポイント サフィックス

これらの環境でのエンドポイント サフィックスは、Azure パブリック エンドポイントとは異なります。 たとえば、Azure パブリックでの BLOB エンドポイント サフィックスは **blob.core.windows.net** です。 Government Cloud では、BLOB エンドポイント サフィックスは **blob.core.usgovcloudapi.net** です。 

### <a name="get-endpoint-using-get-azenvironment"></a>Get-AzEnvironment を使用してエンドポイントを取得する 

[Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) を使用してエンドポイントを取得します。 エンドポイントは、環境の *StorageEndpointSuffix* プロパティです。 この処理方法を、次のコード スニペットで示します。 これらすべてのコマンドは、"core.cloudapp.net" や "core.cloudapi.de" などを返します。これをストレージ サービスに追加して、このサービスにアクセスします。 たとえば、"queue.core.cloudapi.de" は、German Cloud 内のキュー サービスにアクセスします。

次のコード スニペットは、すべての環境とそれぞれのエンドポイント サフィックスを取得します。

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

このコマンドは、次の結果を返します。

| EnableAdfsAuthentication| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

指定した環境のすべてのプロパティを取得するには、**Get AzEnvironment** を呼び出してクラウドの名前を指定します。 次のコード スニペットは、プロパティの一覧を返します。一覧から **StorageEndpointSuffix** を探します。 次の例は German Cloud に対するものです。

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

結果は次のようになります。

|プロパティ名|値|
|----|----|
| EnableAdfsAuthentication | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

ストレージ エンドポイント サフィックス プロパティだけを取得するには、特定のクラウドを取得し、その 1 つのプロパティだけを要求します。

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

次の情報が返されます。

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>ストレージ アカウントからエンドポイントを取得する

ストレージ アカウントのプロパティを確認してエンドポイントを取得することもできます。 これは、PowerShell スクリプトですでにストレージ アカウントを使用している場合に便利です。必要なエンドポイントを取得できます。 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Government Cloud のストレージ アカウントでは、次の情報が返されます。 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>環境をセットアップした後

以降は、ストレージ アカウントを管理するために使用している PowerShell で、「[Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md)」に説明されているように、データ プレーンにアクセスできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この演習用に新しいリソース グループとストレージ アカウントを作成した場合は、リソース グループを削除することですべてのアセットを削除できます。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この場合、作成されたストレージ アカウントとリソース グループ自体が削除されます。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

* [PowerShell セッション間でのユーザー ログインの保持](/powershell/azure/context-persistence)
* [Azure Government ストレージ](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure Government 開発者向けガイド](../../azure-government/documentation-government-developer-guide.md)
* [Azure China アプリケーション開発者向けのメモ](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Germany ドキュメント](../../germany/germany-welcome.md)
