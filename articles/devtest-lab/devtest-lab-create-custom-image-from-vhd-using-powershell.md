---
title: "PowerShell を使用して VHD ファイルから Azure DevTest Labs カスタム イメージを作成する | Microsoft Docs"
description: "PowerShell を使用した VHD ファイルからの Azure DevTest Labs カスタム イメージの作成を自動化します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 38d70ed302eeab912ce4fe33272f85e96f1b1eda
ms.openlocfilehash: 6c85df6bbcf25e687881380fd68bb3ee861098e3


---

# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>PowerShell を使用して VHD ファイルからカスタム イメージを作成する

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順で、PowerShell を使用して VHD ファイルからカスタム イメージを作成します。

1. PowerShell プロンプトで、**Login-AzureRmAccount** コマンドレットに対する次の呼び出しを使用して Azure アカウントにログインします。  
    
    ```PowerShell
    Login-AzureRmAccount
    ```

1.  **Select-AzureRmSubscription** コマンドレットを呼び出して、目的の Azure サブスクリプションを選択します。 次の **$subscriptionId** 変数のプレース ホルダーを、有効な Azure サブスクリプション ID に置き換えます。 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    ```

1.  **Get-AzureRmResource** コマンドレットを呼び出して、ラボ オブジェクトを取得します。 次の **$labRg** 変数と **$labName** 変数のプレース ホルダーを、環境の適切な値に置き換えます。 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  ラボ オブジェクトから、ラボ ストレージ アカウントとラボ ストレージ アカウント キーの値を取得します。 

    ```PowerShell
    $labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  次の **$vhdUri** 変数のプレース ホルダーを、アップロードした VHD ファイルの URI に置き換えます。 VHD ファイルの URI は、Azure Portal でストレージ アカウントの [BLOB] ブレードから取得できます。

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  **New-AzureRmResourceGroupDeployment** コマンドレットを使用してカスタム イメージを作成します。 次の **$customImageName** 変数と **$customImageDescription** 変数のプレースホルダーを、環境で意味のある名前に置き換えます。

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/ARMTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>VHD ファイルからカスタム イメージを作成する PowerShell

次の PowerShell スクリプトを使用して、VHD ファイルからカスタム イメージを作成できます。 プレース ホルダー (山かっこで始まり、山かっこで終わります) を、ニーズに合った適切な値に置き換えます。 

```PowerShell
# Log in to your Azure account.  
Login-AzureRmAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/ARMTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>次のステップ

- [VM をラボに追加する](./devtest-lab-add-vm-with-artifacts.md)



<!--HONumber=Jan17_HO2-->


