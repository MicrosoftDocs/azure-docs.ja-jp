---
title: Image Builder - Windows Virtual Desktop イメージを作成する
description: PowerShell で Azure Image Builder を使用して、Windows Virtual Desktop の Azure VM イメージを作成します。
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602020"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Azure VM Image Builder と PowerShell を使用して Windows Virtual Desktop イメージを作成する

この記事では、次のようなカスタマイズを行って Windows Virtual Desktop イメージを作成する方法について説明します。

* [Fslogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1) をインストールする。
* コミュニティ リポジトリから [Windows Virtual Desktop の最適化スクリプト](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool)を実行する。
* [Microsoft Teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd) をインストールする。
* [Restart](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* [Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer) を実行する。

ここでは、Azure VM Image Builder を使用してこれを自動化する方法について説明します。また、他のリージョンへの複製、スケールの制御、および組織内外でのイメージの共有を行うことができる [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) にイメージを配布する方法について説明します。


Image Builder の構成のデプロイを簡略化するために、この例では、入れ子になった Image Builder テンプレートを使用して Azure Resource Manager テンプレートを使用します。 これにより、変数やパラメーターで入力できるなど、他のいくつかの利点が得られます。 これらのパラメーターをコマンド ラインから渡すこともできます。

この記事では、コピーして貼り付けながら演習を進めることを想定しています。

> [!NOTE]
> アプリをインストールするスクリプトは、[GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD) にあります。 これらは、例示とテストのみを目的としたものであり、運用環境のワークロードで使用することはできません。 

## <a name="tips-for-building-windows-images"></a>Windows イメージの作成に関するヒント 

- VM サイズ - 既定の VM サイズは `Standard_D1_v2` です。これは、Windows には適していません。 `Standard_D2_v2` 以上を使用します。
- この例では、[PowerShell カスタマイザー スクリプト](../linux/image-builder-json.md)を使用します。 これらの設定を使用する必要があります。そうしないと、ビルドが応答を停止します。

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    次に例を示します。

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- コードにコメントを追加 - AIB ビルド ログ (customization.log) は非常に詳細なログを出力します。"write-host" を使用してスクリプトにコメントを追加すると、それらがログに送られ、トラブルシューティングが容易になります。

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- 終了コード - AIB では、すべてのスクリプトが 0 の終了コードを返すことを想定しています。ゼロ以外の終了コードが返されると、AIB によってカスタマイズが失敗し、ビルドが停止します。 複雑なスクリプトがある場合に、インストルメンテーションを追加し、終了コードを生成すると、これらは customization.log に表示されます。

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- テスト: 事前にスタンドアロン VM でコードをテストし、ユーザーにプロンプトが表示されないこと、適切な特権を使用していることなどを確認してください。

- ネットワーク - `Set-NetAdapterAdvancedProperty`。 これは最適化スクリプトで設定されていますが、AIB のビルドは失敗します。ネットワークを切断すると、コメントアウトされます。現在、調査中です。

## <a name="prerequisites"></a>前提条件

最新の Azure PowerShell コマンドレットがインストールされている必要があります。インストールの詳細については、[こちら](https://docs.microsoft.com/powershell/azure/overview)を参照してください。

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>環境と変数を設定する

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>アクセス許可、ユーザー ID、およびロール 


 ユーザー ID を作成します。

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

イメージを配布するためにアクセス許可を ID に割り当てます。 このコマンドは、テンプレートをダウンロードし、前に指定したパラメーターを使用してテンプレートを更新します。

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> 「New-AzRoleDefinition: ロールの定義の制限を超えました。 ロールの定義はこれ以上作成できません」のようなエラーが表示された場合、 解決方法については、 https://docs.microsoft.com/azure/role-based-access-control/troubleshooting の記事を参照してください。



## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery を作成する 

Shared Image Gallery がない場合、作成する必要があります。

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>イメージ テンプレートを構成する

この例には、テンプレートが用意されています。事前に指定されたパラメーターを使用してテンプレートをダウンロードして更新します。これにより、FsLogix、OS 最適化ツール、Microsoft Teams がインストールされ、最後に Windows Update が実行されます。

テンプレートを開くと、ソース プロパティに使用されているイメージが表示されます。この例では、Win 10 Multi セッション イメージが使用されています。 

### <a name="windows-10-images"></a>Windows 10 イメージ
主に、マルチセッションとシングルセッションという 2 つの種類があります。

マルチセッション イメージは、プールして使用することを目的としています。 Azure での出力詳細の例を以下に示します。

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

シングルセッション イメージは、個別に使用することを目的としています。 Azure での出力詳細の例を以下に示します。

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

また、使用可能な Win10 イメージを変更することもできます。

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>テンプレートをダウンロードして構成する

ここで、テンプレートをダウンロードして、用途に合わせて構成する必要があります。

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

[テンプレート](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)を自由に表示できます。すべてのコードを表示することもできます。


## <a name="submit-the-template"></a>テンプレートの編集

テンプレートをサービスに送信する必要があります。これにより、依存成果物 (スクリプトなど) のダウンロード、検証、アクセス許可の確認が行われ、*IT_* で始まるステージング リソース グループに保存されます。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>イメージをビルドする
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> イメージ ビルダー サービスがイメージのビルドを完了するまで、コマンドは待機しません。次の状態に対してクエリを実行できます。

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>VM の作成
ビルドが完了したら、イメージから VM を作成できます。[こちら](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples)からサンプルを使用してください。

## <a name="clean-up"></a>クリーンアップする

まず、リソース グループ テンプレートを削除します (リソース グループ全体は削除しないでください)。そうしないと、AIB で使用されているステージング リソース グループ (*IT_* ) が消去されません。

イメージ テンプレートを削除します。

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

ロールの割り当てを削除します。

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

リソース グループを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

[GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts) で他のサンプルを試すこともできます。
