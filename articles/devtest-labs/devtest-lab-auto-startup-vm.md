---
title: Azure DevTest Labs で VM の自動開始設定を構成する | Microsoft Docs
description: ラボで VM の自動開始設定を構成する方法について説明します。 この設定により、ラボ内の VM はスケジュールに基づいて自動的に開始されます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328482"
---
# <a name="auto-startup-lab-virtual-machines"></a>ラボの仮想マシンの自動スタートアップ  
Azure DevTest Labs を使用すると、ラボ内の仮想マシンを、スケジュールに基づいて自動的に開始およびシャットダウンするように構成できます。 自動シャットダウンの設定の構成については、「[Azure DevTest Labs でラボの自動シャットダウン ポリシーを管理する](devtest-lab-auto-shutdown.md)」を参照してください。 

ポリシーを有効にしたときにすべての VM が含まれる自動シャットダウンとは異なり、自動開始ポリシーでは、ラボ ユーザーが VM を明示的に選択し、このスケジュールにオプトインする必要があります。 こうすることで、不要な VM が誤って自動開始され、想定外の費用が発生する状況に容易に陥ることがありません。

この記事では、ラボの自動開始ポリシーの構成方法について説明します。

## <a name="configure-autostart-settings-for-a-lab"></a>ラボの自動開始設定を構成する 
1. ラボのホーム ページに移動します。 
2. 左側のメニューで **[構成とポリシー]** を選択します。 

    ![DevTest ラボの [構成とポリシー] メニューを示すスクリーンショット。](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. **[構成とポリシー]** ページで、次の手順に従います。
    
    1. **[仮想マシンの自動開始のスケジュールを許可する]** で **[オン]** を選択し、このラボで自動開始機能を有効にします。 
    2. 開始時刻 (例: 8:00:00 AM) を **[スケジュールの開始時刻]** フィールドで選択します。 
    3. 使用する **タイム ゾーン** を選択します。 
    4. VM を自動的に開始する必要がある **曜日** を選択します。 
    5. 次に、ツールバーの **[保存]** を選択して設定を保存します。 

        ![自動開始の設定](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > このポリシーでは、ラボ内の仮想マシンに自動的に自動開始が適用されません。 個々の仮想マシンを **オプトイン** するには、仮想マシンのページに移動し、その VM に対して **自動開始** を有効にします。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>ラボ内の VM の自動開始を有効にする
次の手順では、ラボの自動開始ポリシーに VM をオプトインする手順を示します。 

1. ラボのホーム ページで、 **[仮想マシン]** の一覧から **VM** を選択します。 

    ![[構成とポリシー] メニュー](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. **[仮想マシン]** ページの左側のメニューまたは **[スケジュール]** の一覧で **[自動開始]** を選択します。 

    ![[自動開始] メニューの選択](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. **[自動開始]** ページで、 **[この仮想マシンの自動開始をスケジュールできるようにする]** オプションで. **[オン]** を選択します。

    ![VM の自動開始を有効にする](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 次に、ツールバーの **[保存]** を選択し、設定を保存します。 


## <a name="next-steps"></a>次のステップ
ラボの自動シャットダウン ポリシーの構成については、「[Azure DevTest Labs でラボの自動シャットダウン ポリシーを管理する](devtest-lab-auto-shutdown.md)」を参照してください。
