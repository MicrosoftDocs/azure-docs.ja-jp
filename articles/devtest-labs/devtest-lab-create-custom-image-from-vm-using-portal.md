---
title: VM から Azure DevTest Labs カスタム イメージを作成する | Microsoft Docs
description: Azure ポータルを使用して、プロビジョニングされた VM から Azure DevTest Labs にカスタム イメージを作成する方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87277029"
---
# <a name="create-a-custom-image-from-a-vm"></a>VM からカスタム イメージを作成する

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

プロビジョニングされた VM からカスタム イメージを作成し、その後、そのカスタム イメージを使用して同一の VM を作成できます。 次の手順では、VM からカスタム イメージを作成する方法を説明します。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのメイン ウィンドウで **[仮想マシン]** を選択します。
 
1. **[仮想マシン]** ウィンドウで、カスタム イメージを作成する VM を選択します。

1. VM の管理ウィンドウで、 **[操作]** の **[カスタム イメージの作成]** を選択します。

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="カスタム イメージのメニュー項目の作成":::
1. **[カスタム イメージ]** ウィンドウで、カスタム イメージの名前と説明を入力します。 この情報は、VM を作成する際にベースの一覧に表示されます。 カスタム イメージには、仮想マシンに接続された OS ディスクとすべてのデータ ディスクが含まれます。

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="カスタム イメージの作成ページ":::
1. sysprep を VM で実行したかどうかを選択します。 sysprep を VM 上で実行していない場合は、カスタム イメージの作成時に VM 上で sysprep を実行するかどうかを指定します。
1. 完了したら **[OK]** を選択して、カスタム イメージを作成します。

    数分後にカスタム イメージが作成され、ラボのストレージ アカウント内に保存されます。 ラボのユーザーが新しい VM を作成する場合、基本イメージの一覧からそのイメージを使用できます。

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="基本イメージの一覧で使用できるカスタム イメージ":::

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](./devtest-lab-faq.md#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次のステップ

- [VM をラボに追加する](devtest-lab-add-vm.md)
