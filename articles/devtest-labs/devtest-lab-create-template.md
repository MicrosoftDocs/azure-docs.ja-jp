---
title: VHD ファイルから Azure DevTest Labs カスタム イメージを作成する | Microsoft Docs
description: Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87289396"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>VHD ファイルからカスタム イメージを作成する

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順で、Azure Portal を使用して VHD ファイルからカスタム イメージを作成します。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのメイン ウィンドウで、**[構成とポリシー]** を選択します。 

1. **[構成とポリシー]** ウィンドウで **[カスタム イメージ]** を選択します。

1. **[カスタム イメージ]** ウィンドウで、**[+追加]** を選択します。

    ![カスタム イメージの追加](./media/devtest-lab-create-template/add-custom-image.png)

1. カスタム イメージの名前を入力します。 この名前は、VM を作成するときにベース イメージの一覧に表示されます。

1. カスタム イメージの説明を入力します。 この説明は、VM を作成するときにベース イメージの一覧に表示されます。

1. **[OS の種類]** では、**[Windows]** または **[Linux]** を選択します。

    - **[Windows]** を選択した場合は、チェック ボックスを使用して、コンピューターで *Sysprep* が実行されたかどうかを指定します。 
    - **[Linux]** を選択した場合は、チェック ボックスを使用して、コンピューターで *プロビジョニング解除* が実行されたかどうかを指定します。 

1. ドロップダウン メニューから **[VHD]** を選択します。 これは、新しいカスタム イメージの作成に使用される VHD です。 必要な場合は、**[PowerShell を使用して VHD ファイルをアップロード]** を選択します。

1. カスタム イメージの作成に使用されるイメージがライセンスされていない (Microsoft によって公開されていない) イメージの場合でも、プラン名、プランの提供、プランの公開元を入力できます。

   - **プラン名:** このカスタム イメージの作成元の Marketplace イメージの名前 (SKU) を入力します。 
   - **プランの提供:** このカスタム イメージの作成元の Marketplace イメージ の製品 (オファー) を入力します。 
   - **プランの公開元:** このカスタム イメージの作成元の Marketplace イメージ の公開元を入力します。

   > [!NOTE]
   > カスタム イメージの作成に使用するイメージがライセンスされたイメージ **ではない** 場合、これらのフィールドは空であり、必要に応じて入力できます。 イメージがライセンスされたイメージ **である** 場合、フィールドにはプランの情報が自動的に入力されます。 その場合、フィールドの情報を変更しようとすると、警告メッセージが表示されます。
   >
   >

1. **[OK]** を選択して、カスタム イメージを作成します。

数分後にカスタム イメージが作成され、ラボのストレージ アカウント内に保存されます。 ラボのユーザーが新しい VM を作成する場合、基本イメージの一覧からそのイメージを使用できます。

![基本イメージの一覧で使用できるカスタム イメージ](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](./devtest-lab-faq.md#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次のステップ

- [VM をラボに追加する](./devtest-lab-add-vm.md)
