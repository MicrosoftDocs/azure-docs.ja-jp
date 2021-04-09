---
title: PowerShell から Azure Image Builder を使用して Windows VM を作成する
description: Azure Image Builder PowerShell モジュールを使用して Windows VM を作成します。
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693460"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>プレビュー:PowerShell から Azure Image Builder を使用して Windows VM を作成する

この記事では、Azure VM Image Builder PowerShell モジュールを使用して、カスタマイズした Windows イメージを作成する方法について説明します。

> [!CAUTION]
> 現在、Azure Image Builder はパブリック プレビュー段階にあります。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 運用環境のワークロードにはお勧めしません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。

> [!IMPORTANT]
> PowerShell モジュール **Az.ImageBuilder** と **Az.ManagedServiceIdentity** は、プレビュー段階にある間は、`Install-Module` コマンドレットに `AllowPrerelease` パラメーターを指定して別々にインストールする必要があります。 これらの PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>機能の登録

プレビュー期間中に Azure Image Builder を初めて使用する場合は、新しい **VirtualMachineTemplatePreview** 機能を登録する必要があります。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

機能の登録の状態を確認します。

> [!NOTE]
> **RegistrationState** が `Registering` 状態から `Registered` 状態に変化するまでに数分かかる場合があります。 この状態が **Registered** になってから続行してください。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Azure サブスクリプションで使用する次のリソースプロバイダーを登録します (まだ登録していない場合)。

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.Network
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>変数の定義

いくつかの情報は、繰り返し使用することになります。 それらの情報を格納する変数を作成しましょう。

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Azure サブスクリプション ID の変数を作成します。 `subscriptionID` 変数にサブスクリプション ID が格納されていることを確認するには、次の例の 2 行目を実行します。

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`$location` 変数に指定されたリージョンに、`$imageResourceGroup` 変数内の名前に基づいてリソース グループを作成します。 このリソース グループは、イメージ構成テンプレート成果物およびイメージを格納するために使用されます。

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>ユーザー ID を作成してロールのアクセス許可を設定する

以下の例を使用して、指定したリソース グループにイメージを作成するためのアクセス許可を Azure Image Builder に付与します。 このアクセス許可がないと、イメージのビルド プロセスが正常完了しません。

ロールの定義と ID の名前に使用する変数を作成します。 これらの値は一意である必要があります。

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

ユーザー ID を作成します。

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

ID リソースとプリンシパル ID を変数に格納します。

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>イメージを配布するためにアクセス許可を ID に割り当てる

.json 構成ファイルをダウンロードし、この記事で定義した設定に基づいてファイルに変更を加えます。

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

ロール定義を作成します。

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Image Builder のサービス プリンシパルにロールの定義を付与します。

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> エラー "_New-AzRoleDefinition: ロールの定義の制限を超えました。ロールの定義をこれ以上作成することはできません_" が表示された場合は、「[Azure RBAC のトラブルシューティング](../../role-based-access-control/troubleshooting.md)」を参照してください。

## <a name="create-a-shared-image-gallery"></a>Shared Image Gallery を作成する

ギャラリーを作成します。

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

ギャラリーの定義を作成します。

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>イメージを作成する

Azure Image Builder のソース オブジェクトを作成します。 有効なパラメーター値については、[Azure PowerShell を使用して Azure Marketplace で Windows VM イメージを検索する](./cli-ps-findimage.md)方法に関するページを参照してください。

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Azure Image Builder のディストリビューター オブジェクトを作成します。

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Azure Image Builder のカスタマイズ オブジェクトを作成します。

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

2 番目の Azure Image Builder のカスタマイズ オブジェクトを作成します。

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Azure Image Builder テンプレートを作成します。

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

完了するとメッセージが返され、Image Builder 構成テンプレートが `$imageResourceGroup` に作成されます。

テンプレートの作成プロセスが成功したかどうかは、次の例を使用して調べることができます。

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

また、バックグラウンドで、ステージング リソース グループがサブスクリプションに作成されます。 このリソース グループがイメージのビルドに使用されます。 これは、`IT_<DestinationResourceGroup>_<TemplateName>` という形式になっています。

> [!WARNING]
> ステージング リソース グループは直接削除しないでください。 イメージ テンプレート成果物を削除します。そうすることで、ステージング リソース グループが削除されます。

イメージ構成テンプレートの送信中にサービスによって障害が報告された場合、次を実行します。

- 「[Azure VM Image Builder (AIB) 障害のトラブルシューティング](../linux/image-builder-troubleshoot.md)」を参照します。
- 再試行する前に、次の例に従ってテンプレートを削除します。

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>イメージのビルドを開始する

VM Image Builder サービスにイメージ構成を送信します。

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

イメージ ビルド プロセスが完了するまで待ちます。 このステップには最大 1 時間かかることがあります。

エラーが発生した場合は、「[Azure VM Image Builder (AIB) 障害のトラブルシューティング](../linux/image-builder-troubleshoot.md)」を参照してください。

## <a name="create-a-vm"></a>VM の作成

VM のログイン資格情報を変数に格納します。 パスワードは複雑なものにする必要があります。

```azurepowershell-interactive
$Cred = Get-Credential
```

作成したイメージを使用して VM を作成します。

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>カスタマイズを確認する

VM を作成したときに設定したユーザー名とパスワードを使用して、VM へのリモート デスクトップ接続を作成します。 VM 内で PowerShell を開き、次の例のように `Get-Content` を実行します。

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

イメージ カスタマイズ プロセス中に作成されたファイルの内容に基づいて出力が表示されます。

```Output
Azure-Image-Builder-Was-Here
```

同じ PowerShell セッションから、次の例に示すように、ファイル `c:\buildArtifacts\index.html` が存在するか調べて、2 番目のカスタマイズが正常に完了したことを確認します。

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

結果は、イメージのカスタマイズ プロセス中にダウンロードされたファイルを示すディレクトリの一覧になります。

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要であれば、次の例を実行して削除できます。

### <a name="delete-the-image-builder-template"></a>Image Builder テンプレートを削除する

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>イメージ リソース グループを削除する

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>次のステップ

この記事で使用されている .json ファイルのコンポーネントの詳細については、[Image Builder テンプレートのリファレンス](../linux/image-builder-json.md)に関するページを参照してください。
