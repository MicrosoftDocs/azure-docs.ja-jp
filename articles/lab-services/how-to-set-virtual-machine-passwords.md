---
title: Azure Lab Services で VM のパスワードを設定する | Microsoft Docs
description: Azure Lab Services のラボで仮想マシン (VM) のパスワードを設定およびリセットする方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434228"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>仮想マシン プールを設定および管理する 
この記事では、次のタスクの手順について説明します。

- ラボ内の仮想マシン (VM) の数を増やす
- すべての VM または選択した VM を開始する 
- VM をリセットする

## <a name="update-the-lab-capacity"></a>ラボの容量を更新する
ラボの容量 (ラボ内の仮想マシンの数) を増減するには、次の手順を実行します。

1. **[Virtual machine pool]\(仮想マシン プール\)** ページで、 **[Lab capacity: &lt;number&gt; machines]\(ラボの容量: <数> 台のマシン\)** を選択します。
2. ラボに必要な新しい **VM 数** を入力します。 この数値は、ラボに登録されているユーザーの数以上である必要があります。 
3. 次に、 **[保存]** を選択します。 

    ![[ラボ内の最大マシン数] と [保存] ボタンが選択された [ラボの容量] ウィンドウを示すスクリーンショット。](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 容量を増やした場合は、1 台以上の VM が作成されているのを確認できます。 一覧に新しい VM が表示されない場合は、ページを更新してください。 

    ![VM が作成されている](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM の起動

### <a name="start-ot-stop-all-vms"></a>すべての VM を開始または停止する
1. **[Virtual machine pool]\(仮想マシン プール\)** ページに切り替えます。 
2. ツール バーから **[Start all]\(すべて開始\)** を選択します。 

    ![[Start all]\(すべて開始\) ボタン](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. すべての VM が起動したら、ツールバーの **[Stop all]\(すべて停止\)** ボタンを選択して、すべての VM を停止することができます。 

    ![[Stop all]\(すべて停止\) ボタン](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>選択した VM を開始する
選択した (1 台以上の) VM を開始するには 2 つの方法があります。 最初の方法では、一覧から 1 台以上の VM を選択し、ツールバーの **[Start]\(開始\)** を選択します。 

2 番目の方法では、一覧から 1 つ以上の VM を選択し、 **[状態]** 列のボタンを切り替えます。 

![選択した VM を開始する](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同様に、 **[状態]** 列のボタンを切り替えるか、ツールバーの **[停止]** を選択して、1 つ以上の VM を停止することができます。 

> [!NOTE]
> 教師が学生用 VM をオンにしても、学生のクォータは影響を受けません。 ユーザーのクォータによって指定されるのは、スケジュールされたクラス時間外にユーザーが使用できるラボ時間数です。 クォータの詳細については、「[ユーザーのクォータを設定する](how-to-configure-student-usage.md?#set-quotas-for-users)」を参照してください。

## <a name="reset-vms"></a>VM をリセットする

1 台以上の VM をリセットするには、一覧からそれらを選択し、次にツールバーの **[Reset]\(リセット\)** を選択します。 

![選択した VM をリセットする](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

**[Reset virtual machine(s)]\(仮想マシンのリセット\)** ダイアログ ボックスで、 **[Reset]\(リセット\)** を選択します。 

![[Reset VM]\(VM のリセット\) ダイアログ ボックス](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>VM のパスワードを設定する
ラボ所有者 (教師) は、ラボの作成時 (ラボ作成ウィザード)、またはラボの作成後に **[テンプレート]** ページで、VM のパスワードを設定またはリセットすることができます。 

### <a name="set-password-at-the-time-of-lab-creation"></a>ラボの作成時にパスワードを設定する
ラボ所有者 (教師) は、ラボ作成ウィザードの **[仮想マシンの資格情報]** ページでラボ内の VM のパスワードを設定できます。

![新しいラボのウィンドウ](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

このページの **[すべての仮想マシンに同じパスワードを使用する]** オプションを有効または無効にすることで、教師はラボ内のすべての VM に同じパスワードを使用するか、学生が自分の VM にパスワードを設定できるようにするかを選択できます。 既定でこの設定は、Ubuntu を除くすべての Windows および Linux オペレーティング システム イメージで有効です。 この設定を無効にすると、学生が初めて VM に接続しようとしたときにパスワードを設定するように求められます。 

### <a name="reset-password-later"></a>パスワードを後からリセットする

1. ラボの **[テンプレート]** ページで、ツールバーの **[パスワードのリセット]** を選択します。 
1. **[パスワードのリセット]** ダイアログ ボックスでパスワードを入力し、 **[パスワードのリセット]** を選択します。
    
    ![[Set password]\(パスワードの設定\) ダイアログ ボックス](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>学生用 VM に接続する
ラボ作成者 (教師) は、次の条件が満たされている場合に学生用 VM に接続できます。 

- ラボの作成時に、 **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションを選択した場合
- VM が実行されている場合 

 学生用 VM に接続するには、一覧で VM 上にマウス ポインターを置いて、コンピューター ボタンを選択します。  

![学生用 VM への接続ボタン](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 教師が VM を起動して接続しても、学生のクォータは影響を受けません。 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>CSV ファイルに仮想マシンのリストをエクスポートする

1. **[Virtual machine pool]\(仮想マシン プール\)** タブに切り替えます。
2. ツール バーの **[...] (省略記号)** を選択してから、 **[CSV のエクスポート]** を選択します。 

    ![仮想マシンのエクスポート一覧](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>次のステップ
(ラボ所有者として) 構成できる他の学生の使用オプションの詳細については、次の記事を参照してください。[学生の使用の構成](how-to-configure-student-usage.md)

学生が各自の VM のパスワードをリセットする方法については、「[ラボの仮想マシンに対するパスワードを設定またはリセットする (学生)](how-to-set-virtual-machine-passwords-student.md)」を参照してください。