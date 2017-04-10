---
title: "Azure DevTest Labs での VM アーティファクトの管理 | Microsoft Docs"
description: "Azure DevTest Labs で VM アーティファクトを管理する方法について説明します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Azure DevTest Labs での VM アーティファクトの管理
Azure DevTest Labs "*アーティファクト*" を使用すると、VM のプロビジョニング時に実行される "*アクション*" を指定できます。 

アーティファクトのアクションでは、Windows PowerShell スクリプトや Bash コマンドの実行、ソフトウェアのインストールなどの手順を実行することができます。 

アーティファクト *パラメーター* を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

この記事では、ラボで VM のアーティファクトを管理する方法を説明します。

## <a name="add-an-existing-artifact-to-a-vm"></a>既存のアーティファクトの VM への追加
VM を作成するときに、既存のアーティファクトを追加できます。 各ラボには、パブリック DevTest ラボ アーティファクト リポジトリのアーティファクトと、独自のアーティファクト リポジトリに作成または追加されたアーティファクトが含まれます。
アーティファクトの作成方法については、 [DevTest Labs VM で使用するための独自のアーティファクトを作成する方法](devtest-lab-artifact-author.md)に関する記事を参照してください。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、使用する VM が含まれるラボを選択します。  
1. **[仮想マシン]** を選択します。
1. 目的の VM を選択します。
1. **[アーティファクト]** を選択します。 
1. **[アーティファクトの適用]** を選択します。
1. **[アーティファクトの適用]** ブレードで、VM に追加するアーティファクトを選択します。
1. **[アーティファクトの追加]** ブレードで、必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。  
1. **[追加]** を選択してアーティファクトを追加し、**[アーティファクトの適用]** ブレードに戻ります。
1. VM に必要なアーティファクトを引き続き追加します。
1. アーティファクトを追加したら、 [アーティファクトの実行順を変更する](#change-the-order-in-which-artifacts-are-run)ことができます。 [アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。
1. アーティファクトの追加が完了したら、**[適用]** を選択します。

## <a name="change-the-order-in-which-artifacts-are-run"></a>アーティファクトの実行順序の変更
既定では、アーティファクトのアクションは VM に追加された順序で実行されます。 次の手順では、アーティファクトの実行順序を変更する方法を示しています。

1. **[アーティファクトの適用]** ブレードの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ブレードに、目的の順序になるようにアーティファクトをドラッグ アンド ドロップします。 **注:** アーティファクトをドラッグできない場合は、アーティファクトの左側からドラッグしていることを確認してください。 
1. 完了したら、 **[OK]** を選択します。  

## <a name="view-or-modify-an-artifact"></a>アーティファクトの表示または変更
次の手順では、アーティファクトのパラメーターを表示または変更する方法を示しています。

1. **[アーティファクトの適用]** ブレードの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ブレードで、表示または編集するアーティファクトを選択します。  
1. **[アーティファクトの追加]** ブレードで必要な変更を行ったら、**[OK]** を選択して **[アーティファクトの追加]** ブレードを閉じます。
1. **[OK]** を選択して、**[選択されたアーティファクト]** ブレードを閉じます。

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの保存
Azure Resource Manager テンプレートでは、反復可能なデプロイを定義する宣言的な方法が提供されます。 次の手順では、作成される VM の Azure Resource Manager テンプレートを保存する方法について説明します。
保存した Azure Resource Manager テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイ](../azure-resource-manager/resource-group-overview.md#template-deployment)できます。

1. **仮想マシン**のブレードで、**[ARM テンプレートの表示]** を選択します。
2. **[Azure Resource Manager テンプレートの表示]** ブレードで、テンプレート テキストを選択します。
3. 選択したテキストをクリップボードにコピーします。
4. **[OK]** を選択して、**[View Azure Resource Manager Template]** (Azure Resource Manager テンプレートの表示) ブレードを閉じます。
5. テキスト エディターを開きます。
6. クリップボードからテンプレート テキストを貼り付けます。
7. 後で使用するためにファイルを保存します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
* [DevTest Labs VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)方法を学習します。

