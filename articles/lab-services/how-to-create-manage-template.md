---
title: Azure Lab Services でクラスルーム ラボのテンプレートを管理する | Microsoft Docs
description: Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する方法について学習します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434279"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 その後、テンプレートを発行し、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。

この記事では、Azure Lab Services のクラスルーム ラボでテンプレート仮想マシンを作成し、管理する方法について説明します。 

> [!NOTE]
> ラボを作成すると、テンプレート VM が作成されます。ただし、起動されません。 起動して接続を行い、そのラボの前提条件となるソフトウェアをインストールしてから、発行することができます。 テンプレート VM を発行すると、テンプレート VM がシャットダウン済みでない場合は、自動シャットダウンが実行されます。 
> 
> テンプレート VM は、実行時に **コスト** が発生するため、実行しておく必要がない場合はテンプレート VM がシャットダウンされるようにしてください。 


## <a name="set-or-update-template-title-and-description"></a>テンプレートのタイトルと説明を設定または更新する
タイトルと説明を初めて設定して後で更新する場合は、次の手順を実行します。 

1. **[テンプレート]** ページで、ラボの新しい **タイトル** を入力します。  
2. テンプレートの新しい **説明** を入力します。 テキスト ボックスからフォーカスを外すと、内容が自動的に保存されます。 

    ![テンプレートの名前と説明](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>テンプレート VM を更新する
テンプレート VM は次の手順で更新します。  

1. テンプレート VM が起動するまで待ってから、ツール バーで **[Connect to template]\(テンプレートに接続する\)** を選択してテンプレート VM に接続し、指示に従います。 Windows コンピューターの場合、RDP ファイルをダウンロードするオプションが表示されます。 
1. テンプレートに接続し、変更を加えると、ユーザーに最後に公開した仮想マシンと同じ設定ではなくなります。 再び公開するまで、ユーザーの既存の仮想マシンにはテンプレートの変更が反映されません。

    ![テンプレート VM に接続する](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. 学生がラボの作業を行うために必要なソフトウェア (Visual Studio、Azure Storage Explorer など) をインストールします。 
1. テンプレート VM から切断 (リモート デスクトップ セッションを終了) します。 
1. **[Stop template]\(テンプレートの停止\)** を選択してテンプレート VM を **停止** します。 
1. 更新後のテンプレート VM を **発行** するには、次のセクションの手順を行います。 

## <a name="publish-the-template-vm"></a>テンプレート VM を発行する  
この手順では、テンプレート VM を発行します。 テンプレート VM を発行すると、Azure Lab Services によって、そのテンプレートを使用してラボ内に VM が作成されます。 すべての仮想マシンの構成は、テンプレートと同じになります。


1. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 

    ![テンプレートの発行ボタン](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 一度発行すると、再発行することはできません。 
2. **[Publish template]\(テンプレートの発行\)** ページで、ラボに作成する仮想マシンの数を入力し、 **[発行]** を選択します。 

    ![テンプレートの発行 - VM の数](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. ページには、テンプレートの **発行の状態** が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 発行処理が完了するまで待ってから、左側のメニューで **[仮想マシン]** を選択するか、 **[仮想マシン]** タイルを選択して、 **[仮想マシン プール]** ページに切り替えます。 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてラボにアクセスする](how-to-use-classroom-lab.md)
