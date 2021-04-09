---
title: Azure Lab Services でクラスルーム ラボにアクセスする方法 | Microsoft Docs
description: クラスルーム ラボの登録、自分がアクセスできるすべてのラボの確認、ラボの VM の開始/停止、および VM への接続の方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433140"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボにアクセスする方法
この記事では、クラスルーム ラボを登録したり、自分がアクセスできるすべてのラボを確認したり、ラボの VM を開始または停止したり、VM に接続したりする方法について説明します。 

## <a name="register-to-the-lab"></a>ラボに登録する

1. 教師から受け取った **登録 URL** に移動します。 登録完了後は、登録 URL を使用する必要はありません。 代わりに [https://labs.azure.com](https://labs.azure.com) という URL を使用します。 Internet Explorer 11 はまだサポートされていません。 

    ![ラボに登録する](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. 学校アカウントを使ってサービスにサインインし、登録を完了します。 

    > [!NOTE]
    > Azure Lab Services を使用するためには Microsoft アカウントが必要です。 Yahoo アカウントや Google アカウントなど、Microsoft 以外のアカウントを使用してポータルへのサインインを試みる場合は、Microsoft 以外のアカウントにリンクする Microsoft アカウントの作成手順に従ってください。 そのうえで、登録プロセスの実施手順に従います。 
1. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 

    ![アクセス可能な VM](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. 仮想マシンの準備が完了するまで待ちます。 VM タイルで次のフィールドを確認します。
    1. タイルの上部に、**ラボの名前** が表示されます。
    1. その右側には、VM の **オペレーティング システム (OS)** を表すアイコンが表示されます。 この例では Windows OS です。 
    1. タイルの一番下には、VM の起動と停止、VM への接続に使用するアイコンとボタンが表示されます。 
    1. それらのボタンの右側には、VM の状態が表示されます。 VM の状態が **停止済み** と表示されていることを確認します。

        ![VM が停止状態](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM を起動または停止する
1. 次の画像に示すように、先頭のボタンを選択して、VM を **起動** します。 このプロセスには、ある程度時間がかかります。  

    ![VM を起動する](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM の状態が **実行中** に設定されていることを確認します。 

    ![VM が実行中の状態](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    先頭のボタンが **停止** 操作を表すアイコンに変化していることがわかります。 このボタンを選択すると、VM を停止することができます。 

## <a name="connect-to-the-vm"></a>VM に接続します

1. ラボの VM に **接続** するには、次の画像のように 2 つ目のボタンを選択します。 

    ![VM への接続](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 次のいずれかの手順を実行します。 
    1. **Windows** 仮想マシンの場合は、**RDP** ファイルをハード ディスクに保存します。 仮想マシンに接続するための RDP ファイルを開きます。 教師から提供された **ユーザー名** と **パスワード** を使用してマシンにサインインします。 
    3. **Linux** 仮想マシンの場合は、**SSH** または **RDP** を使用して接続できます (有効な場合)。 詳細については、[Linux マシンのリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。 
    1. **MAC** を使用してラボ VM に接続している場合は、次のセクションの手順に従ってください。 

## <a name="progress-bar"></a>進行状況バー 
タイルの進行状況バーは、ユーザーに割り当てられた[ クォータ時間](how-to-configure-student-usage.md#set-quotas-for-users)数に対して費やされた時間数を示しています。 この時間は、ラボに対してスケジュールされている時間のほかに、追加で割り当てられた時間です。 進行状況バーの色とその下に表示されるテキストは、次のようにシナリオによって異なります。

- クラスが進行中 (クラスのスケジュール内) である場合、進行状況バーは灰色表示され、クォータ時間は使用されていないことがわかります。 

    ![進行状況バーが灰色表示](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- クォータが割り当てられていない (ゼロ時間である) 場合は、進行状況バーの代わりに、"**Available during classes only (クラス中のみ使用可能)** " というテキストが表示されます。 
    
    ![クォータが設定されていないときの状態](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **クォータを使い果たす** と、進行状況バーが **赤色** になります。 

    ![進行状況バーが赤色](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- ラボに対してスケジュールされた時間の範囲外で、かつクォータ時間の一部が使用済みである場合には、進行状況バーが **青色** になります。 

    ![進行状況バーが青色](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>すべてのラボを表示する
ラボに登録した後、次の手順に従ってすべてのラボを表示できます。 

1. [https://labs.azure.com](https://labs.azure.com) に移動します。 Internet Explorer 11 はまだサポートされていません。 
2. ラボへの登録に使用したユーザー アカウントを使用してサービスにサインインします。 
3. 自分にアクセス権があるすべてのラボが表示されることを確認します。 

    ![すべてのラボを表示する](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
 