---
title: Azure Lab Services でクラスルーム ラボのテンプレートを管理する | Microsoft Docs
description: Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する方法について学習します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: b287a67c470cc1697065838e52916c285a2233a7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960192"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボ テンプレートを作成し、管理する
ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。 ラボ ユーザーに表示されるテンプレートの名前と説明を指定できます。 その後、テンプレートを発行し、ラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。

この記事では、Azure Lab Services のクラスルーム ラボでテンプレート仮想マシンを作成し、管理する方法について説明します。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>クラスルーム ラボを作成するとき、テンプレートを発行する
まず、クラスルーム ラボを作成しているとき、テンプレートを設定し、発行できます。

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. ラボ内で許可される**ユーザーの最大数**を指定します。 
    6. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Select virtual machine specifications]** \(仮想マシンの仕様の選択\) ページで、次の手順を実行します。
    1. ラボで作成する仮想マシン (VM) の **[サイズ]** を選択します。 
    2. VM を作成する**リージョン**を選択します。 
    3. ラボで VM の作成に使用する **VM イメージ**を選択します。 
    4. **[次へ]** を選択します。

        ![VM 仕様の指定](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **[資格情報の設定]** ページで、ラボ内のすべての VM に使う既定の資格情報を指定します。 
    1. ラボ内のすべての VM に使う**ユーザーの名前**を指定します。
    2. ユーザーの**パスワード**を指定します。 

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **作成**を選択します。 

        ![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **[Configure template**]\(テンプレートの構成\) ページで、ラボの作成プロセスの状態を確認します。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレートの構成](../media/tutorial-setup-classroom-lab/configure-template.png)
7. テンプレートの構成が完了すると、次のページが表示されます。 

    ![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. このチュートリアルでは、以下の手順は省略可能です。 
    1. **[開始]** を選択してテンプレート VM を開始します。
    2. **[接続]** を選択してテンプレート VM に接続します。 
    3. テンプレート VM にソフトウェアをインストールして構成します。 
    4. VM を**停止**します。  
    5. テンプレートの**説明**を入力します。

        ![[Configure template]\(テンプレートの構成\) ページの [次へ]](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. [Configure template]\(テンプレートの構成\) ページの **[次へ]** を選択します。 
10. **[Publish the template]** \(テンプレートの発行\) ページで、次の操作を行います。 
    1. テンプレートをすぐに発行するには、*[I understand I can't modify the template after publishing.This process can only be done once and can take up to an hour]* \(発効したらテンプレートを変更できなくなること、このプロセスは一度のみ実行でき最大 1 時間かかる可能性があることを理解しています\) チェックボックスをオンにし、**[発行]** を選択します。  

        > [!WARNING]
        > 一度発行すると、再発行することはできません。 
    2. 後で発行する場合は、**[後のために保存]** を選択します。 ウィザードが完了した後に、テンプレート VM を発行することができます。 ウィザード完了後の構成および発行方法の詳細については、「[クラスルーム ラボの管理](how-to-manage-classroom-labs.md)」記事の「テンプレートを発行する」セクションを参照してください。

        ![テンプレートを発行する](../media/tutorial-setup-classroom-lab/publish-template.png)
11. テンプレートの**発行に関する進行状況**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. テンプレートが正常に発行されると、次のページが表示されます。 **[完了]** を選択します。

    ![テンプレートの発行 - 正常](../media/tutorial-setup-classroom-lab/publish-success.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>テンプレートのタイトルと説明を設定または更新する
タイトルと説明を初めて設定して後で更新する場合は、次の手順を実行します。 

1. **[テンプレート]** セクションで、テンプレートの**名前**またはテンプレートの**説明**にマウスを移動して選択します。 
2. テンプレートの**新しい名前**または**新しい説明**を入力し、**Enter** キーを押します。

    ![テンプレートの名前と説明](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>テンプレート VM を設定または更新する
 テンプレート VM を学生に提供する前に、そのテンプレート VM に接続して必要なソフトウェアをインストールします。 テンプレート VM を初めて設定する場合、または VM を更新する場合は、次の手順を実行します。 

1. テンプレート仮想マシンの準備が完了するまで待ちます。 準備が完了すると、**[開始]** ボタンが有効になります。 VM を起動するには、**[開始]** を選択します。

    ![テンプレート VM を起動する](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 警告を確認し、**[開始]** を選択します。 

    ![テンプレートの開始 - 警告](../media/how-to-create-manage-template/start-template-warning.png)
2. **[テンプレート]** セクションのラボ タイルに状態が表示されます。

    ![テンプレートの開始 - 状態](../media/how-to-create-manage-template/template-start-status.png)
1. 開始後、VM に接続するには、**[接続]** を選択して指示に従います。 

    ![テンプレート VM への接続または停止](../media/how-to-create-manage-template/connect-stop-vm.png)
1. 学生がラボの作業を行うために必要なソフトウェア (Visual Studio、Azure Storage Explorer など) をインストールします。 
2. テンプレート VM から切断 (リモート デスクトップ セッションを終了) します。 
3. **[停止]** を選択してテンプレート VM を**停止**します。 

## <a name="publish-the-template-vm"></a>テンプレート VM を発行する  
ラボの作成時にテンプレートを発行しない場合、後で発行できます。 発行前に、テンプレート VM に接続し、ソフトウェアで更新することをお勧めします。 テンプレートを発行すると、Azure Lab Services がそのテンプレートを使用してラボ内に VM を作成します。 このプロセスで作成される VM の数は、ラボ内で許可されるユーザーの最大数 (ラボの利用ポリシーに設定) と同じです。 すべての仮想マシンの構成は、テンプレートと同じになります。 

1. **[テンプレート]** セクションの **[発行]** を選択します。 

    ![テンプレート VM を発行する](../media/tutorial-setup-classroom-lab/public-access.png)
1. **[Publish the template]\(テンプレートの発行\)** メッセージ ボックスでメッセージを確認し、**[発行]** を選択します。 作成する VM の数によっては、このプロセスにしばらく時間がかかる場合があります。
    
    > [!IMPORTANT]
    > いったん発行したテンプレートの発行を取り消すことはできません。 ただし、テンプレートは再発行できます。 
4. テンプレートの状態が **[発行済み]** に変わるまで待ちます。 

    ![発行済み状態](../media/how-to-create-manage-template/publish-status.png)
1. **[仮想マシン]** ページに切り替えて、**[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 VM が作成されるまで待ちます。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>テンプレートを再発行する 
テンプレートの発行後も、テンプレート VM に接続し、更新し、再発行できます。 テンプレート VM を再発行すると、すべてのユーザー VM が切断され、更新後のテンプレートに基づいて再作成されます。 

1. ラボのダッシュボード ページの [テンプレート] セクションで **[再発行]** を選択します。 

    ![[再発行] ボタン](../media/how-to-create-manage-template/republish-button.png)
2. **[Republish the template]\(テンプレートの再発行\)** メッセージ ボックスでテキストを確認し、**[再発行]** を選択して続行します。 続行しない場合、**[キャンセル]** を選択します。 

    ![テンプレート メッセージを再発行する](../media/how-to-create-manage-template/republish-template-message.png)
3. **[テンプレート]** セクションのタイルには再発行の状態が表示されます。

    ![再発行の状態](../media/how-to-create-manage-template/republish-status-publishing.png)
4. テンプレートの発行後、状態は **[発行済み]** に設定されます。 

    ![再発行成功](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
