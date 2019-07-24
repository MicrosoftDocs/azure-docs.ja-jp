---
title: Azure Lab Services でマルチ VM 環境を有効にする | Microsoft Docs
description: Azure Lab Services のクラスルーム ラボでテンプレート仮想マシン内に複数の仮想マシンを含む環境を作成する方法について説明します。
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190485"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>クラスルーム ラボのテンプレート VM 内に複数の VM を備えた環境を作成する
現在、Azure Lab Services では、ラボ内に 1 つのテンプレート仮想マシンを設定し、各ユーザーが 1 つのコピーを使用できるようにすることができます。 ただし、ファイアウォールやサーバーの設定方法について IT クラスに講義する教授であれば、必要に応じて、複数の仮想マシンがネットワークを介して相互に通信できる環境を各学生に用意します。

入れ子になった仮想化を使用すると、ラボのテンプレート仮想マシン内にマルチ VM 環境を作成できます。 テンプレートを公開すると、ラボ内の各ユーザーに、内部に複数の VM が設定された仮想マシンが提供されます。

## <a name="what-is-nested-virtualization"></a>入れ子になった仮想化の概要
入れ子になった仮想化を使用すると、1 つの仮想マシン内に複数の仮想マシンを作成できます。 入れ子になった仮想化は Hyper-V を介して行われ、Windows VM でのみ使用できます。

入れ子になった仮想化の詳細については、以下の記事を参照してください。

- [Azure の入れ子になった仮想化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM で入れ子になった仮想化を有効にする方法](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure Lab Services で入れ子になった仮想化を使用する
重要な手順は次のとおりです。

1. ラボ用に **L** サイズの **Windows** テンプレート マシンを作成します。 
2. それに接続して、[入れ子になった仮想化を有効にします](../../virtual-machines/windows/nested-virtualization.md)。


次に、その手順を詳しく説明します。 

1. まだお持ちでない場合は、ラボ アカウントを作成します。 手順については、「[チュートリアル:Azure Lab Services でラボ アカウントを設定する](tutorial-setup-lab-account.md)」を参照してください。
2. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
3. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
4. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. ラボでの**仮想マシンの数**の最大値を指定します。 ラボの作成後、または既存のラボで、VM の数を増やしたり減らしたりできます。 詳しくは、「[ラボ内の仮想マシンの数を更新する](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)」をご覧ください
    6. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Select virtual machine specifications]** \(仮想マシンの仕様の選択\) ページで、次の手順を実行します。
    1. ラボで作成する仮想マシン (VM) のサイズに **[L]** を選択します。 現在、入れ子になった仮想化をサポートしているのは L サイズのみです。
    2. **Windows イメージ**である仮想マシン イメージを選択します。 入れ子になった仮想化は、Windows マシンでのみ使用できます。 
    3. **[次へ]** を選択します。

        ![VM 仕様の指定](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. **[Configure template]\(テンプレートの構成\)** ページで、**[接続]** を選択してテンプレート VM に接続し、入れ子になった仮想化を構成します。 また、このウィザードの手順を完了した後で設定することもできます。 
9. テンプレート仮想マシン内で、入れ子になった仮想化を設定し、複数の仮想マシンがある仮想ネットワークを構成します。 詳細な手順については、「[Azure VM で入れ子になった仮想化を有効にする方法](../../virtual-machines/windows/nested-virtualization.md)」を参照してください。 手順の概要を次に示します。 
    1. テンプレート仮想マシンで Hyper-V 機能を有効にします。
    2. 入れ子になった仮想マシンにインターネット接続を使用して内部仮想ネットワークを設定します
    3. Hyper-V マネージャーで仮想マシンを作成します
    4. IP アドレスを仮想マシンに割り当てます
10. [Configure template]\(テンプレートの構成\) ページの **[次へ]** を選択します。 
11. **[Publish the template]** \(テンプレートの発行\) ページで、次の操作を行います。 
    1. テンプレートをすぐに発行するには、**[Publish]\(発行\)** を選択します。  

        > [!WARNING]
        > 一度発行すると、再発行することはできません。 
    2. 後で発行する場合は、**[後のために保存]** を選択します。 ウィザードが完了した後に、テンプレート VM を発行することができます。 ウィザード完了後の構成および発行方法の詳細については、「[クラスルーム ラボの管理](how-to-manage-classroom-labs.md)」記事の「[テンプレートを発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

        ![テンプレートを発行する](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. テンプレートの**発行に関する進行状況**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. テンプレートが正常に発行されると、次のページが表示されます。 **[完了]** を選択します。

    ![テンプレートの発行 - 正常](../media/tutorial-setup-classroom-lab/publish-success.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>次の手順

これで、各ユーザーは、内部にマルチ VM 環境を含む 1 つの仮想マシンを取得するようになりました。 ラボにユーザーを追加して登録リンクを送信する方法については、次の記事を参照してください。[ラボへのユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。