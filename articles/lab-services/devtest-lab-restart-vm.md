---
title: ラボ内の VM を Azure DevTest Labs で再起動する | Microsoft Docs
description: Azure DevTest Labs で仮想マシンを再起動する方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149879"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再起動する
この記事の手順に従うと、DevTest Labs で仮想マシンをすばやく簡単に再起動することができます。 VM を再起動する前に、次をご考慮ください。

- 再起動の機能を有効にするには、VM が実行されている必要があります。
- 再起動の実行時に、実行中の VM に接続していたユーザーは、バックアップを開始した後に VM に再接続する必要があります。
- VM を再起動するときに成果物が適用される場合は、成果物が適用されない可能性があるとの警告が表示されます。

    ![再起動時に成果物を適用している間に表示される警告](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > 成果物を適用中に VM が停止した場合は、VM の再起動の機能を使用すると、問題が解決されることがあります。
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再起動する手順
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、再起動する VM が含まれているラボを選択します。
1. 左側のパネルで、**[仮想マシン]** を選択します。
1. VM の一覧で、実行中の VM を選択します。
1. VM の管理ウィンドウの上部で **[再起動]** を選択します。

    ![VM の再起動ボタン](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. ウィンドウの右上にある **[通知]** アイコンを選択することで、再起動の状態を監視します。

    ![VM 再起動の状態の表示](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

**[仮想マシン]** の一覧で実行中の VM の省略記号 (...) を選択して、その VM を再起動することもできます。

![省略記号を使用して VM を再起動する](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>次の手順
* 再起動の後、VM の管理ウィンドウで **[接続]** を選択すれば、VM に再接続できます。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を探索します。
