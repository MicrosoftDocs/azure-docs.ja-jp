---
title: "VM を作成するための Azure DevTest Labs 数式の管理 | Microsoft Docs"
description: "Azure DevTest Labs 数式を更新および削除する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs 数式の管理

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

「[Create Azure DevTest Labs formulas (Azure DevTest Labs 数式の作成)](devtest-lab-create-formulas.md#create-a-formula)」では、Azure DevTest Labs で数式を作成するプロセスについて説明しています。 数式を作成したら、この記事に従って数式を管理してください。

## <a name="modify-a-formula"></a>数式の変更
数式を変更するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのブレードで、 **[数式 (再利用可能なベース)]**を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]** (ラボ数式) ブレードで、変更する数式を選択します。
6. **[Update formula (数式の更新)]** ブレードで、必要な編集を行い、**[更新]** を選択します。

## <a name="delete-a-formula"></a>数式の削除
数式を削除するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[設定]** ブレードで、**[Formulas (数式)]** を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]** (ラボ数式) ブレードで、削除する数式の右側にある省略記号を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 数式のコンテキスト メニューで、 **[削除]**を選択します。
   
    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 削除の確認のダイアログ ボックスで **[はい]** を選択します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>次のステップ
VM の作成時に使用する数式を作成したら、次は [VM をラボに追加](devtest-lab-add-vm-with-artifacts.md)します。


