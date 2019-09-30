---
title: Azure Lab Services でクラスルーム ラボにアクセスする方法 | Microsoft Docs
description: このチュートリアルでは、教師によって設定されたクラスルーム ラボ内の仮想マシンにアクセスします。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 10813600621293e9f559129230193f81ea0c505a
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161535"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボにアクセスする方法
この記事では、クラスルーム ラボを登録したり、自分がアクセスできるすべてのラボを確認したり、ラボの VM を開始または停止したり、VM に接続したりする方法について説明します。 

## <a name="register-to-the-lab"></a>ラボに登録する

1. 教師から受け取った**登録 URL** に移動します。 登録完了後は、登録 URL を使用する必要はありません。 代わりに [https://labs.azure.com](https://labs.azure.com) という URL を使用します。 Internet Explorer 11 はまだサポートされていません。 
1. 学校アカウントを使ってサービスにサインインし、登録を完了します。 

    > [!NOTE]
    > Azure Lab Services を使用するためには Microsoft アカウントが必要です。 Yahoo アカウントや Google アカウントなど、Microsoft 以外のアカウントを使用してポータルへのサインインを試みる場合は、Microsoft 以外のアカウントにリンクする Microsoft アカウントの作成手順に従ってください。 そのうえで、登録プロセスの実施手順に従います。 
1. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 
1. 仮想マシンの準備が完了するまで待ちます。 VM タイルで次のフィールドを確認します。
    1. タイルの上部に、**ラボの名前**が表示されます。
    1. その右側には、VM の**オペレーティング システム (OS)** を表すアイコンが表示されます。 この例では Windows OS です。 
    1. タイルの一番下には、VM の起動と停止、VM への接続に使用するアイコンとボタンが表示されます。 
    1. それらのボタンの右側には、VM の状態が表示されます。 VM の状態が**停止済み**と表示されていることを確認します。

        ![VM が停止状態](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM を起動または停止する
1. 次の画像に示したように、先頭のボタンを選択して、VM を**起動**します。 このプロセスには、ある程度時間がかかります。  

    ![VM を起動する](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM の状態が**実行中**に設定されていることを確認します。 

    ![VM が実行中の状態](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    先頭のボタンが**停止**操作を表すアイコンに変化していることがわかります。 このボタンを選択すると、VM を停止することができます。 

## <a name="connect-to-the-vm"></a>VM に接続します

1. ラボの VM に**接続**するには、次の画像のように 2 つ目のボタンを選択します。 

    ![VM への接続](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 次のいずれかの手順を実行します。 
    1. **Windows** 仮想マシンの場合は、**RDP** ファイルをハード ディスクに保存します。 仮想マシンに接続するための RDP ファイルを開きます。 教師から提供された**ユーザー名**と**パスワード**を使用してマシンにサインインします。 
    3. **Linux** 仮想マシンの場合は、**SSH** または **RDP** を使用して接続できます (有効な場合)。 詳細については、[Linux マシンのリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。 
    1. **MAC** を使用してラボ VM に接続している場合は、次のセクションの手順に従ってください。 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>MAC から RDP を使用して VM に接続する
このセクションでは、MAC から RDP を使用して VM に接続する方法を学生向けに説明します。

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>手順 1:Microsoft リモート デスクトップを MAC にインストールする
1. MAC で App Store を開き、**Microsoft リモート デスクトップ**を検索します。

    ![Microsoft リモート デスクトップ](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 最新バージョンの Microsoft リモート デスクトップをインストールします。 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>手順 2:MAC から RDP を使用して VM にアクセスする
1. **Microsoft リモート デスクトップ**がインストールされているコンピューターで、ダウンロードした **RDP** ファイルを開きます。 VM への接続が開始されます。 

    ![VM への接続](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 次の警告が表示された場合は、 **[Continue]\(続行\)** を選択します。 

    ![証明書の警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. VM が表示されます。 

    > [!NOTE]
    > 次に示したのは、CentOS Linux VM の例です。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>進行状況バー 
タイルの進行状況バーは、ユーザーに割り当てられた[クォータ時間](how-to-configure-student-usage.md#set-quotas-for-users)数に対して費やされた時間数を示しています。 この時間は、ラボに対してスケジュールされている時間のほかに、追加で割り当てられる時間です。 進行状況バーの色とその下に表示されるテキストは、次のようにシナリオによって異なります。

- クラスが進行中 (クラスのスケジュール内) である場合、進行状況バーは灰色表示され、クォータ時間は使用されていないことがわかります。 

    ![進行状況バーが灰色表示](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- クォータが割り当てられていない (ゼロ時間である) 場合は、進行状況バーの代わりに、"**Available during classes only (クラス中のみ使用可能)** " というテキストが表示されます。 
    
    ![クォータが設定されていないときの状態](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **クォータを使い果たす**と、進行状況バーが**赤色**になります。 

    ![進行状況バーが赤色](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- ラボに対してスケジュールされた時間の範囲外で、かつクォータ時間の一部が使用済みである場合には、進行状況バーが**青色**になります。 

    ![進行状況バーが青色](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>すべてのクラスルーム ラボを表示する
ラボへの登録後は、次の手順に従ってクラスルーム ラボを表示できます。 

1. [https://labs.azure.com](https://labs.azure.com) に移動します。 Internet Explorer 11 はまだサポートされていません。 
2. ラボへの登録に使用したユーザー アカウントを使用してサービスにサインインします。 
3. 自分にアクセス権があるすべてのラボが表示されることを確認します。 

    ![すべてのラボを表示する](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
 