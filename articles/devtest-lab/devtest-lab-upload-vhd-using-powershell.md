---
title: "PowerShell を使用して Azure DevTest Labs に VHD ファイルをアップロードする | Microsoft Docs"
description: "PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードします"
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
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: 379544e6592e933e510308bb0ae7193542c099f2


---

# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードします

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用してカスタム イメージを作成でき、そのイメージを使用して仮想マシンをプロビジョニングできます。 次の手順は、PowerShell を使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示しています。 VHD ファイルをアップロードしたら、[「次のステップ」セクション](#next-steps)の記事に従って、アップロードした VHD ファイルからカスタム イメージを作成できます。 Azure でのディスクと VHD の詳細については、「[仮想マシン用のディスクと VHD について](../storage/storage-about-disks-and-vhds-linux.md)」を参照してください。

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順は、PowerShell を使用して VHD ファイルを Azure DevTest Labs にアップロードする方法を示しています。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのブレードで、 **[構成]**を選択します。 

1. ラボの **[構成]** ブレードで、**[カスタム イメージ (VHD)]** を選択します。

1. **[カスタム イメージ]** ブレードで、**[+追加]** を選択します。 

1. **[カスタム イメージ]** ブレードで、**[VHD]** を選択します。

1. **[VHD]** ブレードで、**[Upload a VHD file using PowerShell (PowerShell を使用して VHD ファイルをアップロードする)]** を選択します。

    ![PowerShell を使用した VHD のアップロード](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. **[PowerShell を使用したイメージのアップロード]** ブレードで、生成された PowerShell スクリプトをテキスト エディターにコピーします。

1. **Add-AzureVhd** コマンドレットの **LocalFilePath** パラメーターを、アップロードする VHD ファイルの場所を指すように変更します。

1. PowerShell プロンプトで、**Add-AzureVhd**コマンドレットを実行します (変更した **LocalFilePath** パラメーターを使用します)。

> [!WARNING] 
> 
> VHD ファイルのアップロードは、VHD ファイルのサイズと接続速度によっては、時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)



<!--HONumber=Feb17_HO3-->


