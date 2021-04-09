---
title: PowerShell を使用した Azure Table Storage 操作の実行 | Microsoft Docs
description: PowerShell を使用して、Azure Table ストレージ アカウントからデータの作成、クエリ、削除などの一般的なタスクを実行する方法について説明します。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93306899"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure PowerShell を使用した Azure Table Storage 操作の実行 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage は NoSQL データストアであり、これを使用することで、構造化された非リレーショナル データの巨大なセットを格納および照会できます。 このサービスのメイン コンポーネントは、テーブル、エンティティ、プロパティです。 テーブルは、エンティティのコレクションです。 エンティティは、プロパティのセットです。 各エンティティには、最大 252 個のプロパティを含めることができます。これらはすべて名前と値のペアです。 この記事では、Azure Table Storage サービスの概念について理解しているユーザーを対象としています。 詳しくは、「[テーブル サービス データ モデルについて](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)」および「[.NET を使用して Azure Table Storage を使用する](../../cosmos-db/tutorial-develop-table-dotnet.md)」をご覧ください。

このハウツー記事では、Azure Table Storage の一般的な操作について取り上げます。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティの追加
> * テーブルを照会する
> * テーブル エンティティを削除する
> * テーブルを削除する

このハウツー記事では、作業の完了後に簡単に削除できるように、新しいリソース グループに新しい Azure Storage アカウントを作成する方法について説明します。 既存のストレージ アカウントを使用したい場合はそうしてもかまいません。

例を実行するには、Az PowerShell モジュール `Az.Storage (1.1.0 or greater)` および `Az.Resources (1.2.0 or greater)` が必要です。 PowerShell ウィンドウで、`Get-Module -ListAvailable Az*` を実行して、バージョンを確認します。 何も表示されない場合や、アップグレードが必要な場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

> [!IMPORTANT]
> PowerShell からこの Azure 機能を使用するには、`Az` モジュールがインストールされている必要があります。 `AzTable` の現在のバージョンは、以前の AzureRM モジュールと互換性がありません。
> 必要に応じて、[Az モジュールの最新のインストール手順](/powershell/azure/install-az-ps)に従ってください。

Azure PowerShell をインストールまたは更新した後は、エンティティを管理するコマンドを含む **AzTable** モジュールをインストールする必要があります。 このモジュールをインストールするには、PowerShell を管理者として実行し、**Install-Module** コマンドを使用します。

> [!IMPORTANT]
> モジュール名の互換性の理由から、PowerShell ギャラリーではこれと同じモジュールが古い名前 `AzureRmStorageTables` でまだ公開されています。 このドキュメントでは、新しい名前のみを参照します。

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Add-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>場所の一覧を取得する

使用する場所がわからない場合、利用できる場所を一覧表示できます。 一覧が表示されたら、使用する場所を見つけます。 これらの例では、**eastus** を使用しています。 後で使用するために、この値を変数 **location** に保存します。

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 将来使用するために、リソース グループ名を変数に保存します。 この例では、*pshtablesrg* という名前のリソース グループを *eastus* リージョンに作成しています。

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>ストレージ アカウントの作成

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) を使用して、ローカル冗長ストレージ (LRS) で標準の汎用ストレージ アカウントを作成します。 必ず一意のストレージ アカウント名を指定してください。 次に、ストレージ アカウントを表すコンテキストを取得します。 このコンテキストは、ストレージ アカウントで作業するときに参照できます。ご自身の資格情報を繰り返し入力する必要はありません。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>新しいテーブルを作成する

テーブルを作成するには、[New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) コマンドレットを使用します。 この例では、テーブル名として `pshtesttable` を使用しています。

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>ストレージ アカウントのテーブルの一覧を取得する

[Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) を使用して、ストレージ アカウントのテーブルの一覧を取得します。

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>特定のテーブルへの参照を取得する

テーブルに対して操作を実行するには、その特定のテーブルに対する参照が必要になります。 [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) を使用して、参照を取得します。

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>特定のテーブルの参照 CloudTable プロパティ

> [!IMPORTANT]
> **AzTable** PowerShell モジュールを使用する場合は、必ず CloudTable を使用する必要があります。 **Get-AzStorageTable** コマンドを呼び出して、このオブジェクトへの参照を取得します。 また、テーブルがまだ存在しない場合は、このコマンドでテーブルが作成されます。

**AzTable** を使用してテーブルに対する操作を実行するには、特定のテーブルの CloudTable プロパティへの参照が必要です。

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>テーブルを削除する

テーブルを削除するには、[Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable) を使用します。 このコマンドレットを実行すると、テーブルと、そのすべてのデータが削除されます。

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このハウツー記事に従って最初に新しいリソース グループとストレージ アカウントを作成している場合は、リソース グループを削除することで、この練習で作成したすべてのアセットを削除できます。 このコマンドを実行すると、リソース グループと、そのグループに含まれるすべてのリソースが削除されます。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このハウツー記事では、次のような、PowerShell を使用した Azure Table Storage の一般的な操作について学習しました。 

> [!div class="checklist"]
> * テーブルを作成する
> * テーブルを取得する
> * テーブル エンティティの追加
> * テーブルを照会する
> * テーブル エンティティを削除する
> * テーブルを削除する

詳細については、次の記事を参照してください。

* [Storage PowerShell コマンドレット](/powershell/module/az.storage#storage)

* [PowerShell - AzureRmStorageTable/AzTable PS モジュール v2.0 からの Azure テーブルの操作](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。