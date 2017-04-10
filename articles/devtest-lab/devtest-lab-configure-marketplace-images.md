---
title: "Azure DevTest Labs で Azure Marketplace イメージの設定を構成する | Microsoft Docs"
description: "Azure DevTest Labs で VM を作成する場合にどの Azure Marketplace イメージを使用できるようにするかを構成する"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d5606c538d7ee5afc6b2c3cfcfae0a6aca341c7f


---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure DevTest Labs で Azure Marketplace イメージの設定を構成する
DevTest Labs では、実際のラボで使用する Azure Marketplace イメージの構成方法に応じて、Azure Marketplace イメージに基づく VM を作成することができます。 この記事では、ラボで VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法を説明します。

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>VM を作成する際に使用できるようにする Azure Marketplace イメージを選択する
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。 
4. ラボのブレードで、 **[構成]**を選択します。
5. ラボの **[構成]** ブレードで、**[Marketplace images (Marketplace イメージ)]** を選択します。
6. 対象となるすべての Azure Marketplace イメージを新しい VM のベースとして利用できるようにするかどうかを指定します。 **[はい]**を選択すると、次の条件をすべて満たす Azure Marketplace イメージのすべてがラボで使用できるようになります。
   
   * イメージは 1 つの VM を作成する、 **かつ**
   * イメージは Azure Resource Manager を使用して VM をプロビジョニングする、 **かつ**
   * イメージは追加のライセンス プランの購入を必要としない。
     
     イメージの使用を許可しない場合、または使用できるようにするイメージを指定する場合は、 **[いいえ]**を選択します。
     
     ![すべての Marketplace イメージを VM のベース イメージとして使用できるようにするオプション](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 前の手順で **[いいえ]** を選択した場合は、**[Allowed images (許可するイメージ)] の [すべて選択]** チェック ボックスが有効になります。 
   このオプションを検索ボックスと組み合わせて使用すると、一覧に表示されたすべてのアイテムをすばやく選択または選択解除することができます。
   Azure Marketplace イメージに対応するチェック ボックスをオンにすることで、VM の作成で使用できるようにするイメージを個別に選択することもできます。
   どの Azure Marketplace イメージもラボでの使用を許可しない場合は、一覧で何も選択しないでください。
   
    ![どの Azure Marketplace イメージを VM のベース イメージとして使用できるようにするかを指定できる](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
VM を作成するときに Azure Marketplace イメージを使用できるようにする方法を構成したら、次は [VM をラボに追加](devtest-lab-add-vm-with-artifacts.md)します。




<!--HONumber=Nov16_HO3-->


