---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "67181399"
---
## <a name="sign-in-to-azure"></a>Azure へのサインイン

`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

使用する場所がわからない場合、利用できる場所を一覧表示できます。 次のコード例を使用して場所の一覧を表示し、使用する場所を見つけます。 この例では、**eastus** を使います。 場所を変数に格納し、この変数を使用することで、1 か所で場所を変更できます。

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) を使用して、LRS レプリケーション付きの標準の汎用ストレージ アカウントを作成します。 次に、使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。 ストレージ アカウントを操作するときは、資格情報を繰り返し渡す代わりに、このコンテキストを参照します。 次の例を使用して、ローカル冗長ストレージ (LRS) と BLOB 暗号化機能 (既定で有効になります) を備える *mystorageaccount* というストレージ アカウントを作成します。

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
