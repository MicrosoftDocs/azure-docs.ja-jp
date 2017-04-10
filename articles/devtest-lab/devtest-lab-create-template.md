---
title: "VHD ファイルから Azure DevTest Labs カスタム イメージを作成する | Microsoft Docs"
description: "Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 38d70ed302eeab912ce4fe33272f85e96f1b1eda
ms.openlocfilehash: 9983ea9b847f44ed18a6169a4bdb224b63626a64


---

# <a name="create-a-custom-image-from-a-vhd-file"></a>VHD ファイルからカスタム イメージを作成する

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順で、Azure Portal を使用して VHD ファイルからカスタム イメージを作成します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのブレードで、 **[構成]**を選択します。 

1. ラボの **[構成]** ブレードで、**[カスタム イメージ (VHD)]** を選択します。

1. **[カスタム イメージ]** ブレードで、**[+追加]** を選択します。

    ![カスタム イメージの追加](./media/devtest-lab-create-template/add-custom-image.png)

1. カスタム イメージの名前を入力します。 この名前は、VM を作成するときにベース イメージの一覧に表示されます。

1. カスタム イメージの説明を入力します。 この説明は、VM を作成するときにベース イメージの一覧に表示されます。

1. **VHD** を選択します。

1. **[VHD]** ブレードで、目的の VHD ファイルを選択します。

1. **[OK]** を選択して、**[VHD]** ブレードを閉じます。

1. **[OS 構成]** を選択します。

1. **[OS 構成]** タブで、**[Windows]** または **[Linux]** を選択します。

1. **[Windows]** を選択した場合は、チェック ボックスを使用して、コンピューターで *Sysprep* が実行されたかどうかを指定します。 

1. **[OK]** を選択して、**[OS 構成]** ブレードを閉じます。

1. **[OK]** を選択して、カスタム イメージを作成します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>次のステップ

- [VM をラボに追加する](./devtest-lab-add-vm-with-artifacts.md)



<!--HONumber=Jan17_HO2-->


