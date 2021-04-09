---
title: Azure DevTest Labs でのラボのライセンスされたイメージの有効化 | Microsoft Docs
description: Azure Portal を使って Azure DevTest Labs でライセンスされたイメージを有効にする方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91276358"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボのライセンスされたイメージの有効化

Azure DevTest Labs では、ライセンスされたイメージとは、イメージをラボ内のユーザーからアクセス可能にする前に同意する必要がある (通常はサード パーティからの) 使用条件を含むイメージです。 次の各セクションでは、ライセンスされたイメージを操作して、仮想マシンの作成に使用できるようにする方法について説明します。

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>ライセンスされたイメージをユーザーが使用できるかどうかを確認する
ライセンスされたイメージからユーザーが VM を作成できるようにする最初の手順として、ライセンスされたイメージの使用条件に同意していることを確認します。 次の手順では、ライセンスされたイメージのオファーの状態を表示し、必要に応じて使用条件に同意する方法を示します。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインする

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. 左側のパネルの **[設定]** で、 **[構成とポリシー]** を選択します。

1. 左側のパネルの **[仮想マシンのベース]** で、 **[Marketplace イメージ]** を選択します。 

    ![[Marketplace イメージ] メニュー項目](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    各イメージの **[オファーの状態]** を含む、すべての利用可能な Marketplace イメージの一覧が表示されます。

    ![各イメージのオファーの状態を示す Marketplace イメージの一覧](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    ライセンスされたイメージには以下のオファー状態が表示されます 
    
    - **同意した条件:** ライセンスされたイメージは、ユーザーが VM を作成するために使用できます。 
    - **確認が必要な条件:** ライセンスされたイメージは、現在ユーザーが使用できません。 ラボ ユーザーは、VM の作成にライセンスを使用する前に、そのライセンスの使用条件に同意する必要があります。 

## <a name="making-a-licensed-image-available-to-lab-users"></a>ライセンスされたイメージをラボ ユーザーが使用できるようにする
ライセンスされたイメージをラボ ユーザーが使用できるようにするには、最初に管理者のアクセス許可を持つラボ所有者が、そのライセンスされたイメージの使用条件に同意する必要があります。 ライセンスされたイメージに関連付けられているサブスクリプションに対してプログラムによるデプロイを有効にすると、そのイメージについて法律条項とプライバシーに関する声明に自動的に同意することになります。 Marketplace イメージのプログラムによるデプロイに関する追加情報については、「[Working with Marketplace Images on Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/)」(Azure Resource Manager での Marketplace イメージの操作) をご覧ください。

次の手順に従って、ライセンスされたイメージのプログラムによるデプロイを有効にすることができます。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) で、**Marketplace イメージ** の一覧に移動します。

1. ユーザーがアクセスできるようにしたいが条件に同意されていないライセンスされたイメージを特定します。 たとえば、 **[同意した条件]** または **[確認が必要な条件]** のいずれかの状態を示すデータ サイエンス仮想マシンが表示される可能性があります。

    ![[同意した条件] と [確認が必要な条件] のオファーの状態がある Data Science Virtual Machine イメージを示すスクリーンショット](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > データ サイエンス VM は、データ分析、機械学習、AI トレーニングでよく使用される一般的なツールを使用して事前インストール、構成、テストされた Azure 仮想マシン イメージです。 「[Linux および Windows 用の Azure データ サイエンス仮想マシンの概要](../machine-learning/data-science-virtual-machine/overview.md)」に、DSVM の詳細情報が記載されています。
   >
   >

1. イメージの **[オファーの状態]** 列で、 **[確認が必要な条件]** を選択します。

1. [プログラムによるデプロイの構成] ウィンドウで、 **[有効]** を選択します。

    ![[プログラムによるデプロイの構成] ウィンドウ](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > [プログラムによるデプロイの構成] ウィンドウには複数のサブスクリプションが表示されることがあります。 目的のサブスクリプションに対してのみ、プログラムによるデプロイを有効にしてください。
   >
   >


1. **[保存]** を選択します。 

    Marketplace イメージの一覧で、イメージに **[同意した条件]** が表示され、ユーザーが仮想マシンを作成するために使用できるようになります。

> [!NOTE]
> ユーザーは、ライセンスされたイメージからカスタム イメージを作成できます。 詳細については、「[VHD ファイルからカスタム イメージを作成する](devtest-lab-create-template.md)」を参照してください。
>
>


## <a name="related-blog-posts"></a>関連するブログ記事

- [Custom images or formulas? (カスタム イメージか数式か?)](./devtest-lab-faq.md#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Azure DevTest Labs 間でのカスタム イメージのコピー)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>次のステップ

- [VM からカスタム イメージを作成する](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [VHD ファイルからカスタム イメージを作成する](devtest-lab-create-template.md)
- [VM をラボに追加する](devtest-lab-add-vm.md)
