---
title: "Azure クイックスタート - PowerShell を使用したストレージ アカウントの作成 | Microsoft Docs"
description: "PowerShell で新しいストレージ アカウントを作成する方法を簡単に説明します"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>PowerShell を使用したストレージ アカウントの作成

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、PowerShell を使用して Azure Storage アカウントを作成する方法を詳しく説明します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイック スタートには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

使用する場所がわからない場合、利用できる場所を一覧表示できます。 一覧が表示されたら、使用する場所を見つけます。 この例では **eastus** を使用します。 これを変数に格納し、1 か所で変更できるように変数を使用します。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>汎用の標準ストレージ アカウントを作成する

使用方法と対象のサービス (BLOB、ファイル、テーブル、またはキュー) に応じて、ストレージ アカウントにはいくつかの種類があります。 次の表に可能性を示します。

|**ストレージ アカウントの種類**|**汎用 (Standard)**|**汎用 (Premium)**|**Blob Storage (ホット アクセス レベルとクール アクセス レベル)**|
|-----|-----|-----|-----|
|**サポートされるサービス**| BLOB、ファイル、テーブル、キュー サービス | Blob service | Blob service|
|**サポートされる BLOB の種類**|ブロック BLOB、ページ BLOB、追加 BLOB | ページ blob | ブロック BLOB と追加 BLOB|

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) を使用して、4 つのサービスすべてに使用できる汎用的な標準ストレージ アカウントを作成します。 ストレージ アカウントに "*contosomvcstandard*" という名前を付け、ローカル冗長ストレージと BLOB 暗号化を有効にするように構成します。

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。 この場合、作成したストレージ アカウントが削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、汎用的な標準ストレージ アカウントを作成しました。 ストレージ アカウントで BLOB をアップロードおよびダウンロードする方法を学習するには、Blob Storage のクイックスタートに進みます。
> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Blob Storage との間でオブジェクトを転送する](../blobs/storage-quickstart-blobs-powershell.md)