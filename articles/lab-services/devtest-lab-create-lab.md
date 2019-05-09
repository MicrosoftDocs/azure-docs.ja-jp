---
title: Azure DevTest Labs でのラボの作成 | Microsoft Docs
description: Azure DevTest Labs で仮想マシン用のラボを作成します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: c54b97bdf69908f32015631a9e527c6e289d1d2a
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080499"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボの作成
Azure DevTest Labs におけるラボとは、Virtual Machines (VM) などのリソース グループを包含するインフラストラクチャであり、制限とクォータを指定することでこれらのリソースをより適切に管理することができます。 この記事では、Azure Portal を使用してラボを作成する手順を説明します。

## <a name="prerequisites"></a>前提条件
ラボを作成するには、次のものが必要です。

* Azure サブスクリプション。 Azure 購入オプションの詳細については、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。 ラボを作成するには、サブスクリプションの所有者である必要があります。

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボを作成する手順
次の手順は、Azure Portal を使用して Azure DevTest Labs でラボを作成する方法を示しています。 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. 左側のメイン メニューの **[すべてのサービス]** (リスト最上部) を選択します。 **[DEVOPS]** セクションで **[DevTest Labs]** の隣の [*] (星) を選択します。 この操作により、次に簡単にアクセスできるように左側のナビゲーション メニューに **[DevTest Labs]** が追加されます。 

    ![[すべてのサービス] - [DevTest Labs] を選択する](./media/devtest-lab-create-lab/all-services-select.png)
2. 次に、左側のナビゲーション メニューの **[DevTest Labs]** を選択します。 ツール バーの **[追加]** を選択します。 
   
    ![Add a lab](./media/devtest-lab-create-lab/add-lab-button.png)
1. **[DevTest Lab の作成]** ページで、次のようにします。 
    1. ラボの**名前**を入力します。
    2. ラボに関連付ける **[サブスクリプション]** を選択します。
    3. ラボの**リソース グループの名前**を入力します。 
    4. ラボを格納する**場所**を選択します。
    4. **[自動シャットダウン]** を選択し、ラボのすべての VM の自動シャットダウンを有効にする (およびそのパラメーターを定義する) かどうかを指定します。 自動シャットダウン機能は、主にコストを削減する機能で、VM をいつ自動的にシャットダウンするかを指定することができます。 自動シャットダウンの設定は、ラボの作成後に、「[Azure DevTest Labs でラボのすべてのポリシーを管理](./devtest-lab-set-lab-policy.md#set-auto-shutdown)」という記事に書かれている手順に従って変更することができます。
    1. カスタム タグを作成して、ラボで作成するすべてのリソースに追加する場合は、**[タグ]** の **[名前]** と **[値]** の情報を入力します。 タグを適用することによって、ラボのリソースをカテゴリ別に管理して整理することができます。 ラボの作成後にタグを追加する方法など、タグの詳細については、[ラボにタグを追加する方法](devtest-lab-add-tag.md)に関するページを参照してください。
    6. **[Automation オプション]** を選択して、構成を自動化するための Azure Resource Manager テンプレートを取得します。 
    7. **作成**を選択します。 ラボ作成プロセスの状態は、**[通知]** 領域を見て監視できます。 
    
        ![DevTest Labs のラボの作成セクション](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. 完了したら、通知の **[リソースに移動]** を選択します。 または、**[DevTest Labs]** ページを最新の情報に更新すると、ラボの一覧に新しく作成されたラボが表示されます。  一覧でラボを選択します。 ラボのホーム ページが表示されます。 

        ![ラボのホーム ページ](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
ラボを作成した後は、次の手順を考慮します。

* [ラボへのアクセスをセキュリティで保護する](devtest-lab-add-devtest-user.md)
* [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
* [ラボ テンプレートを作成する](devtest-lab-create-template.md)
* [VM のカスタム アーティファクトを作成する](devtest-lab-artifact-author.md)
* [VM をラボに追加する](devtest-lab-add-vm.md)

