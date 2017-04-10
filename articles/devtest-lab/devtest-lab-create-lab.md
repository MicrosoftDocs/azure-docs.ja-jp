---
title: "Azure DevTest Labs でのラボの作成 | Microsoft Docs"
description: "Azure DevTest Labs で仮想マシン用のラボを作成します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 29c4c2a2818468a2fa8360eebd4b653bdcbbde19
ms.openlocfilehash: 0749d371466226343227c79db544a8e3dca0cca8


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボの作成
## <a name="prerequisites"></a>前提条件
ラボを作成するには、次のものが必要です。

* Azure サブスクリプション。 Azure 購入オプションの詳細については、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。 ラボを作成するには、サブスクリプションの所有者である必要があります。

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボを作成する手順
次の手順は、Azure Portal を使用して Azure DevTest Labs でラボを作成する方法を示しています。 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. **[DevTest ラボ]** ブレードで **[追加]** をクリックします。
   
    ![Add a lab](./media/devtest-lab-create-lab/add-lab-button.png)
4. **[DevTest ラボの作成]** ブレードで:
   
   1. 新しいラボの **[ラボ名]** を入力します。
   2. ラボに関連付ける **[サブスクリプション]** を選択します。
   3. ラボを格納する **[場所]** を選択します。
   4. **[自動シャットダウン]** を選択し、ラボのすべての VM の自動シャットダウンを有効にする (およびそのパラメーターを定義する) かどうかを指定します。 
   5. **[作成]**を選択します。
      
      ![Create a lab blade](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
ラボを作成した後は、次の手順を考慮します。

* [ラボへのアクセスをセキュリティで保護します](devtest-lab-add-devtest-user.md)。
* [ラボのポリシーを設定します](devtest-lab-set-lab-policy.md)。
* [ラボ テンプレートを作成します](devtest-lab-create-template.md)。
* [VM のカスタム アーティファクトを作成します](devtest-lab-artifact-author.md)。
* [アーティファクトを含む VM をラボに追加します](devtest-lab-add-vm-with-artifacts.md)。




<!--HONumber=Dec16_HO3-->


