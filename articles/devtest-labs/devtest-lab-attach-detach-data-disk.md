---
title: Azure DevTest Labs でデータ ディスクを仮想マシンにアタッチまたはデタッチする
description: Azure DevTest Labs でデータ ディスクを仮想マシンにアタッチまたはデタッチする方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8eebfbda421233bcec780d441a4020acce740618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328515"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Azure DevTest Labs でデータ ディスクを仮想マシンにアタッチまたはデタッチする
[Azure Managed Disks](../virtual-machines/managed-disks-overview.md) は、仮想マシンのデータ ディスクに関連付けられているストレージ アカウントを管理します。 ユーザーが新しいデータ ディスクを VM にアタッチし、必要なディスクの種類とサイズを指定すると、Azure によってディスクが自動的に作成され、管理されます。 データ ディスクはその後 VM からデタッチできます。後で同じ VM に再アタッチすることも、同じユーザーが所有する別の VM にアタッチすることもできます。

この機能は、個々の仮想マシンの外部でストレージまたはソフトウェアを管理する場合に便利です。 ストレージまたはソフトウェアがデータ ディスク内に既に存在する場合、そのデータ ディスクを所有するユーザーが所有する任意の VM に対して、アタッチ、デタッチ、再アタッチを簡単に行えます。

## <a name="attach-a-data-disk"></a>データ ディスクの接続
データ ディスクを VM にアタッチする前に、以下のヒントを確認してください。

- VM のサイズによって、アタッチできるデータ ディスクの数が変わります。 詳細については、「 [仮想マシンのサイズ](../virtual-machines/sizes.md)」を参照してください。
- データ ディスクをアタッチできるのは、実行中の VM だけです。 データ ディスクのアタッチを試みる前に、VM が実行されていることを確認してください。

### <a name="attach-a-new-disk"></a>新しいディスクの接続
Azure DevTest Labs で新しいマネージド データ ディスクを作成して VM にアタッチするには、次の手順に従います。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインする
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。 
1. **[自分の仮想マシン]** の一覧で、実行中の VM を選択します。
1. 左側のメニューで **[ディスク]** を選択します。
1. **[Attach new]\(新しいディスクのアタッチ\)** を選択し、新しいデータ ディスクを作成して VM にアタッチします。

    ![新しいデータ ディスクを仮想マシンにアタッチする](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. データ ディスクの名前、種類、サイズを入力して、 **[Attach new disk]\(新しいディスクのアタッチ\)** ウィンドウを完了します。

    ![[Attach new disk]\(新しいディスクのアタッチ\) フォームを完了する](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. **[OK]** を選択します。

しばらくすると、新しいデータ ディスクが作成され、VM にアタッチされて、その VM に関する **[データ ディスク]** の一覧に表示されます。

### <a name="attach-an-existing-disk"></a>既存のディスクの接続
使用可能な既存のデータ ディスクを実行中の VM に再アタッチするには、次の手順に従います。 

1. データ ディスクを再アタッチする、実行中の VM を選択します。
1. 左側のメニューで **[ディスク]** を選択します。
1. **[Attach existing]\(既存のディスクのアタッチ\)** を選択して、使用可能なデータ ディスクを VM にアタッチします。

    ![[ディスク] 設定が選択されて [Attach existing]\(既存のディスクのアタッチ\) が選択されたことを示すスクリーンショット。](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. **[Attach existing disk]\(既存のディスクのアタッチ\)** ウィンドウで、[OK] を選択します。

    ![既存のデータ ディスクを仮想マシンにアタッチする](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

しばらくすると、データ ディスクが VM にアタッチされ、その VM に関する **[データ ディスク]** の一覧に表示されます。

## <a name="detach-a-data-disk"></a>データ ディスクの切断
VM にアタッチされたデータ ディスクは、不要になった場合に簡単にデタッチできます。 デタッチすると、ディスクは VM から削除されますが、後で使用できるようにストレージ内に保持されます。

もう一度ディスク上の既存のデータを使用したい場合は、同じ仮想マシンや別の仮想マシンに再アタッチできます。

### <a name="detach-from-the-vms-management-pane"></a>VM の管理ウィンドウからのデタッチ
1. 仮想マシンの一覧で、データ ディスクがアタッチされている VM を選択します。
1. 左側のメニューで **[ディスク]** を選択します。
1. **[データ ディスク]** の一覧で、デタッチしたいデータ ディスクを選択します。

    ![仮想マシンのデータ ディスクを選択する](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. ディスクの詳細ウィンドウの上部で **[Detach]\(デタッチ\)** を選択します。

    ![[Detach]\(デタッチ\) 操作が強調表示されているディスクの詳細ペインを示すスクリーンショット。](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. **[はい]** を選択して、データ ディスクをアタッチすることを確認します。

ディスクがデタッチされ、別の VM にアタッチできるようになります。 
### <a name="detach-from-the-labs-main-pane"></a>ラボのメイン ウィンドウからのデタッチ
1. ラボのメイン ウィンドウで、 **[個人用データ ディスク]** を選択します。
1. デタッチしたいデータ ディスクを右クリックするか、省略記号 ( **...** ) を選択し、 **[Detach]\(デタッチ\)** を選択します。

    ![データ ディスクの切断](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. **[はい]** を選択して、デタッチすることを確認します。

   > [!NOTE]
   > データ ディスクが既にデタッチされている場合は、 **[削除]** を選択して、使用可能なデータ ディスクの一覧から削除することができます。
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>非管理対象データ ディスクのアップグレード
アンマネージド データ ディスクが使用される既存の VM がある場合、マネージド ディスクが使用されるよう VM を簡単に変換できます。 このプロセスでは、OS ディスクと接続されたすべてのデータ ディスクの両方を変換します。

非管理対象データ ディスクをアップグレードするには、この記事の手順に従って、非管理対象 VM から[データ ディスクをデタッチ](#detach-a-data-disk)します。 次に、データ ディスクが非管理対象から管理対象に自動的にアップグレードされるように、管理対象 VM に[ディスクを再アタッチ](#attach-an-existing-disk)します。

## <a name="next-steps"></a>次のステップ
[要求可能な仮想マシン](devtest-lab-add-claimable-vm.md#unclaim-a-vm)のためにデータ ディスクを管理する方法について学習します。
