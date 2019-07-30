---
title: Azure Lab Services でクラスルーム ラボを管理する | Microsoft Docs
description: クラスルーム ラボを作成して構成したり、すべてのクラスルーム ラボを表示したり、登録リンクをラボ ユーザーと共有したり、ラボを削除したりする方法について説明します。
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 6ba41132c93ebdb2578bafb100416ca3fe579298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123279"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボを管理する 
この記事では、クラスルーム ラボの作成および削除方法について説明します。 また、ラボ アカウントのすべてのクラスルーム ラボを表示する方法についても説明します。 

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 ラボ所有者は、「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」の記事の手順に従って、他のユーザーをラボの作成者ロールに追加できます。

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. **[サインイン]** をクリックします。 ラボ アカウントに**ラボの作成者**のメンバーである**ユーザー ID** を入力または作成し、パスワードを入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. ラボでの**仮想マシンの数**の最大値を指定します。 ラボの仮想マシンの数は、後で増やしたり減らしたりできます。 
    6. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Select virtual machine specifications]** \(仮想マシンの仕様の選択\) ページで、次の手順を実行します。
    1. ラボで作成する仮想マシン (VM) の **[サイズ]** を選択します。 現時点では、 **[小]** 、 **[中]** 、 **[中 (仮想化)]** 、 **[大]** 、および **[GPU]** のサイズが許可されます。 詳細については、「[VM サイズ](#vm-sizes)」セクションをご覧ください。
    1. VM を作成する**リージョン**を選択します。 
    1. ラボで VM の作成に使用する **VM イメージ**を選択します。 Linux イメージを選択した場合は、リモート デスクトップ接続を有効にするためのオプションが表示されます。 詳細については、[Linux のリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。
    1. **[次へ]** を選択します。

        ![VM 仕様の指定](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **[資格情報の設定]** ページで、ラボ内のすべての VM に使う既定の資格情報を指定します。 
    1. ラボ内のすべての VM に使う**ユーザーの名前**を指定します。
    2. ユーザーの**パスワード**を指定します。 

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. 学生に自分自身のパスワードを設定させる場合は、 **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションを無効にします。 この手順は**省略可能**です。 

        教師は、ラボ内のすべての VM に同じパスワードを使用するか、学生に自分の VM のパスワードを設定させるかを選択できます。 既定では、この設定は、Ubuntu を除き、すべての Windows と Linux のイメージで有効になっています。 **Ubuntu** VM を選択すると、この設定は無効になり、初めてサインインしたときに、パスワードを設定するよう求めるプロンプトが学生に表示されます。
    1. **作成** を選択します。 

        ![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **[Configure template**]\(テンプレートの構成\) ページで、ラボの作成プロセスの状態を確認します。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 ラボ内のテンプレートは仮想マシンの基本イメージで、すべてのユーザーの仮想マシンがこのイメージに基づいて作成されます。 テンプレート仮想マシンを設定して、ラボ ユーザーに提供する正しい仮想マシンが構成されるようにします。  

    ![テンプレートの構成](../media/tutorial-setup-classroom-lab/configure-template.png)
7. テンプレートの構成が完了すると、次のページが表示されます。 

    ![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. このチュートリアルでは、以下の手順は省略可能です。 
    2. **[接続]** を選択してテンプレート VM に接続します。 Linux のテンプレート VM の場合は、SSH と RDP のどちらを使用して接続するかを選択します (RDP が有効な場合)。
    1. **[パスワードのリセット]** を選択して、VM のパスワードをリセットします。 
    1. テンプレート VM にソフトウェアをインストールして構成します。 
    1. VM を**停止**します。  
    1. テンプレートの**説明**を入力します。
9. [Configure template]\(テンプレートの構成\) ページの **[次へ]** を選択します。 
10. **[Publish the template]** \(テンプレートの発行\) ページで、次の操作を行います。 
    1. テンプレートをすぐに発行するには、 *[I understand I can't modify the template after publishing.This process can only be done once and can take up to an hour]* \(発効したらテンプレートを変更できなくなること、このプロセスは一度のみ実行でき最大 1 時間かかる可能性があることを理解しています\) チェックボックスをオンにし、 **[発行]** を選択します。  テンプレートを発行して、対象のラボ ユーザーがテンプレート VM のインスタンスを利用できるようにします。

        > [!WARNING]
        > 一度発行すると、再発行することはできません。 
    2. 後で発行する場合は、 **[後のために保存]** を選択します。 ウィザードが完了した後に、テンプレート VM を発行することができます。 ウィザード完了後の構成および発行方法の詳細については、「[クラスルーム ラボの管理](how-to-manage-classroom-labs.md)」記事の「テンプレートを発行する」セクションを参照してください。

        ![テンプレートを発行する](../media/tutorial-setup-classroom-lab/publish-template.png)
11. テンプレートの**発行に関する進行状況**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. テンプレートが正常に発行されると、次のページが表示されます。 **[完了]** を選択します。

    ![テンプレートの発行 - 正常](../media/tutorial-setup-classroom-lab/publish-success.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. **[仮想マシン]** ページに切り替えて、 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>VM サイズ  

| Size | コア | RAM | 説明 | 
| ---- | ----- | --- | ----------- | 
| Small | 2 | 3.5 GB | このサイズは、コマンド ライン、Web ブラウザーの起動、トラフィックが少ない Web サーバー、中小規模のデータベースに最適です。 |
| Medium | 4 | 7 GB | このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。 | 
| 中 (入れ子になった仮想化) | 4 | 16 GB | このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。 また、このサイズは入れ子になった仮想化もサポートしています。 <p>このサイズは、それぞれの学生が複数の VM を必要とするシナリオで使用できます。 教師は、入れ子になった仮想化を使用して、仮想マシン内に、小さいサイズの入れ子になった仮想マシンをいくつか設定することができます。 </p> |
| Large | 8 | 32 GB | このサイズは、高速の CPU、ローカル ディスクのよりすぐれたパフォーマンス、大規模なデータベース、大きなメモリ キャッシュを必要とするアプリケーションに最適です。 このサイズは、入れ子になった仮想化もサポートします。 |  
| GPU | 12 | 112 GB | このサイズは、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードに最適です。 | 

## <a name="view-all-classroom-labs"></a>すべてのクラスルーム ラボを表示する
1. [Azure Lab Services ポータル](https://labs.azure.com)に移動します。
2. **[サインイン]** をクリックします。 ラボ アカウントに**ラボの作成者**のメンバーである**ユーザー ID** を入力または作成し、パスワードを入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. 選択したラボ アカウント内にすべてのラボが表示されることを確認します。 

    ![すべてのラボ](../media/how-to-manage-classroom-labs/all-labs.png)
3. 上部にあるドロップダウン リストを使用して、別のラボ アカウントを選択できます。 選択したラボ アカウントのラボが表示されます。 

## <a name="delete-a-classroom-lab"></a>クラスルーム ラボを削除する
1. ラボ用のタイルで、隅にある 3 つのドット (...) を選択します。 

    ![ラボを選ぶ](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. **[削除]** を選択します。 

    ![[削除] ボタン](../media/how-to-manage-classroom-labs/delete-button.png)
3. **[ラボの削除]** ダイアログ ボックスで、 **[削除]** を選択します。 

    ![[削除] ダイアログ ボックス](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>別のクラスルーム ラボに切り替える
現在のクラスルーム ラボから別のクラスルーム ラボに切り替えるには、一番上のラボ アカウントにあるラボのドロップダウン リストを選択します。

![一番上のドロップダウン リストからラボを選択する](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)

