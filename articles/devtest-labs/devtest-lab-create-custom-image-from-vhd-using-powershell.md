---
title: Azure PowerShell を使用して VHD ファイルからカスタム イメージを作成する
description: PowerShell を使用した VHD ファイルからの Azure DevTest Labs カスタム イメージの作成を自動化します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288975"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>PowerShell を使用して VHD ファイルからカスタム イメージを作成する

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順で、PowerShell を使用して VHD ファイルからカスタム イメージを作成します。

1. PowerShell プロンプトで、**Connect-AzAccount** コマンドレットに対する次の呼び出しを使用して Azure アカウントにログインします。

    ```powershell
    Connect-AzAccount
    ```

1.  **Select-AzSubscription** コマンドレットを呼び出して、目的の Azure サブスクリプションを選択します。 次の **$subscriptionId** 変数のプレース ホルダーを、有効な Azure サブスクリプション ID に置き換えます。

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  **Get-AzResource** コマンドレットを呼び出して、ラボ オブジェクトを取得します。 次の **$labRg** 変数と **$labName** 変数のプレース ホルダーを、環境の適切な値に置き換えます。

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  次の **$vhdUri** 変数のプレース ホルダーを、アップロードした VHD ファイルの URI に置き換えます。 VHD ファイルの URI は、Azure Portal でストレージ アカウントの [BLOB] ブレードから取得できます。

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  **New-AzResourceGroupDeployment** コマンドレットを使用してカスタム イメージを作成します。 次の **$customImageName** 変数と **$customImageDescription** 変数のプレースホルダーを、環境で意味のある名前に置き換えます。

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>VHD ファイルからカスタム イメージを作成する PowerShell

次の PowerShell スクリプトを使用して、VHD ファイルからカスタム イメージを作成できます。 プレース ホルダー (山かっこで始まり、山かっこで終わります) を、ニーズに合った適切な値に置き換えます。

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](./devtest-lab-faq.md#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次のステップ

- [VM をラボに追加する](devtest-lab-add-vm.md)
