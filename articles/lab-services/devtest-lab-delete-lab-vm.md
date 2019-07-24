---
title: Azure DevTest Labs でラボやラボ内の VM を削除する | Microsoft Docs
description: この記事では、ラボまたはラボ内の VM を削除する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 9634c70566aba21bdd28ee016c9fa94464ec9c1b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956332"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボやラボ内の VM を削除する
この記事では、ラボまたはラボ内の VM を削除する方法について説明します。

## <a name="delete-a-lab"></a>ラボを削除する
DevTest Labs インスタンスをリソース グループから削除すると、DevTest Labs サービスは次の処理を実行します。 

- ラボの作成時に自動的に作成されたすべてのリソースは自動的に削除されます。 リソース グループ自体は削除されません。 このリソース グループに手動で作成したリソースがある場合、そのリソースは削除されません。 
- ラボ内のすべての VM と、その VM に関連付けられたリソース グループは自動的に削除されます。 ラボ内に VM を作成すると、別のリソース グループに VM のリソース (ディスク、ネットワーク インターフェイス、パブリック IP アドレスなど) が作成されます。 ただし、このようなリソース グループに手動で追加のリソースを作成した場合、DevTest Labs サービスでは、このようなリソースとリソース グループは削除されません。 

ラボを削除するには、次の手順を実行します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから **[すべてのリソース]** を選択し、サービスの種類として **[DevTest Labs]** を選択し、ラボを選択します。

    ![ラボを選択する](media/devtest-lab-delete-lab-vm/select-lab.png)
3. **[DevTest Lab]** ページで、ツール バーの **[削除]** をクリックします。 

    ![[削除] ボタン](media/devtest-lab-delete-lab-vm/delete-button.png)
4. **[確認]** ページで、ラボの**名前**を入力し、**[削除]** を選択します。 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. 操作の状態を確認するには、**[通知]** アイコン (ベル) を選択します。 

    ![通知](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>ラボ内の VM を削除する
ラボ内の VM を削除すると、ラボの作成時に作成されたリソースの (すべてではなく) 一部が削除されます。 次のリソースは削除されません。 

-   メイン リソース グループのキー コンテナー
-   VM リソース グループの可用性セット、ロード バランサー、パブリック IP アドレス。 これらのリソースは、リソース グループ内の複数の VM で共有されています。 

仮想マシン、ネットワーク インターフェイス、VM に関連付けられたディスクは削除されます。 

ラボ内の VM を削除するには、次の手順を実行します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから **[すべてのリソース]** を選択し、サービスの種類として **[DevTest Labs]** を選択し、ラボを選択します。

    ![ラボを選択する](media/devtest-lab-delete-lab-vm/select-lab.png)
3. VM 一覧にある VM の **[...] \(省略記号\)** を選択し、**[削除]** を選択します。 

    ![メニューの VM の削除](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. **確認**のダイアログ ボックスで **[はい]** を選択します。 
5. 操作の状態を確認するには、**[通知]** アイコン (ベル) を選択します。 

**[仮想マシン] ページ**から VM を削除するには、次の図のようにツール バーの **[削除]** を選択します。

![VM ページから VM を削除する](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>次の手順
ラボを作成する場合は、次の記事を参照してください。 

- [ラボを作成する](devtest-lab-create-lab.md)
- [VM をラボに追加する](devtest-lab-add-vm.md)